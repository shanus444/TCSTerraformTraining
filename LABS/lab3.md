# Lab 3: Using Terraform Target Option for Selective Resource Management

## Objective

Learn how to use the `target` option in Terraform to selectively destroy and recreate specific resources within your configuration. This will help you understand how to manage resources efficiently without affecting the entire infrastructure.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-3
    cd terraform-lab-3
    ```

### 2. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-2` directory and add the following content. This configuration will define multiple EC2 instances.

    ```hcl
    provider "aws" {
      region = "us-east-1"  # You can change this to your preferred region
    }

    # First EC2 instance
    resource "aws_instance" "instance1" {
      ami           = "ami-066784287e358dad1"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"

      tags = {
        Name = "Instance1-Rashi"
      }
    }

    # Second EC2 instance
    resource "aws_instance" "instance2" {
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "Instance2-Rashi"
      }
    }

    # Third EC2 instance
    resource "aws_instance" "instance3" {
      ami           = "ami-066784287e358dad1"
      instance_type = "t2.micro"

      tags = {
        Name = "Instance3-Rashi"
      }
    }
    ```

### 3. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 4. Apply the Configuration

- Apply the configuration to create all three EC2 instances.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create three EC2 instances as specified in the configuration.

### 5. Use the `target` Option to Destroy a Specific Resource

- Suppose you want to destroy only the second EC2 instance (`instance2`). You can use the `target` option with the `destroy` command to specify which resource to destroy.

    ```bash
    terraform destroy -target=aws_instance.instance2
    ```

- Confirm by typing `yes`. Terraform will destroy only the `instance2` resource, leaving `instance1` and `instance3` untouched.

### 6. Verify the Resource Destruction

- After running the command, you can verify in the AWS Management Console that only `Instance2` has been destroyed.

### 7. Use the `target` Option to Recreate a Specific Resource

- Now, let's recreate the `instance2` using the `target` option with the `apply` command.

    ```bash
    terraform apply -target=aws_instance.instance2
    ```

- Confirm by typing `yes`. Terraform will create only `instance2`, leaving the other instances unchanged.

### 8. Verify the Resource Creation

- Check the AWS Management Console to verify that `Instance2` has been recreated.

### 9. Use the `target` Option with Plan

- You can also use the `target` option with the `plan` command to see what changes Terraform will make to a specific resource.

    ```bash
    terraform plan -target=aws_instance.instance3
    ```

- This command will show the plan for changes related only to `instance3`, without considering the other resources.

### 10. Clean Up

- After experimenting, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy all the EC2 instances created during this lab.

## Explanation

- **Target Option**: The `target` option in Terraform is used to limit the scope of `plan`, `apply`, or `destroy` commands to specific resources. This is useful when you want to make changes to, or destroy, a particular resource without affecting others.

- **Selective Management**: Using the `target` option allows you to manage resources selectively. This can be particularly helpful in large infrastructure projects where changing or destroying all resources might not be feasible.

- **Plan Command with Target**: Using the `target` option with the `plan` command helps you understand the impact of changes on specific resources before applying them.

## Outcome

By the end of this lab, participants will:

- Understand how to use the `target` option in Terraform.
- Learn how to selectively destroy and recreate resources without affecting the entire infrastructure.
- Gain experience in managing specific parts of their infrastructure using Terraform commands.

---

This lab provides hands-on experience with the `target` option, allowing users to manage Terraform resources efficiently and selectively.
