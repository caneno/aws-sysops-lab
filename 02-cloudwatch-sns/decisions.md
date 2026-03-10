# CloudWatch Alarms + SNS

## What this builds
- SNS Topic with email subscription
- CloudWatch Alarm: CPU > 70% for 1 x 5-minute period
- Cross-stack reference: imports EC2 instance ID from my-vpc-stack

## Lessons Learned
- SNS subscription requires email confirmation before alerts deliver
- CloudFormation auto-generates physical names unless AlarmName is explicitly set
- stress tool used to simulate CPU load and verify alarm end-to-end