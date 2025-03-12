# AWS DevOps Engineer Homework

## Overview

Design and deploy a simple web application on AWS. This task aims to evaluate your skills in infrastructure setup, documentation, and automation.

## Objective

- Deploy a simple "Hello World" web application on AWS.
- Utilize AWS services to build a secure and scalable architecture.
- Document the deployment process clearly.
- Using Terraform for automation is a plus. If not, provide detailed step-by-step guidelines.

## Architecture Requirements

- **EC2**: Deploy an EC2 instance to host the web application.
- **S3**: Create an S3 bucket for storing static files or application backups.
- **IAM**: Configure necessary IAM roles and policies for secure access.
- **Security Groups**: Set up security groups to allow HTTP/HTTPS traffic to EC2.

## Deliverables

### Infrastructure Deployment

- **Terraform (Preferred)**: Write Terraform scripts to automate infrastructure setup.
- **Manual Guidelines (If not using Terraform)**: Provide clear, step-by-step instructions to manually create the architecture.

### Web Application

- Deploy a simple application that displays "Hello World".
- Ensure the application is accessible via public IP.

### Documentation

- Documentation (README)
- Deployment Instructions:
  - Step-by-step guide for deploying the infrastructure.
  - Instructions to deploy the application and verify it is running.
  - Include Terraform commands if applicable.

### Testing Instructions

- Provide simple curl or browser tests to confirm the application is working.

### Clean-Up Process

- How to destroy or remove all AWS resources to avoid unnecessary costs.

### Sample Architecture Diagram

```sh
[ User ]
    |
 [Internet]
    |
 [EC2 Instance (Web App)]
    |
   [S3 Bucket for Backups]
```

## Evaluation Criteria

- **Infrastructure Quality**: How well the AWS architecture is designed and implemented.
- **Documentation**: Clarity, detail, and completeness of the README and instructions.
- **Automation**: Usage of Terraform or similar tools.
- **Best Practices**: Security, scalability, and cost considerations.
- **Testing and Validation**: How the solution is verified and tested.

## Deadline

The final submission is due by 7 days (you can submit it earlier, the earlier the submission, the higher the score you will get as a bonus if everything is still good enough).

## Contact

For any questions or clarifications, please contact tech@gearment.com.

## Additional Notes

- The simpler and cleaner the solution, the better.
- Focus on using AWS best practices.
- Do not forget to clean up AWS resources after completion to avoid unnecessary charges.

We look forward to seeing your solution!
