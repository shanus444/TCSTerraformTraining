# Lab 5: Multiple Approaches to Variable Assignment in Terraform

## Objective

Learn how to assign values to Terraform variables using different approaches: environment variables, command line flags, values from a file, and variable defaults. This lab will help freshers understand the flexibility Terraform offers for managing configuration values.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-5
    cd terraform-lab-5
    ```

### 2. Create the Variables Definition File (`variables.tf`)

- Create a file named `variables.tf` in the `terraform-lab-5` directory to define input variables.

    ```hcl
    variable "aws_region" {
      description = "The AWS region where resources will be created"
      type        = string
      default     = "us-west-2"
    }

    variable "instance_type" {
      description = "The type of instance to create"
      type        = string
      default     = "t2.micro"
    }

    variable "instance_count" {
      description = "The number of instances to create"
      type        = number
      default     = 1
    }
    ```

### 3. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-5` directory and use the variables defined in `variables.tf`.

    ```hcl
    provider "aws" {
      region = var.aws_region
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = var.instance_type
      count         = var.instance_count

      tags = {
        Name = "TerraformInstance-${count.index + 1}"
      }
    }
    ```

### 4. Using Variable Defaults

- In the `variables.tf` file, default values are already set for each variable.
- Run the following commands to initialize and apply the configuration using these default values.

    ```bash
    terraform init
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will use the default values and create one `t2.micro` EC2 instance in the `us-west-2` region.

### 5. Assign Variables Using Environment Variables

- Terraform can read environment variables prefixed with `TF_VAR_`. Set environment variables to override the defaults.

    ```bash
    export TF_VAR_aws_region="us-east-1"
    export TF_VAR_instance_type="t3.micro"
    export TF_VAR_instance_count="2"
    ```

- Run the `plan` and `apply` commands to use these environment variables.

    ```bash
    terraform plan
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will now create two `t3.micro` EC2 instances in the `us-east-1` region.

### 6. Assign Variables Using Command Line Flags

- You can pass variable values directly in the command line using the `-var` flag.

    ```bash
    terraform plan -var="aws_region=eu-west-1" -var="instance_type=t2.small" -var="instance_count=3"
    terraform apply -var="aws_region=eu-west-1" -var="instance_type=t2.small" -var="instance_count=3"
    ```

- Confirm by typing `yes`. Terraform will create three `t2.small` EC2 instances in the `eu-west-1` region.

### 7. Assign Variables Using a File (`terraform.tfvars`)

- Create a file named `terraform.tfvars` in the `terraform-lab-4` directory to specify variable values.

    ```hcl
    aws_region    = "ap-south-1"
    instance_type = "t3a.medium"
    instance_count = 2
    ```

- Run the `plan` and `apply` commands to use the values from `terraform.tfvars`.

    ```bash
    terraform plan
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create two `t3a.medium` EC2 instances in the `ap-south-1` region.

### 8. Verify the Deployments

- After each apply, check the AWS Management Console to verify the creation of the EC2 instances in the specified regions and with the specified instance types.

### 9. Clean Up

- After experimenting with the different variable assignment methods, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy all the EC2 instances created during this lab.

## Explanation

- **Variable Defaults**: If no other value is provided, Terraform uses the default values specified in `variables.tf`.

- **Environment Variables**: Using environment variables is a good practice for sensitive values (like access keys) or when you want to avoid hardcoding values in files.

- **Command Line Flags**: Passing variables via the command line is useful for temporary overrides or for automation scripts.

- **Values from a File**: Using a `terraform.tfvars` file allows for easy management of variable values, especially when working in different environments (e.g., development, staging, production).

## Outcome

By the end of this lab, participants will:

- Understand multiple approaches to assigning values to Terraform variables.
- Learn how to use environment variables, command line flags, and files for variable assignments.
- Gain experience in creating flexible and dynamic Terraform configurations.

---

This lab provides hands-on experience with different methods of variable assignment in Terraform, helping users manage configuration values more effectively.
