## Cloud Computing Advantages
- Trade upfront expense for variable expense.
- Benefit from massive economies of scale.
- Stop guessing capacity.
- Increase speed and agility.
- Stop spending money running and maintaining data centers.
- Go global in minutes.
## Monitoring, Logging, Remediating
- Aggregating logs
- Configuring Alarms
[[Services#CloudWatch]]
## Reliability and Business Continuity
- Scaling
	- [[Services#Auto Scaling]]
	- Network traffic load
- High availability and resilience -- fault tolerance
	- health checks
	- multi AZ
	- route 53 routing
	- [[Services#Simple Queue Service]]
- Backup, recover and restore -- Disaster recovery
	- snapshots and backups
	- versioning
	- lifecycle rules
	- DR procedures
		- pilot light
		- warm standby

## Deployment, Provisioning, and Automation
Provisions and maintain cloud resources
Automate processes, deployments, patching
Know Deployment scenarios
Service Quotas
[[Services#CloudFormation]]
[[Services#Image Builder]]
[[Services#OpsWorks]]
[[Services#Elastic Beanstalk]]

#### CI/CD
AWS CodePipeline with AWS CodeCommit, AWS CodeBuild, and AWS CodeDeploy
CodeBuild monitors functions and reports metrics through **CloudWatch**
## Security and Compliance
- Security and Compliance Policies
	- IAM
		- automate launch and terminate and changes and not grant to user
	- what is Trusted Advisor?
	- [[Services#CloudTrail]]
		- Good for security to know who did what
	- [[Services#CloudWatch]]
		- set up compliance alerts		
	- Multi-account
		- Control Tower
		- Organizations
- Data and Infrastructure Policies
	- Classify the data by security requirements
	- Encr at rest
		- S3
		- KMS
		- CloudHSM
	- Encr in transit
		- VPN
		- Certificate manager
		- Secrets manager
	- AWS Config
	- GuardDuty
	- Security Hub
	- Inspector
	- Macie
		- Amazon Macie is a data security service that uses machine learning (ML) and pattern matching to discover and help protect your sensitive data.
	- Snowball
## Networking and Content Delivery
dont expose critical components to open internet
	use a vpn
- Implementing features and connectivity
	- [[Services#VPC]]
		- subnets, routing tables, ACL, security groups
	- [[Services#AWS Systems Manager]]
		- session manager
	- VPC endpoints
	- VPC peering
	- VPNs
	- WAF
	- Shield
- Domains
	- [[Services#Route 53]]
		- hosted zones
		- routing policies
	- [[Services#CloudFront]]
	- [[Services#S3]]
- Troubleshooting Network Connectvity
	- evaluate configs
	- collect logs 
		- vpc flow logs
		- ELB load logs
		- logs from anywhere related to network and content delivery
	- route 53 troubleshoot 

## Cost and Performance Optimization
Know the features of each indiv service
Optimize performance and cost
Dedicated Cost services
- [[Services#Trusted Advisor]]
- [[Services#Cost Explorer]]
- [[Services#AWS Budgets]]
Cost Allocation with tags
EC2 Spot
What is reserved capacity?
Design the EC2 according to optimized needs
S3 optimization