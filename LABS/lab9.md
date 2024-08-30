# Lab 9: Saving and Using Terraform Plan

## Objective

Learn how to save a Terraform plan to a file and apply it later. This lab will demonstrate how to create a Terraform plan, save it to a file, and then apply the saved plan to deploy infrastructure. This helps ensure that the plan applied matches the reviewed plan and prevents unexpected changes.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-9
    cd terraform-lab-9
    ```

### 2. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-9` directory with a simple configuration to deploy an EC2 instance.

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"

      tags = {
        Name = "TerraformPlanInstance"
      }
    }
    ```

### 3. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 4. Create a Terraform Plan

- Use the `terraform plan` command to create an execution plan. This command will show you what actions Terraform will take to achieve the desired state described in your configuration.

    ```bash
    terraform plan -out=tfplan
    ```

- The `-out` flag saves the generated execution plan to a file named `tfplan`. This file contains a serialized version of the plan.

### 5. Inspect the Saved Plan

- You can use the `terraform show` command to inspect the contents of the saved plan file. This helps in reviewing the changes before applying them.

    ```bash
    terraform show tfplan
    ```

- This command will display the details of the plan, showing what resources will be created, modified, or destroyed.

### 6. Apply the Saved Plan

- Use the `terraform apply` command with the saved plan file to apply the changes. This ensures that only the changes specified in the saved plan are executed, preventing any unintended modifications.

    ```bash
    terraform apply tfplan
    ```

- Confirm by typing `yes`. Terraform will apply the changes specified in the saved plan file, creating the EC2 instance.

### 7. Verify the Deployment

- After applying the plan, verify that the EC2 instance has been created as specified in your `main.tf` configuration.

- You can check the AWS Management Console to confirm that the instance is running or use the AWS CLI command:

    ```bash
    aws ec2 describe-instances --filters "Name=tag:Name,Values=TerraformPlanInstance"
    ```

### 8. Make Changes and Save a New Plan

- Modify the `main.tf` configuration to change the `instance_type` or any other property.

    ```hcl
    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = "t2.small"               # Changed instance type

      tags = {
        Name = "TerraformPlanInstance"
      }
    }
    ```

- Create a new plan with the changes and save it to a different file.

    ```bash
    terraform plan -out=tfplan_new
    ```

- Review the new plan using the `terraform show` command.

    ```bash
    terraform show tfplan_new
    ```

### 9. Apply the New Saved Plan

- Apply the new saved plan to update the EC2 instance.

    ```bash
    terraform apply tfplan_new
    ```

- Confirm by typing `yes`. Terraform will update the EC2 instance according to the new plan.

### 10. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance created during this lab.

## Explanation

- **Saving a Plan**: Using `terraform plan -out=tfplan` saves the execution plan to a file. This helps in reviewing the changes and ensuring that the same plan is applied later, preventing unapproved changes from being executed.

- **Inspecting a Plan**: The `terraform show` command allows you to inspect the contents of a saved plan file, making it easier to understand what changes will be applied.

- **Applying a Saved Plan**: By using `terraform apply tfplan`, you ensure that only the changes specified in the saved plan file are applied, reducing the risk of unintended changes.

## Outcome

By the end of this lab, participants will:

- Understand how to save a Terraform plan to a file.
- Learn how to inspect a saved plan to review proposed changes.
- Gain experience in applying a saved plan to ensure consistent and predictable changes to infrastructure.

---

This lab provides hands-on experience with saving and using Terraform plans, a crucial skill for managing changes in a controlled and predictable manner.
