### Explanation of EC2 Free Tier, Key Pairs, Instance Configuration, and SSH Login

#### **AWS Free Tier and Instance Time Management**

When using the **AWS Free Tier**, AWS offers a limited number of free hours per month (750 hours) for **T2.micro instances**. Here are some key details to understand:

1. **T2.micro Instance Free Usage**:
   - AWS provides **T2.micro** instances, which come with **1 vCPU and 1 GB RAM**. These are typically sufficient for small-scale applications or for learning purposes.
   - You can only run **one instance** for free if it stays running for the entire month. However, if you run **multiple instances**, ensure that the combined total running hours do not exceed **750 hours per month** to avoid charges.

2. **Optimizing Free Tier Usage**:
   - If you need to use multiple instances, consider running each instance for a specific amount of time (e.g., **200 hours each**), so that the total hours do not exceed the free tier limit.
   - **Best Practice**: After finishing your tasks, always **shut down or terminate** the instance to avoid exceeding the free tier usage and getting charged.

#### **Instance Types Beyond Free Tier**

In AWS, there are larger instance types like **T2.xlarge** that offer **4 vCPUs and 16 GB of RAM**. These are used for more resource-intensive applications but are not covered under the free tier. For example:
- **T2.micro**: Suitable for lightweight applications like a small web server.
- **T2.xlarge**: Ideal for running more demanding applications, such as **machine learning models**, **large databases**, or **high-performance web applications**.

If you have **AWS credits** or support from your organization, you can explore these larger instances for more powerful applications.

#### **Key Pair: Logging into Your EC2 Instance**

1. **What is a Key Pair?**
   - A **key pair** in AWS consists of a **public key** stored on the EC2 instance and a **private key** that you download when you create the instance. The private key is used to securely log into the instance.
   - **Security Note**: Always keep the **private key** (.pem file) safe. Do not share it with anyone, as it provides access to your EC2 instance.

2. **Creating a Key Pair**:
   - When launching an instance, you are prompted to create a new key pair. This key pair will be used to log into the instance.
   - Example steps:
     - **Create a new key pair** named `AWS_login` and download the **.pem** file to your local machine.

#### **Launching the EC2 Instance**

1. **Launch an EC2 Instance**:
   - After selecting the **T2.micro** instance, configure the storage, network, and security settings.
   - Example: AWS provides **8 GB of storage** by default, which is enough for learning and basic tasks. You can increase the storage size if necessary.

2. **Launch**: After configuring the instance, click **Launch Instance**. AWS will start the instance, which should take a few minutes.

---

### **Connecting to EC2 Using SSH (Secure Shell)**

To log into your EC2 instance using the **SSH protocol**, you must use the **private key** (.pem file) you created. Here are the steps:

1. **Install an SSH Client**:
   - If you're using **Windows**, you can use SSH clients like **PuTTY** or **MobaXterm**.
   - If you're using **macOS** or **Linux**, you can use the **default terminal** to access the instance.

2. **Connect to the EC2 Instance via SSH**:
   - After the instance is launched, go to the **EC2 Dashboard** and locate the **public IP address** of your instance. 
   - Use the following command in your terminal to log in:
```bash
     ssh -i ~/Downloads/AWS_login.pem ubuntu@<public-ip-address>
```
     - **Explanation**:
       - `ssh -i`: Specifies the identity file (your **.pem** file).
       - `ubuntu@<public-ip-address>`: The default username for Ubuntu instances on AWS is `ubuntu`. Replace `<public-ip-address>` with the actual IP address of your instance.

   - **Note**: Ensure that the **AWS_login.pem** file is located in your **Downloads** folder (or the folder where the .pem file was downloaded).

3. **Example**:
```bash
   ssh -i ~/Downloads/AWS_login.pem ubuntu@3.10.150.198
```

4. **Security Reminder**: Always set the correct permissions for your **.pem** file to prevent unauthorized access. If required, use the following command to change the permissions:
```bash
   chmod 400 ~/Downloads/AWS_login.pem
```

#### **Using SSH Clients (PuTTY for Windows)**

If you are using **PuTTY** (on Windows), you must first **convert the .pem file** to **.ppk** format:

1. Open **PuTTYgen** and load your **.pem** file.
2. Save the private key as a **.ppk** file.
3. Use **PuTTY** to SSH into your instance by providing the **public IP address** and the **.ppk** file for authentication.

---

### **Practical Scenario**

1. **Use Case**:
   - You have created an EC2 instance in the **North Virginia** region for a development environment.
   - You want to log into this instance, deploy an application, and test it.

2. **Actions**:
   - After launching the instance and creating a key pair, you can use **SSH** to log in and install necessary software, such as **Nginx**, **Apache**, or **Node.js**, depending on the application.

3. **SSH Command to Access EC2**:
   - Example command to log into the EC2 instance:
```bash
     ssh -i ~/Downloads/AWS_login.pem ubuntu@3.120.45.67
```

4. **Post-Login Actions**:
   - Once logged in, you can install software, configure the server, and set up your application.

---

### **Conclusion**

In this session, you learned how to:

1. **Create and manage EC2 instances** within the AWS Free Tier limits.
2. Understand the **importance of key pairs** and how they provide secure access to your EC2 instances.
3. **Connect to your EC2 instance** via **SSH** using terminal commands or SSH clients like **PuTTY**.

Make sure to follow these practices to manage your AWS Free Tier usage and securely access your EC2 instances using key pairs.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

### **Creating and Managing EC2 Instances in AWS: Key Concepts**

---

### **EC2 Free Tier and Cost Management**

When working with AWS under the **Free Tier**, there are some crucial limitations and best practices you should follow to avoid unexpected charges:

- **One Free Instance Limit**: AWS offers **750 hours** of **T2.micro** instance usage per month under the free tier. This means you can run **one instance continuously** for a month without charges. However, if you create a second instance, **AWS will start charging for the additional instance**.
  
  **Scenario**: If you need multiple instances for testing, you can strategically manage their uptime to stay within the free tier limits. For example:
  - Run **Instance A** for **200 hours**.
  - Run **Instance B** for **200 hours**.
  - Run **Instance C** for **350 hours**.

  This way, the total usage remains under the 750-hour limit.

- **Best Practice**: Always **shut down instances** when you are done with your work or testing. Keeping an instance running when it's not in use will consume your free tier limit.

### **Choosing EC2 Instance Types**

AWS provides different types of instances tailored for various workloads. Under the **Free Tier**, you can only use **T2.micro** (1 CPU, 1GB RAM). However, if you have credits (from your organization or promotions), you can experiment with more powerful instances like **T2.xlarge** (4 CPU, 16GB RAM) for larger applications.

- **T2.micro**: Suitable for light workloads, basic applications, and testing.
- **T2.xlarge**: Ideal for more intensive applications, such as **memory-intensive** or **compute-intensive** workloads.

---

### **Key Pair: Secure Access to EC2 Instances**

When you create an EC2 instance, AWS uses a **key pair** (public-private key mechanism) to secure access:

- **Public Key**: Stored on the instance by AWS.
- **Private Key**: Downloaded to your local machine. This private key (`.pem` file) is required to SSH into your EC2 instance.
  
  **Important**: Never share the **private key** with anyone, as it gives direct access to your instance.

#### **Creating a Key Pair**

When launching an instance, you must create a key pair if you don’t have one:
1. **Create Key Pair**: Click **Create new key pair** and name it (e.g., `AWS-login`).
2. **Download**: The key pair will download as a `.pem` file. This file is critical for accessing your EC2 instance.

### **Network and Security Settings**

For now, we are not modifying any **network settings** like security groups, inbound and outbound traffic rules, or route tables. These will be discussed in later videos. AWS provides default network configurations for basic access, which is sufficient for this tutorial.

---

### **Accessing the EC2 Instance via SSH**

Once the instance is running, you can use the **private key** to connect to it. Here's how you can access the instance based on your operating system:

- **Windows Users**: You need to use an SSH client like **PuTTY** or **MobaXterm**.
- **Mac/Linux Users**: You can use the default **terminal**.

### **Step-by-Step: SSH into EC2**

1. **Get the Public IP**: Once the instance is launched, AWS provides a **Public IP address** that allows external access to your instance. Copy this IP.

2. **SSH Command**:
   For Mac/Linux users, open your terminal and navigate to the directory where your `.pem` file is stored (usually in the `Downloads` folder):
   
```bash
   ssh -i ~/Downloads/AWS-login.pem ubuntu@<Public-IP-Address>
```

   - **`-i`**: Specifies the identity file (your `.pem` file).
   - **`ubuntu@`**: The default username for Ubuntu instances.
   - **`<Public-IP-Address>`**: Replace with the public IP of your instance.

3. **Login Example**:
```bash
   ssh -i ~/Downloads/AWS-login.pem ubuntu@13.58.124.123
```

4. **Permissions Issue**: If you face a permissions issue while trying to connect, ensure that your `.pem` file has the correct permissions:
```bash
   chmod 400 ~/Downloads/AWS-login.pem
```

   This command ensures that the private key is only accessible by you, which SSH requires for security.

---

### **Understanding the Key Concepts and Their Purpose**

1. **T2.micro Instance**:
   - **Purpose**: Provides a basic, low-cost instance (1 CPU, 1GB RAM) ideal for learning, development, and testing.
   - **Command Example**:
```bash
     aws ec2 run-instances --image-id ami-12345678 --count 1 --instance-type t2.micro --key-name MyKeyPair
```
     **Scenario**: You use this command to launch a new EC2 instance under the free tier, suitable for basic workloads like testing web applications.

2. **Key Pair**:
   - **Purpose**: Provides secure access to your EC2 instance using SSH. The **public key** is stored in the instance, and the **private key** is stored on your local machine.
   - **Command Example**:
```bash
     ssh -i ~/Downloads/MyKeyPair.pem ubuntu@<Public-IP-Address>
```
     **Scenario**: After launching your instance, you use this command to log in securely and manage your server.

3. **Managing EC2 Instances**:
   - **Stop Instance**:
```bash
     aws ec2 stop-instances --instance-ids i-1234567890abcdef0
```
     **Purpose**: Stop an instance when it’s not in use to avoid incurring charges.
   - **Terminate Instance**:
```bash
     aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
```
     **Purpose**: Terminate an instance permanently, freeing up resources and ensuring you won’t be charged.

4. **Network Settings** (Security Groups, etc.):
   - **Purpose**: Control access to your instance by managing inbound and outbound traffic.
   - **Scenario**: By default, AWS opens **SSH (port 22)** to allow you to connect via SSH. Later, you can configure other ports for specific applications (like HTTP/HTTPS for web servers).

---

### **Practical Management of EC2 Instances**

1. **Public and Private IP**: 
   - **Public IP**: Used to access the instance from outside AWS (e.g., from your local machine).
   - **Private IP**: Used for internal communication within AWS (e.g., between instances in the same VPC).

2. **Logging in to EC2**:
   - Once the instance is in a **running state**, use the **public IP** to log in via SSH and begin configuring or deploying applications.

---

### **Summary**

In this tutorial, you've learned how to create an **EC2 instance** under the **AWS Free Tier**, manage it using **key pairs**, and access it via SSH. You also explored how to manage the instance effectively to avoid unnecessary costs by **shutting down** or **terminating** instances when they’re not needed. This foundational knowledge will allow you to build and manage applications on the cloud in a cost-effective and secure way.

