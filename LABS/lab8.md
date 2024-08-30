# Lab 8: Terraform Debugging, Formatting, and Validation

## Objective

Learn how to debug Terraform configurations, use the `terraform fmt` command to format code, and use the `terraform validate` command to check the correctness of Terraform configurations. This lab will demonstrate how to apply these tools to maintain clean and functional infrastructure code.

## Pre-requisites

1. **AWS Account**: Ensure you have access to an AWS account with the necessary permissions to create and manage EC2 instances.
2. **AWS CLI**: Install and configure the AWS CLI on your local machine.
3. **Terraform Installed**: Ensure Terraform is installed on your local machine. You can download Terraform from the [official Terraform website](https://www.terraform.io/downloads.html).

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-8
    cd terraform-lab-8
    ```

### 2. Create the Main Configuration File (`main.tf`)

- Create a file named `main.tf` in the `terraform-lab-8` directory with a simple configuration to deploy an EC2 instance.

    ```hcl
    provider "aws" {
      region = "us-east-1"
    }

    resource "aws_instance" "example" {
      ami           = "ami-066784287e358dad1"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"

      tags = {
        Name = "TerraformDebugInstance"
      }
    }
    ```

### 3. Introducing an Error for Debugging

- Introduce an intentional syntax error or misconfiguration in `main.tf` to understand debugging. Modify the `instance_type` to an invalid type.

    ```hcl
    instance_type = "t2.invalid_type"
    ```

### 4. Debugging with `TF_LOG`

- Terraform provides the `TF_LOG` environment variable to set the logging level for debugging. Set this environment variable to `DEBUG` to see detailed logs.

    ```bash
    export TF_LOG=DEBUG
    ```

- Run `terraform plan` to see detailed debug information.

    ```bash
    terraform plan
    ```

- Look for lines in the output indicating errors or misconfigurations. The logs will show where Terraform is encountering issues.

### 5. Correct the Error

- After identifying the issue using the debug logs, correct the `instance_type` back to a valid value.

    ```hcl
    instance_type = "t2.micro"
    ```

- Run `terraform plan` again to ensure the configuration is now correct.

    ```bash
    terraform plan
    ```

### 6. Using `terraform fmt`

- `terraform fmt` is used to format the configuration files to follow the canonical standard style. This helps in maintaining readability and consistency.

- Introduce some formatting issues in `main.tf`. For example, remove indentations or add unnecessary spaces.

    ```hcl
    provider "aws" {region = "us-east-1"}
    resource "aws_instance" "example" {ami = "ami-066784287e358dad1"
    instance_type="t2.micro"
    tags={Name="TerraformDebugInstance"}}
    ```

- Run `terraform fmt` to automatically format the file.

    ```bash
    terraform fmt
    ```

- Check the `main.tf` file to see that it has been reformatted according to Terraform's standard style.

### 7. Validating Configuration with `terraform validate`

- `terraform validate` is used to validate the configuration files for syntax errors and other issues. This does not check for issues like missing resource dependencies or runtime errors, but it ensures that the configuration is syntactically correct and internally consistent.

- Run `terraform validate` to check the configuration.

    ```bash
    terraform validate
    ```

- You should see output indicating that the configuration is valid.

    ```plaintext
    Success! The configuration is valid.
    ```

### 8. Apply the Configuration

- Now that the configuration is debugged, formatted, and validated, apply it to create the EC2 instance.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instance as per the configuration.

### 9. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance created during this lab.

### 10. Disable Debug Logging

- After debugging, disable the `TF_LOG` environment variable to avoid unnecessary log output in subsequent Terraform commands.

    ```bash
    unset TF_LOG
    ```

## Explanation

- **Debugging with `TF_LOG`**: Terraform's `TF_LOG` environment variable can be set to various levels (`TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`) to control the verbosity of log output. This is useful for troubleshooting issues in configurations.

- **Formatting with `terraform fmt`**: Ensures that Terraform configuration files are formatted consistently according to Terraform's style guidelines. This improves readability and reduces errors due to formatting inconsistencies.

- **Validating with `terraform validate`**: Checks for syntax errors and logical consistency within a Terraform configuration. It ensures that all required arguments are present and that the configuration makes sense from a Terraform perspective.

## Outcome

By the end of this lab, participants will:

- Understand how to use Terraform's debugging tools to troubleshoot configuration issues.
- Learn how to use `terraform fmt` to format configuration files according to standard practices.
- Gain experience in validating Terraform configurations using `terraform validate` to ensure they are syntactically correct and consistent.

---

This lab provides hands-on experience with Terraform debugging, formatting, and validation, helping users develop best practices for managing infrastructure code. 
