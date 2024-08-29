# Lab 6: Understanding the Order of Precedence in Terraform Variable Assignment

## Objective

Learn about the order of precedence in Terraform variable assignment by exploring different methods: command line flags, environment variables, files, and default values. This lab will demonstrate how Terraform determines which value to use when multiple methods are provided.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-6
    cd terraform-lab-6
    ```

### 2. Create the Variables Definition File (`variables.tf`)

- Create a file named `variables.tf` in the `terraform-lab-6` directory to define input variables with default values.

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

- Create a file named `main.tf` in the `terraform-lab-6` directory and use the variables defined in `variables.tf`.

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

### 4. Using Default Values

- By default, Terraform will use the values specified in `variables.tf` if no other values are provided. Run the following commands to use these default values.

    ```bash
    terraform init
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create one `t2.micro` EC2 instance in the `us-west-2` region.

### 5. Override with Environment Variables

- Set environment variables to override the default values. Environment variables take precedence over defaults in `variables.tf`.

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

### 6. Override with `terraform.tfvars` File

- Create a file named `terraform.tfvars` in the `terraform-lab-6` directory to specify variable values. Values in `terraform.tfvars` override environment variables.

    ```hcl
    aws_region    = "eu-west-1"
    instance_type = "t2.small"
    instance_count = 3
    ```

- Run the `plan` and `apply` commands to use the values from `terraform.tfvars`.

    ```bash
    terraform plan
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create three `t2.small` EC2 instances in the `eu-west-1` region.

### 7. Override with Command Line Flags

- Use command line flags to specify variable values. This has the highest precedence and will override all other methods.

    ```bash
    terraform plan -var="aws_region=ap-south-1" -var="instance_type=t3a.medium" -var="instance_count=4"
    terraform apply -var="aws_region=ap-south-1" -var="instance_type=t3a.medium" -var="instance_count=4"
    ```

- Confirm by typing `yes`. Terraform will create four `t3a.medium` EC2 instances in the `ap-south-1` region.

### 8. Verify the Deployments

- After each apply, check the AWS Management Console to verify the creation of the EC2 instances in the specified regions and with the specified instance types.

### 9. Clean Up

- After experimenting with the different methods of variable assignment, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy all the EC2 instances created during this lab.

## Explanation

- **Variable Defaults**: Used when no other values are provided.
- **Environment Variables**: Override default values in `variables.tf`.
- **`terraform.tfvars` File**: Overrides both environment variables and default values.
- **Command Line Flags**: Have the highest precedence and override all other methods.

## Outcome

By the end of this lab, participants will:

- Understand the order of precedence for variable assignment in Terraform.
- Learn how to use environment variables, `terraform.tfvars`, and command line flags to manage configuration values.
- Gain experience in controlling Terraform behavior using different variable assignment methods.

---

This lab provides hands-on experience with the different methods of variable assignment and their order of precedence in Terraform, helping users understand how to manage configuration values effectively.
