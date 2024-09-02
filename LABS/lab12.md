# Lab 12: Using Terraform Modules

## Objective

Learn how to create and use Terraform modules to organize and reuse Terraform code effectively. This lab will demonstrate how to create a simple module for deploying an EC2 instance and how to use that module in a Terraform configuration.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Introduction to Terraform Modules

A Terraform module is a set of Terraform configuration files located in a dedicated directory. A module consists of:

- **Input Variables**: These are used to pass dynamic values into the module.
- **Resources**: These define the infrastructure components (e.g., EC2 instances, VPCs).
- **Outputs**: These are used to return values from the module that can be used by the calling module or configuration.

Modules allow for the reuse of infrastructure code across different parts of a project or even across different projects. This reduces duplication and makes the infrastructure easier to manage and scale.

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-12
    cd terraform-lab-12
    ```

### 2. Create the Module Directory

- Inside the `terraform-lab-12` directory, create a subdirectory for the EC2 module.

    ```bash
    mkdir ec2-module
    ```

### 3. Define the Module Configuration (`ec2-module/main.tf`)

- Create a file named `main.tf` inside the `ec2-module` directory. This file will define the resources for the EC2 instance.

    ```hcl
    provider "aws" {
      region = var.aws_region
    }

    resource "aws_instance" "example" {
      ami           = var.ami_id
      instance_type = var.instance_type

      tags = {
        Name = var.instance_name
      }
    }
    ```

### 4. Define Module Variables (`ec2-module/variables.tf`)

- Create a file named `variables.tf` inside the `ec2-module` directory to define the input variables for the module.

    ```hcl
    variable "aws_region" {
      description = "The AWS region to deploy resources in"
      type        = string
      default     = "us-west-2"
    }

    variable "ami_id" {
      description = "The ID of the AMI to use for the EC2 instance"
      type        = string
    }

    variable "instance_type" {
      description = "The type of instance to create"
      type        = string
      default     = "t2.micro"
    }

    variable "instance_name" {
      description = "The name of the EC2 instance"
      type        = string
    }
    ```

### 5. Define Module Outputs (`ec2-module/outputs.tf`)

- Create a file named `outputs.tf` inside the `ec2-module` directory to define the output values for the module.

    ```hcl
    output "instance_id" {
      description = "The ID of the created EC2 instance"
      value       = aws_instance.example.id
    }

    output "instance_public_ip" {
      description = "The public IP address of the created EC2 instance"
      value       = aws_instance.example.public_ip
    }
    ```

### 6. Use the Module in Your Terraform Configuration

- Now, create a root configuration that uses the EC2 module. Create a new file named `main.tf` in the `terraform-lab-12` directory.

    ```hcl
    module "ec2_instance" {
      source        = "./ec2-module"
      aws_region    = "us-west-2"
      ami_id        = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"
      instance_name = "TerraformModuleInstance"
    }

    output "ec2_instance_id" {
      description = "The ID of the EC2 instance from the module"
      value       = module.ec2_instance.instance_id
    }

    output "ec2_instance_public_ip" {
      description = "The public IP address of the EC2 instance from the module"
      value       = module.ec2_instance.instance_public_ip
    }
    ```

### 7. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 8. Plan the Configuration

- Run the plan command to see what Terraform will do.

    ```bash
    terraform plan
    ```

- The output will show the actions that Terraform will take, using the configuration defined in the module.

### 9. Apply the Configuration

- Apply the configuration to create the EC2 instance.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instance using the module configuration.

### 10. Verify the Deployment

- After applying the configuration, verify that the EC2 instance has been created as specified.

- You can check the AWS Management Console to confirm that the instance is running or use the AWS CLI command:

    ```bash
    aws ec2 describe-instances --filters "Name=tag:Name,Values=TerraformModuleInstance"
    ```

### 11. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance created during this lab.

## Explanation

- **Modules**: Modules help organize and reuse infrastructure code. By defining resources, variables, and outputs in a module, you can easily reuse and manage your infrastructure components.

- **Input Variables**: Variables allow modules to be flexible and reusable with different configurations. They can be defined in the root module and passed to the child modules.

- **Outputs**: Outputs allow a module to return values to the calling module. They can be used to access resource attributes and pass information between modules.

- **Source**: The `source` argument in the module block specifies the location of the module. In this example, the module is located in the `./ec2-module` directory.

## Outcome

By the end of this lab, participants will:

- Understand how to create and use Terraform modules.
- Learn how to define input variables and outputs in a module.
- Gain experience in using modules to organize and reuse Terraform code.

---

This lab provides hands-on experience with Terraform modules, helping users build modular and maintainable infrastructure. 
