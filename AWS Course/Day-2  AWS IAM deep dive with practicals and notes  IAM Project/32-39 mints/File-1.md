### **Custom Policies in AWS IAM**

In AWS Identity and Access Management (IAM), policies define the permissions for users, groups, or roles to interact with AWS services. While AWS provides **managed policies** for common use cases, there are scenarios where you need to define **custom policies** to meet specific needs. Here's a breakdown of the concept and purpose of custom policies:

### **Scenario: When to Use Custom Policies**

Let’s say you want to allow a user to view only specific S3 buckets, but AWS's managed policies give full or broad access to S3 services. In such cases, you can create **custom policies** that offer more granular control over permissions.

Example: You might allow a user to:
- List only a specific bucket in S3.
- Restrict them from deleting objects, while allowing uploads.

Custom policies allow you to define these more complex and specific actions that AWS managed policies might not cover.

---

### **Managed Policies vs Custom Policies**

- **AWS Managed Policies**: Predefined policies created by AWS to cover common permissions, like full access or read-only access to specific services.
- **Custom Policies**: Policies you write yourself, often using the **JSON format**, that define more specific and complex actions on AWS resources.

**Example AWS Managed Policy**: 
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```
- This policy allows full access (`s3:*`) to all S3 resources (`*`).

**Custom Policy Example**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-aws-demo-bucket",
        "arn:aws:s3:::my-aws-demo-bucket/*"
      ]
    }
  ]
}
```
- **Purpose**: This custom policy allows a user to **list** and **get objects** only from the bucket `my-aws-demo-bucket`. They cannot modify or delete anything.

---

### **Key Components of a Custom Policy**

1. **Version**: 
   - Defines the policy language version.
   - Usually set to `"2012-10-17"` (most recent version).

2. **Statement**: 
   - The core of the policy. A policy can have multiple statements.
   - Contains:
     - **Effect**: Either `Allow` or `Deny`.
     - **Action**: Specifies the AWS service actions (e.g., `s3:ListBucket`, `s3:GetObject`).
     - **Resource**: Specifies the resource the action applies to (e.g., an S3 bucket).

---

### **Practical: Assigning Custom Policies**

#### **Step 1: Attach a Managed Policy (Full Access)**
- **Scenario**: Let’s assign **AmazonS3FullAccess** to a user (e.g., `test-user-501`) to understand its full permissions:
   - Go to **IAM** in the AWS console.
   - Select the user (`test-user-501`).
   - Click **Add Permissions** → **Attach Policies**.
   - Search for and attach **AmazonS3FullAccess**.
   - This will grant the user full control over all S3 buckets.

#### **Step 2: Test Permissions (S3 Bucket Access)**
- Log in as `test-user-501`.
- Go to the **S3 service** in the console.
- **Expected Outcome**: You should be able to list, create, delete, and modify any S3 bucket.

#### **Step 3: Define and Attach a Custom Policy**
- **Scenario**: Restrict the user to only view a specific S3 bucket (`my-aws-demo-bucket`) and prevent them from deleting any objects.
   - **Steps**:
     - Go to **IAM**.
     - Select **Policies** → **Create Policy**.
     - Select the **JSON** tab and paste the following custom policy:
 ```json
       {
         "Version": "2012-10-17",
         "Statement": [
           {
             "Effect": "Allow",
             "Action": [
               "s3:ListBucket",
               "s3:GetObject"
             ],
             "Resource": [
               "arn:aws:s3:::my-aws-demo-bucket",
               "arn:aws:s3:::my-aws-demo-bucket/*"
             ]
           }
         ]
       }
 ```
     - Click **Review Policy** and give it a name (e.g., `S3RestrictedAccess`).
     - Attach this custom policy to `test-user-501`.

#### **Step 4: Test Custom Policy**
- Log in again as `test-user-501`.
- Go to the **S3 service** and try to:
   - **List the buckets**: You should only see the `my-aws-demo-bucket`.
   - **Try to delete objects**: You should get an **"Access Denied"** error.

---

### **Advanced Policy Components**:

1. **Effect**:
   - **Allow**: Grants permission.
   - **Deny**: Explicitly denies permission (even if other policies allow it).

2. **Action**:
   - Specifies which actions are allowed or denied.
   - Example actions: `s3:ListBucket`, `ec2:StartInstances`.

3. **Resource**:
   - Specifies which AWS resource the policy applies to.
   - Example: The ARN (`arn:aws:s3:::my-aws-demo-bucket`) of an S3 bucket or EC2 instance.

---

### **User Groups and Policies for Automation**

**Scenario**: You manage a large organization with developers, QA engineers, and DB admins. Manually attaching policies to each user is inefficient.

#### **Step 1: Create Groups**
   - Go to **IAM** → **Groups**.
   - Create a group (e.g., **Developers**).
   - Attach policies relevant to developers, such as **AmazonEC2FullAccess** and **AmazonS3ReadOnlyAccess**.

#### **Step 2: Add Users to Groups**
   - When a new developer joins, add them to the **Developers** group.
   - This automatically gives them the permissions attached to the group, such as read-only S3 access and full EC2 access.

**Benefits**:
   - Simplifies management: Policies are attached to the group rather than individual users.
   - Easy updates: If you need to change permissions (e.g., adding new services), you can modify the group’s policies, and all group members receive the updates.

---

### **Roles and Cross-Account Access**

#### **When to Use Roles**:
   - Roles are used when AWS services (like EC2 instances) or users from other AWS accounts need temporary access to resources.
   - Example: An EC2 instance in Account A needs access to an S3 bucket in Account B. Instead of creating a user, assign a **role** to the EC2 instance to access the S3 bucket.

---

### **Conclusion**

- **Managed Policies**: Great for standard use cases but may provide more access than needed.
- **Custom Policies**: Use them when you need fine-grained control over what users can do with AWS resources.
- **Groups**: Help manage permissions efficiently in large organizations.
- **Roles**: Provide temporary access for services and cross-account resources without needing to create permanent users.

This combination of **authentication** (via users) and **authorization** (via policies) is fundamental in AWS IAM for controlling secure and efficient access to AWS resources.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

### **Custom Policies and Groups in AWS IAM**

In this section, we will explore how to create custom policies in AWS IAM, why they are important, and how to manage users more efficiently using IAM groups. You’ll also learn how to control specific actions like listing and creating S3 buckets for users. Lastly, we'll see how creating groups can streamline permission management for multiple users.

---

### **1. Custom Policies in AWS IAM**

**Custom Policies** allow you to define highly specific permissions beyond the pre-configured **AWS Managed Policies**. AWS provides default policies like **S3 Full Access** and **S3 Read-Only Access**, but you may need more granular control.

**Scenario:** Suppose you want to allow a user to only view specific types of S3 buckets or restrict certain actions, like listing buckets but not creating them. For that, you would need to define a custom policy using **JSON**.

**Structure of a Custom Policy:**

- **Version:** Policy language version.
- **Statement:** Contains the actions, resources, and effect.
  - **Effect:** Either `Allow` or `Deny`.
  - **Action:** Specifies the AWS service (e.g., `s3:ListBucket`).
  - **Resource:** The resource you are applying the action to (e.g., `arn:aws:s3:::my-bucket`).

**Example of a Custom Policy (to allow listing only specific S3 buckets):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::my-bucket"
    }
  ]
}
```
**Purpose:** This policy allows the user to list only a specific S3 bucket named "my-bucket" but not interact with other buckets or perform other actions like creating or deleting buckets.

---

### **2. Attaching AWS Managed Policies**

Let’s explore how to use AWS Managed Policies, such as **S3 Full Access** or **S3 Read-Only Access**, to grant specific permissions to an IAM user.

1. **Log into AWS as the Root User or an Admin.**
2. Go to **IAM Service** and select **Users**.
3. Choose **test-user-501** and click **Add Permissions**.
4. Attach the **S3 Full Access** policy to give full control over S3, which includes creating, deleting, and listing buckets.

**AWS CLI Command to Attach S3 Full Access Policy:**
```bash
aws iam attach-user-policy --user-name test-user-501 --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

**Expected Outcome:**
- **test-user-501** will have full access to manage S3 buckets. They can list, create, and delete S3 buckets.

**Purpose:** This shows how to grant broad permissions to an IAM user by using AWS Managed Policies, making it easier to manage common use cases.

---

### **3. Logging in and Testing Permissions**

After attaching the **S3 Full Access** policy, let's log in again as **test-user-501** to test whether they now have full access to S3.

1. **Sign out** from the root account.
2. **Sign in** as **test-user-501**.
3. Go to **S3** and verify:
   - **List Buckets:** Check if all S3 buckets are visible.
   - **Create a Bucket:** Try creating a new S3 bucket to confirm full access.

**Steps to Create a Bucket:**
1. Click **Create Bucket**.
2. Enter a name like `my-aws-demo-bucket`.
3. Click **Create**.

**Command to List S3 Buckets:**
```bash
aws s3 ls
```

**Command to Create an S3 Bucket:**
```bash
aws s3 mb s3://my-aws-demo-bucket
```

**Expected Outcome:**
- You will successfully see all S3 buckets and be able to create new ones.

**Purpose:** This demonstrates how granting the **S3 Full Access** policy gives a user the ability to manage S3 buckets without restrictions.

---

### **4. Introducing IAM Groups**

**IAM Groups** make managing permissions easier, especially for large teams where multiple users require similar permissions. Instead of assigning policies to each user individually, you can create groups with predefined permissions and add users to those groups.

**Scenario:** Suppose you have a team of developers who need S3 access. Instead of attaching the S3 policy to each developer, you create a **Developer Group** and add all developers to that group.

**Steps to Create an IAM Group:**
1. Go to the **IAM Service**.
2. Click **Create Group** and name it **Developer-Group**.
3. Attach policies like **S3 Full Access** to the group.

**Command to Create an IAM Group:**
```bash
aws iam create-group --group-name Developer-Group
```

**Command to Attach Policy to Group:**
```bash
aws iam attach-group-policy --group-name Developer-Group --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

**Adding Users to the Group:**
1. After creating the group, select **Add Users** and choose all relevant users, like **test-user-501**.

**Command to Add User to Group:**
```bash
aws iam add-user-to-group --user-name test-user-501 --group-name Developer-Group
```

**Purpose:** By using groups, you can efficiently manage permissions for multiple users. For instance, if a new developer joins, you simply add them to the **Developer-Group**, and they inherit all the policies attached to that group.

---

### **5. Testing Group Permissions**

Now, log back in as **test-user-501** and confirm that they still have access to S3. The permissions are granted via the **Developer-Group** rather than directly attached to the user.

1. **Sign out** and **sign in** again as **test-user-501**.
2. Check the S3 service to see if you can still:
   - List existing buckets.
   - Create a new bucket.
   - Delete a bucket.

**Command to Check Bucket Permissions:**
```bash
aws s3 ls
aws s3 mb s3://my-aws-test-bucket
```

**Expected Outcome:**
- The user will have the same access to S3 as before because they are now part of the **Developer-Group** with **S3 Full Access**.

**Purpose:** This shows the efficiency of using groups in IAM, as you can manage permissions for a whole team by simply updating the group rather than individual users.

---

### **6. Preparing for Custom Policies**

In future sessions, we will delve into **custom policies** where you can define fine-grained permissions using JSON format. For now, focus on understanding the basics of AWS Managed Policies and Groups.

**Scenario:** If a team needs access to additional services, like **EC2**, you can simply attach the necessary policy to the group. This way, all users in the group gain access without individual updates.

---

### **Conclusion**

Here’s a recap of what we covered:

- **Custom Policies:** For fine-grained control over AWS services. You can define what actions a user or group can perform using JSON.
- **AWS Managed Policies:** Predefined policies by AWS, useful for common tasks like granting full or read-only access to S3, EC2, etc.
- **IAM Groups:** Useful for managing permissions for multiple users. By attaching policies to a group, all members inherit the same permissions.
  
This system ensures secure and efficient management of user permissions in AWS, preventing unwanted access while simplifying the administrative burden.