# TecnoRedes S.A. — AWS Infrastructure as Code

## Overview

This project implements a cloud infrastructure Proof of Concept (PoC) for TecnoRedes S.A. using **Amazon Web Services (AWS)** and **Terraform**.

The objective is to transform a cloud architecture design into a functional, reproducible and testable infrastructure while applying principles of network segmentation, security, availability, monitoring and Infrastructure as Code (IaC).

The implementation was developed and tested in an **AWS Academy Learner Lab** envronment.

## Architecture

The infrastructure is organized across two Availability Zones and includes:

* Amazon VPC (`10.0.0.0/16`)
* 2 public subnets
* 2 private subnets
* Internet Gateway
* NAT Gateway
* Application Load Balancer
* 2 Amazon EC2 instances (`t3.micro`)
* Amazon RDS MySQL 8.0
* Amazon S3 with versioning and server-side encryption
* Amazon CloudWatch alarms
* Security Groups implementing layered access control
* Terraform Infrastructure as Code

### Traffic flow

```text
Internet
   │
   ▼
Application Load Balancer
   │
   ▼
EC2 Application Instances
   │
   ▼
RDS MySQL
```

Private EC2 instances do not have public IP addresses. Internet-facing traffic is terminated at the Application Load Balancer.

## Security

The architecture follows a least-privilege approach using security groups:

```text
Internet
   │ :80
   ▼
sg-lb
   │ :80
   ▼
sg-app
   │ :3306
   ▼
sg-db
```

Additional security measures include:

* No SSH access exposed to the Internet
* EC2 instances deployed without public IP addresses
* RDS deployed without public accessibility
* IMDSv2 required on EC2 instances
* S3 public access blocked
* Encryption enabled for persistent storage
* Database access restricted to the application security group

## Infrastructure as Code

The complete infrastructure is organized in the `IaC/` directory.

| File               | Purpose                         |
| ------------------ | ------------------------------- |
| `00_provider.tf`   | AWS and Random providers        |
| `01_variables.tf`  | Variables and configuration     |
| `02_network.tf`    | VPC and subnets                 |
| `03_routes.tf`     | Internet Gateway and routing    |
| `04_nat.tf`        | NAT Gateway and private routing |
| `05_security.tf`   | Security Groups                 |
| `06_ec2.tf`        | EC2 instances                   |
| `07_lb.tf`         | Application Load Balancer       |
| `08_bucket.tf`     | S3 configuration                |
| `09_database.tf`   | RDS MySQL                       |
| `10_cloudwatch.tf` | CloudWatch alarms               |
| `user_data.sh`     | EC2 application bootstrap       |

## Deployment

Initialize Terraform:

```bash
terraform init
```

Review the planned changes:

```bash
terraform plan
```

Deploy the infrastructure:

```bash
terraform apply
```

After testing, remove the infrastructure:

```bash
terraform destroy
```

The infrastructure was intentionally destroyed after testing to avoid unnecessary consumption of AWS Academy Lab resources.

## Testing

The implementation was validated through several tests:

1. Terraform deployment
2. Public application access through the ALB
3. Security Group segmentation
4. S3 versioning and storage validation
5. Load balancer failover after stopping an EC2 instance
6. CloudWatch alarm verification
7. Complete infrastructure destruction

The application served a test page displaying the EC2 Instance ID, allowing the distribution of requests between instances to be verified.

## AWS Academy Constraints

Some design decisions were adapted to the limitations of the AWS Academy Learner Lab:

* RDS was deployed as Single-AZ instead of Multi-AZ due to laboratory time and cost limitations.
* The existing `LabInstanceProfile` was reused because the environment does not allow creation of custom IAM roles.
* The existing S3 bucket was referenced through a Terraform data source because of an AWS Academy SCP restriction affecting Object Lock operations.
* Auto Scaling was not implemented; the PoC uses two fixed EC2 instances.
* CloudWatch alarms were configured and verified in `OK` state, but the `ALARM` state was not intentionally forced during the available laboratory session.

These limitations do not represent the recommended production architecture; they are documented adaptations to the laboratory environment.

## Project Status

**Status: Completed Proof of Concept**

The project successfully demonstrates the deployment of a segmented, monitored and load-balanced AWS infrastructure using Terraform within an AWS Academy environment.

## Technologies

* AWS
* Terraform
* Amazon VPC
* Amazon EC2
* Application Load Balancer
* Amazon RDS
* Amazon S3
* Amazon CloudWatch
* IAM
* Linux
* Infrastructure as Code

## Author

Héctor Palape

Connectivity and Network Engineering

Chile
