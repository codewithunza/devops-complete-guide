### EC2 Instance SSH Access and Application Deployment (Jenkins)

This guide breaks down how to access an AWS EC2 instance using SSH, manage file permissions, and deploy an application (Jenkins) while handling security and network configurations.

### **Step 1: Accessing Your EC2 Instance via SSH**

1. **Issue with Permissions of the `.pem` File**:
   - When connecting to your EC2 instance via SSH, you may encounter an error regarding the **permissions of the `.pem` file**. This error occurs because AWS requires that the private key file (`AWS_login.pem`) be secured and not have open permissions (which could expose sensitive information).

2. **Fixing the Permission Error**:
   - Use the `chmod` command to modify the permissions of the `.pem` file:
 ```bash
     chmod 600 ~/Downloads/AWS_login.pem
 ```
     - `chmod 600` ensures that only the owner of the file has read/write permissions.

3. **SSH Command to Log In**:
   - Once the permissions are set, you can log into your EC2 instance:
 ```bash
     ssh -i ~/Downloads/AWS_login.pem ubuntu@<public-ip-address>
 ```
     - **`ubuntu@<public-ip-address>`**: The username is `ubuntu` for Ubuntu instances, and `<public-ip-address>` is the public IP of your EC2 instance.

4. **Verifying the User**:
   - You can confirm you’re logged in as the Ubuntu user with the `whoami` command:
 ```bash
     whoami
 ```
     - The output will show:
 ```bash
       ubuntu
 ```

---

### **Step 2: Updating the System and Installing Applications**

1. **Updating the EC2 Instance Packages**:
   - It’s a good practice to update the instance's package list after logging in:
 ```bash
     sudo apt update
 ```
     - This ensures that all installed packages are up to date and the latest versions of software can be installed.

2. **Installing Jenkins on EC2**:
   - As a DevOps engineer, you may want to deploy an application like Jenkins. Here’s how to install Jenkins on your EC2 instance:
   
   **Step 1**: Install Java (required by Jenkins):
 ```bash
   sudo apt install openjdk-11-jdk
 ```
   - Once installed, check the Java version:
 ```bash
     java -version
 ```

   **Step 2**: Install Jenkins:
   - Follow the official Jenkins instructions for Ubuntu:
     - Add the Jenkins repository:
 ```bash
       wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
       sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
 ```
     - Update and install Jenkins:
 ```bash
       sudo apt update
       sudo apt install jenkins
 ```

3. **Starting the Jenkins Service**:
   - After installation, ensure that Jenkins is running:
 ```bash
     sudo systemctl start jenkins
     sudo systemctl status jenkins
 ```
   - This will confirm if Jenkins is up and running on port **8080**.

---

### **Step 3: Accessing Jenkins via the Browser**

1. **Jenkins runs on port 8080** by default, and to access it, you need the **public IP address** of your EC2 instance:
   - Use this URL format in your browser:
 ```plaintext
     http://<public-ip-address>:8080
 ```

2. **Security Groups and Port Configuration**:
   - By default, AWS EC2 instances have restricted access. To allow traffic on port **8080**, you need to modify the **inbound rules** of the security group attached to your instance.

3. **Modifying the Security Group for Port Access**:
   - Go to **EC2 Management Console** > **Instances** > Select your instance > **Security Groups** > **Edit Inbound Rules**:
     - Add a rule to allow HTTP traffic on port **8080**:
 ```plaintext
       Type: Custom TCP
       Port Range: 8080
       Source: Anywhere (0.0.0.0/0)
 ```

4. **Access Jenkins**:
   - After setting up the security group, go to your browser and enter the public IP with port **8080**:
 ```plaintext
     http://<public-ip-address>:8080
 ```

---

### **Step 4: Initial Setup of Jenkins**

1. **Unlock Jenkins**:
   - After accessing Jenkins, you will need the initial **admin password** to unlock it. This password can be found in a file on your instance:
 ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
 ```
   - Copy this password, paste it into the Jenkins setup screen, and complete the setup process.

2. **Complete Jenkins Setup**:
   - After entering the admin password, you can install suggested plugins and create an admin user to complete the setup process.

---

### **Scenarios and Use Cases**

1. **Scenario 1: Deploying Jenkins for CI/CD**:
   - Use the EC2 instance with Jenkins to set up a **CI/CD pipeline** for your application. Jenkins can automate testing, building, and deployment.

2. **Scenario 2: Running DevOps Tools on EC2**:
   - As a DevOps engineer, EC2 instances can host critical DevOps tools like Jenkins, Ansible, Terraform, or Kubernetes control planes.

3. **Scenario 3: Troubleshooting and Security**:
   - You learn how to configure **security groups** to allow or block traffic, ensuring that your instance is secure but also accessible when needed.

---

### **Conclusion**

- You’ve successfully created an EC2 instance, logged in via SSH, updated the system, and installed Jenkins, all while configuring AWS security groups to ensure the application is accessible.
  
- This process covers essential skills for managing AWS EC2 instances, including **network configuration, software deployment, and system security**.
  
--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

### **Accessing an EC2 Instance on AWS and Deploying Jenkins: A Detailed Breakdown**

---

### **Connecting to the EC2 Instance Using SSH**

Once you've launched your EC2 instance on AWS, the next step is to access it using **SSH**. However, there's a common issue many users face when trying to SSH into their instance for the first time: **permissions errors** related to the `.pem` file, which contains your private key.

### **Error Explanation and Fix: Changing File Permissions**

If you attempt to SSH using the command:
```bash
ssh -i ~/Downloads/aws-login.pem ubuntu@<Public-IP-Address>
```
And encounter an error such as:
```
Permissions for 'aws-login.pem' are too open.
```
This is because the permissions for your `.pem` file are too lenient. AWS requires that the `.pem` file is only readable by the file owner, as it contains sensitive information.

#### **Fix: Change File Permissions Using `chmod`**
You need to restrict the permissions of the `.pem` file using the `chmod` command:
```bash
chmod 600 ~/Downloads/aws-login.pem
```
This command ensures that only the file owner can read the file, following the required security standards.

#### **Login Command**
Once the permissions are set correctly, you can retry logging in:
```bash
ssh -i ~/Downloads/aws-login.pem ubuntu@<Public-IP-Address>
```
This time, it will work, and you'll be logged in to the instance. 

---

### **Initial Setup on the EC2 Instance**

Once inside the EC2 instance, the next step is to **update the packages**. This ensures that the instance is up-to-date with the latest software versions and security patches.

#### **Updating the Instance**
If logged in as the `ubuntu` user (default for Ubuntu instances), use the following command to update:
```bash
sudo apt update
```
If you've switched to the `root` user:
```bash
apt update
```
It’s always a good practice to keep your system updated before installing any new software.

---

### **Deploying Jenkins on EC2**

Now that you're logged into the instance, let's deploy an application — in this case, **Jenkins**, a popular CI/CD automation tool.

#### **Installing Java**
Since Jenkins requires Java, the first step is to install it:
```bash
sudo apt install openjdk-11-jdk
```
Once installed, verify the installation:
```bash
java -version
```
This will display the installed Java version, confirming that it's ready.

#### **Installing Jenkins**
Next, follow these steps to install Jenkins:

1. **Add Jenkins Package Repository**:
 ```bash
   wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
   sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
 ```
   
2. **Update the System and Install Jenkins**:
 ```bash
   sudo apt update
   sudo apt install jenkins
 ```

#### **Checking Jenkins Status**
Once Jenkins is installed, check if the service is running:
```bash
sudo systemctl status jenkins
```
If it’s not running, start it:
```bash
sudo systemctl start jenkins
```
By default, Jenkins runs on **port 8080**.

---

### **Accessing Jenkins from a Browser**

To access Jenkins via your browser, you need the **public IP address** of your EC2 instance and ensure that port **8080** is open in your instance's security group.

#### **Opening Port 8080 in Security Group**

1. Navigate to your EC2 **instance settings** in the AWS Console.
2. Go to **Security Groups** and locate the security group attached to your EC2 instance.
3. Click on **Edit Inbound Rules**.
4. Add a new rule:
   - **Type**: Custom TCP
   - **Port Range**: 8080
   - **Source**: Anywhere (0.0.0.0/0 for IPv4, ::/0 for IPv6)
5. **Save** the rule.

This allows external traffic to access Jenkins running on port **8080**.

#### **Accessing Jenkins**

Once the inbound rule is set, go to your browser and enter:
```bash
http://<Public-IP-Address>:8080
```
This will load the Jenkins setup page.

---

### **Initial Jenkins Setup**

When you first access Jenkins, it will ask for an **admin password**. This password can be found on your EC2 instance.

#### **Find Initial Jenkins Admin Password**
On your EC2 instance, run the following command:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy the password and paste it into the Jenkins setup page on your browser.

After entering the password, follow the on-screen instructions to complete the Jenkins setup.

---

### **Key Concepts and Scenarios**

1. **SSH Permissions (`chmod`)**:
   - **Purpose**: Ensures the `.pem` file (private key) has restricted access, protecting sensitive data.
   - **Command**:
 ```bash
     chmod 600 ~/Downloads/aws-login.pem
 ```
   - **Scenario**: When you try to SSH into an EC2 instance and encounter a permissions error, this command fixes the issue by securing the private key.

2. **Package Update**:
   - **Purpose**: Updates the system to ensure the latest security patches and software versions are installed.
   - **Command**:
 ```bash
     sudo apt update
 ```
   - **Scenario**: Run this command after logging into a new EC2 instance to ensure the system is ready for software installations.

3. **Installing Jenkins**:
   - **Purpose**: Deploy Jenkins on your EC2 instance for CI/CD pipeline setup.
   - **Command**:
 ```bash
     sudo apt install jenkins
 ```
   - **Scenario**: As a DevOps engineer, you deploy Jenkins to automate tasks such as building, testing, and deploying code.

4. **Opening Ports (Security Groups)**:
   - **Purpose**: Configures the security group to allow traffic to your EC2 instance on specific ports.
   - **Scenario**: Open **port 8080** to allow external access to Jenkins from a web browser.
   - **Steps**: 
     - Navigate to **Security Groups** in AWS Console.
     - Add a rule for **TCP 8080**.

---

### **Summary**

In this session, you learned how to:
- Access an EC2 instance using SSH.
- Solve common SSH permission issues with the `.pem` file.
- Install and set up Jenkins on an EC2 instance.
- Open necessary ports in AWS security groups to allow external access to Jenkins.

This foundational knowledge is essential for managing applications in the cloud, especially when working with DevOps automation tools like Jenkins.