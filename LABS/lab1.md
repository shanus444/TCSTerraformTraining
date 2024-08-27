# Lab 1: Getting Started with Terraform - Deploying a Simple EC2 Instance

## Objective

Learn the basics of using Terraform to deploy a simple EC2 instance on AWS. This lab will introduce participants to Terraform's syntax, configuration files, and basic commands.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-1
    cd terraform-lab-1
    ```

### 2. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-1` directory and add the following content. This configuration will define an EC2 instance using Terraform.

    ```hcl
    provider "aws" {
    region     = "us-east-1"
    access_key = ""
    secret_key = ""
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI, change it as per your region
      instance_type = "t2.micro"  # Instance type

      tags = {
        Name = "TerraformExampleInstance"
      }
    }
    ```

### 3. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 4. Plan the Terraform Deployment

- Run the plan command to see what changes Terraform will make to your infrastructure.

    ```bash
    terraform plan
    ```

- Terraform will show a plan with the changes it will make, which should include creating one EC2 instance.

### 5. Apply the Configuration

- Apply the configuration to create the EC2 instance.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will then create the EC2 instance using the configuration provided.

### 6. Verify the Deployment

- After the apply completes, you can verify the EC2 instance creation in the AWS Management Console under the EC2 dashboard.

### 7. Explore Terraform State

- Check the Terraform state file (`terraform.tfstate`) to see how Terraform tracks the resources it manages. This file is critical for Terraform to understand the current state of your infrastructure.

    ```bash
    cat terraform.tfstate
    ```

### 8. Modify the Instance

- To understand how Terraform manages changes, modify the `main.tf` file to use a different instance type.

    ```hcl
    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI, change it as per your region
      instance_type = "t3.micro"  # Change from t2.micro to t3.micro

      tags = {
        Name = "TerraformExampleInstance"
      }
    }
    ```

- Run `terraform plan` again to see the proposed changes, then apply the changes.

    ```bash
    terraform plan
    terraform apply
    ```

- Terraform will update the instance type of the EC2 instance.

### 9. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance.

## Outcome

By the end of this lab, participants will:

- Understand how to create and configure a basic Terraform script.
- Learn how to initialize, plan, apply, and destroy Terraform-managed infrastructure.
- Gain insight into how Terraform manages infrastructure state.

---

This lab provides a basic introduction to Terraform and how it can be used to manage AWS resources. It serves as a foundation for more complex Terraform configurations in subsequent labs.
