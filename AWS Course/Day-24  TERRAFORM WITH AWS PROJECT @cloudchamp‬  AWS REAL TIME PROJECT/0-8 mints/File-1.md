### Day 24: Terraform for AWS Infrastructure Automation

#### Overview of Terraform

**Concept**: Terraform is an open-source Infrastructure as Code (IaC) tool used to automate infrastructure on various cloud platforms. In this case, Terraform is used to create AWS resources such as EC2 instances, load balancers, VPCs, and more. Unlike AWS CloudFormation, which is AWS-specific, Terraform is platform-agnostic, meaning it can be used with multiple cloud providers.

**Scenario**: In previous videos, you learned how to create infrastructure manually through the AWS console and by using CloudFormation. Today, the focus is on automating the same infrastructure through Terraform, making the process scalable, repeatable, and efficient.

---

### Introduction to the Project
In this project, you will learn how to use Terraform to automate the creation of:
- VPC (Virtual Private Cloud)
- Subnets (public/private)
- Internet Gateway
- EC2 Instances
- Load Balancers
- S3 Buckets
- Target Groups for Load Balancer

---

### 1. **AWS IAM User Setup**

**Concept**: Terraform requires access to your AWS account via programmatic access (not the web console). This is done by setting up **AWS IAM (Identity and Access Management)** keys for authentication.

**Command**:
```bash
aws configure
```
**Explanation**:
- **Access Key**: This is your IAM user's access key, which allows programmatic access to AWS services.
- **Secret Access Key**: This is a secret associated with your access key that must be kept confidential.
- **Region**: AWS region where resources will be deployed (e.g., `us-east-1`).
- **Output Format**: Can be left as `None` or `JSON`.

**Scenario**: Terraform interacts with AWS services like EC2, S3, and load balancers via the IAM user. For example, if your IAM user doesn't have permissions for EC2, Terraform won’t be able to create instances.

---

### 2. **Setting Up Terraform Project**

**Concept**: Terraform configurations are written in `.tf` files. These define the desired state of your AWS infrastructure. You will create a directory for the project and initialize Terraform inside that directory.

**Command**:
```bash
mkdir terraform-aws-project
cd terraform-aws-project
code .
```
**Explanation**:
- **mkdir**: Creates a new directory for your project.
- **cd**: Changes the directory to the project folder.
- **code .**: Opens the folder in VS Code.

**Scenario**: In this project, you will write a Terraform configuration that defines the AWS infrastructure (e.g., EC2 instances, VPCs, subnets). The folder will hold all `.tf` files related to your infrastructure.

---

### 3. **Initializing Terraform**

**Concept**: Terraform requires initialization for any new configuration directory. The `terraform init` command initializes the working directory containing Terraform configuration files.

**Command**:
```bash
terraform init
```
**Explanation**: Initializes the Terraform configuration and downloads the necessary provider plugins (in this case, AWS).

**Scenario**: Terraform needs AWS provider plugins to communicate with AWS APIs. Without initializing, Terraform cannot function as it lacks the required components.

---

### 4. **Defining AWS Provider**

**Concept**: Terraform uses "providers" to interact with cloud platforms. For AWS, you define the AWS provider in your `.tf` file, specifying which AWS region you want to work with.

**Code**:
```hcl
provider "aws" {
  region = "us-east-1"
}
```
**Explanation**: This tells Terraform to use AWS as the cloud provider and sets the region to `us-east-1`.

**Scenario**: If your project is intended to deploy infrastructure in a specific region (like Virginia), you will specify that region here. This ensures that all your resources are created in that region.

---

### 5. **Creating an AWS VPC**

**Concept**: A **VPC (Virtual Private Cloud)** is a logically isolated network in AWS where you can launch AWS resources. Using Terraform, you define a VPC in code.

**Code**:
```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "MainVPC"
  }
}
```
**Explanation**:
- **cidr_block**: Defines the range of IP addresses for the VPC.
- **tags**: Adds metadata to help identify the VPC.

**Scenario**: The VPC forms the backbone of your infrastructure. All other resources like EC2 instances and subnets will reside in this VPC.

---

### 6. **Creating Subnets**

**Concept**: Subnets allow you to divide a VPC into smaller, logically isolated sections. There are public and private subnets that determine whether resources can be accessed from the internet.

**Code**:
```hcl
resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
  map_public_ip_on_launch = true
  availability_zone = "us-east-1a"

  tags = {
    Name = "PublicSubnet"
  }
}
```
**Explanation**:
- **vpc_id**: Associates the subnet with the VPC.
- **cidr_block**: Defines the range of IP addresses for the subnet.
- **map_public_ip_on_launch**: Automatically assigns a public IP to instances in the subnet.

**Scenario**: You can create multiple subnets for different purposes, such as isolating public-facing and internal services.

---

### 7. **Creating Internet Gateway**

**Concept**: An **Internet Gateway (IGW)** allows communication between your VPC and the internet.

**Code**:
```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
  tags = {
    Name = "MainInternetGateway"
  }
}
```
**Explanation**: Associates the internet gateway with your VPC.

**Scenario**: Without an internet gateway, your instances in the public subnet cannot connect to the internet. This is essential for web applications or external APIs.

---

### 8. **Creating EC2 Instance**

**Concept**: **EC2 (Elastic Compute Cloud)** instances are virtual machines in AWS that you can launch using Terraform.

**Code**:
```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345678"  # Replace with actual AMI ID
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  tags = {
    Name = "WebServer"
  }
}
```
**Explanation**:
- **ami**: The Amazon Machine Image (AMI) that the instance will use (e.g., a Linux image).
- **instance_type**: Specifies the size and type of instance (e.g., `t2.micro`).
- **subnet_id**: Associates the instance with a specific subnet.

**Scenario**: You’ll launch an EC2 instance in the public subnet, enabling access from the internet if needed. This EC2 instance could host a web server or any other service.

---

### 9. **Creating a Load Balancer**

**Concept**: **Load Balancers** distribute traffic across multiple instances to ensure high availability and reliability.

**Code**:
```hcl
resource "aws_lb" "my_lb" {
  name               = "my-load-balancer"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.lb_sg.id]
  subnets            = [aws_subnet.public_subnet.id]

  tags = {
    Name = "MyLoadBalancer"
  }
}
```
**Explanation**:
- **internal = false**: Specifies that the load balancer is internet-facing.
- **load_balancer_type**: Can be `application` or `network`.

**Scenario**: Load balancers improve the availability and reliability of your application by distributing incoming traffic to multiple EC2 instances.

---

### 10. **Deploying the Infrastructure**

**Concept**: Once the configuration files are written, Terraform can apply the changes and create the infrastructure.

**Commands**:
```bash
terraform plan
terraform apply
```
**Explanation**:
- **terraform plan**: Shows the changes Terraform will make without actually applying them.
- **terraform apply**: Applies the changes and creates the resources in AWS.

**Scenario**: Running `terraform apply` will create the VPC, subnets, EC2 instances, and all other defined resources in AWS.

---

### Summary

- **IAM Access Keys**: Required for Terraform to interact with AWS.
- **Terraform Setup**: Initialization and provider configuration for AWS.
- **VPC & Subnets**: Divides the network into isolated sections.
- **EC2 & Load Balancers**: Creates virtual machines and distributes traffic across them.

Terraform allows you to define and manage infrastructure in a declarative way. This ensures that your infrastructure is consistent, repeatable, and scalable across multiple environments.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------


### Day 24 of Free AWS DevOps Zero to Hero Series: Terraform for Automating AWS Infrastructure

In this video, we are learning how to use Terraform to automate infrastructure creation on AWS. We’ve previously seen how to create infrastructure using AWS Console, CloudFormation templates, and AWS CLI, but now we’ll focus on **Terraform**—a powerful Infrastructure-as-Code (IaC) tool. Here’s a detailed explanation of key concepts and commands, with real-world use cases.

---

### Introduction to Terraform:
**Terraform** is an open-source tool developed by HashiCorp that allows you to define and provision infrastructure on cloud providers like AWS, GCP, and Azure using a declarative configuration language.

---

### Why Terraform?
- **Infrastructure as Code (IaC)**: Terraform lets you define cloud resources in code, providing version control, repeatability, and easy scalability.
- **Cross-Platform**: Terraform works with multiple cloud providers, making it a versatile tool for multi-cloud or hybrid cloud strategies.
- **Resource Management**: With Terraform, you can define complex resources such as VPCs, subnets, EC2 instances, and load balancers using simple code.

---

### Setting Up the Environment:

#### 1. **IAM Role Creation for Terraform**
   - **Concept**: Terraform requires access to AWS services, which can be done via IAM (Identity and Access Management). You need to create **access keys** for the IAM user and set up Terraform with those credentials.
   - **Steps**:
     1. Log in to your AWS account and create an **IAM user** with programmatic access.
     2. Generate **access keys** (Access Key ID and Secret Access Key) for this user.
     3. Configure AWS CLI with these credentials using:
```bash
        aws configure
```
        - This command will prompt you to input Access Key ID, Secret Access Key, region, and output format.

   - **Purpose**: These access keys allow Terraform to interact with AWS and create resources like EC2 instances, S3 buckets, and more.

---

#### 2. **Creating a New Terraform Project**
   - **Command**: To organize your Terraform project, create a folder for your project:
 ```bash
     mkdir terraform-aws-project
     cd terraform-aws-project
 ```
   - **VS Code Setup**: Open your project in Visual Studio Code:
 ```bash
     code .
 ```
   - **Purpose**: Organizing your Terraform code in separate folders makes it easier to manage, especially for large infrastructure projects.

---

#### 3. **Writing the First Terraform Configuration File**
   - **Terraform Configuration File (main.tf)**:
 ```hcl
     provider "aws" {
       region = "us-east-1"
     }

     resource "aws_vpc" "main_vpc" {
       cidr_block = "10.0.0.0/16"
       tags = {
         Name = "MainVPC"
       }
     }

     resource "aws_subnet" "public_subnet" {
       vpc_id            = aws_vpc.main_vpc.id
       cidr_block        = "10.0.1.0/24"
       availability_zone = "us-east-1a"
       tags = {
         Name = "PublicSubnet"
       }
     }
 ```
   - **Explanation**:
     - **Provider**: Specifies the cloud provider you’re working with. In this case, AWS.
     - **Resource**: Defines the AWS resource you're provisioning. Here, it’s a **VPC** (Virtual Private Cloud) and a **subnet**.
     - **Tags**: Used to name and categorize resources in AWS.
   - **Purpose**: This is a simple setup where we define a VPC and a subnet inside it.

---

#### 4. **Initializing and Applying the Terraform Configuration**
   - **Terraform Initialization**:
 ```bash
     terraform init
 ```
     - **Purpose**: Initializes the working directory, downloads provider plugins (like AWS), and prepares Terraform for execution.
   
   - **Terraform Plan**:
 ```bash
     terraform plan
 ```
     - **Purpose**: Displays the execution plan and previews the changes Terraform will make to your AWS environment.

   - **Terraform Apply**:
 ```bash
     terraform apply
 ```
     - **Purpose**: Applies the Terraform configuration and provisions the AWS resources defined in your **main.tf** file.
   
   - **Output**: Terraform will output a success message once the resources are created, along with details such as the VPC ID.

---

### Real-World Use Case: Automating a Simple AWS Infrastructure with Terraform

In this project, we are automating the following infrastructure on AWS:
1. **VPC** (Virtual Private Cloud): A network that allows you to create your own cloud infrastructure.
2. **Subnets**: A segment of the VPC where resources like EC2 instances are deployed.
3. **Internet Gateway**: Provides internet access to instances in the public subnet.
4. **EC2 Instances**: Virtual machines running in the cloud.
5. **Load Balancer**: Distributes traffic across EC2 instances.

---

#### Adding EC2 Instance and Load Balancer:

**Add EC2 Instance to main.tf:**
```hcl
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  tags = {
    Name = "TerraformEC2Instance"
  }
}
```
- **Explanation**: This creates a simple EC2 instance in the **public subnet**. The instance uses a publicly available **Amazon Machine Image (AMI)**.

**Add Load Balancer:**
```hcl
resource "aws_elb" "my_lb" {
  name               = "my-load-balancer"
  availability_zones = ["us-east-1a"]

  listener {
    instance_port     = 80
    instance_protocol = "HTTP"
    lb_port           = 80
    lb_protocol       = "HTTP"
  }

  health_check {
    target              = "HTTP:80/"
    interval            = 30
    timeout             = 5
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }

  instances = [aws_instance.my_ec2.id]

  tags = {
    Name = "LoadBalancer"
  }
}
```
- **Explanation**: The **Elastic Load Balancer (ELB)** distributes traffic to the EC2 instance. It checks the health of the instance and routes traffic only to healthy ones.

---

### Conclusion:

By following these steps, you’ve learned how to:
- Set up Terraform to interact with AWS using IAM and access keys.
- Define AWS infrastructure resources in Terraform using HCL (HashiCorp Configuration Language).
- Provision a **VPC**, **Subnet**, **EC2 instance**, and **Load Balancer** using Terraform commands.

Terraform simplifies cloud infrastructure management by enabling version control, repeatability, and efficient scaling. This project is a great introduction to using Terraform in real-world AWS environments, automating complex infrastructures easily with just a few lines of code.