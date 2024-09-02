# Lab 11: Using Multiple Terraform Modules - Security Group and EC2

## Objective

Learn how to create and use multiple Terraform modules to manage different infrastructure components. This lab will demonstrate how to create two modules: one for a Security Group and another for an EC2 instance. The EC2 instance will be configured to use the Security Group created by the first module.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances and Security Groups.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-11
    cd terraform-lab-11
    ```

### 2. Create the Security Group Module Directory

- Inside the `terraform-lab-11` directory, create a subdirectory for the Security Group module.

    ```bash
    mkdir security-group-module
    ```

### 3. Define the Security Group Module Configuration (`security-group-module/main.tf`)

- Create a file named `main.tf` inside the `security-group-module` directory. This file will define the resources for the Security Group.

    ```hcl
    resource "aws_security_group" "web_sg" {
      name_prefix = var.sg_name_prefix

      ingress {
        from_port   = 80
        to_port     = 80
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }

      ingress {
        from_port   = 22
        to_port     = 22
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }

      egress {
        from_port   = 0
        to_port     = 0
        protocol    = "-1"
        cidr_blocks = ["0.0.0.0/0"]
      }

      tags = {
        Name = var.sg_name_prefix
      }
    }
    ```

### 4. Define Security Group Module Variables (`security-group-module/variables.tf`)

- Create a file named `variables.tf` inside the `security-group-module` directory to define the input variables for the module.

    ```hcl
    variable "sg_name_prefix" {
      description = "Prefix for the Security Group name"
      type        = string
      default     = "web-sg"
    }
    ```

### 5. Define Security Group Module Outputs (`security-group-module/outputs.tf`)

- Create a file named `outputs.tf` inside the `security-group-module` directory to define the output values for the module.

    ```hcl
    output "sg_id" {
      description = "The ID of the Security Group"
      value       = aws_security_group.web_sg.id
    }
    ```

### 6. Create the EC2 Module Directory

- Inside the `terraform-lab-11` directory, create a subdirectory for the EC2 module.

    ```bash
    mkdir ec2-module
    ```

### 7. Define the EC2 Module Configuration (`ec2-module/main.tf`)

- Create a file named `main.tf` inside the `ec2-module` directory. This file will define the resources for the EC2 instance.

    ```hcl
    resource "aws_instance" "example" {
      ami           = var.ami_id
      instance_type = var.instance_type
      vpc_security_group_ids = [var.security_group_id]

      tags = {
        Name = var.instance_name
      }
    }
    ```

### 8. Define EC2 Module Variables (`ec2-module/variables.tf`)

- Create a file named `variables.tf` inside the `ec2-module` directory to define the input variables for the EC2 module.

    ```hcl
    variable "ami_id" {
      description = "The ID of the AMI to use for the EC2 instance"
      type        = string
    }

    variable "instance_type" {
      description = "The type of instance to create"
      type        = string
      default     = "t2.micro"
    }

    variable "security_group_id" {
      description = "The ID of the Security Group to associate with the instance"
      type        = string
    }

    variable "instance_name" {
      description = "The name of the EC2 instance"
      type        = string
    }
    ```

### 9. Define EC2 Module Outputs (`ec2-module/outputs.tf`)

- Create a file named `outputs.tf` inside the `ec2-module` directory to define the output values for the EC2 module.

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

### 10. Use the Modules in Your Terraform Configuration

- Now, create a root configuration that uses both the Security Group and EC2 modules. Create a new file named `main.tf` in the `terraform-lab-11` directory.

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    module "security_group" {
      source        = "./security-group-module"
      sg_name_prefix = "my-web-sg"
    }

    module "ec2_instance" {
      source           = "./ec2-module"
      ami_id           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type    = "t2.micro"
      security_group_id = module.security_group.sg_id
      instance_name    = "TerraformModuleEC2Instance"
    }

    output "ec2_instance_id" {
      description = "The ID of the EC2 instance from the EC2 module"
      value       = module.ec2_instance.instance_id
    }

    output "ec2_instance_public_ip" {
      description = "The public IP address of the EC2 instance from the EC2 module"
      value       = module.ec2_instance.instance_public_ip
    }
    ```

### 11. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 12. Plan the Configuration

- Run the plan command to see what Terraform will do.

    ```bash
    terraform plan
    ```

- The output will show the actions that Terraform will take, using the configuration defined in both modules.

### 13. Apply the Configuration

- Apply the configuration to create the Security Group and the EC2 instance.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the Security Group and the EC2 instance using the module configurations.

### 14. Verify the Deployment

- After applying the configuration, verify that the Security Group and EC2 instance have been created as specified.

- You can check the AWS Management Console to confirm that the Security Group and instance are present or use the AWS CLI commands:

    ```bash
    aws ec2 describe-security-groups --group-ids $(terraform output -raw sg_id)
    ```

    ```bash
    aws ec2 describe-instances --instance-ids $(terraform output -raw ec2_instance_id)
    ```

### 15. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the Security Group and EC2 instance created during this lab.

## Explanation

- **Modules**: Modules are used to encapsulate related Terraform configurations. By defining separate modules for Security Groups and EC2 instances, the configuration is modular, reusable, and easier to manage.

- **Input Variables**: Variables allow modules to be flexible and reusable with different configurations. They can be defined in the root module and passed to the child modules.

- **Outputs**: Outputs allow a module to return values to the calling module. They can be used to access resource attributes and pass information between modules.

- **Module Composition**: This lab demonstrates how to compose a larger infrastructure by using multiple modules. The EC2 module depends on the Security Group module, and the Security Group ID is passed as a variable to the EC2 module.

## Outcome

By the end of this lab, participants will:

- Understand how to create and use multiple Terraform modules.
- Learn how to define input variables and outputs in modules.
- Gain experience in using multiple modules to manage different infrastructure components in a modular and reusable way.

---

This lab provides hands-on experience with multiple Terraform modules, illustrating how to use modular code to manage different infrastructure components efficiently.
