---
title: "Guided steps to creating Azure Resources with Terraform and GitHub"
datePublished: Fri May 09 2025 07:52:41 GMT+0000 (Coordinated Universal Time)
cuid: cmagi0s0u000008l4etjj7zv8
slug: guided-steps-to-creating-azure-resources-with-terraform-and-github
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746677163604/ef3c47f3-4187-4df4-9479-19aec922da4b.jpeg

---

# **What is Terraform?**

Terraform is an infrastructure-as-code tool that lets you build, change, and version cloud and on-prem resources safely and efficiently.

HashiCorp Terraform is an infrastructure-as-code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

## [**How does T**](https://developer.hashicorp.com/terraform/tutorials/oci-get-started)[**errafo**](https://developer.hashicorp.com/terraform/tutorials/docker-get-started)[**rm work?**](https://developer.hashicorp.com/terraform/tutorials/oci-get-started)

Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces (APIs). Providers enable Terraform to work with virtually any platform or service with an accessible API.

![Terraform creates and manages cloud platforms and services through their APIs](https://developer.hashicorp.com/_next/image?url=https%3A%2F%2Fcontent.hashicorp.com%2Fapi%2Fassets%3Fproduct%3Dterraform%26version%3Drefs%252Fheads%252Fv1.11%26asset%3Dwebsite%252Fimg%252Fdocs%252Fintro-terraform-apis.png%26width%3D2048%26height%3D644&w=3840&q=75&dpl=dpl_3AdfpxykDHVJjsBC36WbyjcUC3Zh align="left")

HashiCorp and the Terraform community have already written **thousands of providers** to manage many different types of resources and services. You can find all publicly available providers on the Terraform Registry, including Amazon Web Services (AWS), Azure, Google Cloud Platform (GCP), Kubernetes, Helm, GitHub, Splunk, DataDog, and many more.

[The co](https://developer.hashicorp.com/terraform/tutorials/oci-get-started)[re Ter](https://developer.hashicorp.com/terraform/tutorials/docker-get-started)[raform wo](https://developer.hashicorp.com/terraform/tutorials/oci-get-started)[rkflow](https://registry.terraform.io/) consists of three stages:

* **Write:** You define resources, which may be across multiple cloud providers and services. For example, you might create a configuration to deploy an application on virtual machines in a Virtual Private Cloud (VPC) network with security groups and a load balancer.
    
* **Plan:** Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration.
    
* **Apply:** On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. For example, if you update the properties of a VPC and change the number of virtual machines in that VPC, Terraform will recreate the VPC before scaling the virtual machines.
    

![The Terraform workflow has three steps: Write, Plan, and Apply](https://developer.hashicorp.com/_next/image?url=https%3A%2F%2Fcontent.hashicorp.com%2Fapi%2Fassets%3Fproduct%3Dterraform%26version%3Drefs%252Fheads%252Fv1.11%26asset%3Dwebsite%252Fimg%252Fdocs%252Fintro-terraform-workflow.png%26width%3D2038%26height%3D1773&w=3840&q=75&dpl=dpl_3AdfpxykDHVJjsBC36WbyjcUC3Zh align="left")

**Terraform Workflow**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717581219795/cea890a9-41ca-4b4a-8ab4-165272911e88.jpeg?auto=compress,format&format=webp align="left")

## [**Why T**](https://developer.hashicorp.com/terraform/tutorials/aws-get-started)[**erraf**](https://developer.hashicorp.com/terraform/tutorials/azure-get-started)[**orm?**](https://developer.hashicorp.com/terraform/tutorials/aws-get-started)

HashiCorp co-founder and CTO Armon Dadgar explains how Terraform solves infrastructure challenges.

#### **Prerequisites**

Before we begin, ensure you have the following prerequisites:

1. **Azure Subscription**: An active Azure subscription.
    
2. **Terraform**: Installed on your local machine. You can download it from Terraform's official site.
    
3. **Azure CLI**: Installed and configured on your local machine. Follow the installation Azure's official documentation.
    
4. [**Visual Stu**](https://developer.hashicorp.com/terraform/tutorials/gcp-get-started)[**dio Code**](https://registry.terraform.io/) **(VSC)**: Installed on your local machine.
    
5. [**GitHub Account**:](https://registry.terraform.io/) A GitHub account to store your Terraform configuration.
    

## **Follow the guide To Install Terraform**

1. ## **Install Terraform On Windows:**
    

We would be using [<mark>Chocolatey</mark>](https://chocolatey.org/install) to install Terraform on our Windows PC.

A. Go to the file explorer, type in **Windows PowerShell,** and click on **Run as Administrator.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746765926513/4c2511cd-0037-4a00-8776-3487c220d34d.jpeg align="center")

B. On the **PowerShell** terminal, type in `choco install terraform`. It would prompt a request to run script, type in **A** to run all as shown below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746766077843/5cbcbda6-a214-4ab7-94ff-a2b951efe51e.jpeg align="center")

Terraform is installed successfully.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746766252163/f8b937b9-8b3a-4924-8dda-06e5b6dee347.jpeg align="center")

C. Type in `terraform --version` to check if Terraform is installed and the version that was installed.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746766355893/50ea1cb6-9faf-46e4-a7ea-ed5ca8693064.jpeg align="center")

**D.** Then go to the official [<mark>Terraform Website</mark>](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) as seen in the image below - **"Select" "Windows**" Operating System to install Terraform.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717584683084/16a5edc0-c596-4ce1-934a-a4513d1b145f.png?auto=compress,format&format=webp align="left")

**E. Select ‘Windows’ operating system and** **click on Download for ‘386 version’**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746766460719/bfcebdf3-5adc-49ef-87ff-71c054028d7d.jpeg align="left")

**F.** **Open "Downloads" as highlight**ed in red and open the File when it downloads.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717585978149/132a0191-8a01-4020-ada8-01b83371f753.jpeg?auto=compress,format&format=webp align="left")

**Copy the Path of the downloaded File from your local System.**

**G.** Click on the Folder where the downloaded Terraform file is, you will be prompted to the location of the file on your PC.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717594655866/893fbffc-ea5c-4e62-94cb-7ecd65acbba4.jpeg?auto=compress,format&format=webp align="left")

**H. Extract Zip files -** Right click on file and select "Extract to "terraform\_1.8.2\_windows".

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717595146195/9bc682f7-ec88-49e7-a6d9-564491e615bc.jpeg?auto=compress,format&format=webp align="left")

**I. Copy the file path.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717595709391/f1186f53-aee9-465c-9088-47917eccb381.jpeg?auto=compress,format&format=webp align="left")

**J.** On your PC, search for **"Edit the system environment"** and **"Click" "Open".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717703736735/144a10fa-caee-4905-a0d7-4ed0153550cf.jpeg?auto=compress,format&format=webp align="left")

**k.** It will prompt you, **"Select" "Advanced"** and **"Click On" "Environment Variables".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717703869035/64cfae9c-b3b3-4d25-aeb4-3d08ef26f83d.png?auto=compress,format&format=webp align="left")

**L.** Under System variables, **"Click On" "Path"** and **"Edit".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717704031061/1cd9c420-4599-4849-81bb-f080e41320da.png?auto=compress,format&format=webp align="left")

**M. "Click On" "New"** and paste the file path you copied as seen in the image in step **"I".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717704368622/47570999-2531-478b-9175-ce18325e4936.png?auto=compress,format&format=webp align="left")

**N.** Validate if you have successfully installed Terraform. Run the command `terraform --version` to see the version of the Terraform installed.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746769646802/e4c905b3-3a81-4ffe-9386-941839f62962.png align="left")

2. ## **Follow the guide to install Terraform on UBUNTU:**
    

**A. Install Terraform on Linux:** Go to the official Terraform Website as seen in the Image - **"Select" "Linux**" Operating System to install Terraform - **"Select" "Ubuntu/Debian"** package manager and copy the command.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717705260262/3202847f-240f-4ab7-aa7e-b561d88077cb.png?auto=compress,format&format=webp align="left")

**B.** Open Ubuntu terminal and Run the command `sudo su` to enter user root - Paste the command copied from the terraform website and press **"Enter".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717705595644/807fbd72-66e6-41a5-bf31-53c51e762cfd.png?auto=compress,format&format=webp align="left")

**Successfully Installed**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717706211564/3b5bc46c-fada-4c99-8432-271a1eea64cf.png?auto=compress,format&format=webp align="left")

**C.** Validate if you have successfully installed terraform, Run command `terraform --version` to see the version of the terraform installed

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717706319426/8708bfac-c0a2-43b5-88d3-797127c8208f.png?auto=compress,format&format=webp align="left")

**D.** Run command `terraform` to see a list of terraform commands

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717706382439/15130671-42a0-4b79-8e2e-69f10f42ae20.png?auto=compress,format&format=webp align="left")

## Follow the guide to install Terraform on VS Code:

3. **Install Terraform on Visual Studio Code(VSC) -** On your PC, search for "Visual Studio Code" (VSC) and open it. - In Visual Studio Code, click on the Extensions icon located in the sidebar ( on the left side of the screen). - In the Extensions marketplace, type "HashiCorp Terraform" into the search bar.
    
    \- Find the "HashiCorp Terraform" extension in the search results and click on it.
    
    \- Click on the "Install" button to add the extension to your Visual Studio Code environment.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717706771531/d30bd8a4-9234-470d-be44-ee41a2ef4a81.jpeg?auto=compress,format&format=webp align="left")

**Step 1: Create a Repository in GitHub -** Log in to your GitHub account. - On the GitHub dashboard, click on the "New" button to create a new repository.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717708615749/537453a5-95dc-4e2b-8109-3fdb1d7a18c1.png?auto=compress,format&format=webp align="left")

**Step 1a:** Give your repository a name. - Add a description for your repository. -Tick the "Public" option to make your repository publicly accessible.

![]( align="left")

\- Tick the option to **"Add a README file."** - Click on the **"Create repository"** button to finalize and create your new repository.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717709499004/f733368a-03a0-47c3-98fc-974273b6265b.png?auto=compress,format&format=webp align="left")

**Step 2: Clone Repository from Visual Studio Code -** Click on **"Explorer"** and click **"Clone Repository".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717710840197/90d8efcc-bf5b-4608-8c55-2669b7444bed.png?auto=compress,format&format=webp align="left")

**i.** You will be prompted to pick a repository source, click on **"Clone from GitHub".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717711127935/058d55cf-9229-4330-87af-b3798fa10f39.jpeg?auto=compress,format&format=webp align="left")

**ii.** Click on **"Allow".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717711780163/8c5bf8d4-182c-43c0-9c86-5c3cdb0a11e2.png?auto=compress,format&format=webp align="left")

**iii.** Select **GitHub** Account.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717711905363/d781bf52-0ce2-47e2-a716-a1d500c2a8e6.png?auto=compress,format&format=webp align="left")

**iv.** Click on **"Authorize Visual-Studio-Code".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717712116529/e3a8a872-9d3a-4072-b77a-801ed63be46f.png?auto=compress,format&format=webp align="left")

**v.** Input **password** and confirm to authenticate user.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717712231239/b51109f5-c909-4ab1-a6de-5b22a41cf106.png?auto=compress,format&format=webp align="left")

**vi.** Click on **‘Open Visual Studio Code’.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717712842425/416465a4-76e1-4563-a3e9-67bf91c14e9b.png?auto=compress,format&format=webp align="left")

**vii.** Select **‘Repository’.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717712948485/a11cefac-1d86-48c1-9e8b-df50637b6ed9.png?auto=compress,format&format=webp align="left")

**Step 2: Create a Folder -** Click on **File** and Select **Open Folder.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717713238240/011087e8-c66a-4e89-8cc5-86887f4f6140.jpeg?auto=compress,format&format=webp align="left")

**Step 2a:** Click on **New folder** and name the folder "**terraform git clone**" or any name. Select this newly created folder. - Click on **"Select as repository destination"** to set it as the destination for your repository.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717713427068/48489da8-a075-4e05-b2ed-b302b660d6b8.png?auto=compress,format&format=webp align="left")

**i.** The prompt below will pop up **"Click On" "Open".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717713780125/5466c5ae-1384-4783-be52-1bf1b55b158d.png?auto=compress,format&format=webp align="left")

**ii. Verify Authors of File -** Check the box, **"Trust the authors of all files in the parent folder"**. Click on **"Yes, I trust the authors".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717713969195/8b9377d0-5a4b-4160-8b1e-1e5d26db53fa.png?auto=compress,format&format=webp align="left")

**Step 3: Create File - Click on the Icon**(red arrow points downwards) to create a new file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717714525323/45d18825-e9f5-4cb4-a3ca-81dfadaf7513.jpeg?auto=compress,format&format=webp align="left")

**Step 4: -** Go to the Terraform official site to select a provider [**https://registry.terraform.io/browse/providers**](https://registry.terraform.io/browse/providers) **\-** The URL in the browser is highlighted, showing the link to the latest version of the AzureRM provider: [`https://registry.terraform.io/providers/hashicorp/azurerm/latest`](https://registry.terraform.io/providers/hashicorp/azurerm/latest). - Click the "USE PROVIDER" button, which allows users to copy the code.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717715196146/e98ff64f-8747-437d-a823-bde846c002a2.png?auto=compress,format&format=webp align="left")

**Step 5:** Open the **"**[**main.tf**](http://main.tf/) **file"** and paste the provider code to define the Azure Resource Manager (azurerm) provider with a specified version (3.104.2) from the HashiCorp source.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717715918297/ab215a61-8210-4a74-9ae2-a7550f625371.png?auto=compress,format&format=webp align="left")

**Step 6: Create Resource Group -** The first resource group named `eunicerg` is for terraform **\-** Create another resource group named `nicelady-rg` in the `UK South` region for Azure

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717716265995/4cf51e55-b6a5-40d0-8b40-903c5eec7491.png?auto=compress,format&format=webp align="left")

**Step 6a: Create a Virtual Network -** Creates a virtual network named `testing-vnet` with an address space of`10.0.0.0/16`. - It is located in the same region (`UK South`) and resource group (`nicelady-rg`) as defined by the `eunicerg` resource group.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717716553064/5b8a3aee-c3e9-4194-9031-70a85acc248c.png?auto=compress,format&format=webp align="left")

**Step 6b: Create a Subnet -** Creates a subnet named `testing-sub` within the `testing-vnet` virtual network. - It has an address prefix of `10.0.2.0/24`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717716584141/72610e14-58dd-41ec-876f-d1aefbad6690.png?auto=compress,format&format=webp align="left")

**Step 7: Commit & Pus**h **Your Configuration to GitHub -** Navigate to the "Source Control" on the left pane and enter the commit message as "Initial commit for Terraform" before proceeding to click on the "Commit" button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717717498930/c0cf35d1-bd31-46e2-b286-a7155624f3aa.png?auto=compress,format&format=webp align="left")

**i.** Click On **"Save All & Commit Changes".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717718070784/7e50b90f-e970-4101-9b76-4488485d8a34.png?auto=compress,format&format=webp align="left")

**ii.** Click On **"Sync Changes"** and Click **"OK".**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717718215416/f643a3db-730d-4289-9328-c91dbacaf766.png?auto=compress,format&format=webp align="left")

**Step 8:** Open GitHub, navigate to the repository, and view the **main.tf** file and commit message.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717718422137/af3e92b5-ce93-40c1-9283-cec039c79fc9.png?auto=compress,format&format=webp align="left")

**i.** View the contents of **main.tf** file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717718654519/6648973f-554c-41bb-9038-84abf6888417.png?auto=compress,format&format=webp align="left")

**Step 9: Log into Azure - Open Terminal in VSC and run the command** `az login or az login --tenant <tenant Id>` to log into Azure.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717718983681/a240292f-16dc-42f9-9324-e00b78152009.png?auto=compress,format&format=webp align="left")

**i. Select Account.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717719204631/cadc9707-c212-48cf-90ab-9911eb99d861.png?auto=compress,format&format=webp align="left")

**ii.** Successfully Logged into Azure.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717719278318/a03aa2d7-3c5e-493b-b25e-bae13a21458a.png?auto=compress,format&format=webp align="left")

S**tep 10: Initialize Terraform -** In the terminal, initialize Terraform within your project directory - Run Command `terraform init` downloads the Azure provider and sets up your working directory.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717719525877/7cf5b921-9020-40fd-a516-1e0240075920.png?auto=compress,format&format=webp align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717719722697/859dd6a8-362f-4829-973d-48704b3c941e.png?auto=compress,format&format=webp align="left")

**Step 10: Plan and Apply Your Terraform Configuration -** Run command `terraform plan` to generate and review the execution plan, If everything looks good, apply the configuration.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717719859336/46585042-4d62-48ce-8f54-2d0e50163aa1.png?auto=compress,format&format=webp align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720148971/03105f45-99e3-4e24-8019-035a3d96617c.png?auto=compress,format&format=webp align="left")

**Step 10a: Apply configuration.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720225493/9204d93e-f817-42b9-bc79-8323f2446ee1.png?auto=compress,format&format=webp align="left")

Type `yes` when prompted to confirm the application.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720306971/98dc5264-4ad9-461d-8d3d-2e702d33e9f4.png?auto=compress,format&format=webp align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720325465/08595781-eebc-458a-b546-83ed69441559.png?auto=compress,format&format=webp align="left")

**Step 11: View Resources In Azure -** Navigate to **"Resource Groups"** and select **"nicelady-rg"** Once there, click on the "Overview" tab to view your virtual network.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720445087/2cb93b8f-fa19-434b-8a45-569143a8348b.png?auto=compress,format&format=webp align="left")

**Step 11a:** While staying within the overview tab of the virtual network, scroll down the left pane and choose "Settings." From there, select "Subnets" to access and view your subnet.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717720798293/c189d023-6379-4649-bb36-1dd3fe5edc3f.png?auto=compress,format&format=webp align="left")

**Step 12: Change Subnet Configuration** - To make a change to the subnet, update the `address_prefixes` in [`main.tf`](http://main.tf/).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717721269676/50b04b4d-cd51-4d83-88f7-83f5707cff04.png?auto=compress,format&format=webp align="left")

**Step 13: Update the Subnet Configuration -** Run `terraform apply` to apply the updated configuration.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717721886427/968aaaed-5df3-4c07-a696-0336cb78973b.png?auto=compress,format&format=webp align="left")

**i.** Confirm the update by typing `yes`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717722008844/2d7775b9-1166-4703-9864-88533c1c82dd.png?auto=compress,format&format=webp align="left")

**ii. Successfully made changes.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717722138433/e7db9b79-3b37-4366-bbc1-f7134c989b3f.png?auto=compress,format&format=webp align="left")

**Step 14: Destroy the Resources -** If you want to clean up and remove the resources, use the destroy command.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717722365929/c5607443-646c-4be3-bf06-c1bf262ab67d.png?auto=compress,format&format=webp align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717722512106/dcc2fdef-3a81-4894-8325-de1a58a4f6f6.png?auto=compress,format&format=webp align="left")

**i. Confirm the destruction by typing** `yes`**.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746775810765/d0a1c8d0-ade5-49a8-b939-e804cb5c4393.jpeg align="left")

**Conclusion**

Congratulations! You have successfully created, modified, and destroyed Azure resources using Terraform. By following this guide, you have also learned how to manage your Terraform configurations using GitHub. This foundational knowledge will help you as you continue to explore the power of Infrastructure as Code with Terraform on Azure.