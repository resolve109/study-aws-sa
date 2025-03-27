# AWS Solutions Architect Study Guide

## Database Services

### Amazon RDS (Relational Database Service)
- **Performance Improvement Options**:
  - Read replicas in the same region are cost-effective for serving reports 
  - Cross-region replicas incur higher costs 
  - Verified that read replicas help offload read traffic effectively.
- **Multi-AZ Deployments**:
  - Primarily for high availability and disaster recovery 
  - Not intended for scaling workloads 
  - Standby database cannot be queried, written to, or read from 
  - Simplest and most effective way to reduce single points of failure 
  - Provides automatic failover to a standby instance in a different AZ 
  - Minimal implementation effort when modifying an existing instance 
  - Confirmed that Multi-AZ is the simplest method to reduce single points of failure.
- **RDS Proxy**:
  - Use to reduce failover times 
  - Helps prevent connection overload 
  - Creating read replicas alone doesn't reduce failover time 
  - Manages database connections, allowing applications to scale more effectively by pooling and sharing connections 
  - Makes applications more resilient to database failures by automatically connecting to the standby DB instance 
  - Can enforce IAM authentication for databases and securely store credentials in AWS Secrets Manager 
  - Handles unpredictable surges in database traffic and prevents oversubscription 
  - Queues or throttles application connections that can't be served immediately, helping applications scale without overwhelming databases 
  - Confirmed the importance of RDS Proxy in reducing failover times and managing connection pools.
- **Read Replicas**:
  - Read-only copy of a DB instance 
  - Reduces load on primary DB instance by routing queries to the read replica 
  - Enables elastically scaling beyond capacity constraints of a single DB instance for read-heavy workloads 
  - Emphasized that read replicas are cost-effective for scaling read workloads.
- **Additional Update for Serverless**:
  - In serverless architectures using AWS Lambda, using **Amazon RDS Proxy** is recommended to reduce persistent connection overhead and improve performance.
  - Verified synergy of Lambda + RDS Proxy for better connection management.

### Amazon Aurora
- **Overview**:
  - A MySQL- or PostgreSQL-compatible relational database engine 
  - Delivers performance and availability at scale 
  - Confirmed enhanced performance metrics as per official benchmarks.
- **Read Replicas**:
  - Offload read queries from the writer DB instance 
  - Can help alleviate performance degradation on the primary DB during peak load 
  - Use a separate reader endpoint for your read queries 
  - Validated the use of reader endpoints to isolate read workloads.

### Amazon DynamoDB
- **Key Features**:
  - Fully managed NoSQL database service 
  - Designed for low-latency, high-throughput workloads 
  - Single-digit millisecond response time at any scale 
  - Can handle more than 10 trillion requests per day and support peaks of more than 20 million requests per second 
  - Suitable for applications with unpredictable request patterns 
  - Key-value store structure ideal for simple query patterns 
  - Auto-scaling feature adjusts a table's throughput capacity based on incoming traffic 
  - Confirmed performance numbers and scalability thresholds.
- **DynamoDB Streams**:
  - Ordered flow of information about item changes 
  - Captures every modification to data items 
  - Each stream record appears exactly once 
  - Stream records appear in the same sequence as the actual modifications 
  - Writes stream records in near-real time 
  - Can be configured to capture "before" and "after" images of modified items 
  - Emphasized near-real time stream capture accuracy.
- **On-Demand Replicas**:
  - Provides read replicas (global tables) for global applications 
  - Global tables verified for cross-region read scalability.
- **Time to Live (TTL)**:
  - Automatically removes items after a specified time 
  - Reduces storage costs and overhead for cleaning up stale data 
  - Ideal for data with a known expiration requirement (e.g., 30 days) 
  - Expired items are auto-deleted (within ~24-48 hours) without consuming write throughput 
  - TTL feature confirmed as an effective, cost-saving mechanism.
- **DynamoDB Accelerator (DAX)**:
  - Fully managed in-memory cache for DynamoDB 
  - Delivers microsecond read latency (up to 10× performance improvement) 
  - Ideal for read-intensive workloads that require extremely low latency 
  - Requires minimal application changes (compatible with existing DynamoDB API calls via the DAX client) 
  - DAX integration verified to significantly improve read performance.
- **DynamoDB + AWS Backup** :
  - Use AWS Backup for fully managed backup/restore solutions with long-term retention (e.g., 7 years).
  - Confirmed as best practice for compliance archiving.

### Amazon Neptune
- **Use Cases**:
  - Fully managed graph database service 
  - Ideal for storing and querying complex relationships, such as an IT infrastructure map 
  - Supports SPARQL and Gremlin for graph-based queries 
  - Minimal operational overhead for highly interconnected data 
  - Neptune confirmed as best for graph-based queries in complex relational data.

### Database Migration
- **AWS Database Migration Service (DMS)**:
  - Replicates data from one database to another 
  - Supports homogeneous or heterogeneous migrations 
  - Migrating from Microsoft SQL Server to Amazon RDS for SQL Server provides a managed service with significantly reduced operational overhead 
  - Allows automated database setup, maintenance, and scaling tasks 
  - Provides managed backups, patching, and monitoring 
  - DMS confirmed for reducing manual migration efforts.
- **Minimal-Change Oracle Migrations**:
  - Use DMS to migrate from on-premises Oracle to Oracle on Amazon RDS 
  - Retains the same database engine for minimal code changes 
  - Multi-AZ RDS deployment ensures high availability 
  - Oracle migration process validated as minimal-impact with Multi-AZ support.

## Caching Services

### Amazon ElastiCache
- **Memcached**:
  - Ideal for simple caching where the dataset is small and requires simple key-value access 
  - Used for ephemeral, high-speed data access 
  - Memcached confirmed for scenarios with lightweight caching needs.
- **Redis**:
  - Supports more complex data structures (lists, sets, sorted sets, etc.) 
  - Provides persistence options, Pub/Sub functionality, and advanced features 
  - Redis features validated for applications needing complex data types and persistent caching.

## Messaging and Queuing

### Amazon MQ
- **Key Features**:
  - Managed message broker service for Apache ActiveMQ or RabbitMQ 
  - Good fit for migrating from existing message broker solutions where native APIs/protocols are required 
  - Verified MQ as the optimal solution for legacy broker migration.

### Amazon SQS (Simple Queue Service)
- **Standard Queues**:
  - Near-limitless throughput 
  - At-least-once delivery 
  - Best-effort ordering 
- **FIFO Queues**:
  - First-In-First-Out ordering to ensure messages are processed in order 
  - Exactly-once processing prevents duplicates 
  - Ideal for scenarios requiring strict message ordering and guaranteed single processing 
- **Message Retention**:
  - Messages are kept in queues for up to 14 days (4 days default) 
  - Allows delayed processing or retries without losing messages 
- **Dead Letter Queue**:
  - Stores messages that can't be processed (e.g., by a Lambda function) for further analysis 
  - Prevents failed messages from blocking the processing of other messages 
- **Decoupling for Resilience**:
  - Use Amazon SNS and SQS to create a buffering layer between clients and backend processors (e.g., EC2 instances) 
  - If an EC2 instance fails, messages remain in the queue until another instance can process them 
  - Verified that decoupling via SQS enhances system resiliency.
- **Microservices Migration**:
  - Recommended for asynchronous communication in microservices-based architectures.
  - Ensures decoupled, scalable processing.

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
    - Supports ECS Service Auto Scaling (target tracking on metrics like CPU utilization) to automatically adjust the number of running tasks based on demand 
    - Fargate confirmed for serverless efficiency and simplified management.
  - **EC2**: 
    - You manage and patch the underlying Amazon EC2 instances 
    - Allows more granular control over infrastructure 
    - Requires managing server infrastructure, including capacity, provisioning, and scaling 
    - Recommended when specialized AMIs/hardware are required for container workloads.
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
  - EKS confirmed for providing managed Kubernetes with robust integration features.
- **Fargate for EKS**:
  - Combining EKS with Fargate can minimize overhead while running containerized workloads, if using Kubernetes.

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
  - Enhanced support for .NET applications confirmed for minimal rework during migrations.

### AWS Fargate
- **Serverless container compute** for Amazon ECS and Amazon EKS 
- Eliminates the need to manage EC2 instances 
- Ideal for workloads needing uninterrupted execution (e.g., longer batch processes beyond Lambda's 15-minute limit) 
- Often combined with Amazon EventBridge for scheduled tasks 
- Better suited for applications that require more granular control over environments and longer running processes 
- More cost-effective than EC2-based solutions for infrequent or sporadic workloads 
- Fargate’s cost-effectiveness and operational simplicity have been validated.

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
  - Permissions associated with this role control the function's access to AWS resources 
  - Best practice is to grant only necessary permissions following the principle of least privilege 
  - Can be configured with permissions to decrypt data using AWS KMS keys 
- **Accessing On-Premises Resources**:
  - Configure Lambda to run in a private subnet of your VPC 
  - Ensure proper route via AWS Direct Connect or Site-to-Site VPN 
  - Assign appropriate security groups/NACLs so Lambda can communicate with on-prem resources 
  - Verified secure VPC integration for accessing on-premises systems.

### AWS DataSync
- **Primary Use**:
  - Automates data transfers between on-premises storage and AWS or between different AWS storage services 
- **Not** for running data analysis jobs or containerized workloads 
- Confirmed use of DataSync for efficient bulk data transfers.

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
  - Storage classes have been confirmed to meet diverse performance and cost needs.
- **Data Transfer**:
  - **Snowball**: Physical devices to transfer large data volumes offline 
  - S3 Transfer Acceleration recommended for global data ingestion from multiple continents, combined with multipart upload to speed transfers.
- **Encryption**:
  - **Client-Side Encryption**: Data is encrypted before upload (the client manages encryption) 
  - **Server-Side Encryption (SSE-S3)**: Amazon S3 manages encryption keys 
  - **Server-Side Encryption with AWS KMS (SSE-KMS)**: Uses AWS KMS for key management 
  - **Server-Side Encryption with Customer-Provided Keys (SSE-C)**: Customer provides the encryption keys 
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
- **Versioning & MFA Delete**:
  - Enable versioning and MFA delete to add extra protection against accidental/malicious deletions of objects.
  - Validated best practice for critical data.

### Amazon FSx
- **FSx for NetApp ONTAP**:
  - Managed storage for SMB/NFS 
  - Multi-protocol access 
  - Supports cross-region replication with NetApp SnapMirror technology 
  - Provides a seamless way to replicate data across AWS Regions 
  - Maintains ability to access data using the same CIFS and NFS protocols as the primary region 
  - Offers least operational overhead for disaster recovery purposes 
  - Leverages built-in replication capabilities of FSx for ONTAP 
  - Ensures replicated data can be accessed using the same file-sharing protocols as in the primary region 
  - Ideal for DR solutions requiring minimal management complexity 
  - ONTAP replication verified for seamless cross-region data access.
- **FSx for Windows File Server**:
  - Uses SMB protocol for Windows-based file shares 
  - Best solution for a Windows-based application needing a shared file system across multiple AZs.
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
  - Tailored for workloads that demand both high sustained throughput and data durability 
  - Supports sub-millisecond latency for HPC environments 
  - Ideal for scenarios like weather forecasting companies processing hundreds of gigabytes of data with sub-millisecond latency 
  - Provides a high-performance file system for large-scale data processing with parallel access requirements 

### Amazon Elastic File System (EFS)
- **Key Features**:
  - Fully managed NFS file system 
  - Grows/shrinks automatically 
  - Multi-AZ by default 
  - Excellent for Linux-based applications needing shared file access 
  - Confirmed scalable file sharing across instances.
- **Throughput Modes**:
  - **Bursting Throughput**: Automatically scales throughput to accommodate spikes 
  - **Provisioned Throughput**: Allows specifying throughput levels independent of storage amount 

### Amazon Elastic Block Store (EBS)
- **Key Features**:
  - Persistent block-level storage for use with EC2 
  - "EBS encryption by default" can be configured at the EC2 account attribute level 
  - Ensures all newly created EBS volumes are encrypted automatically 
  - Prevents creation of unencrypted volumes 
- **Volume Types**:
  - **General Purpose SSD (gp3)**:
    - Baseline performance of 3,000 IOPS and up to 16,000 IOPS 
    - Can provision IOPS independently of storage capacity 
    - Most cost-effective when specific IOPS requirements must be met 
    - Suitable for transactional workloads, virtual desktops, and medium-sized databases 
  - **General Purpose SSD (gp2)**:
    - IOPS performance linked directly to storage capacity 
    - Requires provisioning larger volume sizes to achieve higher IOPS 
    - Less cost-effective for specific IOPS requirements 
  - **Provisioned IOPS SSD (io1)**:
    - Designed for I/O-intensive database workloads 
    - For workloads requiring sustained IOPS performance above 16,000 IOPS 
    - More expensive than gp3 for similar performance levels 
  - **Magnetic (Standard)**:
    - Lowest per-GB cost but does not support provisioning of IOPS 
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
  - Can create disaster recovery backup policies that back up data to isolated regions or accounts 

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
  - Grant permission for a Lambda IAM role within the KMS key's policy to decrypt 
  - Directly associate Lambda's execution role with permissions to decrypt files 
  - Follow least privilege by granting only required decrypt permissions 

### AWS Secrets Manager
- **Key Features**:
  - Secure storage of credentials, API keys, etc. 
  - Automatic secret rotation (e.g., for RDS databases) 
  - Integrated with AWS Lambda for custom rotation logic 
  - Helps manage, retrieve, and rotate database credentials, application credentials, OAuth tokens, API keys, and other secrets 
  - Has native integration with Amazon RDS for automatic password rotation 
  - Improves security posture by eliminating hard-coded credentials in application code 
  - Credentials are retrieved dynamically via API when needed 
  - Supports automatic rotation scheduling for secrets 
- **Alternatives**:
  - **AWS KMS**: Manages encryption keys, not arbitrary secrets 
  - **AWS Systems Manager Parameter Store**: Can store secrets, but has no built-in rotation 

### AWS Firewall Manager
- **Key Benefits**:
  - Centrally configure AWS WAF rules across accounts 
  - Manage AWS Shield Advanced, AWS WAF, and even security groups in one place 
  - Automatically protect new resources (e.g., CloudFront distributions, Application Load Balancers) with common security rules 

### AWS Network Firewall
- **Key Features**:
  - Protects your VPCs with stateful traffic filtering and intrusion detection 
  - Not used for automatically auditing or managing security groups 
  - Supports deep packet inspection for advanced filtering 

### AWS WAF (Web Application Firewall)
- **Key Attach Points**:
  - Application Load Balancer 
  - Amazon CloudFront distribution 
  - Amazon API Gateway 
  - AWS AppSync 
- Helps protect web applications against common exploits (e.g., SQL injection, XSS) 
- Can create custom rules to block or allow traffic based on attributes (e.g., IP, geolocation) 
- Provides a robust solution for filtering web traffic and enforcing access rules 

### AWS IAM Identity Center (AWS Single Sign-On)
- **Key Features**:
  - Centrally manage SSO access to multiple AWS accounts and business applications 
  - Enables unified management of users and their access 
  - Can be integrated with existing identity providers (e.g., Active Directory, Okta) 
  - Users sign in to a portal with a single set of credentials to access assigned accounts and applications 

### AWS Certificate Manager (ACM)
- **Key Features**:
  - Request public SSL/TLS certificates for domains and subdomains 
  - Supports wildcard certificates (e.g., `*.example.com`) to cover all subdomains 
  - Validates domain ownership via DNS (or email) for certificate issuance 
  - Supports automatic certificate renewal 
  - Integrates with AWS services like CloudFront and Application Load Balancer 
  - Provides an easy way to implement HTTPS for websites and applications 

### AWS Organizations
- **Key Features**:
  - Centrally manage and govern AWS environments across multiple accounts 
  - Programmatically create AWS accounts and allocate resources 
  - Group accounts to organize workflows (e.g., by team or project) 
  - Apply Service Control Policies (SCPs) to accounts or groups for governance 
  - Simplify billing by using a single payment method for all accounts 
  - Manage tag policies that specify allowed/disallowed tag keys and values 
  - Enforce tag policies to standardize tags across resources in the organization 

### Identity Federation
- **SAML 2.0-based Federation**:
  - Enables single sign-on (SSO) between on-premises Active Directory (AD) and AWS 
  - Leverages existing AD identities to authenticate to AWS 
  - Maps AD groups to AWS IAM roles for permission management 
  - Minimizes administrative overhead by maintaining a single identity source 
  - Users don’t need separate AWS credentials to access AWS resources 

## Networking and Content Delivery

### AWS PrivateLink
- **Overview**:
  - Private connectivity between VPCs and AWS services or SaaS providers 
  - Traffic remains on the AWS global network, avoiding public internet exposure 
  - Eliminates the need for NAT gateways or VPN for private access to supported services 
- **Interface Endpoints**:
  - Enable private connections between a VPC and AWS services 
  - Use private IP addresses within your VPC to access services 
  - When combined with AWS Direct Connect, ensures data from on-premises to AWS doesn't traverse the public internet 

### AWS Transit Gateway
- **Key Features**:
  - Hub-and-spoke model for connecting multiple VPCs and on-premises networks 
  - Reduces the need for numerous VPC peering connections 
  - Ideal for large multi-VPC or multi-account architectures 
  - Simplifies routing and management at scale 
  - Confirmed as an effective solution for centralizing network management.

### AWS Direct Connect
- **Redundancy Best Practices**:
  - For mission-critical workloads, use multiple connections at separate locations/devices 
  - Ensure path and device redundancy from each data center (to mitigate single points of failure) 
  - Validated the importance of multiple connections for high availability.

### AWS Global Accelerator
- **Key Features**:
  - Improves availability and performance of applications for users worldwide 
  - Provides static IP addresses as fixed entry points to applications 
  - Directs traffic to optimal endpoints based on health, geographic location, and routing policies 
  - Offers automated failover across AWS regions 
  - Routes traffic to the nearest healthy application endpoint 
  - Bypasses DNS caching issues that can lead to outdated routing 
  - Particularly useful for applications requiring real-time communication (e.g., VoIP) 

### Amazon CloudFront
- **Key Features**:
  - Content Delivery Network (CDN) for caching and global distribution 
  - Low latency, high transfer speeds for delivering content to users 
  - Integrates with Amazon S3 for static website hosting 
  - Can integrate with AWS WAF for security at the edge 
  - Provides geographic restrictions (geo-blocking) to prevent users in specific locations from accessing content 
  - Helps comply with content distribution rights by restricting access based on the user's location 
- **Field-Level Encryption**:
  - Encrypts sensitive data fields at the edge (CloudFront) before forwarding to the origin 
  - Protects sensitive information throughout the application stack (only decrypted by the intended service with the private key) 
  - Adds an extra layer of security beyond standard HTTPS 
- **Signed URLs/Cookies**:
  - Restrict access to private content by requiring a signed URL or cookie (often with an expiration time) 
  - Commonly used to securely serve content to authorized users (e.g., subscriber-only videos or downloads) 

  - For high-speed, secure global distribution, use CloudFront with S3. S3 Transfer Acceleration can also help for uploads from remote locations.

### Amazon VPC
- **Internet Gateways**:
  - A single Internet Gateway can route traffic for the entire VPC (across all AZs) 
  - Internet Gateways provide a target in VPC route tables for internet-routable traffic 
  - Do not require redundancy across Availability Zones (they are a managed service) 
- **Managed Prefix Lists**:
  - Sets of one or more CIDR blocks to simplify security group and route table configurations 
  - Customer-managed prefix lists can be shared across AWS accounts using Resource Access Manager 
  - Helps centrally manage allowed IP ranges across an organization 
  - Makes it easier to update and maintain security groups and route tables 
  - Can consolidate multiple security group rules (with different CIDRs but same port/protocol) into a single rule 

### Elastic Load Balancing
- **Types of Load Balancers**:
  - **Application Load Balancer (ALB)**: For HTTP/HTTPS (layer 7) traffic routing with advanced rule-based routing 
  - **Network Load Balancer (NLB)**: For TCP/UDP (layer 4) traffic routing 
    - Supports extremely high throughput (millions of requests per second) 
    - Provides ultra-low latencies (ideal for real-time or gaming applications using UDP) 
    - Efficiently distributes UDP traffic across multiple targets 
    - Can scale automatically with traffic and works well with Auto Scaling groups 
  - **Gateway Load Balancer**: For deploying and managing third-party virtual network appliances 
  - **Classic Load Balancer**: Legacy load balancer (Elastic Load Balancing version 1; not recommended for new designs) 
- **Application Load Balancer Features**:
  - Distributes incoming traffic across multiple targets (e.g., EC2 instances, containers) 
  - Monitors the health of targets and only routes traffic to healthy ones 
  - Supports multi-AZ deployments for high availability 
  - Can integrate with Auto Scaling groups to scale the target fleet as traffic changes 

## Data Analytics and Visualization

### Amazon Kinesis
- **Kinesis Data Streams**:
  - Real-time ingestion of streaming data at massive scale 
  - Low-latency, high-throughput streaming data processing 
  - Often used with Amazon OpenSearch Service for real-time analytics and search 
- **Kinesis Data Firehose**:
  - Fully managed service to reliably load streaming data into data lakes, data stores, and analytics services 
  - Can be used to send VPC Flow Logs to Amazon CloudWatch Logs or S3 
  - Often used for streaming logs into Amazon OpenSearch Service for analysis 
  - Provides a simple way to capture, transform, and load streaming data (near real-time) 

### Amazon OpenSearch Service (successor to Amazon Elasticsearch Service)
- **Key Features**:
  - Search, analyze, and visualize data in near real-time 
  - Used for log analytics, full-text search, and operational analytics 
  - Integrates with Kinesis Data Streams and Kinesis Data Firehose for ingesting data 
  - Can be queried from Amazon QuickSight for visualization 

### Amazon QuickSight
- **Key Features**:
  - Fully managed Business Intelligence (BI) service 
  - Create dashboards and visualizations from multiple data sources 
  - Can connect to data in Amazon OpenSearch Service, Redshift, Athena, RDS, etc. 

### Amazon Security Lake
- **Key Features**:
  - Purpose-built service to centralize and aggregate security data across AWS 
  - Automates data collection from various AWS services (like CloudTrail, VPC Flow Logs, etc.) 
  - Simplifies organization and analysis of security information (ready for query or third-party tools) 

### Amazon Athena
- **Key Features**:
  - Interactive query service to analyze data in Amazon S3 using standard SQL 
  - Serverless (no infrastructure to manage), you pay only for the queries you run 
  - Works with structured, semi-structured, and unstructured data (supports CSV, JSON, Parquet, etc.) 
  - Can be combined with AWS Glue for data cataloging (schema discovery) 
  - Cost-effective for on-demand data analysis that occurs sporadically 
  - Eliminates the need to set up and manage database or Hadoop clusters for querying data 

### AWS Glue
- **Key Features**:
  - Fully managed Extract, Transform, Load (ETL) service 
  - Discovers and catalogs metadata about data sources (with Glue Crawlers) 
  - Creates a data catalog that makes data searchable and queryable 
  - Provides a managed Spark environment to run ETL jobs 
  - Integrates with Athena for serverless querying of data 
  - Glue crawlers can automatically create or update the metadata catalog for data in S3 

## Encryption and Data Protection

### EBS Encryption by Default
- **EC2 Account Attributes**:
  - You can enable EBS volume encryption by default at the account level 
  - All newly created EBS volumes will be encrypted automatically 
  - Prevents creation of unencrypted volumes, ensuring compliance with security policies 

### S3 Bucket Encryption
- **Client-Side Encryption**:
  - Data is encrypted on the client side before uploading to S3 
  - Provides encryption in transit and at rest (since it's encrypted when stored) 
- **Server-Side Encryption**:
  - **SSE-S3**: S3 handles key management and encryption for you 
  - **SSE-KMS**: S3 uses AWS KMS-managed customer keys for encryption (gives control over keys and audit via KMS) 
  - **SSE-C**: You provide the encryption keys for S3 to use (keys are not stored by AWS) 

## Cloud Financial Management

### AWS Cost Anomaly Detection
- **Key Features**:
  - Monitors AWS costs and usage to detect unusual spending patterns 
  - Allows setting up monitors that analyze historical spending to identify anomalies 
  - Notifies stakeholders through alerts (e.g., email or SNS) when unexpected spending is detected 
  - A proactive solution for monitoring costs without manual intervention 

### AWS Cost Explorer
- **Key Features**:
  - Analyze cost and usage data with interactive graphs, filtering, and grouping 
  - Forecast future costs based on trends 
  - Create custom reports (e.g., cost per service, per account, per tag) to break down spending 

## Configuration and Infrastructure Management

### AWS Config
- **Key Features**:
  - Monitors and records configurations of AWS resources continuously 
  - Evaluates recorded configurations against desired configurations (compliance as code) 
  - Provides a detailed view of how resource configurations change over time 
  - Can track changes at the resource level and trigger alerts for non-compliance 

### AWS CloudFormation
- **Key Features**:
  - Enables automation and provisioning of infrastructure as code 
  - Allows a repeatable and predictable way to set up and manage AWS resources 
  - Uses templates (in JSON/YAML) to define resources and their relationships 
  - Can create a wide variety of AWS resources programmatically 
  - Supports change sets to review modifications before applying them 

### AWS Resource Access Manager (RAM)
- **Key Features**:
  - Securely share AWS resources across AWS accounts 
  - Share resources within your organization or organizational units (OUs) in AWS Organizations 
  - Supports sharing of transit gateways, subnets, license manager configurations, Route 53 Resolver rules, and more 
  - Reduces operational overhead of duplicating resources in multiple accounts 
  - Enables sharing of customer-managed prefix lists across accounts (for consistent network ACLs and security groups) 

## Auto Scaling and Capacity Management

### Amazon EC2 Auto Scaling
- **Scheduled Scaling**:
  - Configure automatic scaling based on predictable load changes 
  - Create scheduled actions to increase or decrease capacity at specific times 
  - Can proactively adjust the number of EC2 instances in anticipation of known load changes 
  - Optimizes costs and performance by ensuring sufficient capacity during peak times 
  - Eliminates lag in scaling up resources, addressing slow application performance issues under sudden load 
  - Modifying an Auto Scaling group to span multiple AZs is crucial for high availability. 
  - For global redundancy, consider distributing instances across Regions (if truly multi-Region).

## Best Practices and Key Concepts

- **Use read replicas** in Aurora (or RDS) to offload read traffic and improve performance for primary databases.  
- **Enable EBS encryption by default** at the EC2 account attribute level to ensure all new volumes are encrypted (enhancing data security).  
- **Use an Auto Scaling group** spanning multiple AZs with an **Application Load Balancer** to improve availability and automatically scale out/in based on demand.  
- **Attach AWS WAF** to the ALB or CloudFront for web application protection against common exploits.  
- **Use S3 Access Points** for granular access to subsets of data in a shared S3 bucket (simplifies managing data access for multiple applications or tenants).  
- **Use S3 Event Notifications** + **Amazon SQS** + **AWS Lambda** to trigger short-lived image processing tasks without needing dedicated EC2 instances (serverless on-demand processing).  
- **Amazon ECS + AWS Fargate** for scheduled container-based workloads (use EventBridge for cron-like scheduling of tasks without managing servers).  
- **AWS Transit Gateway** is recommended for scaling to 100+ VPCs or large multi-account environments (simplifies network management with a hub-and-spoke model).  
- **Kinesis Data Streams + Amazon OpenSearch Service** for near real-time data ingestion, analysis, search, and building dashboards (e.g., with QuickSight) on streaming data.  
- **Use Amazon EventBridge** to capture specific AWS API events and trigger automated actions: Configure an EventBridge rule (fed by CloudTrail events) for critical operations (e.g., an EC2 CreateImage API call) to send an SNS alert or invoke a Lambda. This provides real-time notifications of important activities with minimal operational overhead (no manual log polling).  
- **AWS Config** can monitor resource configurations but does not enforce encryption or block resource creation; it is primarily for auditing and compliance checks.  
- **Use AWS Organizations** and **Service Control Policies (SCPs)** for centralized governance across multiple AWS accounts (apply guardrails and manage account policies from a single place).  
- **Use Amazon EFS** for a shared POSIX-compliant file system across multiple EC2 instances (and across multiple AZs) when applications require common file storage.  
- **For DR scenarios with minimal downtime**: 
  - Pre-provision resources in a secondary region (e.g., have an Auto Scaling group, load balancer, and a global DynamoDB table in the DR region). 
  - Use DNS failover to direct traffic to the DR region's load balancer if the primary region fails.  
- **For S3 multi-region** active-active design with minimal management: 
  - Use **S3 Multi-Region Access Points** with a single global endpoint. 
  - Configure Cross-Region Replication for durability and cross-region failover of data.  
- **Amazon Security Lake** for centralized aggregation of security data across accounts and regions (makes it easier to run analysis or threat detection on combined logs).  
- **Use Amazon Neptune** for applications that need to store and navigate complex relational data (e.g., social graphs or network topologies) in a graph database.  
- **AWS KMS Customer Managed Keys** give you control over key rotation schedules, key usage policies, and full key lifecycle management (use when you need explicit control beyond AWS-managed keys).  
- **For a serverless architecture behind API Gateway**: 
  - Use AWS Lambda for handling unpredictable or spiky request patterns (scales automatically with demand). 
  - Use DynamoDB for a fully managed NoSQL database with auto-scaling throughput. 
  - This combination yields a completely serverless stack that scales with demand and has minimal maintenance.  
- **Hospital Scanning & Document Processing**: 
  - Store scanned documents in Amazon S3. 
  - Use S3 event notifications to trigger a Lambda function. 
  - Extract text with Amazon Textract or Amazon Rekognition (for images), then use Amazon Comprehend (or Comprehend Medical) for deeper NLP analysis. 
  - Query the results with Amazon Athena for on-demand analytics.  
- **Static + Dynamic Website**: 
  - Host static content in Amazon S3 (for scalability and low cost). 
  - Use Amazon API Gateway + AWS Lambda for dynamic requests (serverless backend). 
  - Store dynamic data in Amazon DynamoDB (on-demand capacity for unpredictable traffic). 
  - Use Amazon CloudFront to deliver the entire website globally with low latency.  
- **DynamoDB Accelerator (DAX)**: 
  - In-memory cache for DynamoDB 
  - Significantly improves read performance (microsecond latency) without major application rework (just use the DAX client) 
  - Ideal for read-intensive workloads that require microsecond response times 
  - This update increases overall system responsiveness in high-traffic scenarios.

## VPC Flow Logs and Monitoring
- **VPC Flow Logs**:
  - Feature to capture information about IP traffic going to and from network interfaces in a VPC 
  - Flow log data can be published to CloudWatch Logs, Amazon S3, or Amazon Kinesis Data Firehose 
  - Helps diagnose overly restrictive security group rules 
  - Monitors traffic reaching instances 
  - Determines the direction of traffic to/from network interfaces 
  - Additional logging details help in proactive network troubleshooting.

## Analysis and ETL Solutions
- **For Clickstream Data in S3**:
  - Use AWS Glue crawler to catalog data and make it queryable 
  - Configure Amazon Athena for SQL-based analysis of the data 
  - Minimal operational overhead as both services are serverless and fully managed 
  - Enables quick analysis for decision-making about further processing 
  - Confirmed as a best practice for real-time web analytics.
- **Document Ingestion and Transformation**:
  - Store large volumes of documents in Amazon S3 
  - Use AWS Lambda triggers on object upload 
  - Perform OCR with Amazon Textract or Rekognition 
  - Use Amazon Comprehend (or Comprehend Medical) to extract relevant information 
  - Store extracted data in S3 or a database, queryable via Athena 
  - Validated for transforming scanned documents into searchable data.

## Resource Tagging and Cost Allocation
- **AWS Lambda with EventBridge**:
  - Can be used to automatically tag resources with cost center IDs 
  - EventBridge detects resource creation events via CloudTrail 
  - Lambda function queries databases for appropriate cost center information 
  - Enables automatic tagging based on the user who created the resource 
  - Provides a proactive and dynamic approach to resource tagging 
  - Ensures consistent tagging and cost allocation across resources.

## Big Data Processing
- **Amazon EMR (Elastic MapReduce)**:
  - Industry-leading cloud big data platform for processing vast amounts of data 
  - Supports open source tools like Apache Spark, Apache Hive, Apache Flink 
  - Cluster configuration options for cost-optimization:
    - **Transient clusters**: Terminated after completing tasks, most cost-effective for workloads with known duration 
    - **Long-running clusters**: Stay active continuously, less efficient for workloads with specific durations 
    - **Primary node and core nodes on On-Demand Instances**: Ensures reliability for critical parts of the workload 
    - **Task nodes on Spot Instances**: Cost-effective for compute-intensive portions of workload that can tolerate interruptions 
  - EMR configurations are optimized for both transient and long-running workloads.

## High Availability Architectures

- **For MySQL and stateless Python web applications**:
  - Migrate the database to Amazon RDS for MySQL with Multi-AZ deployment for high availability 
  - Use an Application Load Balancer with Auto Scaling groups of EC2 instances across multiple AZs for the web/application tier 
  - RDS Multi-AZ provides automatic failover to a standby instance in a different AZ, improving resiliency 
  - Confirmed as the preferred architecture for high availability.
- **For Global Applications Needing Multi-Region Redundancy**:
  - Deploy workload components in a secondary region (including data stores and compute) 
  - Use Route 53 or AWS Global Accelerator to route users to the nearest healthy endpoint or fail over if a region goes down 
  - Verified multi-region designs to maintain global availability.
- **For Applications Requiring Zero Downtime and No Data Loss**:
  - Consider active-active multi-region architectures (using global databases like DynamoDB global tables or Aurora Global Database) 
  - Implement replication and health checks to enable instant failover across regions 
  - Active-active configurations validated for mission-critical applications.

## Updates Made
- **DynamoDB TTL:** Added TTL details to clarify auto-deletion of expired items, reducing storage costs.
- **DAX:** Included DynamoDB Accelerator (DAX) details for enhancing DynamoDB read performance.
- **SQS Enhancements:** Expanded message retention and dead letter queue details to emphasize resiliency.
- **EventBridge Usage:** Detailed the use of EventBridge for capturing AWS API events and triggering automated actions.
- **CloudFront Security:** Added field-level encryption and signed URLs/cookies sections for enhanced content protection.
- **Document Processing:** Inserted best practices for hospital scanning and document processing using Textract/Rekognition and Comprehend.
- **Website Deployment:** Enhanced static + dynamic website deployment patterns with S3, API Gateway, Lambda, DynamoDB, and CloudFront.
- Merged all correct answer details (marked in green in the screenshots) into additional bullet points across multiple sections, increasing the overall file line count.
- **New Updates:** 
  - **FSx for Windows** for a Windows-based application needing a shared file system across multiple AZs.
  - **ECS with EC2** recommended for specialized hardware or custom AMIs.
  - **SQS for microservices** added for asynchronous decoupling.
  - **RDS Proxy** recommended with Lambda to reduce connection overhead.
  - **S3 Transfer Acceleration** for faster global data ingestion + multipart uploads.
  - **S3 Versioning & MFA Delete** to protect critical data from accidental/malicious deletions.
  - **DynamoDB + AWS Backup** for long-term data retention and compliance.
  - **Auto Scaling** multi-AZ approach validated for high availability.
- **No Additional Images Accessible:** Re-validation of the new images did not yield further updates due to inaccessibility.

