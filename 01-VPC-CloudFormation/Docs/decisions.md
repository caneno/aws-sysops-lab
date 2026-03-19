# Why I'm building this
    I am working on obtaining the AWS SOA-C03, and I am tired of just watching the tutorials and start doing real life porjects that will teach me the skills while hands-on projects.
# What I'm trying to learn
    How to manage and administer AWS infrastructure
# Date started
    02/25/2026


## Architecture Decisions

### 1. CIDR Selection
- VPC: 10.0.0.0/16
- Public Subnet: 10.0.1.0/24
- For ALB Public Subnet 2: 10.0.3.0/24 in us-east-1
- Private Subnet: 10.0.2.0/24
- Why: /16 gives room to grow. Subnets are /24 for simplicity.

### 2. Resource Dependency Order
VPC → Subnets → Security Group → IGW → VPCGatewayAttachment → Route Table → Route
- Why: Each resource depends on the one before it existing first.

### 3. CloudFormation Template Structure
- Logical resource name: MyVpc
- InstanceTenancy: default (dedicated tenancy costs money, never use unless required)
- Why we tag everything: without tags, identifying resources in AWS console 
  becomes impossible at scale

### 4. Subnet Design
- Public subnet: us-east-1a — will route to IGW
- Publib Subnet 2: us-east-1a — for ALB
- Private subnet: us-east-1b — no IGW route
- Why different AZs: if us-east-1a goes down, private resources 
  in us-east-1b remain reachable internally

### 5. Security Group Design
- Inbound: SSH (22) and HTTP (80) from 0.0.0.0/0
- Outbound: default allow all (Security Groups are stateful)
- Why no outbound rule needed: SGs automatically allow return traffic
- Key distinction: NACLs are stateless — require explicit ephemeral 
  port rule (1024-65535) for return traffic

### 6. Routing Design
- Route table associated with public subnet only
- Single route: 0.0.0.0/0 → IGW (all internet traffic)
- Private subnet has no route table association — no internet access by design
- DependsOn IGWandVPCAttachment: route cannot exist before IGW is attached to VPC
### 7. What this template builds
- 1 VPC (10.0.0.0/16)
- 2 Subnets: public (us-east-1a), private (us-east-1b)
- 1 Security Group: allows SSH and HTTP inbound
- 1 IGW attached to VPC
- 1 Route Table: routes 0.0.0.0/0 to IGW, associated with public subnet only

### 8. Deployment
- Deployed via AWS CLI: `aws cloudformation create-stack`
- Stack ID: arn:aws:cloudformation:us-east-1:338418590634:stack/my-vpc-stack
- Date: 2026-03-03
- Status: CREATE_COMPLETE

### 9. State Management
- Stack state is stored in AWS CloudFormation — no local state file needed
- Template (vpc.yaml) stored in GitHub — this is the source of truth
- To manage stack from any machine: configure AWS CLI + pull repo
- Never make manual changes in console without updating the template

### 10. Lessons Learned
- Never manually modify CFN-managed resources — causes drift and stack corruption
- MapPublicIpOnLaunch must be set on subnet BEFORE instance launches
- Manually terminating CFN instances orphans the stack state
- Always delete and recreate the stack to recover from UPDATE_ROLLBACK_FAILED

### 11. Verification
- SSH access confirmed to public EC2 instance
- Public IP assigned via MapPublicIpOnLaunch on public subnet
- Full stack deployed via CloudFormation CLI only — no console

### 12. Cost Management
- Always delete stacks when not in use
- Use t2.micro not t3.micro (free tier eligible)
- Delete dependent stacks first before deleting the VPC stack