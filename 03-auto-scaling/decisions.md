# Auto Scaling Group Lab

## Architecture
Launch Template → Target Group → ALB → ALB Listener → ASG → Scaling Policy

## decisions

### 1. Launch Template
- AMI: SSM resolve string (always latest Amazon Linux 2023)
- Instance type: t2.micro (free tier)
- Security Group imported from my-vpc-stack via cross-stack reference
- Why SSM resolve: avoids hardcoding AMI IDs that change per region/update