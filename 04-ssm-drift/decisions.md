# SSM Session Manager + Drift Detection

## Decisions

### 1. SSM Session Manager Setup
- IAM Role: AmazonSSMManagedInstanceCore policy
- InstanceProfile attaches role to EC2
- No port 22 required — remove from security group in production
- ssm-user is the default session user (not ec2-user)
- IMDSv2 requires token for metadata endpoint queries
- Why SSM over SSH: centralized IAM access control, full audit logs, 
  no exposed private keys

### 2. CloudFormation Drift Detection
- Drift: gap between what CFN expects and what actually exists in AWS
- Caused by: manual changes to CFN-managed resources outside the template
- Detection command: aws cloudformation detect-stack-drift
- Detail command: aws cloudformation describe-stack-resource-drifts
- Remediation options:
  - Revert: remove the manual change (used here)
  - Accept: add the change to the template and update the stack
- DriftStatus values: NOT_CHECKED, DRIFTED, IN_SYNC