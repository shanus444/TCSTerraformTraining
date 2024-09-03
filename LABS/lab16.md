# Lab 16: Using Terraform Import to Manage Existing Resources

## Objective

This lab will teach you how to use the `terraform import` command to bring an existing AWS resource under Terraform management. You'll learn to import an EC2 instance into your Terraform state and create the corresponding Terraform configuration.

## Pre-requisites

1. **AWS Account**: Access to an AWS account with permissions to create and manage EC2 instances.
2. **AWS CLI**: Installed and configured with appropriate credentials.
3. **Terraform Installed**: Terraform should be installed on your local machine. You can download it from the [official Terraform website](https://www.terraform.io/downloads.html).
4. **Existing EC2 Instance**: You should have an existing EC2 instance that was created manually or by another tool. If not, you can create one using the AWS Management Console.

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-16
    cd terraform-lab-16
    ```

### 2. Identify the Existing Resource

- Use the AWS CLI or AWS Management Console to find the ID of an existing EC2 instance. The instance ID typically looks like `i-0abcd1234ef567890`.

    ```bash
    aws ec2 describe-instances --query "Reservations[*].Instances[*].InstanceId" --output text
    ```

- Note down the instance ID you want to import, e.g., `i-0abcd1234ef567890`.

### 3. Create the Terraform Configuration

- Create a file named `main.tf` inside the `terraform-lab-15` directory with the following content. This file defines the provider and a placeholder for the imported EC2 instance.

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    resource "aws_instance" "imported_instance" {
      # The fields here will be filled in after import
    }
    ```

### 4. Initialize Terraform

- Initialize the Terraform project to set up the backend and download necessary provider plugins.

    ```bash
    terraform init
    ```

### 5. Import the Existing Resource

- Use the `terraform import` command to import the existing EC2 instance into Terraform state.

    ```bash
    terraform import aws_instance.imported_instance i-0abcd1234ef567890
    ```

- Replace `i-0abcd1234ef567890` with the actual instance ID you noted earlier. This command tells Terraform to map the resource in the state file to the existing resource in your AWS account.

### 6. Generate the Terraform Configuration

- Review the state file to see the configuration added for the imported resource.

- Based on the state file, manually update `main.tf` with the EC2 instance's attributes:

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    resource "aws_instance" "imported_instance" {
      ami           = "ami-0c55b159cbfafe1f0"  # Replace with actual AMI ID
      instance_type = "t2.micro"              # Replace with actual instance type

      # Add other attributes as per the current configuration of the instance
    }
    ```

### 7. Validate the Configuration

- Run the `terraform plan` command again to verify that the configuration matches the state.

    ```bash
    terraform plan
    ```

- If everything is set up correctly, Terraform should report no changes, indicating that the configuration matches the imported resource.

### 8. Modify and Apply Changes

- Now that the instance is managed by Terraform, you can modify the configuration. For example, add a tag to the instance:

    ```hcl
    resource "aws_instance" "imported_instance" {
      ami           = "ami-0c55b159cbfafe1f0"
      instance_type = "t2.micro"

      tags = {
        Name = "ManagedByTerraform"
      }
    }
    ```

- Apply the changes:

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will apply the configuration and update the EC2 instance accordingly.

### 9. Clean Up

- If you no longer need the EC2 instance, you can destroy it using Terraform:

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance.

## Explanation

- **Terraform Import**: The `terraform import` command allows you to bring existing infrastructure under Terraform management without destroying or recreating the resource.
  
- **Configuration Matching**: After importing a resource, it's important to manually create a configuration that matches the imported state. This ensures Terraform can manage the resource without unexpected changes.

- **Use Cases**:
  - Managing manually created resources.
  - Migrating infrastructure from other management tools to Terraform.
  - Consolidating existing infrastructure under Terraform management.

## Outcome

By the end of this lab, participants will:

- Understand how to use `terraform import` to manage existing resources with Terraform.
- Learn how to generate and match the Terraform configuration for imported resources.
- Gain experience in transitioning from manually managed infrastructure to infrastructure as code with Terraform.

---

This lab provides hands-on experience with the Terraform import feature, demonstrating how to bring existing resources under Terraform's control and manage them using configuration files.
