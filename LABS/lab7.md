# Lab 7: Using Outputs and Attributes in Terraform

## Objective

Learn how to define outputs in Terraform to extract and display specific attributes of created resources. This lab will demonstrate how to use outputs to access resource attributes and pass information between different Terraform configurations.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-7
    cd terraform-lab-7
    ```

### 2. Create the Variables Definition File (`variables.tf`)

- Create a file named `variables.tf` in the `terraform-lab-7` directory to define input variables.

    ```hcl
    variable "aws_region" {
      description = "The AWS region to use"
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

- Create a file named `main.tf` in the `terraform-lab-7` directory to define the provider and resource configurations.

    ```hcl
    provider "aws" {
      region = var.aws_region
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = var.instance_type
      count         = var.instance_count

      tags = {
        Name = "TerraformExampleInstance-${count.index + 1}"
      }
    }
    ```

### 4. Define Outputs (`outputs.tf`)

- Create a file named `outputs.tf` in the `terraform-lab-7` directory to define the outputs. Outputs allow you to extract values from your Terraform configuration.

    ```hcl
    # Output the public IP addresses of the instances
    output "instance_public_ips" {
      description = "Public IP addresses of the created EC2 instances"
      value       = aws_instance.example.*.public_ip
    }

    # Output the instance IDs of the created instances
    output "instance_ids" {
      description = "Instance IDs of the created EC2 instances"
      value       = aws_instance.example.*.id
    }

    # Output the private DNS names of the instances
    output "instance_private_dns" {
      description = "Private DNS names of the created EC2 instances"
      value       = aws_instance.example.*.private_dns
    }

    # Output the availability zones of the instances
    output "instance_availability_zones" {
      description = "Availability zones of the created EC2 instances"
      value       = aws_instance.example.*.availability_zone
    }
    ```

### 5. Initialize Terraform

- Initialize the Terraform project to download the necessary provider plugins and set up the environment.

    ```bash
    terraform init
    ```

### 6. Apply the Configuration

- Run the plan and apply commands to create the EC2 instances and output their attributes.

    ```bash
    terraform plan
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instances and display the output values defined in `outputs.tf`.

### 7. Verify the Outputs

- After applying the configuration, Terraform will display the public IP addresses, instance IDs, private DNS names, and availability zones of the created EC2 instances.

- Check the outputs in your terminal:

    ```plaintext
    instance_public_ips = [
      "54.123.45.67",
      "54.123.45.68"
    ]
    instance_ids = [
      "i-0abcd1234efgh5678",
      "i-0abcd1234efgh5679"
    ]
    instance_private_dns = [
      "ip-172-31-12-34.ec2.internal",
      "ip-172-31-12-35.ec2.internal"
    ]
    instance_availability_zones = [
      "us-west-2a",
      "us-west-2b"
    ]
    ```

### 8. Access Outputs in Another Terraform Configuration

- Create another configuration file (e.g., `main.tf` in a new directory) that uses the outputs from the first configuration. This will simulate using Terraform outputs to pass data between different Terraform configurations or modules.

    - In a new directory (`terraform-lab-7b`), create a `main.tf`:

    ```hcl
    module "ec2_instances" {
      source        = "../terraform-lab-7"
      aws_region    = "us-west-2"
      instance_type = "t2.micro"
      instance_count = 2
    }

    output "module_instance_public_ips" {
      value = module.ec2_instances.instance_public_ips
    }
    ```

- Initialize and apply this configuration:

    ```bash
    cd ../terraform-lab-7b
    terraform init
    terraform apply
    ```

### 9. Clean Up

- After experimenting with outputs and attributes, destroy all the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy all the EC2 instances created during this lab.

## Explanation

- **Outputs**: Outputs in Terraform allow you to extract values from your configuration. They are useful for displaying information, passing values between modules, or exporting data for use by other applications.

- **Attributes**: Attributes represent specific properties of a resource. In this lab, attributes such as `public_ip`, `id`, `private_dns`, and `availability_zone` are used to get information about EC2 instances.

- **Accessing Outputs**: Terraform allows you to access outputs defined in one configuration from another configuration. This feature is particularly useful for modular and reusable infrastructure code.

## Outcome

By the end of this lab, participants will:

- Understand how to define and use outputs in Terraform.
- Learn how to access and use attributes of Terraform resources.
- Gain experience in passing data between different Terraform configurations using outputs.

---

This lab provides hands-on experience with using outputs and attributes in Terraform, helping users manage and utilize information about their infrastructure effectively.
