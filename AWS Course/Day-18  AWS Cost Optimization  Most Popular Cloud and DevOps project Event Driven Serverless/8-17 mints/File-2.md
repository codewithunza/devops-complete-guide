### AWS Cost Optimization: Deleting Stale Resources Using Lambda Functions

**Overview:**  
This session is focused on optimizing cloud costs for organizations by detecting and removing unused (stale) resources. The key aim is to prevent organizations from incurring additional charges due to leftover AWS resources like EC2 snapshots, volumes, EBS snapshots, or S3 buckets. This process is critical for **DevOps engineers** and **Cloud engineers**, who play a vital role in maintaining cloud infrastructure efficiently.

### Concept 1: Cloud Cost Optimization for DevOps and Cloud Engineers

**Why Organizations Use Cloud Platforms:**
- **Reduce infrastructure overhead**: Moving to the cloud eliminates the need to manage physical data centers, hire large IT teams, and maintain expensive hardware.
- **Optimize costs**: The cloud promises cost efficiency if used correctly. However, if resources are left unused (e.g., snapshots, EC2 instances, volumes), costs can increase unexpectedly.

**Example Scenario:**
A developer creates an EC2 instance with an attached EBS volume. The developer takes snapshots of the volume daily for backup. After deleting the EC2 instance and volume, the snapshots are forgotten, yet AWS continues to charge for them. DevOps engineers are responsible for identifying and removing such **stale resources**.

### Concept 2: Why DevOps Engineers Need to Perform Cost Optimization

**Real-Time Monitoring:**  
Even though AWS can automatically handle much of the infrastructure, inefficient usage (e.g., unused resources) can significantly increase costs.  
**Primary Responsibilities:**
1. **Monitor cloud resources**: Detect stale or unused resources.
2. **Take action**: Either notify teams or delete unused resources directly.

### Concept 3: Stale Resources and Their Impact

- **Stale resources**: Resources (e.g., EC2 snapshots, volumes) that are no longer in use but continue to incur costs.
- **Example**: An EBS snapshot that has no attached volume or an unused EC2 instance.
  
**DevOps Engineers' Role:**  
- Identify unused resources using AWS services.
- Either manually delete or automate the deletion using scripts or AWS Lambda functions.

### Concept 4: Lambda Functions and Boto3 for Cost Optimization

**Lambda Function Architecture for Deleting Stale Resources:**
1. **Lambda Functions**: Event-driven functions that execute code without needing a server. These functions help automate cost optimization tasks.
2. **Python & Boto3**: The most common language used in Lambda for AWS-related tasks is Python. The `boto3` library is used to interact with AWS APIs (like fetching information about EBS snapshots).

**Basic Steps:**
1. **Fetch data**: Use `boto3` to retrieve all EBS snapshots.
2. **Filter data**: Identify unused or stale snapshots (e.g., snapshots with no associated EC2 instance).
3. **Delete resources**: Automatically delete stale snapshots using the same Lambda function.

### Concept 5: Automating Snapshot Deletion with Lambda and CloudWatch

Lambda functions can be triggered by AWS **CloudWatch events**. This allows for periodic checks (e.g., daily) for stale resources and automatic deletion if necessary.

**Steps for Implementation**:
- Create a Lambda function in Python that uses `boto3` to interact with AWS.
- Set up a CloudWatch rule to trigger the Lambda function at regular intervals (e.g., every day or week).
  
**Example Python Code for Snapshot Deletion**:
```python
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    # Step 1: Fetch all snapshots
    snapshots = ec2.describe_snapshots(OwnerIds=['self'])
    
    # Step 2: Iterate through snapshots and check for stale ones
    for snapshot in snapshots['Snapshots']:
        # Example condition: Snapshot is older than 6 months
        if snapshot['StartTime'].year < 2023:  # Adjust based on current date
            # Step 3: Delete the stale snapshot
            ec2.delete_snapshot(SnapshotId=snapshot['SnapshotId'])
            print(f"Deleted snapshot {snapshot['SnapshotId']}")
```

### Scenario Explanation:

**Problem Statement:**
- A developer creates multiple snapshots for an EBS volume and later deletes the EC2 instance. The EBS snapshots remain and accumulate costs.

**Solution Steps:**
1. **Write Lambda Function**: A Python Lambda function is written to fetch all EBS snapshots.
2. **Filter**: Identify snapshots not attached to any volume or EC2 instance.
3. **Automate Deletion**: Use CloudWatch to periodically trigger the function to delete stale snapshots automatically.

### Concept 6: Handling Backup Snapshots

**Manual Checks**:  
Sometimes, developers intentionally keep snapshots for future use. The Lambda function can be modified to check the **age of the snapshot** and only delete snapshots older than a defined time period (e.g., 6 months).

**Adding a Timestamp Check**:
```python
import datetime

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    snapshots = ec2.describe_snapshots(OwnerIds=['self'])
    
    # Get today's date
    today = datetime.date.today()
    
    for snapshot in snapshots['Snapshots']:
        # If snapshot is older than 6 months
        snapshot_date = snapshot['StartTime'].date()
        if (today - snapshot_date).days > 180:  # 6 months = 180 days
            ec2.delete_snapshot(SnapshotId=snapshot['SnapshotId'])
            print(f"Deleted snapshot {snapshot['SnapshotId']}")
```

### Concept 7: Code and Resources for Practical Implementation

- **GitHub Repository**: You can find the entire project code in the GitHub repository under the `day 18` folder. This includes the Python script for deleting snapshots (`EBS_stale_snapshots.py`) and documentation for the problem statement.
  
**How to Use This in Interviews**:
- Explain the problem statement.
- Describe how you used AWS services (Lambda, CloudWatch, Boto3).
- Demonstrate the script and explain the automation of cost optimization.
  
### Conclusion:

This project demonstrates **real-time cloud cost optimization** using Lambda functions and Python. It's a crucial task for DevOps engineers, ensuring that the organization doesn’t incur unnecessary costs from stale AWS resources. Regular monitoring and automation help maintain efficient cloud infrastructure.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

### Cloud Cost Optimization: Introduction

Cloud cost optimization is a critical practice for DevOps and Cloud Engineers. It involves identifying and removing unnecessary resources from a cloud environment to reduce expenses. This is essential because, while cloud platforms like AWS reduce the upfront infrastructure cost, inefficient use can still lead to high expenses.

---

### Basic Concept: Why Cost Optimization?

Organizations move to the cloud primarily for two reasons:
1. **Reduce infrastructure overhead:** The cloud allows businesses to avoid the hassle of setting up physical data centers, which include purchasing hardware, hiring maintenance teams, and monitoring resources.
2. **Optimize infrastructure cost:** Cloud platforms, like AWS, offer flexibility with pay-as-you-go models, but costs can still rise if resources are not used efficiently. One key area of cost inefficiency comes from "stale resources"—resources that were created but are no longer in use, like orphaned EBS volumes or unused snapshots.

---

### Common Scenarios Leading to High Cloud Costs

#### Example 1: Orphaned Snapshots
1. **Problem:** A developer creates an EC2 instance, attaches an EBS volume, and regularly takes snapshots for backup. Later, they delete the EC2 instance but forget to delete the EBS volume or snapshots.
2. **Impact:** AWS continues to charge for those snapshots, even though they're no longer useful. This is a common cause of unnecessary costs in the cloud.

#### Example 2: Unused S3 Buckets
1. **Problem:** A developer creates an S3 bucket but forgets to delete it after use. AWS charges based on the storage used, and the cost rises as more data accumulates.
2. **Impact:** Failing to delete old buckets results in unexpected expenses.

#### Example 3: Unused EKS Cluster
1. **Problem:** A developer creates an EKS cluster for testing, then forgets to delete it. Resources like worker nodes and load balancers continue to incur charges.
2. **Impact:** These unused clusters can lead to significant costs if not managed efficiently.

---

### Automation with Lambda for Cloud Cost Optimization

To automate the deletion of stale resources, DevOps Engineers can use **AWS Lambda functions**. The architecture for cost optimization using Lambda involves the following:

1. **Lambda Function**: A serverless compute service where you can run your code without provisioning servers.
2. **Boto3 Module**: A Python SDK for AWS that allows the Lambda function to interact with AWS resources.
3. **CloudWatch**: To trigger the Lambda function on a schedule, CloudWatch Events are used. You can run the function daily, weekly, or as needed.

---

### Project Example: Automatically Deleting Orphaned EBS Snapshots

#### Problem Statement:
You have a set of EBS snapshots that were created for EC2 instances, but now the EC2 instances and associated volumes are deleted. These snapshots are no longer useful and should be removed to avoid unnecessary costs.

#### Solution Architecture:
1. **Lambda Function**: Written in Python to identify and delete orphaned EBS snapshots.
2. **Boto3**: The Lambda function will use this SDK to interact with AWS APIs.
3. **CloudWatch**: Triggers the Lambda function periodically to ensure no stale resources remain.

---

### Lambda Function Code Breakdown

Here is a basic Python Lambda function to delete orphaned EBS snapshots using Boto3.

```python
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    # Step 1: Fetch all EBS snapshots
    snapshots = ec2.describe_snapshots(OwnerIds=['self'])['Snapshots']
    
    # Step 2: Identify stale snapshots (i.e., those not attached to any EBS volume)
    stale_snapshots = []
    for snapshot in snapshots:
        volume_id = snapshot.get('VolumeId', None)
        if not volume_id:
            stale_snapshots.append(snapshot['SnapshotId'])
    
    # Step 3: Delete the stale snapshots
    for snapshot_id in stale_snapshots:
        ec2.delete_snapshot(SnapshotId=snapshot_id)
        print(f"Deleted snapshot: {snapshot_id}")
    
    return {"status": "Snapshots deleted successfully"}

```

#### Step-by-Step Explanation:
1. **Initialize Boto3 Client**: This allows interaction with AWS services, in this case, EC2, to manage EBS snapshots.
2. **Fetch Snapshots**: The `describe_snapshots` method retrieves all EBS snapshots owned by your AWS account.
3. **Identify Stale Snapshots**: The function checks if each snapshot is associated with a volume. If not, it’s considered stale.
4. **Delete Snapshots**: The `delete_snapshot` method removes these stale snapshots, reducing the cloud cost.

---

### Scenario: Triggering the Lambda Function

- **Use Case**: You want this function to run every day at midnight to automatically clean up orphaned snapshots.
- **Solution**: Use **CloudWatch Events** to schedule the Lambda function.

#### CloudWatch Event Rule:
```json
{
  "Name": "DailySnapshotCleanup",
  "ScheduleExpression": "cron(0 0 * * ? *)",  # Runs every day at midnight
  "Targets": [
    {
      "Arn": "arn:aws:lambda:region:account-id:function:function-name",
      "Id": "LambdaFunctionTarget"
    }
  ]
}
```

---

### Handling Edge Cases

1. **Snapshots kept for backup**: In cases where snapshots are kept intentionally, the Lambda function could be enhanced with a check for the snapshot's creation date. Snapshots older than a defined threshold (e.g., six months) could be deleted automatically.

```python
    from datetime import datetime, timedelta

    six_months_ago = datetime.now() - timedelta(days=180)

    for snapshot in snapshots:
        snapshot_date = snapshot['StartTime'].replace(tzinfo=None)
        if snapshot_date < six_months_ago:
            ec2.delete_snapshot(SnapshotId=snapshot['SnapshotId'])
```

2. **Manual Verification**: If certain snapshots should be manually reviewed before deletion, the Lambda function could send alerts via **SNS** to notify relevant teams.

---

### Conclusion

This approach allows DevOps Engineers to implement cloud cost optimization by automatically deleting unused resources like EBS snapshots. Regularly cleaning up stale resources ensures the cloud cost stays low, aligning with the initial goal of moving to the cloud: **efficiency and cost savings**.

By setting up Lambda functions, CloudWatch triggers, and leveraging Boto3, you can fully automate cloud cost optimization tasks—making it easier to manage AWS resources at scale.
