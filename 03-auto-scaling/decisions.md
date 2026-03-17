# Auto Scaling Group Lab

## Architecture
Launch Template → Target Group → ALB → ALB Listener → ASG → Scaling Policy

## decisions

### 1. Launch Template
- AMI: SSM resolve string (always latest Amazon Linux 2023)
- Instance type: t2.micro (free tier)
- Security Group imported from my-vpc-stack via cross-stack reference
- Why SSM resolve: avoids hardcoding AMI IDs that change per region/update
  
### 2. ALB Security Group
- Must be explicitly defined — AWS auto-generated SG does not allow 
  inbound HTTP from internet
- Separate SG for ALB vs EC2 instances is best practice

### 3. Target Tracking Scaling Policy
- Tracks ASGAverageCPUUtilization at 50% target
- ASG scaled down to 1 instance under low load — policy working correctly

### 4. Instance Refresh
- Used after Launch Template update to replace instances without downtime
- Replaces instances one at a time maintaining availability

### 5. Lessons Learned
- ALB requires explicit security group with port 80 inbound
- Target tracking automatically scales down under low CPU — expected behavior