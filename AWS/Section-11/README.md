# AWS Deployment Services

## Golden AMI
Pre-configured AMI with app/dependencies installed - faster EC2 launch

## Elastic Beanstalk
PaaS (Platform as a Service) to deploy apps (just upload code) - handles infrastructure, scaling, monitoring automatically.

### Beanstalk Tiers
- **Web Server:** Handles HTTP
- **Worker:** Processes background tasks from SQS

### Beanstalk Deployment Modes
1. **Single Instance:** 1 EC2 instance with Elastic IP
2. **High Availability:** With Load Balancer

## Key Differences

- **Beanstalk** = "I want to deploy my app, don't care about infrastructure"
- **CloudFormation** = "I want to define exact infrastructure setup"
