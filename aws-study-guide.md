# AWS Solutions Architect Study Guide

## Database Services

### Amazon RDS (Relational Database Service)
- **Performance Improvement Options**:
  - Read replicas in the same region are cost-effective for serving reports
  - Cross-region replicas incur higher costs
- **Multi-AZ Deployments**:
  - Primarily for high availability and disaster recovery
  - Not intended for scaling workloads
  - Standby database cannot be queried, written to, or read from
  - Simplest and most effective way to reduce single points of failure
  - Provides automatic failover to a standby instance in a different AZ
  - Minimal implementation effort when modifying an existing instance
- **RDS Proxy**:
  - Use to reduce failover times
  - Helps prevent connection overload
  - Creating read replicas alone doesn't reduce failover time
  - Manages database connections, allowing applications to scale more effectively by pooling and sharing connections
  - Makes applications more resilient to database failures by automatically connecting to standby DB instance
  - Can enforce IAM authentication for databases and securely store credentials in AWS Secrets Manager
  - Handles unpredictable surges in database traffic and prevents oversubscription
  - Queues or throttles application connections that can't be served immediately, helping applications scale without overwhelming databases
- **Read Replicas**:
  - Read-only copy of a DB instance
  - Reduces load on primary DB instance by routing queries to the read replica
  - Enables elastically scaling beyond capacity constraints of a single DB instance for read-heavy workloads

### Amazon Aurora
- **Overview**:
  - A MySQL- or PostgreSQL-compatible relational database engine
  - Delivers performance and availability at scale
- **Read Replicas**:
  - Offload read queries from the writer DB instance
  - Can help alleviate performance degradation on the primary DB during peak load
  - Use a separate reader endpoint for your read queries

### Amazon DynamoDB
- **Key Features**:
  - Fully managed NoSQL database service
  - Designed for low-latency, high-throughput workloads
  - Single-digit millisecond response time at any scale
  - Can handle more than 10 trillion requests per day and support peaks of more than 20 million requests per second
  - Suitable for applications with unpredictable request patterns
  - Key-value store structure ideal for simple query patterns
  - Auto-scaling feature adjusts table's throughput capacity based on incoming traffic
- **DynamoDB Streams**:
  - Ordered flow of information about item changes
  - Captures every modification to data items
  - Each stream record appears exactly once
  - Stream records appear in the same sequence as the actual modifications
  - Writes stream records in near-real time
  - Can be configured to capture "before" and "after" images of modified items
- **On-Demand Replicas**:
  - Provides read replicas (global tables) for global applications
- **Time to Live (TTL)**:
  - Automatically removes items after a specified time
  - Reduces storage costs and overhead for cleaning up stale data
  - Ideal for data with a known expiration requirement (e.g., 30 days)

### Amazon Neptune
- **Use Cases**:
  - Fully managed graph database service
  - Ideal for storing and querying complex relationships, such as an IT infrastructure map
  - Supports SPARQL and Gremlin for graph-based queries
  - Minimal operational overhead for highly interconnected data

### Database Migration
- **AWS Database Migration Service (DMS)**:
  - Replicates data from one database to another
  - Supports homogeneous or heterogeneous migrations
  - Migrating from Microsoft SQL Server to Amazon RDS for SQL Server provides a managed service with significantly reduced operational overhead
  - Allows automated database setup, maintenance, and scaling tasks
  - Provides managed backups, patching, and monitoring
  - **Minimal-Change Oracle Migrations**:
    - Use DMS to migrate from on-premises Oracle to Oracle on Amazon RDS
    - Retains the same database engine for minimal code changes
    - Multi-AZ RDS deployment ensures high availability

## Caching Services

### Amazon ElastiCache
- **Memcached**:
  - Ideal for simple caching where the dataset is small and requires simple key-value access
  - Used for ephemeral, high-speed data access
- **Redis**:
  - Supports more complex data structures (lists, sets, sorted sets, etc.)
  - Provides persistence options, Pub/Sub functionality, and advanced features

## Messaging and Queuing

### Amazon MQ
- **Key Features**:
  - Managed message broker service for Apache ActiveMQ or RabbitMQ
  - Good fit for migrating from existing message broker solutions where native APIs/protocols are required

### Amazon SQS (Simple Queue Service)
- **Standard Queues**:
  - Near-limitless throughput
  - At-least-once delivery
  - Best-effort ordering
- **FIFO Queues**:
  - First-In-First-Out ordering to ensure messages are processed in order
  - Exactly-once processing prevents duplicates
  - Ideal for scenarios requiring strict message ordering and guaranteed single processing
- **Dead Letter Queue**:
  - Stores messages that can't be processed (for example, by a Lambda function) for further analysis
- **Decoupling for Resilience**:
  - Use Amazon SNS and SQS to introduce a buffering layer between clients and backend processors (e.g., EC2 instances)
  - If an EC2 instance fails, messages remain in the queue until another instance can process them
  - Ensures a more resilient architecture that recovers automatically from component failures

## Container and Kubernetes Services

### Amazon ECS (Elastic Container Service)
- **Launch Types**:
  - **Fargate**:
    - Serverless compute engine for containers
    - No need to provision or manage servers
    - Ideal for cloud-native workloads needing minimal infrastructure management
    - Cost-effective for running tasks that exceed the maximum execution duration limit of AWS Lambda functions (15 minutes)
    - Only pay for the vCPU and memory resources your containerized application uses while it's running
    - Suitable for data processing jobs that run once daily and can take up to 2 hours to complete
  - **EC2**:
    - You manage and patch the underlying Amazon EC2 instances
    - Allows more granular control over infrastructure
    - Requires managing server infrastructure, including capacity, provisioning, and scaling
  - **ECS Anywhere**:
    - Extend ECS to on-premises hardware or other clouds
    - Use an external launch type for hybrid container management
- **Scheduling**:
  - Can schedule ECS tasks on a recurring basis with Amazon EventBridge (e.g., weekly batch jobs)
- **Load Balancing**:
  - Typically use Application Load Balancer (ALB) for HTTP/HTTPS traffic
  - Network Load Balancer (NLB) can be used for TCP/UDP pass-through

### Amazon EKS (Elastic Kubernetes Service)
- **Key Features**:
  - Managed service for Kubernetes clusters
  - Lowers operational overhead for running upstream Kubernetes
  - Integrates well with AWS networking, security, and scaling services

## Compute Services

### AWS Elastic Beanstalk
- **Overview**:
  - Easiest way to deploy and scale web applications developed in .NET, Java, PHP, Node.js, Python, Ruby, Go, or Docker
  - Automatically handles capacity provisioning, load balancing, and auto scaling
  - Provides a fully managed platform while still allowing customization of underlying resources
  - **Multi-Environment & URL Swapping**:
    - Create multiple environments for staging and production
    - Use URL swapping (CNAME swap) to promote changes from staging to production with minimal downtime
  - **.NET on Windows Server**:
    - Supports deploying .NET applications with minimal code changes
    - Can be configured in a Multi-AZ setup for high availability
    - Ideal for rehosting on-premises .NET applications with minimal rework
  - Integrates with Amazon RDS for relational database requirements
  - Use for quick, straightforward migrations that do not require a full refactor or container-based approach

### AWS Fargate
- **Serverless container compute** for Amazon ECS and Amazon EKS
- Eliminates the need to manage EC2 instances
- Ideal for workloads needing uninterrupted execution (e.g., longer batch processes beyond Lambda's 15-minute limit)
- Often combined with Amazon EventBridge for scheduled tasks
- Better suited for applications that require more granular control over environments and longer running processes
- More cost-effective than EC2-based solutions for infrequent or sporadic workloads

### AWS Lambda
- **Key Constraints**:
  - Stateless, short-lived functions
  - 15-minute max execution time
- Ideal for event-driven, short-duration tasks
- Can be triggered by Amazon S3 Event Notifications, Amazon SQS, Amazon SNS, etc.
- Provides automatic scaling capabilities for unpredictable request patterns
- Can scale from a few requests per day to thousands per second
- Efficiently serves varying levels of traffic without manual intervention
- Well-suited for handling serverless API backends with Amazon API Gateway
- Ideal for scenarios with unpredictable request patterns that may change suddenly
- **Lambda Execution Role**:
  - IAM role assumed by Lambda function at runtime
  - Determines what AWS services and resources the function can access
  - Permissions associated with this role control function's access to AWS resources
  - Best practice is to grant only necessary permissions following principle of least privilege
  - Can be configured with permissions to decrypt data using AWS KMS keys
- **Accessing On-Premises Resources**:
  - Configure Lambda to run in a private subnet of your VPC
  - Ensure proper route via AWS Direct Connect or Site-to-Site VPN
  - Assign appropriate security groups/NACLs so Lambda can communicate with on-prem resources

### AWS DataSync
- **Primary Use**:
  - Automates data transfers between on-premises storage and AWS or between different AWS storage services
- **Not** for running data analysis jobs or containerized workloads

## Storage Services

### Amazon S3 (Simple Storage Service)
- **Storage Classes**:
  - **S3 Standard**: Frequent access, high durability
  - **S3 Standard-IA**: Infrequent access, cost savings for lower access frequency, still requires instant accessibility
  - **S3 One Zone-IA**: Stores data in a single AZ, poses higher risk of data loss in the event of an AZ failure
  - **S3 Intelligent-Tiering**: Automated cost optimization if access patterns are unknown
  - **S3 Glacier Instant Retrieval**: For archive data that needs immediate access (retrieval in milliseconds)
  - **S3 Glacier Flexible Retrieval**: For rarely accessed long-term data with retrieval times from minutes to hours
  - **S3 Glacier Deep Archive**: Lowest-cost storage for long-term archiving with retrieval times within 12 hours
- **Data Transfer**:
  - **Snowball**: Physical devices to transfer large data volumes offline
- **Encryption**:
  - **Client-Side Encryption**: Data is encrypted before upload
  - **Server-Side Encryption (SSE-S3)**: Amazon S3 manages keys
  - **Server-Side Encryption with AWS KMS (SSE-KMS)**: Uses AWS KMS for key management
  - **Server-Side Encryption with Customer-Provided Keys (SSE-C)**
- **Static Website Hosting**:
  - You can host static websites on an S3 bucket
  - Typically combined with Amazon CloudFront for edge caching
- **S3 Access Points**:
  - Provide separate custom-hosted endpoints with distinct access policies
  - Simplify managing access to shared datasets
- **Multi-Region Access Points**:
  - Active-active S3 configuration with a single global endpoint
  - Intelligent routing to the closest bucket for performance
  - Supports S3 Cross-Region Replication for durability and failover
- **S3 Storage Lens**:
  - Cloud-storage analytics feature for organization-wide visibility into object storage and activity
  - Analyzes metrics to deliver contextual recommendations for optimizing storage costs
  - Can identify buckets that don't have S3 Lifecycle rules to abort incomplete multipart uploads
  - Helps identify cost-optimization opportunities and implement data-protection best practices
  - Provides dashboards and metrics directly within AWS Management Console without custom configuration

### Amazon FSx
- **FSx for NetApp ONTAP**:
  - Managed storage for SMB/NFS
  - Multi-protocol access
  - Supports cross-region replication with NetApp SnapMirror technology
  - Provides seamless way to replicate data across AWS Regions
  - Maintains ability to access data using same CIFS and NFS protocols
  - Offers least operational overhead for disaster recovery purposes
  - Leverages built-in replication capabilities of FSx for NetApp ONTAP
  - Ensures replicated data can be accessed using same file-sharing protocols as primary Region
  - Ideal for disaster recovery solutions requiring minimal management complexity
- **FSx for Windows File Server**:
  - SMB protocol for Windows-based file shares
- **FSx for OpenZFS**:
  - For NFS-based Unix/Linux file workloads
  - Does not support SMB
- **FSx for Lustre**:
  - Fully managed file system optimized for compute-intensive workloads
  - Designed for high-performance computing (HPC), machine learning, and media processing
  - Available in scratch and persistent deployment types:
    - Scratch: For temporary storage and shorter-term processing, data not replicated
    - Persistent: For longer-term storage and throughput-focused workloads, data replicated within AZ
  - Can seamlessly process data directly from S3
  - Provides high levels of throughput, IOPS, and sub-millisecond latencies
  - Enables thousands of compute instances to access data simultaneously
  - Tailored for workloads that demand both high levels of sustained throughput and data durability
  - Supports sub-millisecond latency for high-performance computing environments
  - File systems enable thousands of compute instances to access and process entire datasets simultaneously
  - Ideal for weather forecasting companies processing hundreds of gigabytes of data with sub-millisecond latency
  - Provides high-performance file system for large-scale data processing with parallel access requirements

### Amazon Elastic File System (EFS)
- **Key Features**:
  - NFS file system
  - Grows/shrinks automatically
  - Multi-AZ by default
  - Excellent for Linux-based applications needing shared file access
- **Throughput Modes**:
  - Bursting Throughput: Automatically scales throughput to accommodate spikes
  - Provisioned Throughput: Specifies throughput levels independent of storage amount

### Amazon Elastic Block Store (EBS)
- **Key Features**:
  - Persistent block-level storage for use with EC2
  - "EBS encryption by default" can be configured at the **EC2 account attribute** level
    - Ensures all newly created EBS volumes are encrypted automatically
    - Prevents creation of unencrypted volumes
- **Volume Types**:
  - **General Purpose SSD (gp3)**:
    - Baseline performance of 3,000 IOPS and up to 16,000 IOPS
    - Can provision IOPS independently of storage capacity
    - Most cost-effective when specific IOPS requirements must be met
    - Suitable for transactional workloads, virtual desktops, medium-sized databases
  - **General Purpose SSD (gp2)**:
    - IOPS performance linked directly to storage capacity
    - Requires provisioning larger volume sizes to achieve higher IOPS
    - Less cost-effective for specific IOPS requirements
  - **Provisioned IOPS SSD (io1)**:
    - Designed for I/O-intensive database workloads
    - For workloads requiring sustained IOPS performance above 16,000 IOPS
    - More expensive than gp3 for similar performance levels
  - **Magnetic (Standard)**:
    - Lowest per GB cost but does not support provisioning of IOPS
    - Best suited for workloads with infrequently accessed data
    - Cannot meet high IOPS requirements reliably or cost-effectively
- **Snapshots**:
  - You can lock EBS snapshots to protect against accidental or malicious deletion
  - Locked snapshots can't be deleted by any user regardless of their IAM permissions
  - Snapshots can be stored in WORM (write-once-read-many) format for a specific duration
  - Can continue to use a locked snapshot just like any other snapshot
- **Lifecycle Management**:
  - Amazon Data Lifecycle Manager (DLM) automates creation, retention, and deletion of EBS snapshots
  - Helps enforce regular backup schedules
  - Creates standardized AMIs that can be refreshed at regular intervals
  - Retains backups as required by auditors or internal compliance
  - Reduces storage costs by deleting outdated backups
  - Creates disaster recovery backup policies that back up data to isolated Regions or accounts

## Security and Identity Services

### AWS KMS (Key Management Service)
- **Customer Managed Keys**:
  - Offers full control over rotation schedules, key policies, and lifecycle
  - Minimal operational overhead if using automated rotation
- **AWS Managed Keys**:
  - Automatically rotated every 3 years by AWS
  - Less control over rotation schedule
- **AWS Owned Keys**:
  - Entirely controlled by AWS
  - No visibility or control for customers
- **External Keys**:
  - Allows key material import
  - Provides highest level of control but more operational overhead
- **Key Policies and Permissions**:
  - Grant permission for Lambda IAM role within KMS key's policy to decrypt
  - Direct association of Lambda's execution role with permissions to decrypt files
  - Allow decrypt capability for functions that need to access encrypted data

### AWS Secrets Manager
- **Key Features**:
  - Secure storage of credentials, API keys, etc.
  - Automatic key rotation (e.g., for RDS, other databases)
  - Integrated with AWS Lambda for custom rotation
  - Helps manage, retrieve, and rotate database credentials, application credentials, OAuth tokens, API keys, and other secrets
  - Has native integration with Amazon RDS for automatic password rotation
  - Helps improve security posture by eliminating hard-coded credentials in application code
  - Credentials are retrieved dynamically via API when needed
  - Supports automatic rotation scheduling for secrets
- **Alternatives**:
  - **AWS KMS**: Manages encryption keys, not secrets
  - **AWS Systems Manager Parameter Store**: Can store secrets, but no built-in rotation

### AWS Firewall Manager
- **Key Benefits**:
  - Centrally configure AWS WAF rules across accounts
  - Manage AWS Shield Advanced, AWS WAF, and security groups in one place
  - Automatically protect new resources (e.g., CloudFront distributions, Application Load Balancers)

### AWS Network Firewall
- **Key Features**:
  - Protects your VPCs with stateful filtering, intrusion detection
  - Not for automatically auditing or managing security groups
  - Deep packet inspection for advanced filtering

### AWS WAF (Web Application Firewall)
- **Key Attach Points**:
  - Application Load Balancer
  - Amazon CloudFront distribution
  - Amazon API Gateway
  - AWS AppSync
- Helps protect web applications against common exploits
- Can create custom rules to block or allow traffic based on geolocation of the requestor
- Provides robust solution for serving correct content without violating distribution rights

### AWS IAM Identity Center (AWS Single Sign-On)
- **Key Features**:
  - Centrally manage SSO access to multiple AWS accounts and business applications
  - Enables unified management of users and their access
  - Can be integrated with existing identity providers
  - Users sign in to a portal with credentials to access assigned accounts and applications

### AWS Certificate Manager (ACM)
- **Key Features**:
  - Request public certificates for domains and subdomains
  - Support for wildcard certificates (e.g., *.example.com) to cover all subdomains
  - Validates domain ownership through DNS validation method
  - Supports automatic certificate renewal
  - Integrates with AWS services like CloudFront and Application Load Balancer
  - Provides easy way to implement HTTPS for websites and applications

### AWS Organizations
- **Key Features**:
  - Centrally manage and govern AWS environment across multiple accounts
  - Programmatically create AWS accounts and allocate resources
  - Group accounts to organize workflows
  - Apply policies to accounts or groups for governance
  - Simplify billing by using a single payment method for all accounts
  - Manage tag policies that can specify allowed/disallowed tag keys and values
  - Tag policies can be enforced to standardize tags across resources in an organization

### Identity Federation
- **SAML 2.0-based Federation**:
  - Enables single sign-on (SSO) between on-premises Active Directory and AWS
  - Leverages existing AD infrastructure to authenticate users
  - Maps AD groups to AWS IAM roles for permissions
  - Minimizes administrative overhead by maintaining a single identity source
  - Users don't need separate AWS credentials

## Networking and Content Delivery

### AWS PrivateLink
- **Overview**:
  - Private connectivity between VPCs and AWS services or SaaS
  - Traffic remains in the AWS network, avoiding public internet exposure
  - Eliminates need for NAT gateways or VPN for private access to supported endpoints
- **Interface Endpoints**:
  - Enable private connections between VPC and AWS services
  - Use private IP addresses within your VPC
  - When combined with AWS Direct Connect, ensures data accessed from on-premises to AWS doesn't traverse public internet

### AWS Transit Gateway
- **Key Features**:
  - Hub-and-spoke model for connecting multiple VPCs and on-premises networks
  - Reduces need for numerous VPC peering connections
  - Ideal for large multi-VPC or multi-account architectures
  - Simplifies routing and management at scale

### AWS Direct Connect
- **Redundancy Best Practices**:
  - For mission-critical workloads, use multiple connections at separate locations/devices
  - Ensure path and device redundancy from each data center

### AWS Global Accelerator
- **Key Features**:
  - Improves availability and performance of applications with users across the world
  - Provides static IP addresses as fixed entry points to applications
  - Directs traffic to optimal endpoints based on health, geographic location, and policies
  - Offers automated failover across AWS Regions
  - Routes traffic to the nearest healthy application endpoint
  - Bypasses DNS caching strategies which can lead to outdated routing information
  - Particularly useful for applications requiring real-time communication like Voice over IP

### Amazon CloudFront
- **Key Features**:
  - Content Delivery Network (CDN)
  - Low latency, high data transfer speeds for content delivery
  - Integrates with S3 for static website hosting
  - Can integrate with AWS WAF for security at the edge
  - Provides geographic restrictions (geo-blocking) to prevent users in specific locations from accessing content
  - Helps comply with distribution rights by restricting access based on user's location
- **Field-Level Encryption and Signed URLs**:
  - Use field-level encryption to protect sensitive data fields end to end
  - Signed URLs or signed cookies can restrict access to private content

### Amazon VPC
- **Internet Gateways**:
  - A single internet gateway can route traffic for the entire VPC regardless of the number of Availability Zones
  - Internet gateways provide a target in VPC route tables for internet-routable traffic
  - Do not enforce or require redundancy across Availability Zones
- **Managed Prefix Lists**:
  - Sets of one or more CIDR blocks for simplifying security group and route table configurations
  - Customer-managed prefix lists can be shared across AWS accounts using Resource Access Manager
  - Helps centrally manage CIDR blocks across organization
  - Makes it easier to update and maintain security groups and route tables
  - Can consolidate security group rules with different CIDR blocks but same port/protocol into a single rule

### Elastic Load Balancing
- **Types of Load Balancers**:
  - **Application Load Balancer (ALB)**: For HTTP/HTTPS traffic routing at the application layer
  - **Network Load Balancer (NLB)**: For TCP/UDP traffic routing at the transport layer
    - Supports high throughput of millions of requests per second
    - Maintains ultra-low latencies ideal for gaming applications using UDP packets
    - Efficiently distributes UDP traffic across multiple targets
    - Enables applications to scale out and in according to traffic fluctuations with minimal latency
    - Commonly used for gaming applications due to low-latency characteristics
  - **Gateway Load Balancer**: For deploying and managing third-party virtual networking appliances
  - **Classic Load Balancer**: Legacy load balancer (not recommended for new implementations)
- **Application Load Balancer Features**:
  - Automatically distributes incoming traffic across multiple targets
  - Monitors health of registered targets and routes traffic only to healthy targets
  - Supports multiple Availability Zones for high availability
  - Scales automatically according to traffic changes
  - Works with Auto Scaling groups for dynamic capacity adjustments

## Data Analytics and Visualization

### Amazon Kinesis
- **Kinesis Data Streams**:
  - Real-time ingestion of streaming data at massive scale
  - Low-latency, high-throughput
  - Often used with Amazon OpenSearch Service for real-time analytics
- **Kinesis Data Firehose**:
  - Fully managed service to reliably load streaming data into data lakes, data stores, and analytics services
  - Can be used to send VPC Flow Logs to Amazon CloudWatch Logs
  - Used for streaming logs to Amazon OpenSearch Service for analysis
  - Provides a way to capture, transform, and load streaming data

### Amazon OpenSearch Service (successor to Amazon Elasticsearch Service)
- **Key Features**:
  - Search, analyze, visualize data in near real-time
  - Used for log analytics, full-text search, operational analytics
  - Integrates with Kinesis Data Streams, Kinesis Data Firehose
  - Can be queried from Amazon QuickSight

### Amazon QuickSight
- **Key Features**:
  - Fully managed BI (Business Intelligence) service
  - Create dashboards and visualizations from multiple data sources
  - Can connect to Amazon OpenSearch Service, Redshift, Athena, RDS, etc.

### Amazon Security Lake
- **Key Features**:
  - Purpose-built service to centralize security data across AWS
  - Automates data collection from AWS services
  - Simplifies organization and analysis of security information

### Amazon Athena
- **Key Features**:
  - Interactive query service for data in Amazon S3 using standard SQL
  - Serverless, paying only for queries run
  - Works with structured, semi-structured, and unstructured data
  - Can be combined with AWS Glue for data discovery and ETL
  - Cost-effective for on-demand analysis of data that occurs sporadically
  - Eliminates need for managing database or cluster infrastructure

### AWS Glue
- **Key Features**:
  - Fully managed ETL (Extract, Transform, Load) service
  - Discovers and catalogs metadata about data sources
  - Catalogs data and makes it searchable, queryable, and available for ETL
  - Integrates with Athena for serverless querying of data
  - Crawler can create metadata catalog for data in S3

## Encryption and Data Protection

### EBS Encryption by Default
- **EC2 Account Attributes**:
  - You can enable encryption by default at the account level
  - All newly created EBS volumes will be encrypted automatically
  - Prevents creation of unencrypted volumes, ensuring compliance

### S3 Bucket Encryption
- **Client-Side Encryption**:
  - Data encrypted before uploading
  - Provides encryption in transit and at rest
- **Server-Side Encryption**:
  - **SSE-S3**: S3 manages the keys
  - **SSE-KMS**: Keys via AWS KMS
  - **SSE-C**: Customer-provided keys

## Cloud Financial Management

### AWS Cost Anomaly Detection
- **Key Features**:
  - Monitors AWS costs and usage, detecting unusual spending patterns
  - Allows setting of monitors that analyze historical spending patterns to identify anomalies
  - Notifies stakeholders through configured alerts when unusual spending is detected
  - Proactive solution for monitoring costs without manual intervention

### AWS Cost Explorer
- **Key Features**:
  - Analyze cost and usage data with visuals, filtering, and grouping
  - Forecast costs and create custom reports

## Configuration and Infrastructure Management

### AWS Config
- **Key Features**:
  - Monitors resource configurations
  - Records and evaluates configurations against desired settings
  - Provides detailed view of resource configurations and their changes over time
  - Can track changes at the resource level

### AWS CloudFormation
- **Key Features**:
  - Enables automation and provisioning of infrastructure as code
  - Allows repeatable and predictable way to set up and manage AWS resources
  - Provides templates for defining resources and their relationships
  - Can be used to create a wide variety of AWS resources programmatically

### AWS Resource Access Manager (RAM)
- **Key Features**:
  - Securely share AWS resources across AWS accounts
  - Share resources within your organization or organizational units in AWS Organizations
  - Support for sharing transit gateways, subnets, license configurations, Route 53 Resolver rules
  - Reduces operational overhead of managing resources in multiple accounts
  - Enables sharing of customer-managed prefix lists across accounts

## Auto Scaling and Capacity Management

### Amazon EC2 Auto Scaling
- **Scheduled Scaling**:
  - Configure automatic scaling based on predictable load changes
  - Create scheduled actions to increase or decrease capacity at specific times
  - Can proactively adjust the number of EC2 instances in anticipation of known load changes
  - Optimizes costs and performance by ensuring sufficient capacity during peak times
  - Eliminates lag in scaling up resources, addressing slow application performance issues

## Best Practices and Key Concepts
- **Use read replicas** in Aurora (or RDS) to offload read traffic and improve performance
- **Enable EBS encryption by default** at the EC2 account attribute level for secure volumes
- **Use an Auto Scaling group** spanning multiple AZs with an **Application Load Balancer** to improve availability and scale
- **Attach AWS WAF** to the ALB or CloudFront for web app protection
- **Use S3 Access Points** for granular access to subsets of data under a single S3 bucket
- **Use S3 Event Notifications** + **Amazon SQS** + **AWS Lambda** to trigger short-lived image processing tasks without needing dedicated EC2 instances
- **Amazon ECS + AWS Fargate** for scheduled container-based workloads (EventBridge for scheduling)
- **AWS Transit Gateway** is recommended for scaling to 100+ VPCs or large multi-account environments
- **Kinesis Data Streams + Amazon OpenSearch Service** for near real-time data ingest, analysis, search, and QuickSight dashboards
- **AWS Config** can monitor resource configurations but does not enforce encryption or block resource creation; it is primarily for compliance checks
- **Use AWS Organizations** and **Service Control Policies (SCPs)** for centralized governance across multiple AWS accounts
- **Use Amazon EFS** for a shared POSIX-compliant file system across multiple EC2 instances in multiple AZs
- **For DR scenarios with minimal downtime**:
  - Pre-provision resources in a secondary Region (Auto Scaling group, load balancer, global DynamoDB table)
  - Use DNS failover to direct traffic to the DR Region's load balancer if primary fails
- **For S3 multi-Region** active-active design with minimal management:
  - Use **S3 Multi-Region Access Points** with a single global endpoint
  - Configure Cross-Region Replication for durability and failover
- **Amazon Security Lake** for centralized security data ingestion and analysis
- **Use Amazon Neptune** for storing and querying complex relationships (e.g., an IT infrastructure map)
- **AWS KMS Customer Managed Keys** provide the ability to control rotation schedules, key policies, and lifecycle
- **For a serverless architecture behind API Gateway**:
  - Use AWS Lambda for handling unpredictable request patterns (scales automatically)
  - Use DynamoDB for a fully managed NoSQL database with auto-scaling capabilities
  - Combine for a completely serverless architecture that scales with demand
- **Hospital Scanning & Document Processing**:
  - Store scanned documents in Amazon S3
  - Use S3 event notifications to trigger a Lambda function
  - Extract text with Amazon Textract or Amazon Rekognition (if images) and use Amazon Comprehend or Comprehend Medical for deeper analysis
  - Query results with Amazon Athena for on-demand analytics
- **Static + Dynamic Website**:
  - Host static content in Amazon S3
  - Use Amazon API Gateway + AWS Lambda for dynamic requests
  - Store data in Amazon DynamoDB (on-demand capacity for unpredictable traffic)
  - Use Amazon CloudFront to deliver the entire website for global low-latency access
- **DynamoDB Accelerator (DAX)**:
  - In-memory cache for DynamoDB
  - Significantly improves read performance without major application rework (if using DAX client)
  - Ideal for read-intensive workloads requiring microsecond response times

## High Availability Architectures
- **For MySQL and stateless Python web applications**:
  - Migrate database to Amazon RDS for MySQL with Multi-AZ deployment for high availability
  - Use Application Load Balancer with Auto Scaling groups of EC2 instances across multiple AZs
  - RDS Multi-AZ provides automatic failover to standby instance in a different AZ with minimal downtime
  - ALB distributes traffic to healthy instances in the Auto Scaling group

## VPC Flow Logs and Monitoring
- **VPC Flow Logs**:
  - Feature to capture information about IP traffic going to and from network interfaces in VPC
  - Flow log data can be published to CloudWatch Logs, Amazon S3, or Amazon Kinesis Data Firehose
  - Helps diagnose overly restrictive security group rules
  - Monitors traffic reaching instances
  - Determines direction of traffic to/from network interfaces

## Analysis and ETL Solutions
- **For clickstream data in S3**:
  - Use AWS Glue crawler to catalog data and make it queryable
  - Configure Amazon Athena for SQL-based analysis of the data
  - Minimal operational overhead as both services are serverless and fully managed
  - Enables quick analysis for decision-making about further processing
- **Document Ingestion and Transformation**:
  - Store large volumes of documents in Amazon S3
  - Use AWS Lambda triggers on object upload
  - Perform OCR with Amazon Textract or Rekognition
  - Use Amazon Comprehend (or Comprehend Medical) to extract relevant information
  - Store extracted data in S3 or a database, queryable via Athena

## Resource Tagging and Cost Allocation
- **AWS Lambda with EventBridge**:
  - Can be used to automatically tag resources with cost center IDs
  - EventBridge detects resource creation events via CloudTrail
  - Lambda function queries databases for appropriate cost center information
  - Enables automatic tagging based on user who created the resource
  - Provides proactive and dynamic approach to resource tagging

## Big Data Processing
- **Amazon EMR (Elastic MapReduce)**:
  - Industry-leading cloud big data platform for processing vast amounts of data
  - Supports open source tools like Apache Spark, Apache Hive, Apache Flink
  - Cluster configuration options for cost-optimization:
    - **Transient clusters**: Terminated after completing tasks, most cost-effective for workloads with known duration
    - **Long-running clusters**: Stay active continuously, less efficient for workloads with specific durations
    - **Primary node and core nodes on On-Demand Instances**: Ensures reliability for critical parts of the workload
    - **Task nodes on Spot Instances**: Cost-effective for compute-intensive portions of workload that can tolerate interruptions
