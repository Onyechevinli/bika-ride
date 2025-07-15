---
title: "Building And Testing a Basic Terraform Module"
datePublished: Tue Jul 15 2025 07:58:56 GMT+0000 (Coordinated Universal Time)
cuid: cmd48qvkt000x02k66gmmf4rt
slug: building-and-testing-a-basic-terraform-module
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1752340366968/aba1abe1-27f1-4006-8930-2ff0e0925531.jpeg
tags: cloud, linux, aws, cloud-computing, devops, terraform, aws-certified-solutions-architect-associate, aws-cli, terraform-cloud, terraform-module

---

Terraform modules are a good way to abstract out repeated chunks of code, making them reusable across other Terraform projects and configurations. We'll create a basic Terraform module from scratch and then test it.

### **Prerequisite:**

1. Please ensure that you have the AWS CLI installed on your Linux VM.
    
2. You can use the command `sudo snap install aws-cli --classic`
    
3. Also, run `aws configure` to authenticate, you would have to generate your ‘**Access key’** and ‘**Secret access key’** from the AWS console.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752562282918/76b6a61f-809f-4ba7-9a37-0d0b1ccd8a05.jpeg align="center")
    
    4. Open the terminal on your Linux VM, and type `aws configure`, put in the Access key and Secret access key, then press enter twice for the ‘region’ and ‘output format’.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752562428501/3075034c-97a5-4050-ba26-b34734aac4b8.jpeg align="center")
    

Note: the Access key and Secret access key are very sensitive materials and, as such, are to be protected at all times, as they can be used to access your AWS account without permission. So once you are done with the project, kindly deactivate the access key from the console.

5. Ensure you have installed Terraform on your Linux VM as well.
    

### **Create the Directory Structure for the Terraform Project**:

1. Check the Terraform status using the version command: `terraform version`
    
2. Since the Terraform version is returned, you have validated that the Terraform binary is installed and functioning properly. Note: If you receive a notification that there is a newer version of Terraform available, you can ignore it
    
3. Go into the root by typing: `sudo su -`
    
4. Create a new directory called ‘terraform\_project’ to house your Terraform code: `mkdir terraform_project`
    
5. Switch to this main project directory: `cd terraform_project`
    
6. Create a custom directory called modules and a directory inside it called vpc: `mkdir -p modules/vpc`
    
7. Switch to the vpc directory using the absolute path: `cd modules/vpc/`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752559348701/354602c4-f82d-4027-8c29-bb587ad8c59c.jpeg align="center")

### **Write Your Terraform VPC Module Code:**

1. Using Vim, create a new file called main.tf: `vim main.tf`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752559751027/c558e9a7-a0ae-4016-b9b8-83b3ddb1e155.jpeg align="center")
    
2. In the file, insert and review the provided code:
    

*<mark>provider "aws" {</mark>*

*<mark>region = var.region</mark>*

*<mark>}</mark>*

*<mark>resource "aws_vpc" "this" {</mark>*

*<mark>cidr_block = "10.0.0.0/16"</mark>*

*<mark>}</mark>*

*<mark>resource "aws_subnet" "this" {</mark>*

*<mark>vpc_id = aws_</mark>*[*<mark>vpc.this.id</mark>*](http://vpc.this.id)

*<mark>cidr_block = "10.0.1.0/24"</mark>*

*<mark>}</mark>*

*<mark>data "aws_ssm_parameter" "this" {</mark>*

*<mark>name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"</mark>*

*<mark>}</mark>*

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752559778354/f1c13f25-2115-4990-8b85-9b82fe7228d8.jpeg align="center")

3. Press Escape and type `:wq` to save and exit the file.
    
4. Create a new file called variables.tf: `vim variables.tf`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752559968246/7736bb6b-70f2-47a6-a487-02a206d22895.jpeg align="center")
    
5. In the file, insert and review the provided code:
    
    *<mark>variable "region" {</mark>*
    
    *<mark>type = string</mark>*
    
    *<mark>default = "us-east-1"</mark>*
    
    *<mark>}</mark>*
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752559980398/06544cb3-3b6e-4a2a-9ca0-c5427c647837.jpeg align="center")
    
6. Press Escape and type `:wq` to save and exit the file.
    
7. Create a new file called outputs.tf: `vim outputs.tf`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752560064980/56fe2b0c-bcd1-4168-af40-15b69ac34bc4.jpeg align="center")
    
8. In the file, insert and review the provided code:
    
    *<mark>output "subnet_id" {</mark>*
    
    *<mark>value = aws_</mark>*[*<mark>subnet.this.id</mark>*](http://subnet.this.id)
    
    *<mark>}</mark>*
    
    *<mark>output "ami_id" {</mark>*
    
    *<mark>value = </mark>* [*<mark>data.aws</mark>*](http://data.aws)*<mark>_ssm_parameter.this.value</mark>*
    
    *<mark>}</mark>*
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752560073779/fc617a72-5eda-4d0a-ae67-5740663ab4e8.jpeg align="center")
    

**Note:** The code in outputs.tf is critical to exporting values to your main Terraform code, where you'll be referencing this module. Specifically, it returns the subnet and AMI IDs for your EC2 instance.

9. Press Escape and type `:wq` to save and exit the file.
    

### **Write Your Main Terraform Project Code:**

1. Switch back to the main project directory: `cd ~/terraform_project`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752560937182/f68209d2-01b7-46e5-854a-83e9753ffba7.jpeg align="center")

2. Create a new file called main.tf: `vim main.tf`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752561024839/b5debc28-c24f-49af-989d-209f9a02013e.jpeg align="center")

3. In the file, insert and review the provided code:
    
    *<mark>variable "main_region" {</mark>*
    
    *<mark>type = string</mark>*
    
    *<mark>default = "us-east-1"</mark>*
    
    *<mark>}</mark>*
    
    *<mark>provider "aws" {</mark>*
    
    *<mark>region = var.main_region</mark>*
    
    *<mark>}</mark>*
    
    *<mark>module "vpc" {</mark>*
    
    *<mark>source = "./modules/vpc"</mark>*
    
    *<mark>region = var.main_region</mark>*
    
    *<mark>}</mark>*
    
    *<mark>resource "aws_instance" "my-instance" {</mark>*
    
    *<mark>ami = module.vpc.ami_id</mark>*
    
    *<mark>subnet_id = module.vpc.subnet_id</mark>*
    
    *<mark>instance_type = "t2.micro"</mark>*
    
    *<mark>}</mark>*
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752561049667/a974e1d1-f77d-4f0d-8174-867567d8139f.jpeg align="center")

Note: The code in [main.tf](http://main.tf) invokes the VPC module that you created earlier. Notice how you're referencing the code using the source option within the module block to let Terraform know where the module code resides.

4. Press Escape and type `:wq` to save and exit the file.
    
5. Create a new file called [outputs.tf](http://outputs.tf): `vim` [`outputs.tf`](http://outputs.tf)
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752561190088/129c3f1c-33c7-437d-b17d-ba90f2c63144.jpeg align="center")

6. In the file, insert and review the provided code:
    
    *<mark>output "PrivateIP" {</mark>*
    
    *<mark>description = "Private IP of EC2 instance"</mark>*
    
    *<mark>value = aws_</mark>*[*<mark>instance.my</mark>*](http://instance.my)*<mark>-instance.private_ip</mark>*
    
    *<mark>}</mark>*
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752561172192/f3c70b41-3e0e-4df0-b13c-a6ae356f923c.jpeg align="center")

7. Press Escape and type in`:wq` to save and exit the file.
    

### **Deploy Your Code and Test Out Your Module:**

1. Format the code in all of your files in preparation for deployment: `terraform fmt -recursive`
    
2. Initialize the Terraform configuration to fetch any required providers and get the code being referenced in the module block: `terraform init`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752563618215/2561eafe-4ced-453e-8f9b-8ebcf9c6a7f8.jpeg align="center")
    
3. Validate the code to look for any errors in syntax, parameters, or attributes within Terraform resources that may prevent it from deploying correctly: `terraform validate`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752564258251/8fb10ca3-e651-42c5-9962-ab831b0c7b2d.jpeg align="center")
    
4. You should receive a notification that the configuration is valid.
    
5. Review the actions that will be performed when you deploy the Terraform code: `terraform plan`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752564551183/de4a0754-5bb6-46dd-b0e5-885bf19aff8c.jpeg align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752564769805/17cf2c33-e3f8-431d-98af-357e5f5a7e49.jpeg align="center")
    
6. In this case, it will create 3 resources, which include the EC2 instance configured in the root code and any resources configured in the module. If you scroll up and view the resources that will be created, any resource with module.vpc in the name will be created via the module code, such as module.vpc.aws\_vpc.this.
    
7. Deploy the code: `terraform apply --auto-approve`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752565019589/f6a01b7d-9748-4a9d-a2e1-b7e264eefee6.jpeg align="center")
    
    Note: The `--auto-approve` flag will prevent Terraform from prompting you to enter yes explicitly before it deploys the code.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752565035096/f2fe76aa-aa8c-4029-8ca6-51db204cbef0.jpeg align="center")
    
8. Once the code has executed successfully, note in the output that 3 resources have been created and the private IP address of the EC2 instance is returned as configured in the outputs.tf file in your main project code.
    
9. View all of the resources that Terraform has created and is now tracking in the state file: `terraform state list`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752565569488/d94735ca-09b4-42a1-9c25-6c479be885eb.jpeg align="center")
    
10. The list of resources should include your EC2 instance, which was configured and created by the main Terraform code, and 3 resources with the module.vpc in the name, which were configured and created via the module code.
    
11. Tear down the infrastructure you just created before moving on: `terraform destroy`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752565729212/8fb31c82-be8c-4a99-95ce-51ac7b30ff29.jpeg align="center")
    
12. When prompted, type yes and press Enter.
    
    ### **Conclusion:**
    
    Congratulations — you've completed this hands-on lab!