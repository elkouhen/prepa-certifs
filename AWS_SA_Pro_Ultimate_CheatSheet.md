# AWS Certified Solutions Architect Professional (SAP-C02) - Ultimate Cheat Sheet

## How This Cheat Sheet Is Built (Methodology)

This guide is an English, exam-focused reference for SAP-C02. It normalizes each service entry and adds the high-frequency services that appear across the official SAP-C02 domains.

---

## Scope and Ordering Rules

**Rule 1 - Scope (one service = one section)**  
Each section covers exactly one cloud service or one exam-critical feature of a cloud service. Grouped comparisons are split into separate entries whenever the exam requires choosing between services.

**Rule 2 - Order (logical architecture pipeline)**  
Services are ordered from organization and identity foundations, through networking and compute, then storage, data, security, operations, migration, and governance/cost controls.

---

## Mandatory Structure for Every Entry

Every entry uses exactly this structure and order:

### [Service name]

#### Definition & Purpose
2-3 sentences explaining what the service does, why it is used, common native integrations, and whether the deployment model is serverless, fully managed, or customer-managed/dedicated infrastructure.

#### Exam Triggers
Bullet list of keywords, exact phrases, or constraints in an exam question that point directly to this service.

#### Not to be confused with (MANDATORY)
Closest confusing service plus the decision rule:

▎ Choose [this service] if ... / Choose [other service] if ...

#### Security & FAQ Insights
- Critical security patterns: IAM, encryption, private connectivity, logging, compliance, and data isolation.
- FAQ/exam limitations: quotas, supported modes, region/global scope, non-transitivity, failover behavior, and pricing constraints.
- Common exam traps linked to this service.

#### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Estimated exam importance from 1 to 5 stars, with a one-line justification.

---

# Part 1 - Multi-Account Organization, Identity & Governance

## AWS Organizations

### Definition & Purpose
A fully managed governance service for centrally managing multiple AWS accounts, organizational units, consolidated billing, and delegated administration. It integrates with IAM, CloudTrail, AWS Config, Control Tower, Security Hub, GuardDuty, and many delegated admin services; deployment model is fully managed control-plane governance.

### Exam Triggers
- "multi-account landing zone"
- "consolidated billing"
- "organizational units"
- "delegated administrator"
- "centralize accounts"

### Not to be confused with (MANDATORY)
AWS Control Tower - Organizations provides the account hierarchy and policy engine, while Control Tower automates a prescriptive landing zone on top of Organizations.

▎ Choose AWS Organizations if you need the underlying multi-account hierarchy, billing, OUs, and delegated administration / Choose AWS Control Tower if you need an opinionated landing-zone setup with guardrails and Account Factory.

### Security & FAQ Insights
- Management account should not host production workloads; use delegated administrators for day-to-day security and operations services.
- Savings Plans and Reserved Instance sharing can be controlled at the organization level.
- SCPs do not apply to the management account but do apply to member account root users.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## Service Control Policies (SCPs)

### Definition & Purpose
SCPs are organization-level permission guardrails that define the maximum available permissions for accounts, OUs, or the organization root. They integrate with AWS Organizations and IAM evaluation logic; deployment model is a fully managed policy control plane.

### Exam Triggers
- "maximum permissions boundary for accounts"
- "explicit deny across an OU"
- "prevent account root from doing an action"
- "guardrails not permissions grants"

### Not to be confused with (MANDATORY)
IAM policies - IAM grants permissions to identities or resources, while SCPs only filter the maximum permissions available inside member accounts.

▎ Choose Service Control Policies (SCPs) if you must enforce a preventive guardrail across accounts or OUs regardless of local IAM policies / Choose IAM policies if you need to grant or deny permissions for a specific identity or resource inside one account.

### Security & FAQ Insights
- Explicit Deny in an SCP overrides any IAM Allow.
- Removing the default FullAWSAccess SCP or failing to add Allows can implicitly block all actions.
- SCPs do not affect service-linked roles or the Organizations management account.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Control Tower

### Definition & Purpose
Control Tower orchestrates a secure multi-account landing zone with preconfigured OUs, centralized logging, audit accounts, Account Factory, and guardrails. It integrates Organizations, AWS Config, CloudTrail, IAM Identity Center, and Service Catalog; deployment model is fully managed orchestration.

### Exam Triggers
- "landing zone"
- "Account Factory"
- "preventive detective proactive guardrails"
- "baseline multi-account environment"

### Not to be confused with (MANDATORY)
AWS Organizations - Organizations is the underlying hierarchy, while Control Tower automates a best-practice landing zone and guardrails.

▎ Choose AWS Control Tower if you need a governed landing zone quickly with account vending and built-in guardrails / Choose AWS Organizations if you only need raw account/OUs/billing management without the Control Tower baseline.

### Security & FAQ Insights
- Preventive guardrails use SCPs, detective guardrails use AWS Config, and proactive guardrails use CloudFormation hooks.
- Log Archive and Audit accounts are core landing-zone accounts.
- Do not bypass Account Factory for governed account provisioning in Control Tower scenarios.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Resource Access Manager (RAM)

### Definition & Purpose
RAM securely shares supported AWS resources such as subnets, Transit Gateways, Route 53 Resolver rules, and license configurations across accounts or within Organizations. It is a fully managed sharing service that reduces duplication while preserving account isolation.

### Exam Triggers
- "share subnets across accounts"
- "central networking account"
- "resource sharing inside an organization"
- "shared VPC architecture"

### Not to be confused with (MANDATORY)
VPC Peering - RAM shares ownership/use of supported resources, while VPC Peering connects separate VPC networks.

▎ Choose AWS Resource Access Manager (RAM) if you need multiple accounts to use the same supported resource such as shared subnets or TGW / Choose VPC Peering if you need network connectivity between two independent VPCs without shared ownership.

### Security & FAQ Insights
- Application accounts in shared VPC subnets cannot modify the owner account route tables or network topology.
- Sharing with Organizations avoids per-account invitation workflows.
- Security groups remain account-scoped, though cross-account references are possible in supported cases.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Service Catalog

### Definition & Purpose
Service Catalog provides governed self-service portfolios of approved infrastructure products defined with CloudFormation or Terraform. It integrates IAM launch constraints, Organizations, and provisioning workflows; deployment model is fully managed governance for approved templates.

### Exam Triggers
- "self-service approved products"
- "launch RDS without direct RDS permissions"
- "portfolio of standardized templates"
- "central governance of infrastructure"

### Not to be confused with (MANDATORY)
CloudFormation - CloudFormation deploys stacks directly, while Service Catalog wraps approved templates with access control, constraints, and product lifecycle governance.

▎ Choose AWS Service Catalog if users must launch pre-approved infrastructure without having direct permissions to create every underlying resource / Choose CloudFormation if an administrator or pipeline simply needs to deploy infrastructure as code directly.

### Security & FAQ Insights
- Launch constraints let Service Catalog assume an execution role to deploy resources safely.
- Use portfolios and products for standardized platform offerings.
- Common trap: user permissions to launch the product differ from permissions used to create underlying resources.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS Config

### Definition & Purpose
AWS Config records resource configuration history and evaluates compliance against managed or custom rules across accounts and regions. It integrates with Organizations, CloudTrail, S3, SNS, Systems Manager Automation, and Security Hub; deployment model is fully managed compliance monitoring.

### Exam Triggers
- "configuration history"
- "detect non-compliant resources"
- "Config aggregator"
- "conformance packs"
- "automatic remediation"

### Not to be confused with (MANDATORY)
CloudTrail - Config tracks resource state and compliance over time, while CloudTrail records API calls and user activity.

▎ Choose AWS Config if you need to know what a resource configuration is or whether it complies with a rule / Choose CloudTrail if you need to know who called an API and when.

### Security & FAQ Insights
- Aggregators centralize multi-account and multi-region compliance views.
- Conformance packs package rules and remediation for organization-wide deployment.
- Remediation commonly uses Systems Manager Automation documents.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS IAM Identity Center

### Definition & Purpose
IAM Identity Center centrally manages workforce SSO access to AWS accounts and cloud applications. It integrates with external IdPs via SAML/OIDC/SCIM and provisions IAM roles from permission sets; deployment model is fully managed identity federation.

### Exam Triggers
- "single sign-on to many AWS accounts"
- "permission sets"
- "SCIM provisioning"
- "federate workforce identities"

### Not to be confused with (MANDATORY)
IAM roles - IAM roles are account-local authorization targets, while IAM Identity Center centrally assigns users/groups to roles across accounts.

▎ Choose AWS IAM Identity Center if humans need centralized SSO access and permission sets across many accounts / Choose IAM roles if an application or AWS service needs assumable permissions inside one account.

### Security & FAQ Insights
- Permission sets create corresponding IAM roles in target accounts.
- Use groups from the corporate IdP rather than individual assignments for scale.
- Best practice is no long-lived IAM users for workforce access.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS IAM

### Definition & Purpose
IAM controls authentication and authorization for users, groups, roles, policies, and resource-based access in an AWS account. It integrates with every AWS service plus STS, IAM Access Analyzer, and Organizations; deployment model is a fully managed identity control plane.

### Exam Triggers
- "least privilege"
- "permissions boundary"
- "resource-based policy"
- "cross-account role"
- "IAM Access Analyzer"

### Not to be confused with (MANDATORY)
SCPs - IAM policies grant or deny permissions inside an account, while SCPs set maximum permissions for the account from Organizations.

▎ Choose AWS IAM if you need identity, role, or resource authorization within an AWS account / Choose SCPs if you need an organization-level preventive guardrail across accounts.

### Security & FAQ Insights
- Explicit Deny always wins across IAM policy evaluation.
- Permissions boundaries limit what an identity-based policy can grant but do not grant permissions by themselves.
- Prefer roles and temporary credentials over long-lived access keys.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS STS and Identity Federation

### Definition & Purpose
AWS STS issues temporary credentials for roles, SAML, OIDC, and web identity federation. It integrates with IAM, Cognito, IAM Identity Center, Kubernetes IRSA, GitHub Actions OIDC, and enterprise IdPs; deployment model is fully managed token brokering.

### Exam Triggers
- "AssumeRoleWithSAML"
- "AssumeRoleWithWebIdentity"
- "temporary credentials"
- "external identity provider"
- "OIDC federation"

### Not to be confused with (MANDATORY)
IAM users - STS federation issues temporary credentials, while IAM users use long-lived credentials and are discouraged for humans and workloads.

▎ Choose AWS STS and Identity Federation if external identities or workloads need short-lived AWS credentials without stored access keys / Choose IAM users if a legacy account requires a permanent identity inside AWS and cannot use federation.

### Security & FAQ Insights
- Use external IDs for third-party cross-account role assumption to prevent confused-deputy risks.
- OIDC is common for CI/CD and Kubernetes service accounts.
- Session policies can further reduce temporary permissions.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon Cognito

### Definition & Purpose
Cognito provides user directories and temporary AWS credential brokering for consumer-facing web and mobile applications. User Pools handle sign-up, sign-in, MFA, and JWTs; Identity Pools exchange tokens for IAM credentials; deployment model is fully managed application identity.

### Exam Triggers
- "customer sign-up and sign-in"
- "mobile users access S3 directly"
- "User Pool vs Identity Pool"
- "social login"

### Not to be confused with (MANDATORY)
IAM Identity Center - Cognito is for customer/application identities, while IAM Identity Center is for workforce SSO into AWS accounts and business apps.

▎ Choose Amazon Cognito if a public application needs user registration, JWTs, social login, or temporary AWS access for end users / Choose IAM Identity Center if employees need centralized access to AWS accounts and enterprise applications.

### Security & FAQ Insights
- User Pools authenticate users; Identity Pools authorize access to AWS resources with temporary credentials.
- Use IAM roles scoped by identity pool rules for least privilege.
- Do not use Cognito as the default answer for workforce multi-account SSO.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

# Part 2 - Advanced Networking, Edge & Traffic Management

## Amazon VPC

### Definition & Purpose
VPC provides isolated regional networking with subnets, route tables, security groups, NACLs, endpoints, NAT gateways, and routing controls. It integrates with EC2, RDS, Lambda, PrivateLink, Transit Gateway, VPN, and Direct Connect; deployment model is customer-designed networking on managed AWS infrastructure.

### Exam Triggers
- "private subnets"
- "route tables"
- "security groups vs NACLs"
- "VPC endpoints"
- "hybrid network foundation"

### Not to be confused with (MANDATORY)
AWS Transit Gateway - VPC is the isolated network boundary, while Transit Gateway is a hub that connects many VPCs and hybrid networks.

▎ Choose Amazon VPC if you are designing subnets, routing, security groups, endpoints, or NAT inside one regional network / Choose AWS Transit Gateway if you need transitive routing across many VPCs, VPNs, and Direct Connect attachments.

### Security & FAQ Insights
- Security groups are stateful; NACLs are stateless and subnet-scoped.
- Gateway endpoints support S3 and DynamoDB; interface endpoints use PrivateLink ENIs.
- VPC CIDR overlap blocks peering and many routing designs.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Transit Gateway

### Definition & Purpose
Transit Gateway is a regional cloud router that provides transitive connectivity among VPCs, VPNs, and Direct Connect via attachments and route tables. It integrates with RAM, Direct Connect Gateway, Site-to-Site VPN, Network Manager, and cross-region peering; deployment model is fully managed regional network hub.

### Exam Triggers
- "transitive routing"
- "central hub for many VPCs"
- "TGW route tables"
- "connect thousands of VPCs"
- "segmented routing domains"

### Not to be confused with (MANDATORY)
VPC Peering - TGW scales hub-and-spoke transitive routing, while peering is simple non-transitive point-to-point connectivity.

▎ Choose AWS Transit Gateway if you need many-to-many routing, segmentation, or hybrid aggregation at scale / Choose VPC Peering if you only need a simple low-cost point-to-point connection between two non-overlapping VPCs.

### Security & FAQ Insights
- TGW route tables can isolate production, development, inspection, and shared-services domains.
- Charges include attachments and data processing, so peering may be cheaper for two high-volume VPCs.
- Cross-region TGW peering supports global network architectures.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS PrivateLink

### Definition & Purpose
PrivateLink exposes services privately through interface endpoints without routing to the producer VPC. It integrates with NLB, endpoint services, interface VPC endpoints, security groups, and AWS service endpoints; deployment model is fully managed private service connectivity.

### Exam Triggers
- "overlapping CIDR VPCs"
- "private access to one service"
- "interface endpoint"
- "producer behind NLB"
- "no full network connectivity"

### Not to be confused with (MANDATORY)
Transit Gateway - PrivateLink grants private access to a specific service endpoint, while Transit Gateway routes networks transitively.

▎ Choose AWS PrivateLink if consumers need private access to one service without exposing or routing the whole producer network / Choose Transit Gateway if you need full routed connectivity among multiple VPCs and on-prem networks.

### Security & FAQ Insights
- Works with overlapping CIDRs because it does not require routing between VPC CIDR blocks.
- Consumer sees an ENI in its VPC; producer exposes a service, commonly behind NLB.
- Use endpoint policies and security groups for least privilege.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Direct Connect

### Definition & Purpose
Direct Connect provides dedicated private connectivity from on-premises or colocation facilities to AWS Direct Connect locations. It integrates with public, private, and transit VIFs, Direct Connect Gateway, Transit Gateway, and VPN; deployment model is dedicated or partner-provided physical connectivity.

### Exam Triggers
- "dedicated private connection"
- "public VIF private VIF transit VIF"
- "maximum resiliency"
- "low latency hybrid"
- "MACsec"

### Not to be confused with (MANDATORY)
AWS Site-to-Site VPN - Direct Connect is private dedicated connectivity, while VPN is encrypted IPsec over the internet.

▎ Choose AWS Direct Connect if you need predictable bandwidth, lower latency, private physical connectivity, or hybrid connectivity at scale / Choose AWS Site-to-Site VPN if you need quick encrypted connectivity over the internet or backup encryption over DX.

### Security & FAQ Insights
- Direct Connect is not encrypted by default; use MACsec on supported dedicated links or VPN over DX.
- Maximum resiliency uses independent connections at different DX locations.
- Transit VIF plus Direct Connect Gateway connects to Transit Gateway for multi-VPC access.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Site-to-Site VPN

### Definition & Purpose
Site-to-Site VPN creates encrypted IPsec tunnels between on-premises networks and AWS over the internet or over Direct Connect public VIFs. It integrates with Virtual Private Gateway, Transit Gateway, customer gateways, and BGP; deployment model is fully managed VPN endpoints with customer-managed on-prem devices.

### Exam Triggers
- "IPsec tunnel"
- "quick hybrid connectivity"
- "VPN backup for Direct Connect"
- "encrypted transit"
- "BGP failover"

### Not to be confused with (MANDATORY)
AWS Direct Connect - VPN is encrypted and fast to provision over internet, while Direct Connect is a dedicated physical connection with predictable bandwidth.

▎ Choose AWS Site-to-Site VPN if you need encrypted connectivity quickly or as a backup path / Choose AWS Direct Connect if you need dedicated private bandwidth, stable latency, or very large hybrid throughput.

### Security & FAQ Insights
- Use two tunnels per VPN connection for HA.
- Dynamic routing with BGP is preferred for failover.
- VPN over Direct Connect can provide encryption when MACsec is unavailable.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon Route 53

### Definition & Purpose
Route 53 provides authoritative DNS, domain registration, health checks, Resolver endpoints, and advanced routing policies. It integrates with ELB, CloudFront, S3 website endpoints, VPC private hosted zones, and hybrid DNS; deployment model is fully managed global DNS.

### Exam Triggers
- "latency routing"
- "failover routing"
- "weighted routing"
- "private hosted zone"
- "hybrid DNS resolver endpoints"

### Not to be confused with (MANDATORY)
AWS Global Accelerator - Route 53 uses DNS responses and TTLs for routing, while Global Accelerator uses static anycast IPs and fast network-layer failover.

▎ Choose Amazon Route 53 if DNS-based routing, domain management, private zones, or hybrid DNS forwarding is required / Choose AWS Global Accelerator if clients need fixed anycast IPs and sub-minute failover for TCP/UDP traffic.

### Security & FAQ Insights
- Inbound Resolver endpoints let on-prem resolve private AWS zones; outbound endpoints forward VPC queries to on-prem DNS.
- Failover routing depends on health checks and DNS TTL behavior.
- Weighted routing is common for canary and gradual migration scenarios.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## Amazon CloudFront

### Definition & Purpose
CloudFront is a global CDN for accelerating static and dynamic content through edge locations. It integrates with S3, ALB, EC2 origins, Lambda@Edge, CloudFront Functions, WAF, Shield, and Origin Access Control; deployment model is fully managed global edge delivery.

### Exam Triggers
- "global CDN"
- "cache static and dynamic content"
- "Origin Access Control"
- "Lambda@Edge vs CloudFront Functions"
- "origin failover"

### Not to be confused with (MANDATORY)
AWS Global Accelerator - CloudFront is optimized for HTTP(S) caching and edge logic, while Global Accelerator optimizes TCP/UDP routing to regional endpoints without caching.

▎ Choose Amazon CloudFront if the workload is HTTP/S content delivery, caching, origin protection, or edge request manipulation / Choose AWS Global Accelerator if the workload needs static anycast IPs, non-HTTP protocols, or rapid regional failover without caching.

### Security & FAQ Insights
- Origin Access Control is preferred over legacy OAI for S3 origins.
- CloudFront Functions are lightweight viewer-edge JavaScript; Lambda@Edge handles heavier origin/viewer logic.
- Origin groups fail over on selected HTTP status codes.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Global Accelerator

### Definition & Purpose
Global Accelerator provides static anycast IPs and routes user traffic over the AWS global backbone to healthy regional endpoints. It integrates with ALB, NLB, EC2, health checks, and multi-region deployments; deployment model is fully managed global network acceleration.

### Exam Triggers
- "two static anycast IPs"
- "fast regional failover"
- "TCP UDP acceleration"
- "avoid DNS caching delay"
- "global entry point"

### Not to be confused with (MANDATORY)
Amazon CloudFront - Global Accelerator does not cache content and supports TCP/UDP, while CloudFront is an HTTP(S) CDN with edge caching.

▎ Choose AWS Global Accelerator if you need fixed global IPs, TCP/UDP acceleration, or failover that avoids DNS TTL delays / Choose Amazon CloudFront if you need HTTP caching, CDN behavior, or edge transformations.

### Security & FAQ Insights
- Health checks can shift traffic away from unhealthy regions quickly.
- Useful for gaming, VoIP, IoT, and APIs requiring fixed IP allowlists.
- It improves network path, not application caching.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Elastic Load Balancing

### Definition & Purpose
ELB distributes traffic across targets using Application, Network, Gateway, and Classic Load Balancers. It integrates with Auto Scaling, ECS, EKS, ACM, WAF, Cognito/OIDC, and CloudWatch; deployment model is fully managed regional load balancing.

### Exam Triggers
- "ALB vs NLB vs GWLB"
- "path-based routing"
- "static IP per AZ"
- "preserve source IP"
- "inline appliance inspection"

### Not to be confused with (MANDATORY)
Amazon CloudFront - ELB is regional load balancing to application targets, while CloudFront is global edge caching and distribution.

▎ Choose Elastic Load Balancing if you need regional traffic distribution across EC2, containers, IPs, Lambda, or appliances / Choose Amazon CloudFront if you need global edge caching and content delivery in front of origins.

### Security & FAQ Insights
- ALB is Layer 7 HTTP/HTTPS/gRPC; NLB is Layer 4 TCP/UDP/TLS with ultra-low latency; GWLB inserts security appliances using GENEVE.
- ALB can authenticate with Cognito or OIDC.
- NLB provides static IPs per AZ and preserves source IP.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Network Firewall

### Definition & Purpose
Network Firewall provides managed stateful inspection, stateless rules, domain filtering, and intrusion-prevention style controls for VPC traffic. It integrates with VPC route tables, Transit Gateway inspection VPCs, Firewall Manager, CloudWatch, and S3 logs; deployment model is fully managed firewall endpoints.

### Exam Triggers
- "central inspection VPC"
- "stateful firewall rules"
- "egress domain filtering"
- "East-West traffic inspection"
- "Suricata-compatible rules"

### Not to be confused with (MANDATORY)
Security groups - Security groups filter instance or ENI traffic, while Network Firewall inspects routed VPC traffic centrally with stateful/stateless rule engines.

▎ Choose AWS Network Firewall if you need centralized network-layer inspection, egress filtering, or third-party-style firewall rules / Choose Security groups if you need simple stateful allow rules on individual ENIs or instances.

### Security & FAQ Insights
- Common design uses TGW to route traffic through inspection VPC endpoints.
- Firewall endpoints are zonal; route each AZ locally to avoid cross-AZ failure dependencies.
- Logs can go to S3, CloudWatch Logs, or Kinesis Data Firehose.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS WAF

### Definition & Purpose
AWS WAF protects web applications with Layer 7 rules for IP reputation, SQL injection, XSS, rate limiting, and managed rule groups. It integrates with CloudFront, ALB, API Gateway, AppSync, Cognito user pools, Firewall Manager, and Shield; deployment model is fully managed web application firewall.

### Exam Triggers
- "SQL injection XSS protection"
- "managed web ACL"
- "rate-based rule"
- "protect CloudFront or ALB"
- "Layer 7 filtering"

### Not to be confused with (MANDATORY)
AWS Shield - WAF filters application-layer web requests, while Shield focuses on DDoS protection.

▎ Choose AWS WAF if you need HTTP/S request inspection and web exploit mitigation / Choose AWS Shield if you need DDoS detection, mitigation, and cost protection for volumetric attacks.

### Security & FAQ Insights
- Use Firewall Manager to deploy WAF policies across accounts.
- Managed rule groups reduce custom rule maintenance.
- WAF does not replace network firewalls for non-HTTP traffic.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Shield

### Definition & Purpose
Shield provides managed DDoS protection, with Shield Standard automatically included and Shield Advanced adding enhanced detection, support, and cost protections. It integrates with CloudFront, Route 53, Global Accelerator, ELB, WAF, and Firewall Manager; deployment model is fully managed DDoS protection.

### Exam Triggers
- "DDoS protection"
- "Shield Advanced"
- "DDoS cost protection"
- "protect CloudFront Route 53 ALB"
- "volumetric attack"

### Not to be confused with (MANDATORY)
AWS WAF - Shield mitigates DDoS attacks, while WAF filters web-layer requests and exploit patterns.

▎ Choose AWS Shield if the scenario emphasizes DDoS resiliency, attack response support, or cost protection / Choose AWS WAF if the scenario emphasizes HTTP request filtering such as SQL injection or rate-based blocking.

### Security & FAQ Insights
- Shield Standard is automatic; Shield Advanced adds DRT access and cost protection for protected resources.
- Often combined with CloudFront and WAF for internet-facing apps.
- Route 53 and CloudFront are edge-protected services.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

# Part 3 - Storage, Databases & Data Platforms

## Amazon S3

### Definition & Purpose
S3 is highly durable object storage for data lakes, backups, static assets, logs, and cross-region replication. It integrates with CloudFront, KMS, IAM, Lambda, EventBridge, Macie, Athena, Glue, and lifecycle policies; deployment model is fully managed serverless object storage.

### Exam Triggers
- "object storage"
- "CRR SRR replication"
- "Object Lock WORM"
- "lifecycle to Glacier"
- "Multi-Region Access Points"

### Not to be confused with (MANDATORY)
Amazon EFS - S3 stores objects through APIs, while EFS is a POSIX file system mounted by Linux clients.

▎ Choose Amazon S3 if you need durable object storage, data lakes, static content, lifecycle, replication, or WORM retention / Choose Amazon EFS if applications require shared POSIX file semantics and NFS mounts.

### Security & FAQ Insights
- Versioning is required for replication.
- Object Lock Compliance mode cannot be bypassed even by root until retention expires.
- Gateway VPC endpoints provide private S3 access without NAT.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## Amazon EFS

### Definition & Purpose
EFS is a serverless elastic NFS file system for shared Linux file access across multiple AZs. It integrates with EC2, ECS, EKS, Lambda, Backup, KMS, and security groups; deployment model is fully managed serverless file storage.

### Exam Triggers
- "shared Linux file system"
- "NFS mount across AZs"
- "serverless elastic file storage"
- "many EC2 instances need same files"

### Not to be confused with (MANDATORY)
Amazon FSx for Windows File Server - EFS is NFS/POSIX for Linux, while FSx for Windows provides SMB and Active Directory integration.

▎ Choose Amazon EFS if Linux workloads need shared POSIX file access across instances or containers / Choose Amazon FSx for Windows File Server if Windows applications need SMB shares and AD-based ACLs.

### Security & FAQ Insights
- Use mount targets in each AZ for HA and local access.
- EFS access points can enforce POSIX identity and paths.
- Lifecycle policies move inactive files to lower-cost storage classes.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## Amazon FSx

### Definition & Purpose
FSx provides managed high-performance file systems including Windows File Server, Lustre, NetApp ONTAP, and OpenZFS. It integrates with AD, S3, Backup, KMS, VPC, and compute services; deployment model is fully managed file storage with service-specific engines.

### Exam Triggers
- "Windows SMB file share"
- "FSx for Lustre HPC with S3"
- "NetApp ONTAP migration"
- "high-performance file system"

### Not to be confused with (MANDATORY)
Amazon EFS - FSx is selected for protocol/engine-specific requirements, while EFS is generic serverless NFS for Linux.

▎ Choose Amazon FSx if the workload needs SMB/Windows, Lustre HPC, NetApp ONTAP features, or OpenZFS semantics / Choose Amazon EFS if the workload simply needs elastic shared NFS for Linux.

### Security & FAQ Insights
- FSx for Lustre links to S3 for high-performance processing of object data.
- FSx for Windows integrates with Microsoft AD for ACLs.
- FSx for ONTAP is a strong migration path for existing NetApp estates.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS Storage Gateway

### Definition & Purpose
Storage Gateway is a hybrid appliance that exposes file, volume, or tape interfaces on-premises while storing data durably in AWS. It integrates with S3, EBS snapshots, Glacier, KMS, VMware/Hyper-V/hardware appliances, and CloudWatch; deployment model is a customer-deployed gateway with managed cloud storage.

### Exam Triggers
- "hybrid storage cache"
- "File Gateway"
- "Volume Gateway"
- "Tape Gateway"
- "replace tape backup"

### Not to be confused with (MANDATORY)
AWS DataSync - Storage Gateway is for ongoing hybrid access with local protocols/caching, while DataSync is for high-speed transfer or synchronization jobs.

▎ Choose AWS Storage Gateway if on-prem applications must keep using NFS, SMB, iSCSI, or tape interfaces backed by AWS storage / Choose AWS DataSync if you need one-time or scheduled bulk data movement between storage systems.

### Security & FAQ Insights
- File Gateway exposes NFS/SMB backed by S3.
- Volume Gateway supports cached or stored iSCSI volumes with EBS snapshots.
- Tape Gateway replaces physical tape libraries with virtual tapes in S3 Glacier.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Snow Family

### Definition & Purpose
Snow Family provides rugged physical devices for offline edge computing and large-scale data transfer when networks are too slow or unavailable. It integrates with S3, EC2-compatible compute on Snowball Edge, EKS Anywhere in some edge patterns, and DataSync after import; deployment model is AWS-shipped physical hardware.

### Exam Triggers
- "petabyte offline transfer"
- "network too slow"
- "disconnected edge compute"
- "Snowball Edge"
- "Snowmobile"

### Not to be confused with (MANDATORY)
AWS DataSync - Snow moves data physically offline, while DataSync moves data over a network connection.

▎ Choose AWS Snow Family if network transfer would take too long, bandwidth is unavailable, or disconnected edge compute is required / Choose AWS DataSync if there is reliable network bandwidth for online transfer or ongoing sync.

### Security & FAQ Insights
- Rule of thumb: if online transfer would take more than about a week, consider Snowball.
- Snowball Edge Compute Optimized can run local compute.
- Plan chain-of-custody, encryption, and import/export timelines.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Backup

### Definition & Purpose
AWS Backup centrally manages backup policies, vaults, retention, cross-account backup, and cross-region copy for supported AWS services. It integrates with Organizations, EBS, EFS, RDS, DynamoDB, FSx, Storage Gateway, KMS, and Audit Manager; deployment model is fully managed backup orchestration.

### Exam Triggers
- "central backup policy"
- "cross-account backup"
- "backup vault lock"
- "organization-wide backups"
- "audit backup compliance"

### Not to be confused with (MANDATORY)
S3 replication - AWS Backup coordinates backups across many services, while S3 replication copies S3 objects between buckets.

▎ Choose AWS Backup if you need centralized policy-based backups and compliance across many AWS services/accounts / Choose S3 replication if you only need object replication for S3 availability or locality.

### Security & FAQ Insights
- Backup Vault Lock supports WORM-like backup protection.
- Use cross-account backup to protect against account compromise.
- Backup Audit Manager reports backup compliance.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon Aurora

### Definition & Purpose
Aurora is a managed MySQL/PostgreSQL-compatible relational database optimized for high availability, read scaling, global replication, and serverless capacity. It integrates with RDS, KMS, Secrets Manager, Lambda, CloudWatch, RDS Proxy, and Global Database; deployment model is fully managed relational database clusters.

### Exam Triggers
- "Aurora Global Database"
- "storage-level replication under 1 second"
- "Aurora Serverless v2"
- "writer plus readers"
- "RDS-compatible high performance"

### Not to be confused with (MANDATORY)
Amazon RDS - Aurora is AWS-optimized clustered relational storage, while RDS runs standard database engines with more traditional instance/storage architecture.

▎ Choose Amazon Aurora if you need high-performance managed MySQL/PostgreSQL with fast failover, reader scaling, serverless v2, or global database / Choose Amazon RDS if you need a non-Aurora engine, standard DB features, or simpler managed relational deployment.

### Security & FAQ Insights
- Aurora Global Database supports low-lag cross-region replication and fast DR promotion.
- Aurora Serverless v2 scales capacity in ACUs without the v1 pause/resume model.
- Readers scale reads but only the writer handles writes except special multi-master/limit cases.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## Amazon RDS

### Definition & Purpose
RDS manages relational engines such as MySQL, PostgreSQL, MariaDB, Oracle, and SQL Server with automated backups, patching, Multi-AZ, and read replicas. It integrates with KMS, Secrets Manager, IAM authentication, CloudWatch, DMS, and RDS Proxy; deployment model is fully managed database instances.

### Exam Triggers
- "Multi-AZ vs Read Replica"
- "managed Oracle or SQL Server"
- "automated backups PITR"
- "read scaling"
- "relational database"

### Not to be confused with (MANDATORY)
Amazon Aurora - RDS is standard managed engines, while Aurora is AWS-native MySQL/PostgreSQL-compatible clustered storage with advanced scaling/global features.

▎ Choose Amazon RDS if you need a managed standard relational engine, especially Oracle or SQL Server, or classic Multi-AZ/read replica behavior / Choose Amazon Aurora if you need Aurora-specific performance, Global Database, or Serverless v2.

### Security & FAQ Insights
- Multi-AZ is for HA and failover; read replicas are for read scaling and can be promoted.
- Backups enable point-in-time restore within retention.
- Use RDS Proxy for connection pooling with Lambda or spiky applications.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon DynamoDB

### Definition & Purpose
DynamoDB is fully managed serverless NoSQL key-value/document storage with single-digit millisecond latency at scale. It integrates with Lambda, Streams, Global Tables, DAX, KMS, IAM, EventBridge, and backups; deployment model is serverless managed NoSQL.

### Exam Triggers
- "serverless NoSQL"
- "Global Tables active-active"
- "single-digit millisecond latency"
- "DAX microsecond reads"
- "DynamoDB Streams"

### Not to be confused with (MANDATORY)
Amazon Aurora - DynamoDB is NoSQL key-value/document and scales horizontally without servers, while Aurora is relational SQL.

▎ Choose Amazon DynamoDB if the workload needs massive scale, predictable key-value access, serverless operations, or active-active global NoSQL / Choose Amazon Aurora if the workload needs relational SQL joins, transactions, and schema-based relational modeling.

### Security & FAQ Insights
- Global Tables provide multi-region active-active writes with last-writer-wins conflict handling.
- DAX accelerates eventually consistent reads but is not for strongly consistent reads.
- Design partition keys to avoid hot partitions.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon ElastiCache

### Definition & Purpose
ElastiCache provides managed Redis/Valkey or Memcached in-memory caching for low-latency reads, sessions, leaderboards, and pub/sub patterns. It integrates with VPC, KMS, IAM auth for Redis/Valkey in supported modes, CloudWatch, and application stacks; deployment model is managed cache clusters/serverless options depending on engine.

### Exam Triggers
- "in-memory cache"
- "reduce database read load"
- "session store"
- "Redis sorted sets"
- "microsecond cache latency"

### Not to be confused with (MANDATORY)
DynamoDB DAX - ElastiCache is general-purpose caching and data structures, while DAX specifically accelerates DynamoDB API reads.

▎ Choose Amazon ElastiCache if you need a general Redis/Valkey/Memcached cache, session store, or advanced in-memory data structures / Choose DynamoDB DAX if you only need API-compatible read acceleration for DynamoDB.

### Security & FAQ Insights
- Cache invalidation and TTL strategy are application responsibilities.
- Redis cluster mode supports sharding; Multi-AZ improves availability.
- Do not use a cache as the system of record unless the architecture accepts data loss semantics.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon Redshift

### Definition & Purpose
Redshift is a managed data warehouse for analytics over structured and semi-structured data at scale. It integrates with S3, Glue, Lake Formation, IAM, KMS, QuickSight, Redshift Spectrum, and serverless workgroups; deployment model is managed or serverless analytic warehousing.

### Exam Triggers
- "data warehouse"
- "OLAP analytics"
- "Redshift Spectrum query S3"
- "columnar SQL analytics"
- "BI dashboards"

### Not to be confused with (MANDATORY)
Amazon RDS - Redshift is for analytical OLAP workloads, while RDS is for transactional OLTP applications.

▎ Choose Amazon Redshift if you need complex analytical SQL, BI, columnar warehousing, or querying lake data with Spectrum / Choose Amazon RDS if you need low-latency transactional reads/writes for an application database.

### Security & FAQ Insights
- Use Redshift Spectrum to query S3 without loading all data.
- Choose serverless for variable analytics workloads.
- Not a replacement for OLTP databases.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

# Part 4 - Compute, Containers, Serverless & Integration

## Amazon EC2

### Definition & Purpose
EC2 provides resizable virtual machines with configurable instance families, storage, networking, placement, and purchasing models. It integrates with Auto Scaling, ELB, EBS, IAM instance profiles, SSM, CloudWatch, and VPC; deployment model is customer-managed compute on AWS infrastructure.

### Exam Triggers
- "instance families"
- "placement groups"
- "Dedicated Hosts"
- "Spot Instances"
- "self-managed servers"

### Not to be confused with (MANDATORY)
AWS Lambda - EC2 gives full OS/runtime control, while Lambda is event-driven serverless functions with no server management.

▎ Choose Amazon EC2 if you need OS-level control, custom agents, specialized instances, or long-running server workloads / Choose AWS Lambda if you need event-driven code execution without managing servers.

### Security & FAQ Insights
- Cluster placement groups optimize low latency but increase correlated rack failure risk.
- Spread placement groups maximize isolation with a seven-instance-per-AZ limit.
- Use SSM Session Manager to reduce SSH/bastion exposure.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## EC2 Auto Scaling

### Definition & Purpose
EC2 Auto Scaling maintains desired capacity and scales EC2 fleets using launch templates, health checks, scaling policies, and mixed instance policies. It integrates with ELB, CloudWatch alarms, Spot, Warm Pools, and lifecycle hooks; deployment model is managed fleet orchestration for EC2.

### Exam Triggers
- "scale EC2 fleet"
- "target tracking policy"
- "mixed instances Spot On-Demand"
- "replace unhealthy instances"
- "lifecycle hooks"

### Not to be confused with (MANDATORY)
AWS Fargate - Auto Scaling manages EC2 capacity, while Fargate runs containers without managing EC2 instances.

▎ Choose EC2 Auto Scaling if you need to scale or heal EC2 instances directly / Choose AWS Fargate if you want AWS to provision container compute without EC2 fleet management.

### Security & FAQ Insights
- Use multiple AZs for HA.
- Mixed instance policies improve Spot resilience and cost.
- Lifecycle hooks support graceful bootstrap or drain workflows.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Lambda

### Definition & Purpose
Lambda runs event-driven functions without provisioning servers and scales automatically per invocation. It integrates with API Gateway, EventBridge, SQS, SNS, DynamoDB Streams, S3, VPC, CloudWatch, and X-Ray; deployment model is serverless functions.

### Exam Triggers
- "event-driven function"
- "no servers"
- "S3 trigger"
- "DynamoDB Streams processing"
- "short-lived code execution"

### Not to be confused with (MANDATORY)
Amazon EC2 - Lambda is serverless and event-driven, while EC2 provides full server and OS control.

▎ Choose AWS Lambda if you need automatic scale-to-zero event processing with minimal operations / Choose Amazon EC2 if you need long-running processes, OS control, or unsupported runtime/daemon behavior.

### Security & FAQ Insights
- Concurrency controls prevent downstream overload.
- VPC-attached Lambdas need network design for private resources and outbound internet.
- Use DLQs or destinations for failure handling.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon API Gateway

### Definition & Purpose
API Gateway creates, secures, throttles, and monitors REST, HTTP, and WebSocket APIs. It integrates with Lambda, VPC links, IAM, Cognito, WAF, CloudWatch, and custom authorizers; deployment model is fully managed API front door.

### Exam Triggers
- "serverless REST API"
- "throttling and usage plans"
- "WebSocket API"
- "Lambda integration"
- "private API"

### Not to be confused with (MANDATORY)
Elastic Load Balancing - API Gateway provides API management features, while ELB is general regional load balancing to targets.

▎ Choose Amazon API Gateway if you need managed API authentication, throttling, stages, keys, or Lambda-native API exposure / Choose Elastic Load Balancing if you need high-throughput regional load balancing without API management features.

### Security & FAQ Insights
- REST APIs have more management features; HTTP APIs are lower cost/lower latency for simpler APIs.
- Private APIs use interface VPC endpoints.
- Use WAF and throttling to protect public APIs.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon EventBridge

### Definition & Purpose
EventBridge routes events from AWS services, SaaS partners, and custom applications using buses, rules, schedules, and Pipes. It integrates with Lambda, Step Functions, SQS, SNS, API destinations, CloudTrail events, and Scheduler; deployment model is serverless event bus.

### Exam Triggers
- "event bus"
- "SaaS integration"
- "event-driven architecture"
- "schema registry"
- "scheduled rule"

### Not to be confused with (MANDATORY)
Amazon SNS - EventBridge filters and routes structured events across buses and targets, while SNS is high-throughput pub/sub fanout messaging.

▎ Choose Amazon EventBridge if you need event routing, SaaS events, event patterns, or decoupled application integration / Choose Amazon SNS if you need simple fanout notifications to many subscribers with high throughput.

### Security & FAQ Insights
- Event patterns filter by event fields.
- EventBridge Scheduler is preferred for advanced scheduling at scale.
- Archive and replay support event-driven recovery/testing.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon SQS

### Definition & Purpose
SQS is a fully managed queue for decoupling producers and consumers with durable asynchronous messaging. It integrates with Lambda, ECS, EC2, SNS fanout, EventBridge Pipes, and DLQs; deployment model is serverless queues.

### Exam Triggers
- "decouple producers and consumers"
- "buffer spikes"
- "dead-letter queue"
- "FIFO ordering"
- "at-least-once delivery"

### Not to be confused with (MANDATORY)
Amazon SNS - SQS queues work for consumers to pull/process, while SNS pushes notifications to multiple subscribers.

▎ Choose Amazon SQS if you need buffering, retries, backpressure, or durable work queues / Choose Amazon SNS if you need push fanout notification to multiple independent subscribers.

### Security & FAQ Insights
- Standard queues provide at-least-once delivery and best-effort ordering; FIFO provides ordering and exactly-once processing semantics within limits.
- DLQs isolate failed messages.
- Visibility timeout must exceed processing time.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon SNS

### Definition & Purpose
SNS is a fully managed pub/sub notification service for fanout to SQS, Lambda, HTTP/S, email, SMS, and mobile push. It integrates with EventBridge, SQS, Lambda, CloudWatch alarms, and KMS; deployment model is serverless pub/sub.

### Exam Triggers
- "fanout message to many subscribers"
- "push notification"
- "SMS email mobile push"
- "SNS to SQS pattern"

### Not to be confused with (MANDATORY)
Amazon SQS - SNS pushes notifications to subscribers, while SQS stores messages for consumers to poll.

▎ Choose Amazon SNS if one event must notify or fan out to multiple subscribers / Choose Amazon SQS if a worker fleet needs a durable queue and controlled message consumption.

### Security & FAQ Insights
- SNS-to-SQS fanout is a common decoupling pattern.
- FIFO topics support ordered fanout to FIFO queues.
- Use filter policies to reduce subscriber-side filtering.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS Step Functions

### Definition & Purpose
Step Functions orchestrates workflows using state machines, retries, branching, parallelism, callbacks, and human approval patterns. It integrates with Lambda, ECS, Glue, SageMaker, DynamoDB, API Gateway, EventBridge, and over 200 AWS SDK integrations; deployment model is serverless workflow orchestration.

### Exam Triggers
- "orchestrate multi-step workflow"
- "retries and branching"
- "human approval"
- "saga pattern"
- "visual state machine"

### Not to be confused with (MANDATORY)
AWS Lambda - Lambda runs code for one step, while Step Functions coordinates many steps and failure paths.

▎ Choose AWS Step Functions if you need durable orchestration, branching, retries, parallel workflows, or long-running business processes / Choose AWS Lambda if you only need a single event-driven compute step.

### Security & FAQ Insights
- Standard workflows support long-running durable workflows; Express supports high-volume short workflows.
- Use service integrations to reduce custom glue code.
- Built-in retries/catches are exam-favorite resilience features.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon ECS

### Definition & Purpose
ECS is AWS-native container orchestration for running containers on EC2 or Fargate with tight AWS integrations. It integrates with ALB/NLB, IAM task roles, CloudWatch Logs, Service Connect, Cloud Map, ECR, and Auto Scaling; deployment model is managed orchestration with EC2 or serverless Fargate compute.

### Exam Triggers
- "AWS-native container service"
- "task definition"
- "IAM role per task"
- "ECS on Fargate"
- "simple container orchestration"

### Not to be confused with (MANDATORY)
Amazon EKS - ECS is AWS-native and simpler operationally, while EKS is managed Kubernetes for Kubernetes ecosystem compatibility.

▎ Choose Amazon ECS if you want AWS-native container orchestration with minimal Kubernetes overhead / Choose Amazon EKS if the organization requires Kubernetes APIs, tooling, or portability.

### Security & FAQ Insights
- Task roles avoid sharing instance profile credentials across containers.
- Fargate removes EC2 cluster management.
- Service Connect/Cloud Map support service discovery.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon EKS

### Definition & Purpose
EKS runs managed upstream Kubernetes control planes for container workloads needing Kubernetes APIs and ecosystem compatibility. It integrates with IAM/IRSA, VPC CNI, ALB controller, ECR, CloudWatch, Fargate profiles, and EKS add-ons; deployment model is managed Kubernetes with customer-managed or serverless worker capacity.

### Exam Triggers
- "managed Kubernetes"
- "Kubernetes portability"
- "IRSA"
- "node groups"
- "EKS Fargate profile"

### Not to be confused with (MANDATORY)
Amazon ECS - EKS is Kubernetes-compatible, while ECS is AWS-native container orchestration with simpler operations.

▎ Choose Amazon EKS if you need Kubernetes-native APIs, tooling, portability, or advanced scheduling semantics / Choose Amazon ECS if you prefer simpler AWS-native container orchestration without Kubernetes management.

### Security & FAQ Insights
- IRSA maps Kubernetes service accounts to IAM roles.
- Private endpoint and node networking design are common exam traps.
- Managed node groups reduce but do not eliminate worker-node responsibility unless using Fargate.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Fargate

### Definition & Purpose
Fargate is serverless compute for ECS and EKS containers that removes the need to provision or patch EC2 worker nodes. It integrates with ECS, EKS, VPC networking, IAM task roles, CloudWatch, and ALB/NLB; deployment model is serverless containers.

### Exam Triggers
- "no EC2 nodes for containers"
- "serverless containers"
- "pay per task CPU memory"
- "ECS Fargate"
- "EKS Fargate"

### Not to be confused with (MANDATORY)
EC2 launch type - Fargate abstracts the host fleet, while EC2 launch type gives control over instances, GPUs, daemonsets, and host configuration.

▎ Choose AWS Fargate if you want to run containers without managing worker nodes / Choose EC2 launch type if you need host-level control, specialized instances, GPUs, or daemon workloads.

### Security & FAQ Insights
- Each task/pod receives isolated compute capacity.
- Pricing is based on requested CPU and memory.
- Some Kubernetes daemonset and privileged workload patterns require EC2 nodes instead.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

# Part 5 - Migration, Modernization & Deployment Automation

## AWS Application Migration Service (MGN)

### Definition & Purpose
MGN performs lift-and-shift server migration by continuously replicating source servers at the block level to AWS and launching cutover instances. It integrates with EC2, EBS, VPC, IAM, and migration waves; deployment model is managed migration replication with agents.

### Exam Triggers
- "rehost lift and shift"
- "continuous block-level replication"
- "migrate servers with minimal downtime"
- "cutover wave"

### Not to be confused with (MANDATORY)
AWS DMS - MGN migrates whole servers, while DMS migrates databases and data replication streams.

▎ Choose AWS Application Migration Service (MGN) if you need to rehost entire physical, virtual, or cloud servers to EC2 / Choose AWS DMS if you need database engine migration, CDC, or schema/data replication.

### Security & FAQ Insights
- Install agents on source servers for continuous replication.
- Use launch templates and cutover testing before final migration.
- Maps to the Rehost strategy in the 7 Rs.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Database Migration Service (DMS)

### Definition & Purpose
DMS migrates and replicates databases with full load and change data capture between homogeneous or heterogeneous engines. It integrates with SCT, RDS, Aurora, Redshift, S3, Kinesis, IAM, and CloudWatch; deployment model is managed replication instances/serverless options.

### Exam Triggers
- "database migration with CDC"
- "full load plus ongoing replication"
- "heterogeneous database migration"
- "minimal database downtime"

### Not to be confused with (MANDATORY)
AWS Application Migration Service (MGN) - DMS migrates database data, while MGN migrates whole servers.

▎ Choose AWS Database Migration Service (DMS) if you need database migration, replication, or CDC between data stores / Choose AWS Application Migration Service (MGN) if you need to move full servers/VMs as-is to EC2.

### Security & FAQ Insights
- Use Schema Conversion Tool for heterogeneous schema conversion where needed.
- DMS can target S3/Redshift for analytics pipelines.
- Replication instance sizing and LOB handling are common pitfalls.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS Schema Conversion Tool (SCT)

### Definition & Purpose
SCT converts database schemas, code objects, and assessment reports for heterogeneous database migrations. It integrates with DMS, commercial databases, open-source engines, and data warehouse migrations; deployment model is a client-side migration planning/conversion tool.

### Exam Triggers
- "convert Oracle schema to Aurora PostgreSQL"
- "heterogeneous migration assessment"
- "schema conversion report"
- "commercial database modernization"

### Not to be confused with (MANDATORY)
AWS DMS - SCT converts schema and code, while DMS moves and replicates data.

▎ Choose AWS Schema Conversion Tool (SCT) if you need to convert schema objects or assess heterogeneous database migration effort / Choose AWS DMS if you need to load or replicate the actual data.

### Security & FAQ Insights
- SCT is usually paired with DMS for heterogeneous migrations.
- It identifies manual conversion effort for incompatible objects.
- Do not choose SCT alone for ongoing CDC replication.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS DataSync

### Definition & Purpose
DataSync automates high-speed online transfer and synchronization between on-premises storage, edge locations, and AWS storage services. It integrates with NFS, SMB, HDFS, S3, EFS, FSx, IAM, CloudWatch, and task scheduling; deployment model is managed transfer service with deployable agents when needed.

### Exam Triggers
- "online file transfer"
- "scheduled incremental sync"
- "NFS SMB to S3"
- "migrate files over network"
- "preserve metadata"

### Not to be confused with (MANDATORY)
AWS Storage Gateway - DataSync transfers data, while Storage Gateway provides ongoing hybrid storage access through local protocols and cache.

▎ Choose AWS DataSync if you need one-time or recurring high-speed file/object transfer over a working network / Choose AWS Storage Gateway if applications need continuous on-prem protocol access backed by cloud storage.

### Security & FAQ Insights
- Supports verification, bandwidth controls, and scheduling.
- Requires network connectivity; use Snow Family when bandwidth is insufficient.
- Can preserve metadata depending on source/target compatibility.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through multi-service architecture tradeoffs and failure-mode scenarios.

---

## AWS CloudFormation

### Definition & Purpose
CloudFormation provisions AWS infrastructure as code using templates, stacks, change sets, StackSets, and drift detection. It integrates with most AWS services, IAM, Service Catalog, CodePipeline, and Control Tower proactive controls; deployment model is fully managed IaC orchestration.

### Exam Triggers
- "infrastructure as code"
- "change set"
- "drift detection"
- "StackSets across accounts"
- "CloudFormation hooks"

### Not to be confused with (MANDATORY)
AWS CDK - CloudFormation is the deployment engine/template model, while CDK generates CloudFormation from programming languages.

▎ Choose AWS CloudFormation if you need native declarative IaC stacks, change sets, drift detection, or cross-account StackSets / Choose AWS CDK if developers want to define infrastructure using TypeScript/Python/Java/etc. abstractions.

### Security & FAQ Insights
- StackSets deploy stacks across accounts and regions, especially with Organizations.
- Change sets preview resource changes before execution.
- Drift detection compares actual resources with stack templates.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS CDK

### Definition & Purpose
CDK lets developers define AWS infrastructure in familiar programming languages and synthesize it to CloudFormation templates. It integrates with CloudFormation, constructs, pipelines, IAM, and most AWS services; deployment model is developer-side IaC framework backed by CloudFormation.

### Exam Triggers
- "define infrastructure in TypeScript Python Java"
- "constructs"
- "synthesize CloudFormation"
- "developer-friendly IaC"

### Not to be confused with (MANDATORY)
CloudFormation - CDK is a higher-level programming framework, while CloudFormation is the underlying declarative provisioning service.

▎ Choose AWS CDK if teams want reusable coded abstractions and software-engineering patterns for IaC / Choose CloudFormation if the requirement is direct declarative templates or native stack operations only.

### Security & FAQ Insights
- CDK still deploys through CloudFormation.
- Construct libraries accelerate standardized patterns.
- Least-privilege synthesis and bootstrap roles matter in enterprise accounts.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche constraints, governance, or modernization scenarios.

---

## AWS Systems Manager

### Definition & Purpose
Systems Manager centrally manages fleets with Session Manager, Patch Manager, Run Command, Automation, Parameter Store, Inventory, and State Manager. It integrates with EC2, hybrid activations, IAM, CloudWatch, KMS, Config remediation, and Organizations; deployment model is fully managed operations control plane with SSM Agent.

### Exam Triggers
- "patch fleet"
- "run commands without SSH"
- "Session Manager"
- "Parameter Store"
- "automation documents"

### Not to be confused with (MANDATORY)
AWS Config - Systems Manager executes operational actions and automation, while Config evaluates resource compliance state.

▎ Choose AWS Systems Manager if you need fleet operations, patching, remote command execution, secure shell replacement, or automation runbooks / Choose AWS Config if you need continuous configuration compliance recording/evaluation.

### Security & FAQ Insights
- Session Manager reduces bastion and inbound SSH exposure.
- Automation documents are often used for remediation.
- Parameter Store stores config/secrets but Secrets Manager has stronger rotation features.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

# Part 6 - Security, Observability, Cost & Reliability

## AWS KMS

### Definition & Purpose
KMS creates and controls cryptographic keys for encryption across AWS services and custom applications. It integrates with S3, EBS, RDS, DynamoDB, CloudTrail, Secrets Manager, IAM, CloudHSM, and multi-Region keys; deployment model is fully managed key management backed by AWS HSMs.

### Exam Triggers
- "customer managed key"
- "envelope encryption"
- "multi-Region key"
- "BYOK imported key material"
- "KMS key policy"

### Not to be confused with (MANDATORY)
AWS CloudHSM - KMS is managed key service integrated broadly across AWS, while CloudHSM provides customer-controlled dedicated HSM clusters.

▎ Choose AWS KMS if you need managed encryption keys integrated with AWS services and IAM/key policies / Choose AWS CloudHSM if you need dedicated HSM control, custom cryptographic modules, or strict single-tenant HSM requirements.

### Security & FAQ Insights
- Key policies are the primary access control for KMS keys.
- Imported key material cannot be automatically rotated by KMS.
- Multi-Region keys share key ID/material for cross-region decrypt without cross-region KMS calls.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Secrets Manager

### Definition & Purpose
Secrets Manager stores, encrypts, retrieves, and rotates secrets such as database credentials and API keys. It integrates with KMS, RDS/Aurora rotation, Lambda rotation functions, IAM, CloudTrail, and applications; deployment model is fully managed secrets storage and rotation.

### Exam Triggers
- "rotate database password automatically"
- "store API key securely"
- "Lambda rotation function"
- "retrieve secrets at runtime"

### Not to be confused with (MANDATORY)
Systems Manager Parameter Store - Secrets Manager focuses on secrets and rotation, while Parameter Store is cheaper hierarchical configuration storage with basic secure string support.

▎ Choose AWS Secrets Manager if you need managed secret rotation, especially for database credentials / Choose Systems Manager Parameter Store if you need simple configuration parameters or low-cost secure strings without rotation workflow.

### Security & FAQ Insights
- Secrets are encrypted with KMS.
- Rotation commonly uses Lambda and staging labels.
- Audit access with CloudTrail and scope IAM permissions tightly.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## Amazon GuardDuty

### Definition & Purpose
GuardDuty detects threats using CloudTrail management events, VPC Flow Logs, DNS logs, EKS audit logs, malware protection, and other telemetry. It integrates with Organizations, Security Hub, EventBridge, Detective, and S3 protection; deployment model is fully managed threat detection.

### Exam Triggers
- "threat detection"
- "suspicious API call"
- "cryptocurrency mining finding"
- "malware protection"
- "enable org-wide detection"

### Not to be confused with (MANDATORY)
AWS Security Hub - GuardDuty generates threat findings, while Security Hub aggregates and scores findings from many security services.

▎ Choose Amazon GuardDuty if you need managed threat detection from AWS telemetry / Choose AWS Security Hub if you need centralized security posture aggregation and compliance checks.

### Security & FAQ Insights
- Enable delegated administrator for organization-wide deployment.
- Findings can trigger EventBridge automation.
- GuardDuty is detective, not preventive.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS Security Hub

### Definition & Purpose
Security Hub centralizes security findings and compliance posture using standards such as AWS Foundational Security Best Practices and CIS. It integrates GuardDuty, Inspector, Macie, IAM Access Analyzer, Config, Firewall Manager, Organizations, and EventBridge; deployment model is fully managed security posture aggregation.

### Exam Triggers
- "central security findings"
- "CIS benchmark"
- "AWS Foundational Security Best Practices"
- "aggregate GuardDuty Inspector Macie"
- "compliance score"

### Not to be confused with (MANDATORY)
Amazon GuardDuty - Security Hub aggregates and prioritizes findings, while GuardDuty performs threat detection.

▎ Choose AWS Security Hub if you need a central dashboard, standards checks, and normalized findings across accounts / Choose Amazon GuardDuty if you need the actual threat detection engine for suspicious activity.

### Security & FAQ Insights
- Use Organizations delegated admin for multi-account enablement.
- Findings use AWS Security Finding Format.
- Security Hub does not replace the source detection services.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## Amazon Macie

### Definition & Purpose
Macie discovers and classifies sensitive data such as PII in S3 using managed data identifiers and custom patterns. It integrates with S3, Organizations, Security Hub, EventBridge, KMS, and IAM; deployment model is fully managed data security and privacy scanning.

### Exam Triggers
- "discover PII in S3"
- "sensitive data classification"
- "data privacy scan"
- "unprotected bucket contains credentials"

### Not to be confused with (MANDATORY)
Amazon GuardDuty - Macie classifies sensitive S3 data, while GuardDuty detects threats and suspicious activity.

▎ Choose Amazon Macie if you need to discover, classify, or report sensitive data stored in S3 / Choose Amazon GuardDuty if you need behavioral threat detection from logs and telemetry.

### Security & FAQ Insights
- Macie focuses primarily on S3 data discovery/classification.
- Findings can flow to Security Hub and EventBridge.
- Use sampling and job scope to manage cost.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## Amazon Inspector

### Definition & Purpose
Inspector scans EC2, ECR container images, and Lambda functions for software vulnerabilities and network exposure. It integrates with Systems Manager Agent, ECR, Lambda, Organizations, Security Hub, and EventBridge; deployment model is fully managed vulnerability management.

### Exam Triggers
- "vulnerability scan EC2"
- "scan container images in ECR"
- "Lambda package vulnerabilities"
- "CVE findings"

### Not to be confused with (MANDATORY)
Amazon GuardDuty - Inspector finds vulnerabilities and exposure, while GuardDuty detects active threats and suspicious behavior.

▎ Choose Amazon Inspector if you need continuous vulnerability management for compute workloads and images / Choose Amazon GuardDuty if you need runtime threat detection based on account/network activity.

### Security & FAQ Insights
- Inspector findings include CVE context and prioritization.
- SSM Agent coverage matters for EC2 scanning.
- Push findings to Security Hub for centralized posture.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## Amazon CloudWatch

### Definition & Purpose
CloudWatch collects metrics, logs, alarms, events, dashboards, synthetics, and application insights for AWS resources and applications. It integrates with most AWS services, Lambda, EC2 agents, Container Insights, X-Ray, EventBridge, and SNS; deployment model is fully managed observability.

### Exam Triggers
- "metrics alarms dashboards"
- "centralized logs"
- "CloudWatch Agent"
- "Container Insights"
- "Synthetics canary"

### Not to be confused with (MANDATORY)
AWS CloudTrail - CloudWatch monitors performance/log telemetry and alarms, while CloudTrail records API activity for audit.

▎ Choose Amazon CloudWatch if you need operational metrics, logs, alarms, dashboards, or application monitoring / Choose AWS CloudTrail if you need an audit trail of AWS API calls and identity activity.

### Security & FAQ Insights
- Use metric filters to turn log patterns into alarms.
- High-resolution metrics cost more but support finer periods.
- Alarms commonly notify through SNS or trigger automation.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS CloudTrail

### Definition & Purpose
CloudTrail records AWS account activity and API calls for governance, audit, and security analysis. It integrates with Organizations trails, S3, CloudWatch Logs, EventBridge, Lake, KMS, and Security Hub; deployment model is fully managed audit logging.

### Exam Triggers
- "who called this API"
- "organization trail"
- "management events data events"
- "audit AWS account activity"
- "CloudTrail Lake"

### Not to be confused with (MANDATORY)
Amazon CloudWatch - CloudTrail is audit/API activity logging, while CloudWatch is operational metrics/logs/alarms.

▎ Choose AWS CloudTrail if you need to audit API calls, identity activity, or resource changes initiated through AWS APIs / Choose Amazon CloudWatch if you need performance metrics, application logs, or operational alarms.

### Security & FAQ Insights
- Organization trails centralize multi-account logging.
- Data events for S3/Lambda are high-volume and must be enabled selectively.
- Log file validation and KMS encryption support compliance.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-selection traps.

---

## AWS X-Ray

### Definition & Purpose
X-Ray traces distributed application requests across services to identify latency, errors, and service map dependencies. It integrates with Lambda, API Gateway, ECS, EKS, EC2 agents, Elastic Beanstalk, and CloudWatch ServiceLens; deployment model is fully managed tracing with application instrumentation.

### Exam Triggers
- "distributed tracing"
- "service map"
- "trace latency through microservices"
- "debug serverless API"

### Not to be confused with (MANDATORY)
Amazon CloudWatch - X-Ray traces request paths, while CloudWatch collects metrics/logs/alarms.

▎ Choose AWS X-Ray if you need end-to-end request tracing and latency breakdown across microservices / Choose Amazon CloudWatch if you need broad metrics/logs/alarms without request trace context.

### Security & FAQ Insights
- Requires SDK/agent/instrumentation for application segments.
- Sampling controls cost and volume.
- Useful for continuous improvement and root-cause scenarios.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS Trusted Advisor

### Definition & Purpose
Trusted Advisor provides best-practice checks across cost optimization, performance, security, fault tolerance, and service limits. It integrates with AWS Support plans, Organizations views, CloudWatch Events/EventBridge, and cost workflows; deployment model is fully managed advisory checks.

### Exam Triggers
- "best practice checks"
- "service limits"
- "underutilized resources"
- "cost optimization recommendations"
- "support plan advisor"

### Not to be confused with (MANDATORY)
AWS Compute Optimizer - Trusted Advisor spans multiple pillars, while Compute Optimizer focuses on rightsizing compute resources from utilization metrics.

▎ Choose AWS Trusted Advisor if you need broad best-practice checks across cost, limits, security, performance, and fault tolerance / Choose AWS Compute Optimizer if you need detailed rightsizing recommendations for EC2, EBS, Lambda, ECS, or Auto Scaling.

### Security & FAQ Insights
- Full check access depends on support plan.
- Common exam use is cost optimization and service quota risk.
- Use with Cost Explorer and Compute Optimizer for continuous improvement.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

## AWS Cost Explorer

### Definition & Purpose
Cost Explorer analyzes AWS spend and usage trends, forecasts, and cost dimensions for optimization. It integrates with Organizations, cost allocation tags, Budgets, Savings Plans, and RI recommendations; deployment model is fully managed cost analytics.

### Exam Triggers
- "analyze historical cost"
- "forecast spend"
- "cost allocation tags"
- "Savings Plans recommendations"
- "chargeback showback"

### Not to be confused with (MANDATORY)
AWS Budgets - Cost Explorer analyzes and forecasts spend, while Budgets alerts or controls against thresholds.

▎ Choose AWS Cost Explorer if you need interactive spend analysis, forecasting, or purchase recommendations / Choose AWS Budgets if you need notifications or actions when spend/usage exceeds thresholds.

### Security & FAQ Insights
- Activate cost allocation tags before relying on them for reporting.
- Organizations payer account sees consolidated costs.
- Use with CUR for detailed data-lake style cost analytics.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the primary triggers, limitations, and nearest alternatives.

---

# Exam Coverage Check

Coverage verified against the official SAP-C02 domain areas: organizational complexity; new solutions; continuous improvement; migration and modernization. This version explicitly covers the major exam service families: Organizations/SCP/Control Tower/RAM/Service Catalog/Config/IAM/IAM Identity Center/STS/Cognito; VPC/TGW/PrivateLink/DX/VPN/Route 53/CloudFront/Global Accelerator/ELB/Network Firewall/WAF/Shield; S3/EFS/FSx/Storage Gateway/Snow/Backup; RDS/Aurora/DynamoDB/ElastiCache/Redshift; EC2/Auto Scaling/Lambda/API Gateway/EventBridge/SQS/SNS/Step Functions/ECS/EKS/Fargate; MGN/DMS/SCT/DataSync/CloudFormation/CDK/Systems Manager; KMS/Secrets Manager/GuardDuty/Security Hub/Macie/Inspector/CloudWatch/CloudTrail/X-Ray/Trusted Advisor/Cost Explorer.

Reference: AWS Certified Solutions Architect - Professional (SAP-C02) exam guide and official AWS service FAQs/documentation.
