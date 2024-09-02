# Lab 13: Using Terraform Workspaces to Manage Multiple Environments

## Objective

Learn how to create and use Terraform workspaces to manage different environments using the same Terraform configuration. This lab will demonstrate how to create workspaces for `development` and `production` environments and how to apply different configurations using these workspaces.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-13
    cd terraform-lab-13
    ```

### 2. Create the Terraform Configuration

- Create a file named `main.tf` inside the `terraform-lab-12` directory. This file will define the configuration for creating an EC2 instance.

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"
      tags = {
        Name = "TerraformInstance-${terraform.workspace}"
      }
    }

    output "instance_id" {
      description = "The ID of the created EC2 instance"
      value       = aws_instance.example.id
    }

    output "instance_public_ip" {
      description = "The public IP address of the created EC2 instance"
      value       = aws_instance.example.public_ip
    }
    ```

### 3. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 4. Create and Switch to a New Workspace

- By default, you are in the `default` workspace. Create a new workspace named `development`.

    ```bash
    terraform workspace new development
    ```

- Switch to the `development` workspace.

    ```bash
    terraform workspace select development
    ```

### 5. Apply the Configuration in the Development Workspace

- Apply the configuration to create the EC2 instance in the `development` workspace.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instance with the tag `Name = TerraformInstance-development`.

### 6. Create and Switch to a Production Workspace

- Create a new workspace named `production`.

    ```bash
    terraform workspace new production
    ```

- Switch to the `production` workspace.

    ```bash
    terraform workspace select production
    ```

### 7. Apply the Configuration in the Production Workspace

- Apply the configuration to create the EC2 instance in the `production` workspace.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instance with the tag `Name = TerraformInstance-production`.

### 8. Verify the Deployment

- After applying the configuration in both workspaces, verify that the EC2 instances have been created in both the `development` and `production` environments.

- You can check the AWS Management Console to confirm that the instances are running with different tags or use the AWS CLI commands:

    ```bash
    aws ec2 describe-instances --filters "Name=tag:Name,Values=TerraformInstance-development"
    ```

    ```bash
    aws ec2 describe-instances --filters "Name=tag:Name,Values=TerraformInstance-production"
    ```

### 9. List Workspaces

- List the available workspaces to see all the environments.

    ```bash
    terraform workspace list
    ```

### 10. Switch Between Workspaces

- Switch back to the `development` workspace.

    ```bash
    terraform workspace select development
    ```

- You can now make changes specific to the `development` environment without affecting the `production` environment.

### 11. Clean Up

- After completing the lab, destroy the resources in each workspace to avoid unnecessary charges.

- First, switch to the `development` workspace and destroy the resources:

    ```bash
    terraform workspace select development
    terraform destroy
    ```

- Next, switch to the `production` workspace and destroy the resources:

    ```bash
    terraform workspace select production
    terraform destroy
    ```

- Confirm by typing `yes` for each destroy command. Terraform will destroy the EC2 instances created during this lab.

## Explanation

- **Workspaces**: Workspaces allow you to manage multiple environments (e.g., development, staging, production) using the same Terraform configuration. Each workspace maintains a separate state file.

- **Using `terraform.workspace`**: The `${terraform.workspace}` interpolation in the resource tag dynamically inserts the name of the current workspace into the resource configuration. This makes it easy to identify resources based on the environment.

- **Switching Workspaces**: Use `terraform workspace select <workspace_name>` to switch between different environments.

- **Listing Workspaces**: The `terraform workspace list` command displays all available workspaces in the current directory.

## Outcome

By the end of this lab, participants will:

- Understand how to create and use Terraform workspaces.
- Learn how to switch between workspaces to manage different environments.
- Gain experience in managing infrastructure for multiple environments using the same configuration files.

---

This lab provides hands-on experience with Terraform workspaces, illustrating how to use them to manage multiple environments effectively. Feel free to modify or expand this lab as needed.
