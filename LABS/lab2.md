# Lab 2: Understanding Local Resource Names and the Importance of Uniqueness

## Objective

Learn how to define local resource names in Terraform, understand why they must be unique within a configuration, and see what happens when duplicate resource names are used.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-2
    cd terraform-lab-2
    ```

### 2. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-2` directory and add the following content. This configuration will define multiple EC2 instances with both unique and duplicate resource names to illustrate the importance of uniqueness.

    ```hcl
    provider "aws" {
      region = "us-east-1"  # You can change this to your preferred region
    }

    # First EC2 instance with a unique name
    resource "aws_instance" "example1" {
      ami           = "ami-066784287e358dad1"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"

      tags = {
        Name = "UniqueInstance1-Rashi"
      }
    }

    # Second EC2 instance with a unique name
    resource "aws_instance" "example2" {
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "UniqueInstance2-Rashi"
      }
    }

    # Third EC2 instance with a duplicate name (intentionally wrong)
    resource "aws_instance" "example1" {  # Same name as the first instance
      ami           = "ami-0c55b159cbfafe1f0"
      instance_type = "t2.micro"

      tags = {
        Name = "DuplicateInstance-Rashi"
      }
    }
    ```

### 3. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 4. Plan the Terraform Deployment

- Run the plan command to see how Terraform handles the configuration.

    ```bash
    terraform plan
    ```

- Terraform will throw an error indicating that the resource name `aws_instance.example1` is defined more than once. This error illustrates the importance of unique local resource names within a Terraform configuration.

### 5. Correct the Configuration

- To fix the error, you need to use a unique local name for each resource. Modify the `main.tf` file to ensure all resource names are unique.

    ```hcl
    provider "aws" {
      region = "us-east-1"  # You can change this to your preferred region
    }

    # First EC2 instance with a unique name
    resource "aws_instance" "example1" {
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "UniqueInstance1-Rashi"
      }
    }

    # Second EC2 instance with a unique name
    resource "aws_instance" "example2" {
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "UniqueInstance2-Rashi"
      }
    }

    # Third EC2 instance with a corrected unique name
    resource "aws_instance" "example3" {  # Changed to a unique name
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "UniqueInstance3-Rashi"
      }
    }
    ```

### 6. Re-run Plan and Apply

- After correcting the names, re-run the `plan` command to see the changes.

    ```bash
    terraform plan
    ```

- Now, Terraform should show a valid plan with three EC2 instances being created.

- Apply the changes to create the instances.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create three EC2 instances, each with a unique local resource name.

### 7. View Outputs (Optional)

- You can add an `outputs.tf` file to show the IDs and public IPs of the created instances.

    ```hcl
    output "instance_ids" {
      description = "The IDs of the EC2 instances"
      value       = [aws_instance.example1.id, aws_instance.example2.id, aws_instance.example3.id]
    }

    output "instance_public_ips" {
      description = "The public IP addresses of the EC2 instances"
      value       = [aws_instance.example1.public_ip, aws_instance.example2.public_ip, aws_instance.example3.public_ip]
    }
    ```

- View the outputs using the following command:

    ```bash
    terraform output
    ```

### 8. Clean Up

- After experimenting, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instances.

## Explanation

- **Local Resource Names**: In Terraform, each resource must have a unique name within the scope of its configuration. The name (e.g., `example1`, `example2`) helps Terraform uniquely identify and manage the resource.

- **Importance of Uniqueness**:
  - **State Management**: Terraform maintains a state file that keeps track of resources it manages. If two resources have the same name, Terraform cannot distinguish between them, leading to errors and potentially unintended modifications.
  - **Resource Management**: Unique names help Terraform apply changes correctly and avoid conflicts. When updating or destroying resources, Terraform relies on these unique identifiers to know which specific resource to act upon.

- **Error Handling**: This lab shows what happens when duplicate resource names are used and how Terraform provides error messages to help users identify and fix issues.

## Outcome

By the end of this lab, participants will:

- Understand the concept of local resource names in Terraform.
- Learn why resource names must be unique within a Terraform configuration.
- Gain experience in debugging and fixing errors related to resource naming conflicts.

---

This lab will help participants grasp the importance of using unique names for resources and how Terraform handles these scenarios.
