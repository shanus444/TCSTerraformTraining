# Lab 4: Using Variables in Terraform

## Objective

Learn how to use variables in Terraform to make configurations dynamic, flexible, and reusable. This lab will introduce freshers to the use of input variables, defining default values, and overriding them using a `terraform.tfvars` file.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-4
    cd terraform-lab-4
    ```

### 2. Create the Variables Definition File (`variables.tf`)

- Create a file named `variables.tf` in the `terraform-lab-4` directory to define input variables.

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

    variable "ami_id" {
      description = "The ID of the AMI to use for the instance"
      type        = string
      default     = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
    }
    ```

### 3. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-3` directory and use the variables defined in `variables.tf`.

    ```hcl
    provider "aws" {
      region = var.aws_region
    }

    resource "aws_instance" "example" {
      ami           = var.ami_id
      instance_type = var.instance_type
      count         = var.instance_count

      tags = {
        Name = "TerraformInstance-${count.index + 1}"
      }
    }
    ```

### 4. Create a Variables File (`terraform.tfvars`)

- Create a file named `terraform.tfvars` in the `terraform-lab-4` directory to override default values. This file will define values for variables at runtime.

    ```hcl
    aws_region    = "us-east-1"
    instance_type = "t3.micro"
    instance_count = 2
    ami_id        = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
    ```

### 5. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 6. Plan the Terraform Deployment

- Run the plan command to see how Terraform will use the variables defined in the `terraform.tfvars` file.

    ```bash
    terraform plan
    ```

- Terraform will show a plan that includes the creation of two EC2 instances (`instance_count = 2`) in the `us-east-1` region (`aws_region = "us-east-1"`) with the specified instance type.

### 7. Apply the Configuration

- Apply the configuration to create the instances using the defined variables.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create two EC2 instances according to the configurations specified in `main.tf` and overridden by `terraform.tfvars`.

### 8. Verify the Deployment

- After the apply completes, you can verify the creation of the EC2 instances in the AWS Management Console under the EC2 dashboard in the `us-east-1` region.

### 9. Experiment with Different Variable Values

- Modify the `terraform.tfvars` file to use different values. For example, change the `instance_count` to 3 and `instance_type` to `t2.small`:

    ```hcl
    instance_count = 3
    instance_type  = "t2.small"
    ```

- Re-run the `plan` and `apply` commands to see how Terraform updates the configuration.

    ```bash
    terraform plan
    terraform apply
    ```

### 10. Clean Up

- After experimenting, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy all the EC2 instances created during this lab.

## Explanation

- **Variables**: Terraform variables allow you to parameterize your configuration files. This means you can reuse the same configuration for different environments, regions, or instance types by simply changing the variable values.

- **Default Values**: Variables can have default values, making them optional. If not specified, Terraform will use the default value.

- **Overriding Variables**: Variables can be overridden using the `terraform.tfvars` file or command-line options. This allows for different configurations without changing the code.

- **Count and Indexing**: The `count` parameter allows you to create multiple instances of a resource, and `${count.index}` helps in differentiating them.

## Outcome

By the end of this lab, participants will:

- Understand how to define and use variables in Terraform.
- Learn how to set default values and override them using `terraform.tfvars`.
- Gain experience in creating reusable and dynamic Terraform configurations.

---

This lab introduces the concept of variables in Terraform and how they can make infrastructure management more flexible and reusable.
