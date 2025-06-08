## Private EC2 Access via SSM – No NAT Gateway Needed
Tired of paying ~£30/month for a NAT Gateway just to connect to an EC2 instance in a private subnet?

This project provides a secure, cost-effective alternative using AWS Session Manager and VPC interface endpoints—fully automated with CloudFormation.

## What This Project Demonstrates
How to launch a private EC2 instance with no public IP, no NAT Gateway, and still access it securely using AWS Session Manager.

How to use interface VPC endpoints (SSM, EC2Messages, SSMMessages) with private DNS enabled to make this work.

Fully working CloudFormation template + parameters file, ready to deploy.

## Why This Isn’t Trivial
Many engineers run into trouble with this setup, often due to:

❌ Missing private DNS resolution on the VPC endpoints.

❌ Defaulting to us-east-1 in AWS CLI or config when working in another region.

❌ SSM silently failing when these issues aren't addressed.

## This repo exists because I personally spent hours debugging these issues. Now you don't have to.

Cost Comparison: NAT vs VPC Endpoints

NAT Gateway	$0.045(HOUR)	~$32.40(MONTH)	~$388.80(YEAR)
VPC Endpoints (x3)	$0.01 each(HOUR)	~$21.60(MONTH)	~$259.20(YEAR)
Savings	$0.72/day	~$10.80	~$129.60

That's a meaningful saving for small businesses or personal labs.

## Key Benefits
Zero internet exposure (no public IPs, no bastion host)

Cheaper than NAT (see above)

Fully testable via AWS CLI or console

CloudFormation for repeatable deployment

## Common Pitfall: Missing Private DNS
SSM connectivity via VPC endpoints requires private DNS resolution. If you forget to enable it:

aws ssm start-session silently fails

The instance won't show as connected in describe-instance-information

No obvious error is returned

## How I Solved It
Used PrivateDnsEnabled: true in the CloudFormation template.

Enabled DNS support and DNS hostnames in the VPC.

Confirmed interface endpoints were associated with the correct route tables and subnet.

## Files in This Repo
SSM-with-private-endpoint-access.yaml – main CloudFormation template

parameters.json – parameters file for stack creation

README.md – this document

## How to Test
After deployment, connect with:


'aws ssm start-session --target i-xxxxxxxxxxxxxxxxx --region us-east-1'

Within the session, try commands like:

'ip addr show'
'curl http://169.254.169.254/latest/meta-data/'
'systemctl status amazon-ssm-agent'

## Future Enhancements
This is v1 – deliberately minimal.

Planned for v2:

S3 access via gateway endpoint

CloudTrail logs parsed by the EC2

SNS alerts on suspicious activity

