### Detailed Breakdown of Cloud Cost Optimization Using AWS Lambda and EBS Snapshots

#### **1. Introduction to the Scenario**
- **Concept**: The goal of this exercise is to demonstrate how to automate **cloud cost optimization** by deleting stale **EBS snapshots** using an **AWS Lambda function**.
- **Scenario**: A developer creates an **EC2 instance** with an attached volume and regularly takes **snapshots** for backup purposes. Over time, the developer deletes the EC2 instance but forgets to delete the snapshots, which continue to incur costs.

#### **2. Setting Up the Environment**
- **Step 1**: Create an EC2 instance and EBS volume.
  
  **Command to Launch EC2 Instance**:
```bash
  aws ec2 run-instances \
    --image-id ami-12345678 \
    --instance-type t2.micro \
    --key-name MyKeyPair
```
  - **Explanation**: This command launches a `t2.micro` instance. When the instance is launched, a default EBS volume is automatically attached.
  - **Purpose**: This is the setup for simulating the scenario where the developer is using an EC2 instance with an attached EBS volume.

- **Step 2**: Create an EBS snapshot from the volume of the EC2 instance.
  
  **Command to Create EBS Snapshot**:
```bash
  aws ec2 create-snapshot \
    --volume-id vol-0123456789abcdef0 \
    --description "Backup snapshot for cost optimization demo"
```
  - **Explanation**: The command takes a snapshot of the EBS volume attached to the EC2 instance.
  - **Output**: Creates a snapshot that can later be used to restore the volume or data.

#### **3. Creating the Lambda Function for Cost Optimization**
- **Step 3**: Writing the Lambda function to identify and delete stale snapshots.

  **Python Code for Lambda Function**:
```python
  import boto3

  def lambda_handler(event, context):
      ec2 = boto3.client('ec2')
      
      # Describe all snapshots
      snapshots = ec2.describe_snapshots(OwnerIds=['self'])['Snapshots']
      
      for snapshot in snapshots:
          # Check if the snapshot is not associated with any volume (stale)
          if not snapshot.get('VolumeId'):
              print(f"Deleting snapshot: {snapshot['SnapshotId']}")
              ec2.delete_snapshot(SnapshotId=snapshot['SnapshotId'])
```
  - **Explanation**: This Lambda function uses the **Boto3** library to describe all EBS snapshots in the AWS account and deletes the ones that are not associated with any active volume.
  - **Purpose**: The function automates the detection and removal of snapshots that no longer serve any purpose, optimizing cloud storage costs.

#### **4. Executing the Lambda Function**
- **Step 4**: Deploy the Lambda function and configure it.
  1. **Create the Lambda function**.
  2. **Set execution time**: The default execution time for Lambda functions is 3 seconds, but you can increase it to accommodate longer-running tasks.

  **Command to Deploy Lambda Function**:
```bash
  aws lambda create-function \
    --function-name EBS-Snapshot-Cleanup \
    --runtime python3.8 \
    --role arn:aws:iam::123456789012:role/MyLambdaRole \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://lambda_function.zip
```
  - **Explanation**: This command creates the Lambda function, uploading the Python script and assigning it the appropriate IAM role.
  - **Output**: A Lambda function is created that can now be invoked manually or via scheduled events.

#### **5. Configuring IAM Permissions for the Lambda Function**
- **Step 5**: Attach the necessary IAM policies to the Lambda function’s role to allow it to describe and delete snapshots.

  **IAM Policy for Snapshot Permissions**:
```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "ec2:DescribeSnapshots",
          "ec2:DeleteSnapshot"
        ],
        "Resource": "*"
      }
    ]
  }
```
  - **Explanation**: This policy allows the Lambda function to list (`DescribeSnapshots`) and delete (`DeleteSnapshot`) EBS snapshots.
  - **Command to Attach Policy**:
```bash
    aws iam put-role-policy \
      --role-name MyLambdaRole \
      --policy-name EBS-Snapshot-Cleanup-Policy \
      --policy-document file://policy.json
```
    - **Purpose**: This grants the Lambda function the permissions needed to interact with EBS snapshots.

#### **6. Testing the Lambda Function**
- **Step 6**: Invoke the Lambda function to test its behavior.

  **Command to Test Lambda**:
```bash
  aws lambda invoke \
    --function-name EBS-Snapshot-Cleanup \
    output.txt
```
  - **Explanation**: This command manually triggers the Lambda function and saves the output in a text file.
  - **Output**: The function will attempt to describe and delete stale snapshots.

#### **7. Automating the Lambda Function with CloudWatch**
- **Step 7**: Use **CloudWatch** to schedule the Lambda function to run daily.

  **Command to Schedule Lambda with CloudWatch**:
```bash
  aws events put-rule \
    --schedule-expression "rate(1 day)" \
    --name DailySnapshotCleanup
  aws events put-targets \
    --rule DailySnapshotCleanup \
    --targets "Id"="1","Arn"="arn:aws:lambda:region:account-id:function:EBS-Snapshot-Cleanup"
```
  - **Explanation**: This command creates a CloudWatch rule to trigger the Lambda function every day.
  - **Purpose**: This automation ensures that stale snapshots are regularly checked and deleted without manual intervention.

#### **8. Managing Costs and Deleting Resources**
- **Step 8**: After the demo is complete, delete the resources to avoid unnecessary costs.

  **Command to Delete the EC2 Instance**:
```bash
  aws ec2 terminate-instances \
    --instance-ids i-0123456789abcdef0
```
  - **Explanation**: Terminates the EC2 instance created for the demo.

  **Command to Delete the Lambda Function**:
```bash
  aws lambda delete-function \
    --function-name EBS-Snapshot-Cleanup
```
  - **Explanation**: Deletes the Lambda function to avoid further charges.
  
  **Command to Delete EBS Snapshots**:
```bash
  aws ec2 delete-snapshot --snapshot-id snap-0123456789abcdef0
```
  - **Explanation**: Manually deletes any remaining snapshots that were created for the demo.

#### **9. Real-World Use Case**
- **Use Case**: In a real-world scenario, this setup would help organizations optimize cloud storage costs by automating the cleanup of stale EBS snapshots. This is a routine task that DevOps engineers would perform to ensure efficient resource utilization.
  
- **Benefits**:
  1. **Cost Efficiency**: Reduces unnecessary costs associated with orphaned snapshots.
  2. **Automation**: Eliminates the need for manual intervention by automating the deletion process with Lambda and CloudWatch.
  3. **Scalability**: Can be easily scaled to manage large environments with hundreds of EC2 instances and EBS snapshots.

### Conclusion
This project showcases a practical approach to **cloud cost optimization** using **AWS Lambda** and **CloudWatch**. By automating the deletion of stale EBS snapshots, DevOps engineers can significantly reduce unnecessary storage costs. This demonstration provides a foundational understanding of how to leverage serverless architecture to improve cloud resource management.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

Let's break down the provided content and explain each concept in detail. I'll also explain the commands and scenarios as required.

### 1. **Creating an EC2 Instance and EBS Snapshot**

#### EC2 Instance Creation
- **Step-by-Step Process:**
  1. **Access the EC2 Dashboard:** Go to the AWS EC2 dashboard.
  2. **Create an EC2 Instance:** Click on "Launch Instance."
  3. **Choose Configuration:** 
     - For example, select "Ubuntu" as the AMI (Amazon Machine Image).
     - Choose the instance type, such as "t2.micro."
     - Leave the default volume settings or add additional volumes if needed.
  4. **Launch the Instance:** Click on "Launch Instance."

  **Purpose:** This instance is a virtual server in AWS's cloud. The associated volume holds data, and creating an instance allows you to interact with this volume.

  **Command/Output Example:** No direct command is involved as this is done through the AWS Management Console. The output will be the creation of an EC2 instance, visible in the dashboard.

#### Creating an EBS Snapshot
- **Step-by-Step Process:**
  1. **Access EC2 Dashboard:** Navigate to the EC2 dashboard.
  2. **Locate the Volume:** Find the volume attached to your instance.
  3. **Create Snapshot:**
     - Select the volume.
     - Choose "Actions" > "Create Snapshot."
     - Provide a description (e.g., "test snapshot") and click "Create Snapshot."

  **Purpose:** A snapshot is a backup of your volume. It captures the data at a specific point in time and can be used to restore or create new volumes.

  **Command/Output Example:** No direct command is used here; it is performed through the AWS Console. The snapshot's status will be "Pending" initially and then "Completed."

### 2. **Lambda Function for Cost Optimization**

#### Lambda Function Overview
- **Purpose:** A Lambda function can automate the deletion of unused EBS snapshots, reducing costs by cleaning up resources that are no longer needed.
- **Implementation:**
  1. **Create Lambda Function:**
     - Go to the AWS Lambda dashboard.
     - Click "Create Function" and name it (e.g., "cost-optimization-EBS-snapshot").
     - Select Python as the runtime.
     - Configure permissions as needed.

  **Purpose:** Lambda functions allow you to execute code in response to events without provisioning servers. This function will automate the process of deleting old snapshots.

  **Command/Output Example:**
  - **Code Upload:**
```python
    import boto3
    
    def lambda_handler(event, context):
        ec2 = boto3.client('ec2')
        snapshots = ec2.describe_snapshots(OwnerIds=['self'])['Snapshots']
        
        for snapshot in snapshots:
            snapshot_id = snapshot['SnapshotId']
            volume_id = snapshot.get('VolumeId')
            
            if not volume_id:
                ec2.delete_snapshot(SnapshotId=snapshot_id)
```
  - **Output:** The function will list all snapshots, filter out those not associated with volumes, and delete them.

#### Common Issues and Fixes
1. **Timeout Error:**
   - **Issue:** Lambda functions have a default timeout of 3 seconds, which may not be sufficient.
   - **Fix:** Increase the timeout to 10 seconds in the Lambda configuration settings.

2. **Permissions Issue:**
   - **Issue:** Lambda function might lack permissions to describe or delete snapshots.
   - **Fix:** Attach an appropriate IAM policy to the Lambda execution role.

  **Command/Output Example:**
  - **Update Execution Time:**
```shell
    aws lambda update-function-configuration --function-name cost-optimization-EBS-snapshot --timeout 10
```
  - **Output:** Successful update of the Lambda function configuration.

  - **Grant Permissions:**
```shell
    aws iam put-role-policy --role-name LambdaExecutionRole --policy-name SnapshotPolicy --policy-document file://policy.json
```
```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "ec2:DescribeSnapshots",
            "ec2:DeleteSnapshot"
          ],
          "Resource": "*"
        }
      ]
    }
```
  - **Output:** Policy attached to the IAM role, allowing Lambda to manage snapshots.

### 3. **IAM Policy Creation and Attachment**

#### Creating an IAM Policy
- **Purpose:** An IAM policy defines permissions for accessing AWS resources. In this case, it grants permissions to describe and delete EBS snapshots.

- **Step-by-Step Process:**
  1. **Create Policy:**
     - Go to the IAM dashboard.
     - Navigate to "Policies" and click "Create Policy."
     - Use the JSON editor to define the policy (e.g., allow describing and deleting snapshots).
     - Name and create the policy.

  **Command/Output Example:**
```shell
  aws iam create-policy --policy-name cost-optimization-EBS --policy-document file://policy.json
```
  - **Output:** Policy ARN is provided.

#### Attaching IAM Policy to Lambda Execution Role
- **Purpose:** Attach the created policy to the Lambda execution role to grant necessary permissions.

- **Step-by-Step Process:**
  1. **Attach Policy:**
     - Go to the IAM dashboard.
     - Navigate to "Roles" and find the Lambda execution role.
     - Attach the newly created policy.

  **Command/Output Example:**
```shell
  aws iam attach-role-policy --role-name LambdaExecutionRole --policy-arn arn:aws:iam::123456789012:policy/cost-optimization-EBS
```
  - **Output:** Policy attached successfully.

### Summary

- **EC2 Instance and EBS Snapshot Creation:** Essential for setting up and managing cloud resources.
- **Lambda Function:** Automates the cleanup of unused snapshots to optimize costs.
- **IAM Policy and Role Management:** Ensures that Lambda functions have the necessary permissions to perform their tasks.

By understanding these concepts and processes, you can effectively manage your AWS resources and implement cost-saving measures using automation.
