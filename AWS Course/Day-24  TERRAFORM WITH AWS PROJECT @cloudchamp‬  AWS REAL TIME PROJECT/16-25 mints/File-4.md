### AWS Custom VPC Creation Using Terraform

This section covers how to automate AWS infrastructure setup using Terraform. The focus is on creating a Virtual Private Cloud (VPC) with subnets and an Internet Gateway for internet access.

---

### Why Use a Custom VPC?
Creating a custom VPC gives you control over your network configuration, including IP range, subnetting, route tables, and security. While you can use AWS's default VPC, it’s best practice to create your own VPC to customize it according to your needs.

---

### Step-by-Step: Automating with Terraform

1. **Creating a VPC**
   - **Resource Definition:** In Terraform, a resource represents an AWS service or component. For a VPC, the resource block is written as `aws_vpc`.
   
   - **Example Code:**
 ```hcl
     resource "aws_vpc" "my_vpc" {
       cidr_block = var.vpc_cidr_block
       enable_dns_support = true
       enable_dns_hostnames = true
     }
 ```
   - **CIDR Block:** The range of IP addresses (e.g., `10.0.0.0/16`) is defined for the VPC. This allows the allocation of subnets.
   
   - **Tenancy:** Single or dedicated tenancy options for the VPC can be specified.

2. **Variables for Reusability**
   Instead of hardcoding values, use variables to make your Terraform configuration more reusable.
   
   - **Example Variable Declaration:**
 ```hcl
     variable "vpc_cidr_block" {
       default = "10.0.0.0/16"
     }
 ```
   
   - **Referencing Variables in Resources:**
 ```hcl
     cidr_block = var.vpc_cidr_block
 ```

3. **Creating Subnets**
   Subnets are segments of a VPC. Each subnet is assigned to an Availability Zone (AZ).
   
   - **Subnet Resource:**
 ```hcl
     resource "aws_subnet" "subnet_1" {
       vpc_id = aws_vpc.my_vpc.id
       cidr_block = "10.0.0.0/24"
       availability_zone = "us-east-1a"
       map_public_ip_on_launch = true
     }
 ```
   
   - **Explanation:**
     - `vpc_id`: Associates the subnet with the created VPC.
     - `cidr_block`: Defines a smaller range within the VPC (e.g., `/24` gives 256 IPs).
     - `availability_zone`: Specifies which AWS AZ the subnet belongs to.
     - `map_public_ip_on_launch`: Determines whether the instances launched in this subnet will automatically get a public IP.

4. **Internet Gateway**
   An Internet Gateway provides internet access to your VPC. It must be attached to the VPC.

   - **Resource for Internet Gateway:**
 ```hcl
     resource "aws_internet_gateway" "igw" {
       vpc_id = aws_vpc.my_vpc.id
     }
 ```

---

### Practical Explanation of Resources

1. **AWS VPC (`aws_vpc`)**
   - **Purpose:** Creates a network in AWS where you can launch AWS resources such as EC2 instances.
   - **Output Command Example:**
 ```bash
     terraform apply
 ```
     **Output:**
 ```bash
     Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
 ```

2. **AWS Subnet (`aws_subnet`)**
   - **Purpose:** Allows you to logically subdivide your VPC into smaller networks.
   - **Command Output Example:**
 ```bash
     terraform apply
 ```
     **Output:**
 ```bash
     Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
 ```

3. **AWS Internet Gateway (`aws_internet_gateway`)**
   - **Purpose:** Allows outbound traffic to flow between your VPC and the internet.
   - **Command Output Example:**
 ```bash
     terraform apply
 ```
     **Output:**
 ```bash
     Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
 ```

---

### Networking Concepts

1. **CIDR Block (Classless Inter-Domain Routing)**
   - **Purpose:** Defines the range of IP addresses available in your network.
   - **Example:** A `10.0.0.0/16` block allows for 65,536 IP addresses, while a `10.0.1.0/24` subnet allows for 256 IP addresses.

2. **Availability Zones (AZ)**
   - AWS regions are divided into multiple AZs for redundancy. By placing subnets in different AZs, you ensure higher availability for your applications.

3. **Public IP Mapping**
   - **Purpose:** When set to true (`map_public_ip_on_launch`), instances in the subnet will be assigned a public IP address, making them reachable from the internet.

---

### Best Practices and Common Scenarios

1. **Use Variables for Flexibility**
   By declaring CIDR blocks and other configurations as variables, you can easily modify your infrastructure by changing one variable.

2. **Multi-AZ Deployment**
   Distribute your resources across multiple Availability Zones to ensure high availability. This minimizes the impact of AZ failures.

3. **Troubleshooting Tips**
   Errors in Terraform can often be fixed by reading the documentation, checking logs, or reviewing your configuration line by line.

---

### Next Steps: Adding Internet Access
While you’ve created the subnets and VPC, they remain private until you attach an Internet Gateway and configure route tables.

Next steps:
1. Attach the Internet Gateway to the VPC.
2. Update the route tables to allow public traffic.
3. Launch an EC2 instance to test the internet connectivity.

---

This comprehensive guide provides a deep dive into VPC creation and subnet configuration using Terraform. The overall goal is to automate AWS infrastructure while adhering to best practices, such as reusability and error handling.