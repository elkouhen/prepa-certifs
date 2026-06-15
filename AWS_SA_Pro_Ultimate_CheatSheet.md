# AWS Certified Solutions Architect Professional (SAP-C02) - Ultimate Cheat Sheet

Legend: 🔥⭐ = Estimated Exam Frequency/Importance (1 to 5 stars).

---

# Part 1 - Multi-Account Organization & Governance

## AWS Organizations

Definition: Centralized management service to consolidate multiple AWS accounts into hierarchical Organizational Units (OUs), enabling consolidated billing, centralized logging, and programmatic account creation.

Key Points:
- **Management (Master) Account:** Best practice dictates hosting **ZERO** production workloads here. Use it strictly for billing, SCP roots, and delegated administration.
- **Consolidated Billing:** Aggregates usage across accounts to trigger volume discounts. **Reserved Instances (RI) and Savings Plans (SP) sharing** can be enabled/disabled at the Organization level to distribute unutilized discounts.
- **Delegated Administrator:** Enables specific member accounts to manage AWS services organization-wide (e.g., GuardDuty, Security Hub, AWS Config) without granting full access to the Management account.

🔥⭐⭐⭐⭐⭐

---

## Service Control Policies (SCP)

Definition: Organizational policies applied at the Root, OU, or Account level that specify the **MAXIMUM available permissions** (guardrails) for member accounts. They do not grant permissions; they only filter them.



Trap: SCP Evaluation Logic
- **Explicit Deny Overrides All:** If an action is denied by an SCP, no IAM policy (even `AdministratorAccess`) can override it.
- **Missing Explicit Allow:** By default, Organizations attaches the `FullAWSAccess` SCP. If you create a custom SCP, you must explicitly include an `Allow` statement for allowed actions, or everything else is implicitly denied.
- **Root Exceptions:** SCPs do **NOT** apply to the Management Account. They do apply to the `root` user of **member accounts**.
- **Service Inclusions:** SCPs do not affect service-linked roles or specific system operations (like CloudFront key populating).

Mnemonic: SCP = The "sieve" or ceiling. IAM Policy = The "door" or key. You need both to be open to pass through.

🔥⭐⭐⭐⭐⭐

---

## AWS Control Tower

Definition: An orchestration service that automates the setup of a baseline multi-account framework (**Landing Zone**) aligned with AWS Well-Architected best practices. It configures Core OUs (Security, Sandbox), a centralized log repository (Log Archive account), an Audit account, and account provisioning workflows via Account Factory.

Guardrails Classification:
- **Preventive Guardrails:** Implemented using **SCPs** (prevents actions from occurring).
- **Detective Guardrails:** Implemented using **AWS Config Rules** (detects non-compliance after actions occur).
- **Proactive Guardrails:** Implemented using **AWS CloudFormation Hooks** (scans resources before deployment).

🔥⭐⭐⭐⭐

---

## AWS Resource Access Manager (RAM)

Definition: Centralized resource sharing service that allows accounts to securely share specific AWS resources within an Organization (or with external accounts via IAM) without duplicating infrastructure or creating complex peering.

Core Architecture Concept: **Shared VPC**
- A centralized "Networking" account owns the VPC, Subnets, Route Tables, and Transit Gateway attachments.
- It shares specific subnets with "Application" accounts via AWS RAM.
- **Permissions Isolation:** Application accounts can deploy resources (EC2, RDS, ECS) inside the shared subnets, but they **cannot** see or modify the underlying network topology, security groups of other accounts, or route tables.
- **Security Groups:** Resources in a Shared VPC can only associate with Security Groups owned by their **own account**. They can, however, reference Security Groups from other accounts in their rules.

🔥⭐⭐⭐⭐⭐

---

## AWS Service Catalog

Definition: Enables organizations to create, govern, and manage catalogs of IT services (Portfolios of Products) that are approved for use on AWS. Products are defined using CloudFormation templates or Terraform configurations.

Key Exam Mechanics: **Launch Constraints**
- Users can browse and launch approved infrastructure in a self-service manner *without* having direct IAM permissions to create the underlying resources (e.g., a user can launch an RDS database product via Service Catalog even if their IAM policy explicitly denies `rds:CreateDBInstance`).
- Service Catalog assumes a pre-configured **IAM Execution Role (Launch Constraint Role)** to deploy the resources safely.

🔥⭐⭐⭐

---

## AWS Config

Definition: Provides a detailed inventory of AWS resources, captures configuration history, and evaluates real-time state changes against compliance rules (**Config Rules**).

Advanced Multi-Account Architecture:
- **Aggregator:** Collects configuration history and compliance data from multiple accounts and multiple regions into a single, designated "Security Audit" account.
- **Conformance Packs:** Packaged collections of AWS Config rules and remediation actions that can be deployed atomically across an entire Organization via the Management account.
- **Remediation:** Automates compliance fixes using **AWS Systems Manager (SSM) Automation Documents** (e.g., automatically terminating an EC2 instance if it's launched with an unencrypted EBS volume).

🔥⭐⭐⭐⭐

---

## AWS IAM Identity Center (Successor to AWS SSO)

Definition: A centralized administrative portal to manage single sign-on access to multiple AWS accounts and cloud applications. It coordinates with external identity providers (IdPs) via SAML 2.0 or manages identities natively.

Key Concepts:
- **Permission Sets:** Templates defined centrally in IAM Identity Center that specify IAM policies. When assigned to a group or user for a specific account, Identity Center automatically provisions corresponding IAM roles in that target member account.
- **Directory Integration:** Supports automatic provisioning from Azure AD, Okta, Ping, or on-premises Active Directory using the **SCIM (System for Cross-domain Identity Management)** protocol.

🔥⭐⭐⭐⭐

---

## Permissions Boundaries vs SCPs vs IAM Policies

The exam heavily tests the exact intersection of these authorization barriers.

| Feature | Scope | Applies To | Can Override Explicit Deny? |
| :--- | :--- | :--- | :--- |
| **IAM Policy** | Identity or Resource | Specific User / Role / Resource | No |
| **Permissions Boundary** | Identity Level | Specific IAM User or Role only | No (Sets maximum boundary for that identity) |
| **SCP** | Account Level | Entire Account (including local root) | No (Sets maximum boundary for the account) |



- **Permissions Boundary Use Case:** Delegating administrative powers. For example, allowing a DevOps team to create IAM roles for their applications, but forcing them to attach a Permissions Boundary to those roles so they cannot escalate their own privileges.

🔥⭐⭐⭐⭐⭐

---

## Identity Federation (SAML 2.0, OIDC, Amazon Cognito)

Definition: Grants external identities (corporate directories or consumer social logins) temporary access to AWS resources without provisioning long-lived IAM users, leveraging **AWS STS (Security Token Service)**.

Key API Actions & Components:
- **SAML 2.0 Identity Providers:** Used for Enterprise Single Sign-On. The enterprise IdP authenticates the employee and generates an assertion token. The user calls `sts:AssumeRoleWithSAML` to exchange the assertion for short-lived AWS credentials.
- **Web Identity Federation / OIDC:** Used for public client applications (mobile/web apps). Authenticates users against public providers (Google, Apple, Amazon) via `sts:AssumeRoleWithWebIdentity`.
- **Amazon Cognito User Pools:** A user directory that handles registration, login, MFA, and JWT token issuance for custom applications.
- **Amazon Cognito Identity Pools:** Translates identity tokens (from Cognito User Pools, Google, or Anonymous Guests) into temporary **AWS IAM Credentials** to allow client apps to read/write directly to services like S3 or DynamoDB safely.

🔥⭐⭐⭐⭐

---

# Part 2 - Advanced Networking

## VPC Peering vs Transit Gateway vs PrivateLink

Choosing the correct inter-VPC and hybrid networking pattern is a cornerstone of the SAP-C02 exam.

- **VPC Peering:** Simple, point-to-point connection. **Non-transitive** (If A is peered with B, and B is peered with C, A cannot communicate with C through B). Mesh routing scales poorly ($N(N-1)/2$ connections required). No data processing fees, making it highly cost-effective for massive data replication between two specific VPCs. Cannot have overlapping CIDR blocks.
- **Transit Gateway (TGW):** A centralized regional network hub (router) that aggregates VPCs, VPNs, and Direct Connect links. Supports **transitive routing**. Uses **TGW Route Tables** associated with specific attachments to isolate routing domains (e.g., separating Production VPC attachments from Development VPC attachments). Can peer TGWs across regions. Charges per attachment + per GB data processed.
- **AWS PrivateLink (Interface Endpoints):** Exposes a service hosted behind a **Network Load Balancer (NLB)** into a consumer VPC as a private Elastic Network Interface (ENI). Traffic travels completely over the AWS backbone. 
  - **Overlapping CIDRs:** This is the *only* choice if you need to connect two VPCs that have identical or overlapping IP spaces.
  - **Security:** The consumer VPC only gains access to the specific exposed service ports, not the entire producer network.

🔥⭐⭐⭐⭐⭐

---

## AWS Direct Connect (DX)

Definition: A dedicated physical network connection from an on-premises datacenter or colocation facility directly to an AWS Direct Connect Location, bypassing internet service providers entirely.

Advanced Components:
- **Virtual Interfaces (VIFs):**
  - **Public VIF:** Access public AWS endpoints (S3, DynamoDB, EC2 public IPs) over the private DX line.
  - **Private VIF:** Connects to a Virtual Private Gateway (VGW) of a single VPC using private IP addresses.
  - **Transit VIF:** Connects to an **AWS Direct Connect Gateway** associated with a **Transit Gateway**, allowing access to up to thousands of VPCs across any AWS region (except China).
- **Resiliency Architectures:**
  - *High Resiliency:* 2 connections at a single DX location (protects against device failure).
  - *Maximum Resiliency:* 2 active-active connections terminating at **two completely independent DX locations** using independent customer gateways (protects against location-wide disasters).
- **Encryption:** DX is **NOT** encrypted by default. To secure data in transit:
  - Implement **MACsec** (Layer 2 encryption, requires a dedicated connection and supported hardware at the DX location).
  - Provision an **AWS Site-to-Site VPN over a Public VIF** (Layer 3 IPsec encryption over the private link).

🔥⭐⭐⭐⭐

---

## Amazon Route 53 - Advanced Routing & Hybrid DNS

Advanced DNS routing forms the basis for global high availability and disaster recovery plans.

Routing Policies:
- **Latency-Based Routing:** Routes traffic to the AWS region that provides the lowest round-trip time for the end-user.
- **Failover Routing:** Configures active-passive failover. Route 53 monitors primary endpoints using **Route 53 Health Checks**. If the primary endpoint fails, DNS responses automatically pivot to the secondary DR endpoint.
- **Weighted Routing:** Distributes traffic based on numerical weights (perfect for canary deployments or migrating workloads gradually between old and new infrastructures).
- **Multi-Value Answer:** Returns up to 8 healthy records in response to a single query. Provides a lightweight client-side load balancing mechanism with health checks.

Hybrid DNS Architectures (On-Premises ↔ AWS):
- **Route 53 Resolver Endpoints:**
  - **Inbound Endpoint:** Allows on-premises DNS servers to forward queries for internal AWS domains (`*.amazonaws.com`, custom private hosted zones) to Route 53.
  - **Outbound Endpoint:** Allows EC2 instances inside a VPC to forward custom DNS queries for corporate on-premises domains (`*.corporate.local`) to on-premises DNS resolvers based on **Route 53 Resolver Rules**.

🔥⭐⭐⭐⭐⭐

---

## Advanced Amazon CloudFront

Definition: A global Content Delivery Network (CDN) that optimizes delivery of static and dynamic web content via Edge Locations.

SAP-Level Architectures:
- **Origin Access Control (OAC):** Secures S3 origins by ensuring users can *only* access S3 buckets through CloudFront. Replaces the legacy Origin Access Identity (OAI) by adding support for AWS Signature Version 4 and encrypted S3 buckets.
- **Edge Compute Abstractions:**
  - **CloudFront Functions:** Lightweight, short-running JavaScript execution at the immediate edge. Optimized for high-volume, low-latency tasks (< 1ms execution time) like URL rewrites, header manipulations, and token validations.
  - **Lambda@Edge:** Full-featured Node.js/Python execution at Regional Edge Caches. Best for heavy compute tasks, complex transformations, and direct integration with external databases or third-party APIs.
- **Origin Groups (Failover):** Configures primary and secondary origins (e.g., an S3 bucket primary and an EC2/ALB secondary). CloudFront automatically retries the secondary origin if the primary returns specific HTTP status codes (500, 502, 503, 504).

🔥⭐⭐⭐⭐

---

## AWS Global Accelerator

Definition: A networking service that uses AWS's global private network backbone to route traffic from end-users to endpoints (ALBs, NLBs, or EC2 instances) across one or multiple AWS regions.

Key Architectural Differentiators from CloudFront:
- **IP Anycast:** Assigns two static, global Anycast IP addresses as fixed entry points. Eliminates DNS propagation caching delays during failover events.
- **Protocols:** Works for both HTTP/HTTPS and **Non-HTTP protocols (TCP and UDP)**, making it ideal for gaming, VoIP, and custom IoT data ingestion.
- **Failover Speed:** Monitors regional endpoint health and instantly shifts TCP traffic to a healthy region within seconds.

🔥⭐⭐⭐⭐

---

## Elastic Load Balancing (ELB) Deep Dive

Selecting, scaling, and configuring load balancers for highly available distributed topologies.

- **Application Load Balancer (ALB):** Evaluates Layer 7 traffic (HTTP/HTTPS/gRPC). Supports path-based, host-based, and query-string routing. Integrated natively with AWS Cognito or OIDC providers to authenticate users directly at the network boundary.
- **Network Load Balancer (NLB):** Evaluates Layer 4 traffic (TCP/UDP/TLS). Capable of scaling to handle millions of volatile requests per second with ultra-low latency. Provides **static Elastic IPs per Availability Zone**. Preserves the client's source IP address natively without requiring proxy protocols.
- **Gateway Load Balancer (GWLB):** Evaluates Layer 3 traffic. Acts as a transparent bump-in-the-wire to route all incoming/outgoing VPC traffic through virtual inline security appliances (third-party firewalls, deep-packet inspection, IDS/IPS). Uses the **GENEVE protocol** to encapsulate packets.

🔥⭐⭐⭐⭐

---

# Part 3 - Migration & Modernisation Strategies

## The 7 Rs of Cloud Migration

The exam presents detailed business constraints (downtime tolerances, available engineering skills, budget ceilings) and asks you to select the appropriate migration taxonomy.

1. **Rehost ("Lift and Shift"):** Moving applications to the cloud exactly as they are without architectural changes. Uses **AWS Application Migration Service (MGN)** to continuously replicate blocks at the OS level. Best for tight timelines.
2. **Replatform ("Lift, Tinker, and Shift"):** Making minor optimization changes during migration to reduce operational overhead without modifying the core architecture (e.g., migrating an on-premises database to **Amazon RDS**, or moving a containerized app to **Amazon ECS/Fargate**).
3. **Refactor / Re-architect:** Completely rewriting the application code to leverage cloud-native features like serverless, microservices, and auto-scaling. Maximizes agility but carries high migration complexity and cost.
4. **Relocate:** Moving VMware vSphere or hypervisor-managed containers straight into cloud abstractions without changing virtual machines (e.g., **VMware Cloud on AWS**). Zero code changes, zero OS adjustments.
5. **Repurchase:** Transitioning from a perpetual software license model to a third-party Software-as-a-Service (SaaS) platform (e.g., replacing an on-premises CRM with Salesforce).
6. **Retain:** Keeping workloads on-premises due to severe legacy dependencies, data sovereignty laws, or remaining hardware depreciation cycles.
7. **Retire:** Identifying and decommissioning redundant or unutilized applications that provide no real business value.

🔥⭐⭐⭐⭐⭐

---

## Large-Scale Data Transfer: DataSync vs Storage Gateway vs Snow Family

Choosing the appropriate data transfer tool depends entirely on your **available network bandwidth, dataset size, and replication frequency**.

- **AWS DataSync:** Automated, high-performance data transfer service used to move files over the network between on-premises storage (NFS, SMB, HDFS) and AWS (S3, EFS, FSx). Ideal for one-time migrations or scheduled, high-speed incremental synchronization over a functioning internet or DX link.
- **AWS Storage Gateway:** An appliance providing an on-premises cache connected to cloud storage for long-term hybrid architectures (not just temporary migrations).
  - **S3 File Gateway:** Exposes a standard file share (NFS/SMB) backed directly by S3 objects.
  - **Volume Gateway:** Exposes block-storage volumes via iSCSI. *Cached mode* stores primary data in S3 and caches frequently used data on-premises. *Stored mode* stores the full dataset on-premises and backs up asynchronous snapshots to S3.
  - **Tape Gateway:** Replaces physical magnetic tape libraries with a virtual tape infrastructure backed by S3 Glacier.
- **AWS Snow Family (Snowcone, Snowball Edge, Snowmobile):** Physical, ruggedized hardware appliances sent via courier to transfer petabytes of data offline when network bandwith is non-existent or too slow. 
  - *Rule of Thumb:* If data migration takes more than a week over an active network link, order a Snowball device. 
  - **Snowball Edge Compute Optimized** can run EC2 instances and EKS clusters locally at the disconnected edge.

🔥⭐⭐⭐⭐⭐

---

# Part 4 - Advanced Storage Architectures

## Amazon S3 Advanced Configurations

S3 acts as the central data lake repository in enterprise multi-account setups.

- **Replication Frameworks:**
  - **Cross-Region Replication (CRR):** Automatically replicates newly uploaded objects across different regions to satisfy Disaster Recovery (DR) compliance or minimize data access latency for global users.
  - **Same-Region Replication (SRR):** Replicates objects between buckets within the same region. Useful for consolidating logs from multiple staging environments or isolating ownership to a different account.
  - *Note:* Replication requires **S3 Versioning** enabled on both buckets. Historical objects are not replicated automatically unless **S3 Batch Replication** is executed.
- **S3 Object Lambda:** Intercepts standard S3 GET requests and routes them through an **AWS Lambda function** to transform data on the fly before returning it to the calling application (e.g., redacting PII, compressing files, masking values, or dynamic watermarking).
- **Multi-Region Access Points:** Provides a single, global routing hostname that maps to multiple S3 buckets across different regions. Automatically routes traffic via the lowest-latency AWS network paths.
- **S3 Object Lock:** Provides WORM (Write Once, Read Many) compliance to prevent object deletion or modification for a specified retention period.
  - *Governance Mode:* Protected users cannot delete objects unless they possess special IAM administrative overrides.
  - *Compliance Mode:* **Strict isolation.** No user, including the AWS Root Account, can delete or override the retention boundaries until the timer expires.

🔥⭐⭐⭐⭐⭐

---

## Enterprise File Systems: Amazon EFS vs Amazon FSx

- **Amazon EFS:** A fully managed, serverless, POSIX-compliant file system. Supports concurrent multi-AZ attachment for thousands of **Linux-based EC2 instances** or containers via NFSv4.
- **Amazon FSx for Windows File Server:** Fully managed Microsoft Windows file systems natively compatible with the **SMB protocol**. Integrates with corporate Active Directory domains for granular ACL permission management.
- **Amazon FSx for Lustre:** Ultra-high-performance file system optimized for High-Performance Computing (HPC), machine learning training jobs, and massive big data analytics. Natively links to S3 buckets, pulling data lazily on demand and pushing results back.
- **Amazon FSx for NetApp ONTAP:** Provides the full suite of NetApp data management features (snapshots, cloning, deduplication, thin provisioning) natively on AWS. Excellent for migrating on-premises NetApp storage arrays without updating storage scripts.

🔥⭐⭐⭐

---

# Part 5 - Database Orchestration & Scaling

## Amazon Aurora Global Architecture

- **Aurora Global Database:** Provisions a single primary database cluster in an active writing region, and replicates data continuously to up to 5 read-only secondary regions with a **storage-level replication lag under 1 second**.
- **Disaster Recovery (DR):** In the event of a total regional failure, a secondary region can be promoted to become the primary writer in less than 1 minute (Low RTO/RPO).
- **Aurora Serverless v2:** Automatically and instantly scales database compute capacity (measured in Aurora Capacity Units - ACUs) up or down based on actual application workload demand, preventing over-provisioning during idle periods.

🔥⭐⭐⭐⭐⭐

---

## Amazon DynamoDB Advanced Scaling

DynamoDB is the default NoSQL option for resilient, globally distributed microservices.

- **Global Tables:** Provides a fully managed, multi-region, **Active-Active** database solution. Writes can occur in any configured region, and updates are replicated multi-directionally. Conflicts are automatically evaluated via a "Last-Writer-Wins" strategy.
- **DynamoDB Accelerator (DAX):** A fully managed, highly available inline in-memory caching cluster that drops read latencies down from milliseconds to **microseconds** under extreme read-heavy demands. Does not require rewriting application logic (API compatible).
- **DynamoDB Streams:** Captures an ordered, chronological sequence of item-level modifications in a DynamoDB table. Emits events to **AWS Lambda** to trigger real-time downstream microservices (e.g., sending emails, populating search indexes, or auditing state changes).

🔥⭐⭐⭐⭐

---

## RDS Architectures: Multi-AZ vs Read Replicas

The exam expects clear separation of Availability concerns versus Scaling concerns.

| Dimension | Multi-AZ Deployment | Read Replicas |
| :--- | :--- | :--- |
| **Primary Purpose** | High Availability / Disaster Recovery | Read Scalability / Query Offloading |
| **Replication Mode** | **Synchronous** (Zero data loss) | **Asynchronous** (Eventual consistency) |
| **Target Read/Write** | Standby instance is completely hidden; cannot accept traffic. | Open for read queries only. Can span multiple AWS regions. |
| **Failover Action** | Fully automated DNS swap to the standby instance upon failure. | Requires manual promotion to standalone database instance status. |

🔥⭐⭐⭐⭐

---

# Part 6 - High-Scale Security & Cryptography

## AWS KMS Advanced Key Architecture

- **Key Ownership:**
  - *AWS Managed:* Free, created automatically when selecting encryption in AWS services. Cannot rotate manually, cannot edit key policies.
  - *Customer Managed (CMK):* Gives full control over key rotation schedules, IAM usage policies, and cryptographic operations.
  - *Imported Keys (BYOK):* You generate the key material inside your own on-premises HSM and securely transfer it to AWS KMS. **Caveat:** You are responsible for ensuring availability, and imported keys do not support automatic KMS rotation.
- **Multi-Region Keys:** Completely independent Customer Managed Keys provisioned across different AWS regions that share the **exact same key ID and key material**. This allows decrypting ciphertext in Region B without making cross-region API calls to KMS in Region A (critical for DynamoDB Global Tables and Aurora Global Databases).
- **Envelope Encryption:** KMS leverages a hierarchy to optimize performance. Instead of transmitting your raw gigabyte datasets across the network to the KMS API, KMS generates a unique **Data Key**. The Data Key encrypts the data locally, and KMS encrypts the Data Key using your root Customer Master Key (CMK).

🔥⭐⭐⭐⭐

---

# Part 7 - Resilience & Disaster Recovery (DR)

## Disaster Recovery Strategies

Selecting the optimal DR topology based on the organization's **RTO (Recovery Time Objective)** and **RPO (Recovery Point Objective)** goals versus cost ceilings.



1. **Backup and Restore (Highest RTO/RPO, Lowest Cost):** Data is backed up to S3 continuously or on a schedule. In a disaster event, a full environment is provisioned from scratch via infrastructure as code, and data backups are restored. RTO/RPO = Hours.
2. **Pilot Light:** The core data layer is continuously running and replicating in the DR region (e.g., an Aurora Read Replica or DynamoDB Global Table). The application compute layers (EC2, ALBs) are pre-configured but **turned off** or unprovisioned. During a failover event, Auto Scaling groups scale up to handle traffic. RTO = Minutes.
3. **Warm Standby:** A fully functional but scaled-down version of the architecture runs continuously in the DR region (e.g., a minimal EC2 count behind an ALB, paired with live database replication). During a disaster, the infrastructure rapidly **scales out** horizontally to absorb production volumes. RTO = Under 10 minutes.
4. **Multi-Site Active-Active (Hot Standby - Lowest RTO/RPO, Highest Cost):** Complete, uncompromised production infrastructures run in parallel across multiple regions simultaneously. Traffic is actively load-balanced globally via Route 53 or Global Accelerator. If a region goes offline, traffic shifts instantly with near-zero data loss. RTO/RPO = Real-time / Seconds.

🔥⭐⭐⭐⭐⭐

---

# Part 8 - Compute & Container Orchestration

## ECS vs EKS vs Fargate Launch Topologies

- **Amazon ECS:** AWS's native, highly integrated container orchestrator. Offers deep integration with native IAM roles per task, CloudWatch Logs, Route 53 Service Discovery, and Application Load Balancers. Highly performant with minimal operational complexity.
- **Amazon EKS:** A fully managed, upstream-compliant Kubernetes service. Chosen explicitly when the organization requires open-source Kubernetes parity, cross-cloud tool standardization, or complex pod scheduling layouts. Requires internal engineering expertise.
- **AWS Fargate:** A serverless compute engine available for *both* ECS and EKS. It eliminates the need to provision, scale, patch, or secure underlying EC2 worker nodes. You pay strictly for the CPU and Memory specified at the Task/Pod definition level.

🔥⭐⭐⭐⭐

---

## EC2 Placement Groups

Optimizing hardware placement templates for resiliency or throughput performance.

- **Cluster Placement Group:** Groups EC2 instances onto a single physical hardware rack within a single Availability Zone. Delivers ultra-low latency and high network throughput (up to 100 Gbps). Used for High-Performance Computing (HPC), big data map-reduce processing, and tightly coupled cluster apps. *Risk:* Hardware rack failure affects all instances simultaneously.
- **Spread Placement Group:** Places instances onto completely separate, isolated physical hardware racks across different AZs. Maximize isolation so a single hardware fault cannot impact more than 1 instance. Strictly limited to **7 running instances per Availability Zone**. Ideal for core enterprise domain controllers and database master nodes.
- **Partition Placement Group:** Divides the Auto Scaling layout into distinct logical segments called partitions. Each partition contains its own distinct group of physical racks. No two partitions share the same hardware. Can scale to hundreds of instances. Best for large distributed platforms like Apache Kafka, Hadoop, or Cassandra clusters where data is replicated across known logical topological boundaries.

🔥⭐⭐⭐