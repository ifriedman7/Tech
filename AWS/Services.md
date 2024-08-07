# Compute
## EC2 
Know instance types, storage types to optimize
### Pricing
- on-demand -- regular on-off, no discount
- savings -- **commitment to amount of hours** -- without specifying type -- need to specify family and region
- reserved instances -- discount for upfront commitment -- including instance type
- spot -- tolerate interruptions
- dedicated host -- need total privacy for compliance -- can use per socket or per core licenses
### Instance Metadata Service 
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html
curl http://169.254.169.254/latest/meta-data/
	curl http://169.254.169.254/latest/meta-data/public-ipv4
curl http://169.254.169.254/latest/dynamic/
### Auto Scaling
scaling up or scaling out
Dynamic or predictive or both
Need Elastic Load Balancing to match or there will be traffic problem
simple scaling policy
	 Simple scaling policies can be configured to take action when a CloudWatch alarm is active. Simple scaling policies can be configured to scale in or out, and they repeat the action during a set time interval. These policies scale a fixed number of instances.
	 The simple scaling policy does not maintain a metric at a certain threshold. It increases or decreases the current capacity of the group based on a single scaling adjustment.
target scaling policy: set a target, or threshold
step scaling policy: Step scaling policies apply a percentage of instances to change.
### Elastic Load Balancing
Knows to coordinate with EC2 auto scaling
front end Decoupled from back end
Types of Load Balancer

## Clusters
Placement Groups
	Cluster Placement group
		Nodes placed near each other in same AZ
		good for tight coupling, low latency network performance
		bad for resilience
	Partition placement group
		multi AZ or single
		good for resilience if multi AZ

## CloudFormation
Build and deploy templates for IAC
JSON and or YAML

## Image Builder

## Lambda
Serverless functions
auto scales
should run in under 15 mins
*can increase concurrent runs*

## Elastic Container Service
container orchestration -- supports docker
use API to start and stop containers
can run over EC2 or serverless Fargate
## Elastic Kubernetes Service
container orchestration
can run over EC2 or serverless Fargate

## Fargate
AWS managed serverless for containers

ElastiCache
for DBs
Memcached

# Monitoring and Logs
## AWS Systems Manager
- Manage changes over all machines
- schedule updates and launches
- log aggregation
- automation
- Run scripts remotely
*Systems Manager Automation runbook can automate configuration changes*
you can run **scripts** on a specific system or on groups of systems. This centralized access is controllable through IAM policies. Systems Manager does not require the SSH ports to be open and removes the need to manage bastion hosts.
## OpsWorks
## Resource Access Manager

## Elastic Beanstalk
Provisioning tool

## CloudWatch
- Amazon CloudWatch **collects** and **visualizes** real-time **logs**, **metrics**, and **event data** in automated **dashboards** to streamline your infrastructure and application maintenance.
- Alarms
- Analytic tools
- What is Logs Insight?
- Agents
	- If you install a CloudWatch Logs Agent:
		- The CloudWatch Logs agent (awslogs agent) requires access to AWS credentials with the appropriate permissions so that it can send **API calls to CloudWatch Logs**. The recommended actions are to create an **IAM role** that has the appropriate permissions and to associate that role with the EC2 instance by using an EC2 instance profile.
- what is diff bet CloudTrail and CloudWatch: Watch is real-time. Watch has alarms, metrics, graphs. *CloudTrail records API calls for an AWS account and delivers log files to an Amazon S3 bucket.*
*CloudWatch **basic** monitoring data is available in **5-minute** periods.
For **detailed** monitoring, data is available in 1-minute periods for an additional charge.*

## Health Dashboard
EC2 Maintenance:
*When an EBS-backed instance that is scheduled for a maintenance event is stopped and restarted, AWS will restart the instance on different hardware that does not require maintenance.*

## CloudTrail
Logs **activities** performed
complete history of **user activity and API calls**
filter logs to assist with op analysis and troubleshooting
takes 15 mins to appear. **not real time**
cloudtrail insights: find suspicious activity

## Simple Notification Service
Sends messages to users
based on publish/subscribe
a function or webhook can also subscribe
also can push message to SMS, email

## Simple Queue Service
limit scaling and put **application** requests in a decoupled queue
if a server goes down, you dont lose the request -- minimize data loss
gives resilience and elasticity
scales automatically
Note: messages are not deleted automatically. Need to send a delete message...?

## EventBridge
EventBridge is a serverless service that uses events to connect **application** components together, making it easier for you to build scalable event-driven applications. Event-driven architecture is a style of building loosely-coupled software systems that work together by emitting and responding to events. Event-driven architecture can help you boost agility and build reliable, scalable applications.
*Amazon EventBridge can check for noncompliant resources according to the organization policy that is in place. You can use EventBridge to monitor when noncompliant tags are introduced.*

## AWS Config
AWS Config provides a detailed view of the configuration of AWS **resources** in your AWS account. This includes how the resources are related to one another and how they were configured in the past so that you can see how the configurations and relationships change over time.
Resources like EC2, VPC, EBS volumes
*You can create a custom AWS Config rule to monitor changes in security groups, etc.*
*AWS Config provides managed rules that are used to evaluate whether AWS resources comply with a desired configuration.*

# Storage
## EBS
Block storage -- optimized for files, partial writes. good for edits
take regular snapshots (incremental)
attach volume to instance -- dont use an instance store if you want persistence
> Recall that when you modify a file in block storage, only the pieces that are changed are updated. When a file in object storage is modified, the entire object is updated.
## S3
Stores "objects"
	can version the objects, but that takes a lot of space
	good for write once-read many
	In **object storage**, each object consists of data, metadata, and a key.
Optimize with 
- Transfer Accel
- Multipart upload
- tiers
Lifecycle policies -- move between tiers or delete old
high reliability -- 
can access bucket with URL directly -- it can be a website
Versioning
	Every change creates a new version. Can roll back.
	But size and number of objects grows tremendously.
	Also, when an object is deleted, it is marked. All old versions still exist and are not deleted. So bucket is not empty. Need to delete all versions IDs.
Storage classes -- different levels based on how frequent access
- Amazon S3 Standard
- Amazon S3 Intelligent-Tiering
- Amazon S3 Standard-Infrequent Access
- Amazon S3 One Zone-Infrequent Access
- Amazon S3 Glacier Instant Retrieval
- Amazon S3 Glacier Flexible Retrieval
- Amazon S3 Glacier Deep Archive

	Glacier 
		Glacier Vault Lock, a feature that can prevent the deletion or modification of the archives.
	infrequent access
	
*S3 Transfer Acceleration enables fast and secure transfers of files over long distances between a client and an S3 bucket. S3 Transfer Acceleration takes advantage of Amazon CloudFront’s globally distributed edge locations.*
For large uploads, use AWS S3 CLI, (not File gateway)

## EFS
elastic file system
can be attached to more than one instance
scalable
Compared to block storage and object storage, file storage is ideal for use cases in which a large number of services and resources need to access the same data at the same time.
 *EFS file systems do not have the capability to directly serve HTTP content, so they cannot act as a custom origin for CloudFront.*
## Snowball
Accelerate moving offline data or remote storage to the cloud
physical device that ships to you, connect on-pre,m and then ship to amazon
use OpsHub to manage the device

## Amazon Data Life Cycle Manager
automate the creation, retention, and deletion of EBS snapshots and EBS-backed AMIs
Backup policies for EC2/EBS for DR/compliance
Can make use of tags
*Amazon DLM uses an IAM role to get the permissions that are required to manage EBS snapshots on your behalf. An IAM role is a prerequisite for the service and for users to access Amazon DLM.*

# Network
## Route 53

## VPC
Networking
	Public and private subnets -- contain resources
	IGW or virtual private gateway
	VPN with VPG
	Direct Connect -- provides a physical line that connects your network to your AWS VPC
	Load Balancer types
Network hardening
	ACLs -- check traffic going in or out of a **subnet** -- stateless: does not remember what packets it saw before
		default ACL allows all
		custom ACL allows none by default
	security groups -- **instance** level -- stateful: any packet that left can return (as a response)
		default allows none inbound , all outbound allowed


Traffic mirroring
	*Traffic Mirroring copies inbound and outbound traffic from the network interfaces that are attached to Amazon EC2 instances. You can send the mirrored traffic to the network interface of another EC2 instance, or to a Network Load Balancer that has a UDP listener. Filters can reduce the traffic that gets mirrored.*
VPC Flow Logs
VPC Endpoint
	*With a VPC endpoint, you can privately connect your VPC to supported AWS services and VPC endpoint services without requiring an internet gateway, NAT device, VPN connection, or Direct Connect connection. Instances in your VPC do not require public IP addresses to communicate with resources in the service. Traffic between your VPC and the other service does not leave the Amazon network.*
## CloudFront
CDN -- on the edge
low latency -- high transfer
**origin shield** for cache improvement
TTL can also control caching: 0 means zero secs of caching: the obj will not be cached!
*CloudFront provides OACs (origin access control) to send authenticated requests to Amazon S3. To meet the requirements, you should store the digital content in an S3 bucket that has public access blocked. Then you should use the OAC to deliver the content through CloudFront. You can use CloudFront signed URLs to grant temporary access to the digital content to a set of authorized users.  you need signed URLs to restrict access to the digital content.*
## API Gateway

## WAF
Rules to block attacks by agents, bots
use Web ACLs
	 *WAF web ACL can detect and block SQL injection*

## Network Firewall
Network Firewall does not work on the subnet level. Network Firewall works on the **VPC** level.
Network Firewall can be configured to be stateful or stateless. Also, Network Firewall can be configured to perform **deep packet inspection**.

## Shield
DDOS protection -- not much else
Standard is free and automatic
Advanced:

## KMS
Encryption
	at rest
	in transit

## Inspector
Assess security on **EC2 instances**, **ECR containers** and **Lambda functions**
Checks configurations, unintended network exposure, software vulnerabilities
Installs an agent on instance
*Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications that are deployed on AWS. Amazon Inspector automatically assesses applications for exposure, vulnerabilities, and deviations from best practices.*

## Macie
Amazon Macie is a data security service that uses machine learning (ML) and pattern matching to discover and help protect your sensitive data.

## GuardDuty
Examines logs for security issues
monitors network and account activity

## Detective
*Detective helps analyze, investigate, and quickly identify the root cause of potential security issues or suspicious activities. Detective automatically collects log data from AWS resources and uses machine learning, statistical analysis, and graph theory to build a linked set of data for faster and more efficient security investigations.*

## IAM
Policies
Users Groups Roles
MFA
User: 
	has no permissions by default
	attach a policy to the user to allow
Groups:
	of users
Roles:
	set of permissions
	can be attached and detached
	Role replaces all other sets of permissions?
	You can actually avoid creating IAM users for every person in your organization by federating users into your account. This means that they could use their regular corporate credentials to log into AWS by mapping their corporate identities to IAM roles.

## Organizations
Manage multiple accounts
Consolidated Billing -- can get bulk discount
OUs like groups -- permissions inherited from OU
One Primary account
Service Control Policies determine what each account can access
 *SCPs can enforce tagging at the account level for specific AWS resources. You can use SCPs for an organizational unit (OU) to prevent the creation of AWS resources that do not have specific tags for all accounts under the OU.*

## Artifact
Compliance Reports and documentation

## Trusted Advisor
 evaluate your resources against five pillars. The pillars are cost optimization, performance, security, fault tolerance, and service limits.
 free level -- I should try it
## Cost Explorer
Visualize, filter, see top, use tags, generate reports
*cost allocation tags must be activated*
Cannot use cost allocation tags on S3
## AWS Budgets
Set a limit

## Cloud Adoption Framework
creates an action plan for companies
Methods of migration
	- Rehosting. This is otherwise known as lift and shift. No changes.
	- Replatforming. Or lift, tinker, and shift. Minimal changes. No code change. No dev.
	- Retire
	- Retain. Parts to not migrate
	- Repurchase
	- Refactoring. Now, you're writing new code. This is driven by a strong business need to add features or performance that might not be possible on prem, but now are within your reach

## Well-Architected Framework
helps you understand how to design and operate reliable, secure, efficient, and cost-effective systems in the AWS Cloud. It provides a way for you to consistently measure your architecture against best practices and design principles and identify areas for improvement.
Pillars:
- Operational Excellence
	- automating changes with deployment pipelines or responding to events that are triggered
	- performing operations as code, annotating documentation, anticipating failure, and frequently making small, reversible changes.
- Security
	- checking integrity of data 
	- - Protect data in transit and at rest.
- Reliability
	- Recover from infrastructure or service disruptions
	- Dynamically acquire computing resources to meet demand
	- Mitigate disruptions such as misconfigurations or transient network issues
- Performance Efficiency
	-  using the right Amazon EC2 type based on workload and memory requirements
- Cost Optimization
	- checking if you have overestimated your Amazon EC2 server size
- Sustainability
	- minimizing the environmental impacts of running cloud workloads
## Snow Family devices
Snowcone - up to 14 TB
Snowball Edge - Smart devices that can run code, VMs. Storage opt or Compute Opt.
Snowmobile

# Databases
Migrate DB as-is to EC2: lift and shift: use Database Migration Service
## RDS
Amazon Relational Database Service: Managed service by amazon
set up snapshots and restore
point in time restore
what is read replica?
RDS Proxy
Performance Insights
Metrics
**Amazon RDS is available on six database engines**, which optimize for memory, performance, or input/output (I/O). Supported database engines include:

- Amazon Aurora
- PostgreSQL
- MySQL
- MariaDB
- Oracle Database
- Microsoft SQL Server

Encryption with KMS
	*You can enable encryption for an RDS DB instance only when you create the DB instance.*
	*You can create a snapshot of your DB instance, create an encrypted copy of that snapshot, and restore a new DB instance from the encrypted snapshot.*
Notifications
	Amazon RDS uses Amazon SNS to provide notification when an RDS event occurs.
## Aurora
[**Amazon Aurora**(opens in a new tab)](https://aws.amazon.com/rds/aurora/) is an enterprise-class relational database. It is compatible with MySQL and PostgreSQL relational databases. It is up to five times faster than standard MySQL databases and up to three times faster than standard PostgreSQL databases.
Cheaper than the non-Amazon options
Consider Amazon Aurora if your workloads require high availability. It replicates six copies of your data across three Availability Zones and continuously backs up your data to Amazon S3.
## DynamoDB
non-relational, no-sql, 
serverless
Tables/document-based/key-value
flexible schema : not all tables have same attributes
fast response time
scalable automatically
Use DAX to accelerate

## Redshift
data warehousing service that you can use for big data analytics

## DocumentDB
document-based plus more management
Supports MongoDB
## Neptune
Graph DB

## Athena
Athena is an interactive query service that makes it easy to use standard SQL to analyze data in Amazon S3. Athena is serverless, so there is no infrastructure to manage. You pay only for the queries that you run.

## Database Migration Service
Same type or diff type
If diff type: AWS Schema Conversion Tool
Can migrate from source
	on-prem
	ec2
	RDS
Target can be
	EC2
	RDS
	any different type
DMS can be used for continuous replication, DR
## ElastiCache
Speed up DBs
It supports two types of data stores: Redis and Memcached.

# Innovation Services
- Transcribe: Speech to text
- Comprehend: Patterns in text
- Fraud Detector
- Lex: Voice and text chatbots
- Sagemaker: Machine learning
	