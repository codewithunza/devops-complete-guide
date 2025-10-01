### Writing the AWS Resource Tracker Shell Script

We'll break down the process step-by-step for creating an AWS Resource Tracker shell script that can be scheduled to run periodically using Cron jobs. This script will track various AWS resources and generate a report. 

#### 1. **File Creation and Setup**

Open a terminal and create a new script file named `aws_resource_tracker.sh` using a text editor like `vim`.

```bash
vim aws_resource_tracker.sh
```

Once inside `vim`, press `i` to enter insert mode. Start writing your script by specifying the interpreter and adding metadata.

#### 2. **Shebang and Metadata**

Begin with the shebang to specify the script interpreter, followed by author information and script details.

```bash
#!/bin/bash

# Author: Abhishek
# Date: 11th Jan
# Version: V1
# Description: This script will report the AWS resource usage.
```

#### 3. **Defining the Purpose and Tracking Objects**

Add comments to define what resources the script will track. This helps in maintaining and understanding the script.

```bash
# This script tracks the following AWS resources:
# 1. EC2 Instances
# 2. S3 Buckets
# 3. Lambda Functions
# 4. IAM Users
```

#### 4. **AWS CLI Configuration Check**

Ensure the AWS CLI is configured properly. Add a check for AWS CLI availability and configuration.

```bash
# Check if AWS CLI is installed
if ! command -v aws &> /dev/null
then
    echo "AWS CLI not installed. Please install it to proceed."
    exit 1
fi

# Check if AWS CLI is configured
aws sts get-caller-identity &> /dev/null
if [ $? -ne 0 ]; then
    echo "AWS CLI is not configured. Please run 'aws configure' to set up."
    exit 1
fi
```

#### 5. **Defining the Output File**

Specify the output file where the report will be saved.

```bash
# Define the output file
output_file="aws_resource_report_$(date +'%Y-%m-%d').txt"
```

#### 6. **Tracking EC2 Instances**

Use AWS CLI commands to get the list of EC2 instances and their statuses.

```bash
# Track EC2 Instances
echo "EC2 Instances:" > $output_file
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, State.Name]" --output text >> $output_file
echo "" >> $output_file
```

#### 7. **Tracking S3 Buckets**

Similarly, get the list of S3 buckets.

```bash
# Track S3 Buckets
echo "S3 Buckets:" >> $output_file
aws s3api list-buckets --query "Buckets[*].Name" --output text >> $output_file
echo "" >> $output_file
```

#### 8. **Tracking Lambda Functions**

Get the list of Lambda functions.

```bash
# Track Lambda Functions
echo "Lambda Functions:" >> $output_file
aws lambda list-functions --query "Functions[*].FunctionName" --output text >> $output_file
echo "" >> $output_file
```

#### 9. **Tracking IAM Users**

Get the list of IAM users.

```bash
# Track IAM Users
echo "IAM Users:" >> $output_file
aws iam list-users --query "Users[*].UserName" --output text >> $output_file
echo "" >> $output_file
```

#### 10. **Finalizing the Script**

End the script by printing a success message.

```bash
echo "AWS resource usage report generated: $output_file"
```

#### 11. **Making the Script Executable**

Save and exit the editor by pressing `ESC`, typing `:wq`, and pressing `Enter`. Then, make the script executable.

```bash
chmod +x aws_resource_tracker.sh
```

#### 12. **Automating with Cron Job**

To automate the script execution daily at 6 PM, add a Cron job. Edit the Cron table using `crontab -e`.

```bash
0 18 * * * /path/to/aws_resource_tracker.sh
```

This Cron job will run the script every day at 6 PM.

### Complete Script

Here is the complete script for reference:

```bash
#!/bin/bash

# Author: Abhishek
# Date: 11th Jan
# Version: V1
# Description: This script will report the AWS resource usage.

# Check if AWS CLI is installed
if ! command -v aws &> /dev/null
then
    echo "AWS CLI not installed. Please install it to proceed."
    exit 1
fi

# Check if AWS CLI is configured
aws sts get-caller-identity &> /dev/null
if [ $? -ne 0 ]; then
    echo "AWS CLI is not configured. Please run 'aws configure' to set up."
    exit 1
fi

# Define the output file
output_file="aws_resource_report_$(date +'%Y-%m-%d').txt"

# Track EC2 Instances
echo "EC2 Instances:" > $output_file
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, State.Name]" --output text >> $output_file
echo "" >> $output_file

# Track S3 Buckets
echo "S3 Buckets:" >> $output_file
aws s3api list-buckets --query "Buckets[*].Name" --output text >> $output_file
echo "" >> $output_file

# Track Lambda Functions
echo "Lambda Functions:" >> $output_file
aws lambda list-functions --query "Functions[*].FunctionName" --output text >> $output_file
echo "" >> $output_file

# Track IAM Users
echo "IAM Users:" >> $output_file
aws iam list-users --query "Users[*].UserName" --output text >> $output_file
echo "" >> $output_file

echo "AWS resource usage report generated: $output_file"
```

This detailed explanation should help you understand the steps involved in creating and automating an AWS resource tracker shell script.


# 20:24 mints video Print Statement:
Let's break down the concepts and steps in this part of the script, explaining them in detail and making them easy to understand.

### 1. **Adding Print Statements for Clarity**

When you run a script that interacts with AWS services, it can be hard to distinguish between different outputs, especially when there’s a lot of data. To make the script more user-friendly, you can add print statements using the `echo` command in Bash.

- **Print Statements**: By adding `echo` commands before each section of your script, you provide context to the output, making it clear what each section is about.

  ```bash
  echo "List of S3 buckets"
  aws s3 ls

  echo "List of EC2 instances"
  aws ec2 describe-instances

  echo "List of Lambda functions"
  aws lambda list-functions

  echo "List of IAM users"
  aws iam list-users
  ```

  **Explanation**:
  - `echo "message"`: This prints a message to the terminal, helping users understand the context of the following command's output.
  - Each `aws` command now has a corresponding print statement that explains what the output will show.

### 2. **Using Debug Mode with `set -x`**

In shell scripting, the `set` command can be used to control the behavior of the shell. Two common options are `set -x` and `set +x`, which toggle debug mode.

- **Debug Mode (`set -x`)**: When you enable this mode, the shell prints each command to the terminal as it is executed, which can be very helpful for debugging.

  ```bash
  set -x
  ```

  **Explanation**:
  - `set -x`: Enables debugging by printing each command before it is executed.
  - `set +x`: Disables debugging, returning to normal execution.

  **Use Case**:
  - This mode is useful when you want to see the exact commands being run, which helps in understanding the flow of the script and diagnosing where things might be going wrong.

### 3. **Filtering Output with `jq`**

AWS CLI often returns data in JSON format, which can be overwhelming if you’re only interested in specific pieces of information, such as instance IDs. The `jq` tool in Linux is used to parse and filter JSON data.

- **Filtering with `jq`**: To extract specific information like instance IDs from a large JSON response, you can use `jq`.

  ```bash
  aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
  ```

  **Explanation**:
  - **`jq`**: A powerful command-line tool for parsing JSON. It allows you to filter and transform JSON data in various ways.
  - **Filtering the JSON**: The command `jq '.Reservations[].Instances[].InstanceId'` navigates through the JSON structure returned by `describe-instances` and extracts only the instance IDs.

  **Benefit**:
  - This makes the output much more manageable and easier to understand, especially when reporting to others who may only need to see key information like instance IDs.

### 4. **Summary of Output Organization**

The modifications improve the script’s output in several ways:
- **Clarity**: By using `echo` statements, you provide clear headers for each section of the output, making it easier for users to understand what they’re looking at.
- **Debugging**: Using `set -x` helps you or others understand how the script executes, which is invaluable during development or troubleshooting.
- **Simplicity**: Filtering large outputs with `jq` reduces the noise and focuses on the most important data, such as instance IDs, making the report more concise and easier to digest.

### Example of the Final Script with All Improvements

```bash
#!/bin/bash

echo "List of S3 buckets"
aws s3 ls

echo "List of EC2 instances"
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'

echo "List of Lambda functions"
aws lambda list-functions

echo "List of IAM users"
aws iam list-users

set -x  # Enable debug mode

# Example commands with debug mode enabled to see their execution
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'

set +x  # Disable debug mode
```

This script is now much more user-friendly, easier to debug, and provides clear, focused output that’s easy to understand and share.

# 24: 28 mints Video 'JQ'
Let's break down the concepts discussed here and explain them in detail, step by step, including how the commands work, their output, and practical examples.

### 1. **Using `jq` for JSON Parsing**

In AWS, many commands return data in JSON format. Sometimes, this JSON data is too extensive, and you only need a specific piece of information, like an instance ID. The `jq` tool is a powerful JSON processor that allows you to extract the specific data you need from a JSON response.

#### **Basic Command Example:**
```bash
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
```

#### **Explanation:**
- **Piping (`|`)**: The pipe operator sends the output of the command on the left (`aws ec2 describe-instances`) as input to the command on the right (`jq`).
- **`jq`**: A command-line tool used for parsing JSON data. It allows you to filter, transform, and extract specific fields from JSON.
- **`'.Reservations[].Instances[].InstanceId'`**: This is the `jq` query. Let's break it down:
  - **`Reservations[]`**: Refers to the list of reservations in the JSON structure. The square brackets `[]` mean that it can handle multiple items in this list.
  - **`Instances[]`**: Refers to the list of instances within each reservation.
  - **`InstanceId`**: The specific field we're interested in, which is nested within each instance.

#### **Example Output:**
If your account has instances, the output would look something like this:
```
"i-0123456789abcdef0"
"i-0987654321abcdef0"
```
This output is a simple list of instance IDs, which is much easier to understand and use compared to the full JSON output.

### 2. **Simplifying the Script**

In the context of the script, we replace the previous verbose command with the simpler one that uses `jq` to only return the necessary information.

#### **Original Verbose Command:**
```bash
aws ec2 describe-instances
```
This command would return a large JSON output with a lot of details about each instance, which can be overwhelming.

#### **Simplified Command Using `jq`:**
```bash
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
```
This version only returns the instance IDs, making the output cleaner and more focused.

### 3. **Redirecting Output to a File**

You can redirect the output of your script to a file instead of displaying it directly in the terminal. This is useful for creating logs or reports.

#### **Redirecting Output Example:**
```bash
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId' > resource_tracker.txt
```

#### **Explanation:**
- **`>`**: This operator redirects the output to a file. If the file already exists, it will be overwritten.
- **`resource_tracker.txt`**: The name of the file where the output will be saved.

#### **Example of a Script with Redirection:**

```bash
#!/bin/bash

# Redirect all output to resource_tracker.txt
exec > resource_tracker.txt

echo "List of S3 buckets"
aws s3 ls

echo "List of EC2 instances"
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'

echo "List of Lambda functions"
aws lambda list-functions

echo "List of IAM users"
aws iam list-users
```

### 4. **Automating with Cron Jobs**

Once the script is working and you've verified that the output is what you need, you might want to automate it. This is where cron jobs come in.

#### **Setting Up a Cron Job:**

To run the script automatically at regular intervals, you can set up a cron job. For example, to run the script every day at midnight, you would:

1. Open the crontab editor:
   ```bash
   crontab -e
   ```
2. Add a new line to schedule the script:
   ```bash
   0 0 * * * /path/to/your/script.sh
   ```

#### **Explanation:**
- **`0 0 * * *`**: This is the cron schedule syntax, meaning "at minute 0, hour 0, every day."
- **`/path/to/your/script.sh`**: Replace this with the actual path to your script.

### 5. **Final Example Script**

Here is a final version of the script incorporating all the discussed concepts:

```bash
#!/bin/bash

# Redirect all output to resource_tracker.txt
exec > resource_tracker.txt

echo "List of S3 buckets"
aws s3 ls

echo "List of EC2 instances"
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'

echo "List of Lambda functions"
aws lambda list-functions

echo "List of IAM users"
aws iam list-users
```

### 6. **Practical Use Case**

This script is now ready to be used in a production environment where you might want to:
- Automatically generate a daily report of your AWS resources.
- Store these reports in a file for auditing or review.
- Simplify the output so that it's easy for non-technical stakeholders to understand.

With the addition of `jq`, the output is much more concise and focused, which is particularly useful when the script is integrated with other systems or when the output needs to be reviewed by someone else.

# OR In Detail Explanation:
Let's break down the script provided and understand each concept in detail, with examples and expected outputs.

### Overview of the Script

The script lists various AWS resources (S3 buckets, EC2 instances, Lambda functions, IAM users) and formats the output for clarity. It uses the `jq` tool to filter JSON output from AWS CLI commands.

### Concepts Explained

1. **Using `jq` for JSON Parsing**
   
   `jq` is a lightweight and flexible command-line JSON processor. It allows you to slice, filter, map, and transform JSON data. 

   **Basic Usage Example:**
   - **Command:**
     ```bash
     aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
     ```
   - **Explanation:**
     - `aws ec2 describe-instances`: This AWS CLI command retrieves detailed information about EC2 instances.
     - `|`: This pipe sends the output of the command on its left as input to the command on its right.
     - `jq '.Reservations[].Instances[].InstanceId'`: This jq filter navigates through the JSON structure and extracts the instance IDs.
       - `.Reservations[]`: Iterates over each element in the `Reservations` array.
       - `.Instances[]`: Iterates over each element in the `Instances` array.
       - `.InstanceId`: Extracts the `InstanceId` field from each instance.

   **Output Example:**
   ```json
   "i-0123456789abcdef0"
   "i-0abcdef1234567890"
   ```

2. **Adding Print Statements with `echo`**

   **Command:**
   ```bash
   echo "List of S3 buckets"
   aws s3 ls
   ```

   **Explanation:**
   - `echo "List of S3 buckets"`: Prints the text "List of S3 buckets" to the terminal. This helps in identifying the output section.

3. **Redirecting Output to a File**

   **Command:**
   ```bash
   aws s3 ls > resource_tracker.txt
   ```

   **Explanation:**
   - `>`: Redirects the output of the command on its left to the file specified on its right (`resource_tracker.txt`). If the file exists, it is overwritten.

4. **Combining Commands with Improved Output**

   **Script:**
   ```bash
   #!/bin/bash

   echo "List of S3 buckets" > resource_tracker.txt
   aws s3 ls >> resource_tracker.txt

   echo "List of EC2 instances" >> resource_tracker.txt
   aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId' >> resource_tracker.txt

   echo "List of Lambda functions" >> resource_tracker.txt
   aws lambda list-functions >> resource_tracker.txt

   echo "List of IAM users" >> resource_tracker.txt
   aws iam list-users >> resource_tracker.txt
   ```

   **Explanation:**
   - `>`: Creates or overwrites the file `resource_tracker.txt` with the specified output.
   - `>>`: Appends the output to `resource_tracker.txt` without overwriting existing content.

### Final Improved Script

This script collects and formats AWS resource information and saves it to a file for easier reading and reporting.

```bash
#!/bin/bash

# Print list of S3 buckets and save to file
echo "List of S3 buckets" > resource_tracker.txt
aws s3 ls >> resource_tracker.txt

# Print list of EC2 instances (only instance IDs) and save to file
echo "List of EC2 instances" >> resource_tracker.txt
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId' >> resource_tracker.txt

# Print list of Lambda functions and save to file
echo "List of Lambda functions" >> resource_tracker.txt
aws lambda list-functions >> resource_tracker.txt

# Print list of IAM users and save to file
echo "List of IAM users" >> resource_tracker.txt
aws iam list-users >> resource_tracker.txt
```

### Running the Script and Viewing the Output

1. **Make the Script Executable:**
   ```bash
   chmod +x aws_resource_tracker.sh
   ```

2. **Run the Script:**
   ```bash
   ./aws_resource_tracker.sh
   ```

3. **View the Output:**
   ```bash
   cat resource_tracker.txt
   ```

### Example Output

```
List of S3 buckets
2024-01-01 12:34:56 bucket-name-1
2024-01-02 12:34:56 bucket-name-2

List of EC2 instances
"i-0123456789abcdef0"
"i-0abcdef1234567890"

List of Lambda functions
{
  "Functions": [
    {
      "FunctionName": "example-function-1",
      "Runtime": "nodejs12.x",
      ...
    },
    ...
  ]
}

List of IAM users
{
  "Users": [
    {
      "UserName": "user1",
      "CreateDate": "2023-01-01T00:00:00Z",
      ...
    },
    ...
  ]
}
```

### Integrating with `cron`

You can schedule this script to run at regular intervals using `cron`.

1. **Open the `cron` Editor:**
   ```bash
   crontab -e
   ```

2. **Add a New Cron Job:**
   ```bash
   0 0 * * * /path/to/aws_resource_tracker.sh
   ```

   **Explanation:**
   - `0 0 * * *`: Runs the script daily at midnight.
   - `/path/to/aws_resource_tracker.sh`: The full path to your script.

This setup will ensure that your AWS resource information is regularly updated and saved to a file, providing an easy reference for you or your team.