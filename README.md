# AWS Graviton4 Benchmark Setup for Amazon RDS

This AWS CloudFormation template allows you to replicate the benchmark setup to compare Graviton4, Graviton3, and Graviton2 processors on Amazon RDS. The template provisions the necessary infrastructure for benchmarking, as described in the [blog post](https://issues.amazon.com/issues/DBBLOG-4568).

⚠ **Note:** This template is intended for benchmarking purposes only and is not suitable as a production workload baseline.

## Costs

Using this setup incurs charges for the resources provisioned. Based on our configuration, the estimated cost is approximately $2.5/hour. To minimize expenses, ensure you delete the stack when testing concludes.

## Prerequisites

Before deploying this setup, ensure you have the following:

- An active AWS account.
- Sufficient permissions to create and manage AWS CloudFormation stacks.

## How the Template Works

The AWS CloudFormation template provisions the following:

- **Networking Setup**:
  - A custom Amazon VPC.
  - Private and public subnets, where:
    - The private subnet hosts test RDS instances.
    - The public subnet contains a NAT Gateway, facilitating internet access for the private subnet.
- **Compute Resources**:
  - An Amazon EC2 instance (m7g.4xlarge) to run the benchmarks.
- **Database Resources**:
  - A single-AZ Amazon RDS instance with the chosen Graviton-based instance type and database engine.
  - A test database named sbtest.
- **Connectivity**:
  - Three Amazon VPC endpoints for seamless AWS Systems Manager integration.

The template outputs key parameters:

- RDS instance endpoint URL.
- RDS instance port (default values: 5432 for PostgreSQL, 3306 for MySQL/MariaDB).
- Admin username.
- A direct Systems Manager session URL for accessing the EC2 instance.

## Installation Instructions

1. Download the CloudFormation template file (rds-graviton-benchmark.json) or upload it to an S3 bucket.
2. Log in to your AWS account and navigate to a region supporting Graviton-based RDS instances (e.g., **US East [N. Virginia, Ohio]**, **US West [Oregon]**, **Europe [Frankfurt]**).
3. Open the AWS CloudFormation console and choose **Create Stack > With new resources (standard)**. Alternatively, use the direct link: [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home#/stacks/create).
4. Select **Template is ready**, upload your JSON file, and click **Next**.
5. Enter a stack name, such as rds-graviton-benchmark.
6. Provide the required parameters:
    - **RDS Instance Type**: Choose either m8g (Graviton4), m7g (Graviton3), or m6g (Graviton2).
    - **Database Engine**: Select one of the supported options: PostgreSQL (16.3), MySQL (8.0.39), or MariaDB (10.11.9).
    - **SSM Parameter**: It automatically resolves to the latest Amazon Linux AMI (do not modify this field).
7. Acknowledge that IAM resources may be created, then click **Submit**.
8. Wait approximately 15 minutes for the stack to deploy.
9. Once the setup is complete, the environment is ready to execute the benchmark as described in the blog post.

## Cleanup

When you finish testing, save the benchmark results and delete the created resources by removing the CloudFormation stack. Follow these steps:

1. Navigate to the **CloudFormation** service in the AWS Management Console.
2. Select the stack you created (use the name provided during setup).
3. Click **Delete** to remove the stack and all associated resources.

This action will automatically delete all resources provisioned by the stack, including the EC2 instance, RDS instance, VPC, subnets, and NAT Gateway.
