Let's break down the entire AWS Config process and the concepts mentioned in the text in detail.

### **Day 25 of AWS DevOps Zero to Hero Series**
This video deep-dives into **AWS Config** and its relation to **compliance**. The idea is to ensure that your AWS resources (e.g., EC2 instances, S3 buckets, etc.) follow the defined rules and regulations set by your organization.

### **What is AWS Config?**
**AWS Config** is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources to ensure compliance with your organization’s governance. It continuously monitors and records AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations.

AWS Config answers these kinds of questions:
- **Are all resources following organizational rules?**
- **Which resources are out of compliance?**
- **What changed in a resource over time?**

#### **Key Features of AWS Config:**
1. **Continuous Monitoring**: It tracks resource configurations in real-time.
2. **Compliance Management**: Ensures resources adhere to organizational compliance policies.
3. **Change Detection**: Detects and records configuration changes of AWS resources.
4. **Integration**: AWS Config can integrate with AWS Lambda to perform customized actions (e.g., custom rules).

### **Compliance in AWS Config**
The example here uses **compliance monitoring** on EC2 instances. For instance, if your organization's rule is to have detailed monitoring enabled for all EC2 instances, AWS Config can help you ensure that.

### **Scenario Explanation**
In the example:
- **Two EC2 instances** are set up: one with monitoring enabled and the other without.
- The organization has a compliance rule that all EC2 instances must have detailed monitoring enabled.
- AWS Config is used to track which EC2 instance is compliant with this rule and which is not.

### **Setting up AWS Config**
1. **Search for AWS Config in AWS Console**: AWS Config can be found by searching in the AWS Management Console. Once you’re in AWS Config, you can set up rules for compliance.

2. **Create Rules**: 
   - AWS Config allows you to create rules that define the conditions for resource compliance.
   - There are **predefined rules** (AWS provides them) and **custom rules** that you can create using AWS Lambda.

3. **Integrating with Lambda**:
   - In the demo, the custom rule is created using a **Lambda function** that is triggered whenever an EC2 instance is created, updated, or deleted.
   - The Lambda function checks whether the detailed monitoring is enabled for the EC2 instance and reports the instance as either compliant or non-compliant.

### **Lambda Function Example**
Lambda is serverless, and it is **event-driven**. In this case, AWS Config is the event that triggers the Lambda function. The function evaluates the EC2 instance based on whether detailed monitoring is enabled or not.

#### **Example Code for Lambda Function**
```python
import boto3

def lambda_handler(event, context):
    ec2_client = boto3.client('ec2')
    instance_id = event['detail']['instance-id']
    
    # Fetch instance details
    response = ec2_client.describe_instances(InstanceIds=[instance_id])
    
    # Extract monitoring state
    monitoring_state = response['Reservations'][0]['Instances'][0]['Monitoring']['State']
    
    # Check compliance (detailed monitoring enabled)
    if monitoring_state == 'disabled':
        return {
            'compliance': 'NON_COMPLIANT',
            'message': f'Instance {instance_id} does not have detailed monitoring enabled.'
        }
    else:
        return {
            'compliance': 'COMPLIANT',
            'message': f'Instance {instance_id} is compliant.'
        }
```

In this example:
- **AWS Config sends an event** to the Lambda function when an EC2 instance is created or updated.
- **The Lambda function checks the monitoring state** of the EC2 instance.
- If monitoring is disabled, it reports the instance as non-compliant. Otherwise, it reports compliance.

### **Managing Compliance for Multiple Resources**
AWS Config can manage the compliance status for thousands of AWS resources, not just EC2. For example, you can ensure:
- **S3 buckets** have lifecycle management enabled or public access disabled.
- **IAM policies** follow least privilege.

### **AWS Config Dashboard**
Once AWS Config is set up, you can view the compliance status of all resources from the AWS Config dashboard:
- **Compliant**: Resources following the rules.
- **Non-compliant**: Resources that don’t follow the organizational rules.

### **Why Use AWS Config?**
1. **Regulatory Compliance**: Many industries have regulatory requirements that demand resource compliance.
2. **Operational Excellence**: Helps identify misconfigurations that could lead to security issues or inefficiencies.
3. **Security**: AWS Config helps ensure that resources follow strict security standards.
4. **Audit and Governance**: Provides detailed records of configuration changes for auditing purposes.

### **Enabling Compliance**
- **Manual Remediation**: You can manually fix the compliance issues reported by AWS Config.
- **Automated Remediation**: AWS Config can trigger Lambda functions to automatically remediate non-compliant resources.

### **Example of a Compliance Rule** (EC2 Monitoring)
1. **Rule**: All EC2 instances must have detailed monitoring enabled.
2. **Compliance Check**: AWS Config monitors EC2 instances and calls the Lambda function to check their monitoring status.
3. **Non-compliance**: If detailed monitoring is disabled on any EC2 instance, AWS Config marks it as non-compliant.
4. **Remediation**: AWS Config can either notify you about non-compliance or trigger a Lambda function to enable monitoring automatically.

### **Commands and Output**

#### **Step 1: Search AWS Config**
```bash
# This command is done via the AWS Console interface
aws configservice describe-compliance-by-resource
```
This will describe the compliance status of specified resources.

#### **Step 2: Integrating with Lambda**
In the AWS Config interface, configure a rule that will invoke the Lambda function whenever an EC2 instance is created or modified.

#### **Step 3: Verifying Compliance**
You can view compliance reports in the AWS Config dashboard or use the AWS CLI to retrieve compliance data.

```bash
aws configservice get-compliance-details-by-resource \
    --resource-type "AWS::EC2::Instance" \
    --resource-id i-1234567890abcdef0
```

This command will return the compliance status of a specific EC2 instance.

### **Conclusion**
AWS Config is a powerful tool for automating the compliance of your AWS resources, ensuring they meet your organizational standards. By integrating with Lambda functions, you can create custom rules and automatically remediate any non-compliance issues, making it a vital part of any DevOps or cloud management strategy.