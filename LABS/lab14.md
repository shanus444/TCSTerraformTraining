# Lab 14: Using Local Exec and Remote Exec Provisioners in Terraform

## Objective

This lab will teach you how to use Terraform's `local-exec` and `remote-exec` provisioners. You'll learn to execute local scripts and run remote commands on newly created EC2 instances. By the end of this lab, you'll be able to apply these provisioners to real-world scenarios, such as setting up software on instances or triggering scripts during provisioning.

## Pre-requisites

1. **AWS Account**: Access to an AWS account with permissions to create and manage EC2 instances.
2. **AWS CLI**: Installed and configured with appropriate credentials.
3. **Terraform Installed**: Terraform should be installed on your local machine. You can download it from the [official Terraform website](https://www.terraform.io/downloads.html).
4. **Key Pair**: A key pair should be available in the AWS region you plan to use for SSH access to EC2 instances. You can create one using the AWS Management Console.

## Steps

### 1. Set Up Your Workspace

- Create a new directory for this lab.

    ```bash
    mkdir terraform-lab-14
    cd terraform-lab-14
    ```

### 2. Create the Terraform Configuration

- Create a file named `main.tf` in the `terraform-lab-14` directory with the following content. Replace `your-key-pair-name` with the name of your existing AWS key pair.

    ```hcl
    provider "aws" {
      region = "us-west-2"
    }

    resource "aws_instance" "example" {
      ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2 AMI
      instance_type = "t2.micro"
      key_name      = "your-key-pair-name"

      provisioner "local-exec" {
        command = "echo Instance ID: ${self.id} > instance_id.txt"
      }

      provisioner "remote-exec" {
        inline = [
          "sudo yum update -y",
          "sudo yum install -y httpd",
          "sudo systemctl start httpd",
          "sudo systemctl enable httpd"
        ]

        connection {
          type        = "ssh"
          user        = "ec2-user"
          private_key = file("path/to/your/private-key.pem")
          host        = self.public_ip
        }
      }

      tags = {
        Name = "TerraformProvisionerExample"
      }
    }

    output "instance_public_ip" {
      description = "The public IP address of the created EC2 instance"
      value       = aws_instance.example.public_ip
    }
    ```

- Replace `your-key-pair-name` with the name of your existing AWS key pair and `path/to/your/private-key.pem` with the path to your private key file.

### 3. Initialize Terraform

- Initialize the Terraform project to download necessary provider plugins.

    ```bash
    terraform init
    ```

### 4. Plan the Configuration

- Run the plan command to see what Terraform will do.

    ```bash
    terraform plan
    ```

- The output will show the actions that Terraform will take, including creating an EC2 instance and using the provisioners.

### 5. Apply the Configuration

- Apply the configuration to create the EC2 instance and run the provisioners.

    ```bash
    terraform apply
    ```

- Confirm by typing `yes`. Terraform will create the EC2 instance and execute the local and remote commands defined in the provisioners.

### 6. Verify the Provisioners

- **Local Exec Provisioner**: This provisioner runs a local command to save the instance ID to a file named `instance_id.txt` in the current directory. Check the contents of `instance_id.txt` to verify that it contains the instance ID.

    ```bash
    cat instance_id.txt
    ```

- **Remote Exec Provisioner**: This provisioner connects to the EC2 instance via SSH and runs a series of commands to install and start the Apache HTTP server. Use the public IP output to access the Apache HTTP server in a web browser.

    ```bash
    echo "Public IP: $(terraform output instance_public_ip)"
    ```

- Open a web browser and enter the public IP address. You should see the default Apache HTTP server page.

### 7. Clean Up

- After completing the lab, destroy the resources to avoid unnecessary charges.

    ```bash
    terraform destroy
    ```

- Confirm by typing `yes`. Terraform will destroy the EC2 instance created during this lab.

## Explanation

- **Local Exec Provisioner**: The `local-exec` provisioner runs commands on the machine where Terraform is executed. In this lab, it was used to save the instance ID to a local file.
  
- **Remote Exec Provisioner**: The `remote-exec` provisioner runs commands on the resource being created. In this lab, it was used to install and start the Apache HTTP server on the EC2 instance.

- **Use Cases**:
  - **Local Exec**: Can be used to trigger local scripts, send notifications, or log information during the deployment process.
  - **Remote Exec**: Useful for bootstrapping servers, installing packages, and configuring applications after an instance is created.

## Outcome

By the end of this lab, participants will:

- Understand the difference between `local-exec` and `remote-exec` provisioners.
- Learn how to run local and remote commands using Terraform.
- Gain experience in provisioning resources with additional configuration steps.

---

This lab provides hands-on experience with Terraform provisioners, demonstrating how to execute local and remote commands as part of infrastructure provisioning.
