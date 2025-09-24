---
title: "Creating and deploying a Web application(Nginx) on AWS using Terraform IaC."
datePublished: Wed Sep 24 2025 06:21:34 GMT+0000 (Coordinated Universal Time)
cuid: cmfxlj5my000002jm97x30a6k
slug: creating-and-deploying-a-web-applicationnginx-on-aws-using-terraform-iac
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1758689837972/bb32c2ad-9846-4158-83da-fdb7c2e97131.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1758694836543/f3b5ac50-e1e6-47c4-a62d-0be5442f3dca.webp
tags: aws, nginx, terraform, vs-code

---

Creating a web application on AWS using Terraform involves defining and provisioning your infrastructure as code. This approach offers consistency, repeatability, and version control for your AWS resources.

**Prerequisites:**

* **AWS Account:** An active AWS account with necessary permissions.
    
* **AWS CLI:** Installed and configured with your credentials.
    
* **Terraform:** Installed on your local machine.
    
* VS-Code: Installed the application on your PC, as we would be performing the task on it.
    

**General Steps:**

Put on your VS-Code and build your IaC

* **Set up your Terraform project:**
    
    * Create a dedicated directory for your project in VS-Code by creating a new folder.
        
    * Inside, create [`main.tf`](http://main.tf), [`variables.tf`](http://variables.tf), and [`outputs.tf`](http://outputs.tf) (in this case, it is embedded in the `main.tf`) files.
        
    * Define the AWS provider in [`main.tf`](http://main.tf)(the image to be used in this case is Nginx):
        

```plaintext
# Main Terraform configuration file for setting up AWS infrastructure
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# AWS Provider configuration
provider "aws" {
  region = "us-east-1"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true
  
  tags = {
    Name = "nginx-vpc"
  }
}

# Create public and private subnets
resource "aws_subnet" "public" {
  count             = 2
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index}.0/24"
  availability_zone = "us-east-1${count.index == 0 ? "a" : "b"}"
  map_public_ip_on_launch = true
  
  tags = {
    Name = "nginx-public-${count.index}"
  }
}

resource "aws_subnet" "private" {
  count             = 2
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 2}.0/24"
  availability_zone = "us-east-1${count.index == 0 ? "a" : "b"}"
  
  tags = {
    Name = "nginx-private-${count.index}"
  }
}

# Create Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
  
  tags = {
    Name = "nginx-igw"
  }
}

resource "aws_eip" "nat" {
  tags = {
    Name = "nginx-nat-eip"
  }
}

# Create NAT Gateway
resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public[0].id
  
  tags = {
    Name = "nginx-nat"
  }
   depends_on = [aws_internet_gateway.igw]
}

# Create Route Tables
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
  
  tags = {
    Name = "nginx-public-rt"
  }
}

resource "aws_route_table" "private" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat.id
  }
  
  tags = {
    Name = "nginx-private-rt"
  }
}

# Associate Route Tables with Subnets
resource "aws_route_table_association" "public" {
  count          = 2
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "private" {
  count          = 2
  subnet_id      = aws_subnet.private[count.index].id
  route_table_id = aws_route_table.private.id
}

# Create Security Groups
resource "aws_security_group" "alb_sg" {
  name        = "nginx-alb-sg"
  description = "Security group for ALB"
  vpc_id      = aws_vpc.main.id
  
  # Allow HTTP from anywhere
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "nginx-alb-sg"
  }
}

resource "aws_security_group" "ecs_service_sg" {
  name        = "ecs-service-sg"
  description = "Security group for ECS tasks"
  vpc_id      = aws_vpc.main.id
  
  # Allow traffic from ALB only
  ingress {
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_security_group.alb_sg.id]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "nginx-ecs-sg"
  }
}

resource "aws_security_group" "rds_sg" {
  name        = "nginx-rds-sg"
  description = "Security group for RDS"
  vpc_id      = aws_vpc.main.id
  
  # Allow MySQL from ECS only
  ingress {
    from_port       = 5432
    to_port         = 5432
    protocol        = "tcp"
    security_groups = [aws_security_group.ecs_service_sg.id]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "nginx-rds-sg"
  }
}

# Create Application Load Balancer
resource "aws_lb" "alb" {
  name               = "nginx-alb"
  load_balancer_type = "application"
  internal           = false
  subnets            = aws_subnet.public[*].id
  security_groups    = [aws_security_group.alb_sg.id]

  tags = {
    Name = "nginx-alb"
  }
}

# Create ALB Target Group
resource "aws_lb_target_group" "nginx_tg" {
  name        = "nginx-tg"
  port        = 80
  protocol    = "HTTP"
  target_type = "ip"
  vpc_id      = aws_vpc.main.id
 
  health_check {
    path                = "/"
  }
}

# Create ALB Listener
resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.alb.arn
  port              = "80"
  protocol          = "HTTP"
  
 default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.nginx_tg.arn
  }
}
resource "aws_lb_target_group" "main" {
  name     = "app-tg"
  port     = 3000
  protocol = "HTTP"
  target_type = "ip"
  vpc_id   = aws_vpc.main.id
  health_check {
    path                = "/health"
    interval            = 30
    timeout             = 5
    healthy_threshold   = 2
    unhealthy_threshold = 2
    matcher             = "200-399"
  }
  tags = {
    Name = "nginx-app-tg"
  }
}

# Create ECS Cluster
resource "aws_ecs_cluster" "nginx_cluster" {
  name = "nginx-cluster"
  
  setting {
    name  = "containerInsights"
    value = "enabled"
  }
  
  tags = {
    Name = "nginx-ecs-cluster"
  }
}

# Create ECS Task Definition (NGINX)
resource "aws_ecs_task_definition" "nginx_task" {
  family                   = "nginx-task"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = 256
  memory                   = 512
  execution_role_arn      = aws_iam_role.ecs_task_execution_role.arn
  task_role_arn            = aws_iam_role.ecs_task_role.arn
  
  container_definitions = jsonencode([
    {
      name      = "nginx"
      image     = "nginx:latest"  # Replace with your image
      essential = true
      portMappings = [
        {
          containerPort = 80
          hostPort      = 80
          protocol      = "tcp"
        }
      ]
    
      logConfiguration = {
        logDriver = "awslogs"
        options = {
          awslogs-group         = aws_cloudwatch_log_group.ecs.name
          awslogs-region        = "us-east-1"
          awslogs-stream-prefix = "backend"
        }
      }
    }
  ])
  
  tags = {
    Name = "nginx-backend-task"
  }
}

resource "aws_lb_target_group" "backend" {
  name        = "ecs-app-tg"
  port        = 8080
  protocol    = "HTTP"
  target_type = "ip"
  vpc_id      = aws_vpc.main.id
  health_check {
    path                = "/health"
    interval            = 30
    timeout             = 5
    healthy_threshold   = 2
    unhealthy_threshold = 2
    matcher             = "200-399"
  }
  tags = {
    Name = "nginx-ecs-app-tg"
  }
}

# Create ECS Service
resource "aws_ecs_service" "nginx_service" {
  name            = "nginx-service"
  cluster         = aws_ecs_cluster.nginx_cluster.id
  task_definition = aws_ecs_task_definition.nginx_task.arn
  desired_count   = 2
  launch_type     = "FARGATE"

  network_configuration {
    subnets          = aws_subnet.public[*].id
    security_groups  = [aws_security_group.ecs_service_sg.id]
    assign_public_ip = true
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.nginx_tg.arn
    container_name   = "nginx"
    container_port   = 80
  }

  depends_on = [
    aws_lb.alb,
    aws_lb_target_group.nginx_tg,
    aws_lb_listener.http
  ]
}

resource "aws_db_subnet_group" "rds_subnet_group" {
  name       = "rds-subnet-group"
  subnet_ids = aws_subnet.private[*].id

  tags = {
    Name = "rds-subnet-group"
  }
}
# Create RDS MySQL
resource "aws_db_instance" "default" {
  allocated_storage    = 10
  db_name              = "clearworkdb"
  engine               = "mysql"
  engine_version       = "8.0"
  instance_class       = "db.t3.micro"
  username             = "nginx"
  password             = "Onyechevinli17"
  parameter_group_name = "default.mysql8.0" 
  db_subnet_group_name = aws_db_subnet_group.rds_subnet_group.name # ✅ use custom subnet group
  vpc_security_group_ids = [aws_security_group.rds_sg.id]
  skip_final_snapshot  = true
 
}

# Create CloudWatch Log Group
resource "aws_cloudwatch_log_group" "ecs" {
  name              = "/ecs/clearwork"
  retention_in_days = 30
  
  tags = {
    Name = "nginx-cw-log-group"
  }
}

# Create IAM Roles for ECS
resource "aws_iam_role" "ecs_task_execution_role" {
  name = "nginx-ecs-task-execution-role"
  
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ecs-tasks.amazonaws.com"
        }
      }
    ]
  })
  
  tags = {
    Name = "nginx-ecs-task-execution-role"
  }
}

resource "aws_iam_role_policy_attachment" "ecs_task_execution_role_policy" {
  role       = aws_iam_role.ecs_task_execution_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"
}

resource "aws_iam_role" "ecs_task_role" {
  name = "nginx-ecs-task-role"
  
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ecs-tasks.amazonaws.com"
        }
      }
    ]
  })
  
  tags = {
    Name = "nginx-ecs-task-role"
  }
}

# Output important values
output "alb_dns_name" {
  description = "DNS name of the ALB"
  value       = aws_lb.alb.dns_name
}
```

Below is an image of the setup in VS-code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758691021283/3ad80771-d767-4ed4-9072-6564a5728dd7.jpeg align="center")

* **Define variables in** [`variables.tf`](http://variables.tf):
    
    * Parameterize your configurations, such as AWS region, instance types, AMI IDs, and application-specific settings.
        

```plaintext
# Variables for ClearWork Infrastructure

variable "database_password" {
  description = "Password for the RDS database"
  type        = string
  sensitive   = true
}

variable "environment" {
  description = "Deployment environment (dev, staging, prod)"
  type        = string
  default     = "dev"
}

variable "domain_name" {
  description = "Domain name for the application"
  type        = string
  default     = "example.com"
}

variable "rds_engine_version" {
  description = "The RDS engine version"
  type        = string
  default     = "15.5"
}

variable "subnet_ids" {
  description = "List of subnet IDs for ECS tasks"
  type        = list(string)
  default     = ["subnet-0123456789abcdef0", "subnet-0fedcba9876543210"]
}
variable "vpc_id" {
  description = "The ID of the VPC where resources will be created"
  type        = string
}
```

Below is an image of the variables setup in VS-Code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758691371714/546242e3-5b11-49b2-bd2b-4289b975a432.jpeg align="center")

Follow this link to guide you on how to [connect your VS Code to an AWS account.](https://www.google.com/search?q=how+to+connect+your+vs+code+with+your+aws+account&oq=how+to+connect+your+vs+code+with+your+aws+account&gs_lcrp=EgZjaHJvbWUyBggAEEUYOdIBCjEwNTcyNmowajeoAgCwAgA&sourceid=chrome&ie=UTF-8)

* **Initialize Terraform:**
    

Go to the top left corner of the VS Code, click on the 3 dots, and select `Terminal`, then initiate the following code:

Code

```plaintext
    terraform init
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758693564336/6d516822-2aa5-4af8-9984-1121c921b958.jpeg align="center")

Plan the deployment.

Code

```plaintext
    terraform plan
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692176294/bfdd29b8-b800-40d7-80ad-d8010604a2a3.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692199537/1037ad1c-ac01-4d0b-b61e-031e094d379f.jpeg align="center")

This command shows you what resources Terraform will create, modify, or destroy.

Apply the configuration.

Code

```plaintext
    terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692241772/a0d39d7d-316e-4327-927c-0a4442a3931f.jpeg align="center")

**Confirm the prompt with "yes" to provision the resources in AWS.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692308743/778cddc2-8ca4-4378-8ecd-64d3c5951c56.jpeg align="center")

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692337520/2c70cc64-fa90-45c4-a327-05b8a38321de.jpeg align="center")
    
* **Access your web application:** Use the outputted DNS name or IP address to access your deployed web application.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692417755/cb153dc7-02cd-47f5-b43e-e738e70141ca.jpeg align="center")

In the AWS console, search for Load Balancer in the search bar:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692467120/28e619b8-f3ca-4242-867c-cef6f5510c67.jpeg align="center")

Select the created load balancer and copy the DNS name:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692490322/bcf835d3-0473-4bda-bb35-f03d702eb984.jpeg align="center")

Open a tab on your web browser and paste the DNS name. It should take you to the site you intended to create(Nginx):

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692541454/b3c81932-9e17-4301-b5c8-6570ce53405f.jpeg align="center")

Click on `Continue to site` to review the website for you, and in this case, Nginx:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692562243/0dfde59e-226b-41d5-8e32-2b45e5a00e2e.jpeg align="center")

* **Clean up (when needed):**
    

Code

```plaintext
    terraform destroy
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692607203/338b7bba-d8b3-47d7-bac8-217168bd6f70.jpeg align="center")

When prompted, type `yes` to continue:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692632259/d1b96606-6305-428a-8d94-8889ebbb736a.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1758692648788/8da3513c-ae84-41b7-8741-d419cef58fd5.jpeg align="center")

This command removes all resources provisioned by your Terraform configuration.

Please note that in the course of building your code or running it, like during the `Terraform plan`, you might encounter errors in the build. You have to troubleshoot the code by using any AI tool (ChatGPT, DeepSeek) to correct and adjust the code for you.