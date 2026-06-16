# GCP Professional Cloud Architect - Ultimate Cheat Sheet

## How This Cheat Sheet Is Built (Methodology)

This guide is an English, exam-focused reference for the Google Cloud Professional Cloud Architect exam. It uses one service or one exam-critical feature per section, preserves AWS comparison context only when it helps avoid common traps, and adds major services that commonly appear in PCA case studies.

---

## Scope and Ordering Rules

**Rule 1 - Scope (one service = one section)**  
Each section covers exactly one Google Cloud service or one key exam-critical feature. Grouped comparisons from older versions have been split into separate entries when the exam requires choosing between services.

**Rule 2 - Order (logical architecture pipeline)**  
Services are ordered from resource hierarchy and identity, through networking and compute, then storage/data, migration, security, operations, DevOps, API management, and cost/governance.

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

# Part 1 - Resource Hierarchy, Identity & Governance

## Resource Manager

### Definition & Purpose
Resource Manager defines the Google Cloud hierarchy: organization, folders, projects, and resources. It integrates with IAM, Organization Policy, Cloud Billing, Cloud Asset Inventory, labels, tags, and quotas; deployment model is a fully managed control plane.

### Exam Triggers
- "organization folder project hierarchy"
- "project ID project number"
- "inherit IAM policies"
- "one project per environment"
- "quotas by project"

### Not to be confused with (MANDATORY)
AWS Organizations - Resource Manager projects are lighter-weight resource containers than AWS accounts, while AWS Organizations manages accounts and OUs.

▎ Choose Resource Manager if you need to structure Google Cloud resources, projects, folders, labels, tags, or quotas / Choose AWS Organizations if the question is explicitly about AWS account/OUs rather than GCP projects/folders.

### Security & FAQ Insights
- IAM policies inherit downward from organization to folders to projects/resources.
- Projects are the base unit for billing, API enablement, quotas, and many isolation boundaries.
- Project IDs are globally unique and immutable after creation.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Cloud Identity

### Definition & Purpose
Cloud Identity provides managed users, groups, and directory services for organizations that do not use Google Workspace. It integrates with IAM, Google Groups, SAML/OIDC federation, device management, and admin controls; deployment model is fully managed identity directory.

### Exam Triggers
- "user directory without Google Workspace"
- "central users and groups"
- "Cloud Identity domain"
- "workforce identity base"

### Not to be confused with (MANDATORY)
IAM - Cloud Identity manages identities and groups, while IAM grants roles to those identities on resources.

▎ Choose Cloud Identity if you need to manage human identities/groups as the directory foundation / Choose IAM if you need to grant permissions to identities on Google Cloud resources.

### Security & FAQ Insights
- An organization resource is tied to a Cloud Identity or Google Workspace domain.
- Use groups rather than individual users for scalable IAM.
- Cloud Identity is about identity lifecycle, not resource authorization by itself.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud IAM

### Definition & Purpose
IAM grants roles to principals on resources using allow policies and role bindings. It integrates with Resource Manager, service accounts, conditions, deny policies, Policy Troubleshooter, and Recommender; deployment model is a fully managed authorization control plane.

### Exam Triggers
- "predefined custom basic roles"
- "least privilege"
- "IAM bindings"
- "conditional IAM"
- "Policy Troubleshooter"

### Not to be confused with (MANDATORY)
Organization Policy - IAM grants who can do what, while Organization Policy constrains what configurations are allowed.

▎ Choose Cloud IAM if you need to grant or analyze permissions for users, groups, service accounts, or domains / Choose Organization Policy if you need to restrict resource configuration choices such as allowed regions or external IPs.

### Security & FAQ Insights
- Basic roles are broad legacy roles and should be avoided in production.
- IAM is additive unless IAM Deny Policies are used.
- Use predefined roles before custom roles when possible.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Service Accounts

### Definition & Purpose
Service Accounts are non-human identities used by applications, VMs, GKE workloads, CI/CD systems, and services to call Google Cloud APIs. They integrate with IAM, Workload Identity Federation, Compute Engine attached identities, GKE Workload Identity, Cloud Run, and key management; deployment model is fully managed workload identity.

### Exam Triggers
- "application identity"
- "attached service account"
- "avoid JSON keys"
- "serviceAccountTokenCreator"
- "impersonation"

### Not to be confused with (MANDATORY)
Cloud Identity users - Service Accounts are workload identities, while Cloud Identity users are human identities.

▎ Choose Service Accounts if an application or workload needs to call Google Cloud APIs with least privilege / Choose Cloud Identity users if a human user or group needs login and workforce access.

### Security & FAQ Insights
- Avoid long-lived JSON keys; prefer attached service accounts, impersonation, or Workload Identity Federation.
- The actAs permission controls who can attach or impersonate a service account.
- Default service accounts often have overly broad permissions in legacy environments.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Organization Policy Service

### Definition & Purpose
Organization Policy enforces hierarchical constraints on resources, such as allowed regions, external IP restrictions, and service account key creation bans. It integrates with Resource Manager, folders, projects, IAM, and secure tags; deployment model is fully managed governance guardrails.

### Exam Triggers
- "restrict allowed regions"
- "disable service account key creation"
- "block external IPs"
- "policy inherited from folder"
- "govern resource configuration"

### Not to be confused with (MANDATORY)
Cloud IAM - Organization Policy constrains allowed configurations, while IAM controls who has permissions.

▎ Choose Organization Policy Service if you need preventive configuration guardrails across projects or folders / Choose Cloud IAM if you need to grant or revoke access to principals.

### Security & FAQ Insights
- Policies inherit downward but can be overridden or merged depending on constraint type.
- Common exam trap: IAM may allow an action but Org Policy can still block the resulting configuration.
- Use tags for conditional governance on selected resources.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## IAM Deny Policies

### Definition & Purpose
IAM Deny Policies explicitly deny permissions for principals and override allow policies in supported scenarios. They integrate with IAM and Resource Manager hierarchy; deployment model is fully managed authorization guardrails.

### Exam Triggers
- "explicit deny in GCP"
- "block permission even if allowed"
- "deny policy"
- "exception principal"

### Not to be confused with (MANDATORY)
Organization Policy - IAM Deny blocks permissions/actions, while Organization Policy blocks resource configurations.

▎ Choose IAM Deny Policies if you need an explicit permission deny that overrides IAM allows / Choose Organization Policy if you need to restrict configurations such as location, public IPs, or service usage.

### Security & FAQ Insights
- Deny policies are evaluated before allow policies for covered permissions.
- Use carefully because they can block administrators if exceptions are not designed.
- Not every permission/resource type has identical support, so validate service compatibility.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

# Part 2 - Networking, Edge & Private Connectivity

## Virtual Private Cloud (VPC)

### Definition & Purpose
Google Cloud VPC is a global virtual network with regional subnets, global routes, firewall rules, and private connectivity controls. It integrates with Compute Engine, GKE, Cloud NAT, Cloud Router, load balancing, Private Service Connect, and VPC Flow Logs; deployment model is customer-designed networking on Google-managed infrastructure.

### Exam Triggers
- "global VPC regional subnets"
- "auto mode vs custom mode"
- "firewall rules at VPC level"
- "routes are global"
- "subnets span zones"

### Not to be confused with (MANDATORY)
AWS VPC - GCP VPCs are global with regional subnets, while AWS VPCs are regional.

▎ Choose Virtual Private Cloud (VPC) if the scenario depends on GCP global VPC behavior, subnet design, routing, or firewall rules / Choose AWS VPC if the question compares with AWS regional VPC semantics or account-level VPCs.

### Security & FAQ Insights
- Custom mode VPC is preferred for production because it gives explicit subnet control.
- Default ingress is denied and default egress is allowed unless changed.
- Firewall rules can target network tags or service accounts.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Shared VPC

### Definition & Purpose
Shared VPC lets a host project share subnets with service projects while centralizing network administration. It integrates with Resource Manager, IAM, GKE, Compute Engine, Cloud SQL private connectivity, and firewall policies; deployment model is managed cross-project network sharing.

### Exam Triggers
- "host project service project"
- "centralized networking team"
- "shared subnets"
- "separate billing per project"
- "network isolation with shared VPC"

### Not to be confused with (MANDATORY)
VPC Network Peering - Shared VPC shares subnets from one host project, while Peering privately connects independent VPC networks.

▎ Choose Shared VPC if you need centralized network administration with application resources in separate service projects / Choose VPC Network Peering if you need connectivity between two separate VPC networks without sharing subnets.

### Security & FAQ Insights
- Service projects use host project subnets but keep resource ownership and IAM separation.
- Subnet IAM controls who can use shared subnets.
- Common enterprise pattern for hub networking and project isolation.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## VPC Network Peering

### Definition & Purpose
VPC Network Peering privately connects two VPC networks across projects or organizations without public IPs. It integrates with VPC routes, DNS, and Private Services Access patterns; deployment model is managed private network peering.

### Exam Triggers
- "private connectivity between two VPCs"
- "non-transitive peering"
- "no overlapping CIDR"
- "cross-project VPC connection"

### Not to be confused with (MANDATORY)
Network Connectivity Center - VPC Peering is non-transitive pairwise connectivity, while NCC is a hub for broader network connectivity patterns.

▎ Choose VPC Network Peering if two non-overlapping VPC networks need private connectivity with simple routing / Choose Network Connectivity Center if many networks need hub-and-spoke or transitive connectivity.

### Security & FAQ Insights
- Peering is not transitive by default.
- Overlapping IP ranges are not supported.
- Export/import custom routes must be designed carefully.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Network Connectivity Center

### Definition & Purpose
Network Connectivity Center provides a hub-and-spoke model to connect VPCs, on-premises networks, and other cloud networks. It integrates with Cloud VPN, Cloud Interconnect, Router appliances, VPC spokes, and hybrid connectivity; deployment model is fully managed network connectivity hub.

### Exam Triggers
- "hub-and-spoke networking"
- "transitive connectivity"
- "hybrid network hub"
- "connect many VPCs and on-prem"

### Not to be confused with (MANDATORY)
VPC Network Peering - NCC is a central connectivity hub, while peering is direct non-transitive VPC-to-VPC connectivity.

▎ Choose Network Connectivity Center if you need scalable hub-and-spoke connectivity across many networks or hybrid spokes / Choose VPC Network Peering if you only need simple private connectivity between two VPCs.

### Security & FAQ Insights
- Used for enterprise hybrid and multi-cloud connectivity patterns.
- Cloud Router/BGP design still matters for dynamic routing.
- Do not use simple peering meshes when a hub architecture is required.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Cloud Interconnect

### Definition & Purpose
Cloud Interconnect provides dedicated or partner private connectivity from on-premises networks to Google Cloud. It integrates with Cloud Router, VLAN attachments, Dedicated Interconnect, Partner Interconnect, Cross-Cloud Interconnect, and HA VPN; deployment model is dedicated or partner-provided physical connectivity.

### Exam Triggers
- "Dedicated Interconnect"
- "Partner Interconnect"
- "Cross-Cloud Interconnect"
- "private high bandwidth hybrid"
- "Cloud Router BGP"

### Not to be confused with (MANDATORY)
Cloud VPN - Interconnect is private dedicated connectivity, while Cloud VPN is encrypted IPsec over the internet.

▎ Choose Cloud Interconnect if you need private high-bandwidth low-latency hybrid connectivity / Choose Cloud VPN if you need quick encrypted internet-based connectivity or backup tunnels.

### Security & FAQ Insights
- Cloud Interconnect is not automatically encrypted; use MACsec where supported or VPN overlay if encryption is required.
- Cloud Router exchanges routes dynamically with BGP.
- Partner Interconnect is useful when you lack presence in a colocation facility.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud VPN

### Definition & Purpose
Cloud VPN provides encrypted IPsec tunnels between Google Cloud and on-premises or other cloud networks. It integrates with HA VPN, Cloud Router, VPC, and hybrid routing; deployment model is fully managed VPN gateway with customer-managed peer gateway.

### Exam Triggers
- "IPsec VPN"
- "HA VPN SLA 99.99"
- "Classic VPN legacy"
- "encrypted hybrid tunnel"
- "BGP with Cloud Router"

### Not to be confused with (MANDATORY)
Cloud Interconnect - VPN is encrypted over the internet, while Interconnect is private dedicated connectivity.

▎ Choose Cloud VPN if you need encrypted hybrid connectivity quickly or as a backup path / Choose Cloud Interconnect if you need predictable high-throughput private connectivity.

### Security & FAQ Insights
- HA VPN with two interfaces is preferred over Classic VPN.
- Use Cloud Router/BGP for dynamic failover.
- VPN can complement Interconnect for encryption or resilience.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud Load Balancing

### Definition & Purpose
Cloud Load Balancing provides global and regional load balancing for HTTP(S), TCP/SSL proxy, passthrough network, internal, and external workloads. It integrates with Cloud CDN, Cloud Armor, MIGs, GKE ingress, serverless NEGs, health checks, and anycast IPs; deployment model is fully managed load balancing.

### Exam Triggers
- "global external Application Load Balancer"
- "single anycast IP"
- "serverless NEG"
- "passthrough vs proxy load balancer"
- "GKE ingress creates load balancer"

### Not to be confused with (MANDATORY)
Cloud DNS - Load Balancing actively routes traffic to backends with health checks, while Cloud DNS provides name resolution and simpler DNS records.

▎ Choose Cloud Load Balancing if you need managed traffic distribution, global anycast, health checks, or L7/L4 load balancing / Choose Cloud DNS if you only need DNS zones, records, or name resolution policies.

### Security & FAQ Insights
- Global external Application Load Balancer is a major PCA differentiator.
- Passthrough Network Load Balancer preserves source IP; proxy load balancers terminate proxy connections.
- Cloud Armor and Cloud CDN attach to supported external Application Load Balancers.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Cloud CDN

### Definition & Purpose
Cloud CDN caches HTTP(S) content at Google edge locations through supported load balancer backends. It integrates with external Application Load Balancing, Cloud Storage backends, signed URLs/cookies, Cloud Armor, and logging; deployment model is fully managed edge caching.

### Exam Triggers
- "cache content at edge"
- "enable CDN on load balancer backend"
- "signed URLs"
- "reduce latency for static assets"

### Not to be confused with (MANDATORY)
Cloud Load Balancing - CDN caches content at the edge, while Load Balancing distributes traffic to backends.

▎ Choose Cloud CDN if you need edge caching and reduced latency/cost for cacheable web content / Choose Cloud Load Balancing if you need traffic distribution even when caching is not relevant.

### Security & FAQ Insights
- Cloud CDN is enabled on compatible load balancer backend services/buckets.
- Signed URLs and signed cookies support private cached content.
- Not for arbitrary TCP/UDP traffic.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud DNS

### Definition & Purpose
Cloud DNS provides managed public and private DNS zones, records, forwarding, peering, and DNS policies. It integrates with VPC private zones, hybrid forwarding, DNS peering, and Cloud Logging; deployment model is fully managed DNS.

### Exam Triggers
- "public private DNS zones"
- "DNS forwarding to on-prem"
- "DNS peering"
- "split-horizon DNS"
- "managed DNS"

### Not to be confused with (MANDATORY)
Cloud Load Balancing - Cloud DNS resolves names, while Load Balancing distributes application traffic and health checks.

▎ Choose Cloud DNS if you need authoritative DNS, private DNS, hybrid forwarding, or split-horizon resolution / Choose Cloud Load Balancing if you need active traffic distribution to healthy backends.

### Security & FAQ Insights
- Cloud DNS does not mirror every Route 53 advanced policy directly.
- Private zones are visible only to authorized VPC networks.
- DNS peering lets one VPC resolve another VPC project private zone.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud NAT

### Definition & Purpose
Cloud NAT provides managed outbound internet access for private VM and GKE workloads without external IP addresses. It integrates with Cloud Router, VPC subnets, Compute Engine, GKE, logging, and regional NAT configuration; deployment model is fully managed regional NAT.

### Exam Triggers
- "VMs without external IP need internet egress"
- "managed NAT"
- "private GKE nodes outbound access"
- "no inbound access"

### Not to be confused with (MANDATORY)
Cloud VPN - Cloud NAT is outbound internet address translation, while Cloud VPN is encrypted network-to-network connectivity.

▎ Choose Cloud NAT if private resources need outbound internet access without public IPs / Choose Cloud VPN if two networks need encrypted private connectivity.

### Security & FAQ Insights
- Cloud NAT is regional and uses Cloud Router for control-plane configuration, not necessarily dynamic routes.
- It does not allow inbound initiation from the internet.
- Enable NAT logging for troubleshooting and audit.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Armor

### Definition & Purpose
Cloud Armor provides WAF and DDoS protections for applications behind supported Google Cloud load balancers. It integrates with external Application Load Balancers, edge security policies, reCAPTCHA Enterprise, preconfigured WAF rules, and logging; deployment model is fully managed edge security.

### Exam Triggers
- "WAF for load balancer"
- "DDoS protection"
- "SQL injection XSS rules"
- "rate limiting"
- "geo IP filtering"

### Not to be confused with (MANDATORY)
VPC Firewall Rules - Cloud Armor filters L7 edge traffic at load balancers, while VPC firewall rules filter network traffic to resources.

▎ Choose Cloud Armor if you need web-layer protection or DDoS controls at the edge/load balancer / Choose VPC Firewall Rules if you need VPC-level ingress/egress controls for internal resources.

### Security & FAQ Insights
- Cloud Armor policies attach to supported external load balancer backends.
- Preconfigured WAF rules cover common OWASP patterns.
- Use with Cloud CDN/Load Balancing for internet-facing apps.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Private Service Connect

### Definition & Purpose
Private Service Connect provides private endpoints for consuming Google APIs, managed services, or producer services without exposing traffic to the public internet. It integrates with VPC, service attachments, endpoints, Google APIs, producer/consumer projects, and load balancing; deployment model is fully managed private service connectivity.

### Exam Triggers
- "PrivateLink-like endpoint"
- "consume service privately"
- "overlapping CIDR friendly"
- "private endpoint to Google APIs"
- "producer service attachment"

### Not to be confused with (MANDATORY)
Private Services Access - PSC uses endpoints/service attachments without peering, while PSA uses VPC peering to allocate private IPs for certain Google managed services.

▎ Choose Private Service Connect if you need endpoint-based private access to Google APIs, managed services, or producer services without VPC peering / Choose Private Services Access if Cloud SQL/AlloyDB/Memorystore private IP connectivity requires allocated range peering semantics.

### Security & FAQ Insights
- PSC avoids some peering transitivity and overlapping CIDR issues.
- It supports private access to Google APIs and producer services.
- Producer/consumer IAM and endpoint controls matter in cross-project designs.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Private Google Access

### Definition & Purpose
Private Google Access lets VMs without external IPs reach Google APIs and services using internal/private routes. It integrates with VPC subnets, Cloud Storage, BigQuery, routing, restricted.googleapis.com, and Private Service Connect for Google APIs; deployment model is a VPC subnet/private access feature.

### Exam Triggers
- "VM without external IP calls Cloud Storage"
- "private access to Google APIs"
- "restricted.googleapis.com"
- "no NAT for Google APIs"

### Not to be confused with (MANDATORY)
Cloud NAT - Private Google Access reaches Google APIs privately, while Cloud NAT provides outbound internet access to public endpoints.

▎ Choose Private Google Access if private VMs need access to Google APIs such as Cloud Storage or BigQuery without external IPs / Choose Cloud NAT if private VMs need general outbound internet access beyond Google APIs.

### Security & FAQ Insights
- Enabled per subnet for VM-based access patterns.
- Use restricted.googleapis.com with VPC Service Controls for stronger data exfiltration controls.
- Serverless products may have different private access mechanisms.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## VPC Service Controls

### Definition & Purpose
VPC Service Controls creates security perimeters around supported Google Cloud resources to reduce data exfiltration risk even when credentials are valid. It integrates with Access Context Manager, Cloud Storage, BigQuery, restricted.googleapis.com, IAM, and audit logs; deployment model is fully managed data perimeter security.

### Exam Triggers
- "security perimeter"
- "prevent data exfiltration"
- "valid credentials but outside perimeter"
- "restricted.googleapis.com"
- "Access Context Manager"

### Not to be confused with (MANDATORY)
Cloud IAM - IAM decides who can access a resource, while VPC Service Controls restrict where access can come from and where data can move.

▎ Choose VPC Service Controls if you need a data exfiltration perimeter around supported services/projects / Choose Cloud IAM if you only need identity-based allow/deny authorization.

### Security & FAQ Insights
- It is not a network firewall; it is a service perimeter control.
- Dry-run mode helps test policies before enforcement.
- Common exam trap: IAM may allow access but the perimeter still denies it.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Private Services Access

### Definition & Purpose
Private Services Access connects your VPC to Google-managed service producer networks using allocated IP ranges and service networking peering. It integrates with Cloud SQL private IP, AlloyDB, Memorystore, Vertex AI, VPC peering, and Service Networking API; deployment model is managed private connectivity via peering.

### Exam Triggers
- "Cloud SQL private IP"
- "allocated IP range"
- "service networking peering"
- "managed service in Google tenant VPC"
- "PSA not PSC"

### Not to be confused with (MANDATORY)
Private Service Connect - PSA is peering-based for first-party managed services, while PSC is endpoint/service-attachment based and more flexible.

▎ Choose Private Services Access if a supported Google managed service needs a private IP allocated from your VPC range / Choose Private Service Connect if you need endpoint-based private consumption/exposure without VPC peering.

### Security & FAQ Insights
- PSA inherits VPC Peering non-transitivity constraints.
- Plan allocated ranges carefully; too-small ranges cause future expansion problems.
- A single private connection can serve multiple supported services in a VPC.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Serverless VPC Access

### Definition & Purpose
Serverless VPC Access connects serverless environments such as Cloud Run, Cloud Functions, and App Engine Standard to resources with internal VPC IPs. It integrates with VPC connectors, Cloud SQL private IP, Memorystore, Compute Engine, and serverless services; deployment model is managed connector infrastructure.

### Exam Triggers
- "Cloud Run access private VM"
- "serverless to VPC internal IP"
- "VPC connector"
- "Cloud Functions private database access"

### Not to be confused with (MANDATORY)
Private Service Connect - Serverless VPC Access sends serverless egress into your VPC, while PSC provides private service endpoints.

▎ Choose Serverless VPC Access if serverless workloads need to reach internal VPC resources / Choose Private Service Connect if clients need endpoint-based private access to managed or producer services.

### Security & FAQ Insights
- Connector sizing and region placement affect throughput and cost.
- Cloud Run also supports direct VPC egress options in newer patterns.
- Without a connector/direct egress, serverless cannot reach arbitrary internal VM IPs.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Firewall Policies and VPC Firewall Rules

### Definition & Purpose
Google Cloud firewall controls include VPC firewall rules, global/regional network firewall policies, and hierarchical firewall policies. They integrate with VPC, secure tags, service accounts, folders/organizations, logging, and Network Security; deployment model is fully managed distributed firewall enforcement.

### Exam Triggers
- "hierarchical firewall policy"
- "network tags vs service accounts"
- "priority number lower wins"
- "organization-level firewall rule"
- "secure tags"

### Not to be confused with (MANDATORY)
Cloud Armor - Firewall rules/policies filter VPC network traffic, while Cloud Armor protects HTTP/S apps at the load balancer edge.

▎ Choose Firewall Policies and VPC Firewall Rules if you need VPC or organization-level network access controls / Choose Cloud Armor if you need L7 WAF/DDoS controls for internet-facing load-balanced apps.

### Security & FAQ Insights
- Lower priority numbers take precedence.
- Hierarchical firewall policies enforce org/folder-level rules that projects cannot bypass.
- Firewall Rules Logging shows which rules matched traffic.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## VPC Flow Logs

### Definition & Purpose
VPC Flow Logs samples and records network flows for VM NICs and subnets for troubleshooting, forensics, and network analysis. It integrates with Cloud Logging, BigQuery, Pub/Sub, and Network Intelligence Center; deployment model is fully managed telemetry collection.

### Exam Triggers
- "capture network flows"
- "troubleshoot connectivity"
- "export flow logs to BigQuery"
- "audit traffic patterns"

### Not to be confused with (MANDATORY)
Firewall Rules Logging - VPC Flow Logs records network flow metadata, while Firewall Rules Logging records connections evaluated by specific firewall rules.

▎ Choose VPC Flow Logs if you need broad network flow visibility for subnets or VM NICs / Choose Firewall Rules Logging if you need to know which firewall rule allowed or denied a connection.

### Security & FAQ Insights
- Flow logs are sampled and metadata-focused, not packet capture.
- Exports to BigQuery support network analytics.
- Enable only where useful to control log volume and cost.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

# Part 3 - Compute, Containers & Serverless

## Compute Engine

### Definition & Purpose
Compute Engine provides virtual machines, machine families, GPUs/TPUs attachments, disks, metadata, and purchasing options. It integrates with VPC, Persistent Disk, MIGs, service accounts, OS Login, Cloud Monitoring, and load balancers; deployment model is customer-managed VM compute on Google infrastructure.

### Exam Triggers
- "VM instances"
- "machine types"
- "Spot VMs"
- "sole-tenant nodes"
- "custom machine types"

### Not to be confused with (MANDATORY)
Cloud Run - Compute Engine provides VM/OS control, while Cloud Run runs containers serverlessly.

▎ Choose Compute Engine if you need full VM control, custom OS agents, GPUs, sole tenancy, or long-running servers / Choose Cloud Run if you need stateless containerized services with scale-to-zero and no server management.

### Security & FAQ Insights
- Spot VMs are cheaper but can be preempted.
- OS Login centralizes SSH access through IAM.
- Shielded VM and Confidential VM improve boot integrity and memory protection.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Managed Instance Groups (MIGs)

### Definition & Purpose
MIGs manage groups of identical VM instances from templates with autoscaling, autohealing, rolling updates, and multi-zone deployment. They integrate with Compute Engine, health checks, Cloud Load Balancing, autoscaling metrics, and instance templates; deployment model is managed VM fleet orchestration.

### Exam Triggers
- "autoscale VM group"
- "autohealing"
- "rolling update canary"
- "regional MIG"
- "instance template"

### Not to be confused with (MANDATORY)
Google Kubernetes Engine - MIGs manage VM replicas, while GKE manages container workloads and Kubernetes orchestration.

▎ Choose Managed Instance Groups (MIGs) if you need autoscaled and autohealed VM fleets rather than containers / Choose Google Kubernetes Engine if you need Kubernetes container scheduling and orchestration.

### Security & FAQ Insights
- Regional MIGs distribute instances across zones for HA.
- Health checks drive autohealing and load balancer membership.
- Use rolling/canary updates for controlled VM template changes.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Google Kubernetes Engine (GKE)

### Definition & Purpose
GKE is managed Kubernetes for running containerized workloads with Standard and Autopilot operating modes. It integrates with IAM/Workload Identity, VPC-native networking, Cloud Load Balancing, Artifact Registry, Cloud Operations, Config Sync, and Anthos/GKE Enterprise; deployment model is managed Kubernetes with customer-managed or Autopilot-managed nodes.

### Exam Triggers
- "managed Kubernetes"
- "GKE Autopilot vs Standard"
- "node pools"
- "Workload Identity"
- "regional private cluster"

### Not to be confused with (MANDATORY)
Cloud Run - GKE is Kubernetes orchestration for complex container platforms, while Cloud Run is serverless container execution for request/event services.

▎ Choose Google Kubernetes Engine (GKE) if you need Kubernetes APIs, multi-container orchestration, custom scheduling, or platform control / Choose Cloud Run if you need simple serverless containers without cluster operations.

### Security & FAQ Insights
- Autopilot manages nodes and charges closer to pod resources; Standard gives node-pool control.
- VPC-native alias IP clusters are preferred.
- Private clusters and authorized networks are common security exam traps.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Cloud Run

### Definition & Purpose
Cloud Run runs stateless containers serverlessly with automatic scaling, scale-to-zero, HTTP/event triggers, and revisions. It integrates with Artifact Registry, Cloud Build, Eventarc, Pub/Sub, Serverless VPC Access/direct VPC egress, IAM, and Cloud Load Balancing; deployment model is fully managed serverless containers.

### Exam Triggers
- "serverless containers"
- "scale to zero"
- "stateless HTTP service"
- "container image without Kubernetes"
- "Cloud Run revisions"

### Not to be confused with (MANDATORY)
GKE - Cloud Run abstracts infrastructure for stateless containers, while GKE provides full Kubernetes control.

▎ Choose Cloud Run if you need to deploy stateless containers quickly with no cluster/node management / Choose GKE if you need Kubernetes-native control, stateful workloads, custom scheduling, or complex networking.

### Security & FAQ Insights
- Concurrency, min instances, and CPU allocation affect latency and cost.
- Use VPC egress/connectors for private resources.
- IAM controls whether a service is public or authenticated.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## App Engine

### Definition & Purpose
App Engine is a fully managed application platform with Standard and Flexible environments for deploying web apps without managing infrastructure. It integrates with Cloud Build, Cloud Tasks, Cloud Scheduler, Firestore/Datastore, Cloud Logging, and IAM; deployment model is managed PaaS.

### Exam Triggers
- "PaaS web application"
- "Standard vs Flexible"
- "legacy app platform"
- "deploy app without managing servers"

### Not to be confused with (MANDATORY)
Cloud Run - App Engine is opinionated PaaS with runtimes/environments, while Cloud Run is container-first serverless.

▎ Choose App Engine if the application fits App Engine runtimes or existing App Engine architecture / Choose Cloud Run if you want portable containers and more explicit container/runtime control.

### Security & FAQ Insights
- Standard can scale to zero quickly; Flexible runs on managed VM infrastructure and is slower to scale.
- Good for legacy GCP app-platform scenarios.
- Do not pick App Engine when the exam emphasizes arbitrary containers.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Functions

### Definition & Purpose
Cloud Functions runs event-driven functions triggered by HTTP, Pub/Sub, Cloud Storage, Firestore, Eventarc, and other events. It integrates with Cloud Run infrastructure in 2nd gen, IAM, Cloud Logging, Secret Manager, and VPC access; deployment model is serverless functions.

### Exam Triggers
- "event-driven function"
- "Cloud Storage trigger"
- "Pub/Sub trigger"
- "2nd gen runs on Cloud Run"
- "small function without container management"

### Not to be confused with (MANDATORY)
Cloud Run - Cloud Functions is function-centric event code, while Cloud Run is container-centric service deployment.

▎ Choose Cloud Functions if you need simple event-driven function execution with minimal packaging / Choose Cloud Run if you need full container image control or a long-running HTTP container service.

### Security & FAQ Insights
- 2nd gen Cloud Functions uses Cloud Run/Eventarc foundations.
- Set retries carefully to avoid duplicate side effects.
- Use least-privilege service accounts and Secret Manager for secrets.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## GKE Workload Identity

### Definition & Purpose
GKE Workload Identity lets Kubernetes service accounts access Google Cloud APIs through mapped IAM service accounts without static keys. It integrates with GKE, IAM, service accounts, and metadata server; deployment model is managed workload federation inside GKE.

### Exam Triggers
- "Pods access GCP APIs without JSON keys"
- "map Kubernetes service account to IAM service account"
- "least privilege for Pods"
- "GKE identity"

### Not to be confused with (MANDATORY)
Service Account keys - Workload Identity avoids static keys, while JSON keys are long-lived credentials that must be managed and rotated.

▎ Choose GKE Workload Identity if GKE pods need Google Cloud API access securely without key files / Choose Service Account keys if a legacy external workload cannot use federation or attached identity and requires a managed key temporarily.

### Security & FAQ Insights
- Preferred over mounting service account JSON keys in pods.
- Requires IAM binding between Kubernetes and IAM service accounts.
- Exam trap: node service account permissions should not be shared by all pods.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

# Part 4 - Storage, Databases, Analytics & AI

## Cloud Storage

### Definition & Purpose
Cloud Storage is globally accessible object storage for data lakes, backups, static content, logs, and archival tiers. It integrates with IAM, KMS/CMEK, lifecycle management, Autoclass, Cloud CDN, BigQuery, Dataflow, Pub/Sub notifications, and Storage Transfer Service; deployment model is fully managed serverless object storage.

### Exam Triggers
- "object storage"
- "bucket location region dual-region multi-region"
- "Autoclass"
- "retention policy Bucket Lock"
- "signed URLs"

### Not to be confused with (MANDATORY)
Filestore - Cloud Storage stores objects through APIs, while Filestore provides NFS file shares.

▎ Choose Cloud Storage if you need durable object storage, data lake storage, lifecycle, signed URLs, or WORM retention / Choose Filestore if applications require POSIX/NFS shared file semantics.

### Security & FAQ Insights
- Bucket names are globally unique.
- Retention policies with Bucket Lock enforce WORM-style compliance.
- Dual-region/multi-region choices affect availability, latency, cost, and residency.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Persistent Disk and Hyperdisk

### Definition & Purpose
Persistent Disk and Hyperdisk provide block storage for Compute Engine and GKE nodes with zonal/regional availability and configurable performance. They integrate with Compute Engine, snapshots, images, regional replication, KMS/CMEK, and GKE; deployment model is managed block storage attached to VMs.

### Exam Triggers
- "block storage for VM"
- "regional Persistent Disk"
- "Hyperdisk configurable IOPS throughput"
- "boot disk data disk"
- "snapshots"

### Not to be confused with (MANDATORY)
Cloud Storage - Persistent Disk/Hyperdisk are block devices for VMs, while Cloud Storage is object storage accessed through APIs.

▎ Choose Persistent Disk and Hyperdisk if a VM or node needs boot/data block storage with disk semantics / Choose Cloud Storage if you need object storage, data lake, static content, or archival storage.

### Security & FAQ Insights
- Regional Persistent Disk replicates synchronously across two zones.
- Hyperdisk separates performance provisioning from capacity more flexibly.
- Snapshots support backup and migration patterns.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Filestore

### Definition & Purpose
Filestore provides managed NFS file shares for applications that need POSIX-style shared file access. It integrates with Compute Engine, GKE, VPC, backups, KMS/CMEK in supported tiers, and enterprise file workloads; deployment model is fully managed file storage.

### Exam Triggers
- "managed NFS file share"
- "shared files for GKE or VMs"
- "POSIX semantics"
- "lift-and-shift file workload"

### Not to be confused with (MANDATORY)
Cloud Storage - Filestore is mounted file storage, while Cloud Storage is object storage.

▎ Choose Filestore if applications need shared NFS/POSIX file access / Choose Cloud Storage if applications can use object APIs and do not need file-system semantics.

### Security & FAQ Insights
- Choose tier based on throughput, capacity, and availability needs.
- Not a general replacement for object storage data lakes.
- Network placement and mount permissions matter for access control.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Cloud SQL

### Definition & Purpose
Cloud SQL provides managed MySQL, PostgreSQL, and SQL Server with backups, HA, read replicas, private IP, IAM database auth options, and maintenance. It integrates with Private Services Access, Cloud SQL Auth Proxy/connectors, KMS, DMS, Cloud Run, GKE, and Monitoring; deployment model is fully managed relational database instances.

### Exam Triggers
- "managed MySQL PostgreSQL SQL Server"
- "regional HA standby"
- "read replicas"
- "Cloud SQL Auth Proxy"
- "private IP via PSA"

### Not to be confused with (MANDATORY)
Cloud Spanner - Cloud SQL is traditional relational managed database, while Spanner is globally distributed horizontally scalable relational database with strong consistency.

▎ Choose Cloud SQL if you need managed standard relational engines with moderate scaling and familiar SQL administration / Choose Cloud Spanner if you need global strong consistency and horizontal relational scale.

### Security & FAQ Insights
- HA standby is not used for reads.
- Read replicas are asynchronous and can be cross-region.
- Private IP uses Private Services Access and inherits peering design considerations.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## AlloyDB

### Definition & Purpose
AlloyDB is a high-performance PostgreSQL-compatible managed database optimized for demanding transactional and analytical workloads. It integrates with Private Services Access, Database Migration Service, IAM, KMS, backups, and Cloud Monitoring; deployment model is fully managed PostgreSQL-compatible database clusters.

### Exam Triggers
- "high-performance PostgreSQL compatible"
- "HTAP workload"
- "migrate PostgreSQL to managed service"
- "AlloyDB read pools"

### Not to be confused with (MANDATORY)
Cloud SQL for PostgreSQL - AlloyDB targets higher performance and advanced PostgreSQL-compatible workloads, while Cloud SQL is the general managed relational service.

▎ Choose AlloyDB if PostgreSQL compatibility is required with higher performance, read pools, or HTAP-style workloads / Choose Cloud SQL for PostgreSQL if you need a simpler managed PostgreSQL service or SQL Server/MySQL support.

### Security & FAQ Insights
- Private connectivity typically uses PSA.
- Read pools scale read traffic separately.
- Good modernization target for PostgreSQL-heavy workloads.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Spanner

### Definition & Purpose
Cloud Spanner is a globally distributed relational database with SQL, horizontal scaling, high availability, and external strong consistency. It integrates with IAM, KMS, backups, change streams, Dataflow, BigQuery federation, and multi-region configurations; deployment model is fully managed distributed relational database.

### Exam Triggers
- "global relational database"
- "strong consistency"
- "horizontal SQL scale"
- "multi-region OLTP"
- "Spanner nodes processing units"

### Not to be confused with (MANDATORY)
Cloud SQL - Spanner is globally distributed and horizontally scalable, while Cloud SQL is a traditional managed relational database.

▎ Choose Cloud Spanner if you need global OLTP, strong consistency, SQL, and horizontal scale together / Choose Cloud SQL if you need a simpler regional relational database with standard engines.

### Security & FAQ Insights
- Regional and multi-region configurations trade cost, latency, and availability.
- Scaling uses nodes or processing units.
- Common trap: Spanner is not the default answer for small relational workloads due to cost/complexity.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Firestore

### Definition & Purpose
Firestore is a serverless NoSQL document database for web/mobile apps, real-time sync, offline support, and flexible document queries. It integrates with Firebase, Cloud Functions, App Engine, IAM/security rules, backups, and event triggers; deployment model is fully managed serverless document database.

### Exam Triggers
- "mobile web app realtime sync"
- "NoSQL document database"
- "offline support"
- "Firestore Native mode"
- "Firebase app backend"

### Not to be confused with (MANDATORY)
Cloud Bigtable - Firestore is document/application-oriented with real-time sync, while Bigtable is wide-column high-throughput storage for massive analytical/IoT workloads.

▎ Choose Firestore if you need serverless document storage for app/mobile/web use with real-time/offline features / Choose Cloud Bigtable if you need petabyte-scale low-latency wide-column workloads such as IoT/time-series.

### Security & FAQ Insights
- Design document paths and indexes for query patterns.
- Security rules are common in Firebase-style apps.
- Not for relational joins or analytical warehouse queries.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud Bigtable

### Definition & Purpose
Bigtable is a managed wide-column NoSQL database for petabyte-scale low-latency workloads such as IoT, time series, ad tech, and analytics ingestion. It integrates with Dataflow, Dataproc/HBase API, BigQuery, Cloud Monitoring, and backups/replication; deployment model is fully managed distributed NoSQL clusters.

### Exam Triggers
- "wide-column database"
- "IoT time-series"
- "HBase compatible"
- "petabyte scale low latency"
- "massive write throughput"

### Not to be confused with (MANDATORY)
Firestore - Bigtable is for massive wide-column workloads, while Firestore is document-oriented app database with realtime/mobile features.

▎ Choose Cloud Bigtable if you need very high throughput wide-column storage for time-series or analytical operational data / Choose Firestore if you need app-centric document storage with realtime sync and offline support.

### Security & FAQ Insights
- Schema design centers on row key distribution to avoid hotspots.
- Cluster/node sizing affects cost and performance.
- Not a relational database and not a serverless document database.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Memorystore

### Definition & Purpose
Memorystore provides managed Redis, Valkey, and Memcached-compatible in-memory caching for low-latency data access. It integrates with VPC, Private Service Access/Connect depending on product/mode, Cloud Run/GKE/Compute clients, and Monitoring; deployment model is fully managed in-memory cache.

### Exam Triggers
- "managed Redis cache"
- "session store"
- "reduce database read latency"
- "Memcached"
- "in-memory data store"

### Not to be confused with (MANDATORY)
Cloud Bigtable - Memorystore is an in-memory cache, while Bigtable is persistent wide-column storage.

▎ Choose Memorystore if you need low-latency caching, session storage, or Redis/Memcached semantics / Choose Cloud Bigtable if you need persistent large-scale NoSQL storage.

### Security & FAQ Insights
- Cache data is not a substitute for durable source-of-truth storage.
- Network/private connectivity design matters for serverless access.
- Sizing and high-availability tier affect cost and resilience.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## BigQuery

### Definition & Purpose
BigQuery is a serverless data warehouse for analytical SQL over large datasets with separated storage/compute and integrated ML/geospatial features. It integrates with Cloud Storage, Dataflow, Dataproc, Pub/Sub, Looker, BI Engine, Data Transfer Service, IAM, KMS, and authorized views; deployment model is fully managed serverless analytics.

### Exam Triggers
- "serverless data warehouse"
- "partitioning clustering"
- "on-demand vs slots"
- "authorized views"
- "BigQuery Omni"

### Not to be confused with (MANDATORY)
Cloud SQL - BigQuery is OLAP analytics, while Cloud SQL is OLTP application database.

▎ Choose BigQuery if you need large-scale analytical SQL, BI, data warehouse, or query data in Cloud Storage / Choose Cloud SQL if you need transactional application reads/writes.

### Security & FAQ Insights
- Partitioning and clustering reduce scanned data, cost, and latency.
- Capacity editions/slots fit predictable high-volume workloads; on-demand fits variable workloads.
- Authorized views share derived results without exposing base tables.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core exam topic; frequently tested through architecture case studies and service-selection tradeoffs.

---

## Looker

### Definition & Purpose
Looker provides governed business intelligence, semantic modeling, dashboards, and embedded analytics. It integrates with BigQuery and other databases, LookML models, IAM/identity providers, and data governance workflows; deployment model is managed BI/semantic analytics platform.

### Exam Triggers
- "business intelligence dashboards"
- "semantic model"
- "LookML"
- "governed metrics"
- "embedded analytics"

### Not to be confused with (MANDATORY)
BigQuery - Looker visualizes and governs analytics consumption, while BigQuery stores and queries warehouse data.

▎ Choose Looker if users need governed dashboards, semantic metrics, and BI consumption / Choose BigQuery if engineers need the underlying serverless data warehouse and SQL execution engine.

### Security & FAQ Insights
- Looker is often paired with BigQuery for enterprise analytics.
- Governed semantic models reduce inconsistent metric definitions.
- Not the service that performs large-scale storage/compute itself.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Vertex AI

### Definition & Purpose
Vertex AI is Google Cloud’s unified ML platform for building, training, deploying, evaluating, and using ML and generative AI models. It integrates with BigQuery, Cloud Storage, Dataflow, GPUs/TPUs, Model Registry, Feature Store, Pipelines, Matching Engine/Vector Search, and Gemini APIs; deployment model is fully managed ML/AI platform with managed training and endpoints.

### Exam Triggers
- "managed ML platform"
- "train deploy custom model"
- "Gemini generative AI"
- "feature store pipelines"
- "vector search"

### Not to be confused with (MANDATORY)
BigQuery ML - Vertex AI is the broader ML platform for custom and generative AI workflows, while BigQuery ML trains models directly in SQL inside BigQuery.

▎ Choose Vertex AI if you need end-to-end ML/AI lifecycle, managed model training/deployment, Gemini, or feature pipelines / Choose BigQuery ML if analysts need to create models directly with SQL inside BigQuery.

### Security & FAQ Insights
- Use Vertex AI endpoints for deployed model serving.
- CMEK, VPC-SC, private endpoints, and IAM matter for enterprise ML security.
- PCA may test service selection for AI-assisted architecture even if the exam is not ML-specialist.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

# Part 5 - Data Processing, Messaging & Migration

## Pub/Sub

### Definition & Purpose
Pub/Sub is a global messaging service for asynchronous event ingestion, fanout, and decoupling with push or pull subscriptions. It integrates with Dataflow, Cloud Functions, Cloud Run, BigQuery subscriptions, Eventarc, and dead-letter topics; deployment model is fully managed serverless messaging.

### Exam Triggers
- "global pub/sub messaging"
- "decouple producers consumers"
- "at-least-once delivery"
- "push pull subscription"
- "stream ingestion"

### Not to be confused with (MANDATORY)
Cloud Tasks - Pub/Sub is event fanout and streaming messaging, while Cloud Tasks queues rate-controlled HTTP tasks for execution.

▎ Choose Pub/Sub if you need durable event ingestion, fanout, or streaming pipeline decoupling / Choose Cloud Tasks if you need per-task HTTP execution with rate limits, retries, and scheduling controls.

### Security & FAQ Insights
- Delivery is at least once, so consumers must be idempotent.
- Dead-letter topics handle poison messages.
- Ordering keys support ordered delivery where needed.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Dataflow

### Definition & Purpose
Dataflow is a serverless Apache Beam service for unified batch and streaming data processing. It integrates with Pub/Sub, BigQuery, Cloud Storage, Bigtable, Data Catalog, and templates; deployment model is fully managed autoscaling data processing.

### Exam Triggers
- "Apache Beam"
- "stream and batch processing"
- "ETL pipeline"
- "Pub/Sub to BigQuery"
- "autoscaling data processing"

### Not to be confused with (MANDATORY)
Dataproc - Dataflow is serverless Beam pipelines, while Dataproc runs managed Spark/Hadoop clusters.

▎ Choose Dataflow if you need serverless batch/stream processing with Apache Beam and autoscaling / Choose Dataproc if you need to migrate existing Spark/Hadoop jobs or manage cluster-level frameworks.

### Security & FAQ Insights
- Streaming pipelines require windowing, watermark, and late-data design.
- Templates support repeatable deployment.
- Use Dataflow when the exam emphasizes unified batch and stream ETL without cluster management.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Dataproc

### Definition & Purpose
Dataproc provides managed Hadoop, Spark, Hive, and related open-source clusters for big data processing. It integrates with Cloud Storage, BigQuery connector, Metastore, IAM, autoscaling, and initialization actions; deployment model is managed ephemeral or long-running clusters.

### Exam Triggers
- "managed Spark Hadoop"
- "migrate existing Hadoop jobs"
- "ephemeral cluster"
- "Dataproc Metastore"
- "open-source big data tools"

### Not to be confused with (MANDATORY)
Dataflow - Dataproc is cluster-based Spark/Hadoop, while Dataflow is serverless Beam.

▎ Choose Dataproc if existing Spark/Hadoop workloads or ecosystem tools must run with minimal rewrite / Choose Dataflow if you want serverless unified streaming/batch pipelines without cluster management.

### Security & FAQ Insights
- Use Cloud Storage as a durable data lake instead of HDFS when possible.
- Ephemeral clusters reduce cost for scheduled jobs.
- Cluster sizing and autoscaling remain architectural responsibilities.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Composer

### Definition & Purpose
Cloud Composer is managed Apache Airflow for orchestrating workflows with dependencies, schedules, sensors, and multi-service DAGs. It integrates with Dataflow, Dataproc, BigQuery, Cloud Storage, Cloud Functions, GKE, and IAM; deployment model is managed Airflow environments.

### Exam Triggers
- "managed Airflow"
- "orchestrate ETL workflow"
- "DAG dependencies"
- "multi-step data pipeline"

### Not to be confused with (MANDATORY)
Cloud Scheduler - Composer orchestrates multi-step workflows, while Scheduler triggers simple cron-style jobs.

▎ Choose Cloud Composer if you need complex workflow orchestration with dependencies and retries / Choose Cloud Scheduler if you only need to trigger a simple job on a schedule.

### Security & FAQ Insights
- Composer environments have underlying GKE/resources that affect cost.
- Use service accounts and least privilege for DAG execution.
- Not a data processing engine itself; it orchestrates other services.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Tasks

### Definition & Purpose
Cloud Tasks manages asynchronous task queues with rate control, retries, scheduling, and HTTP target dispatch. It integrates with Cloud Run, Cloud Functions, App Engine, IAM/OIDC tokens, and queue-level throttling; deployment model is fully managed task queue.

### Exam Triggers
- "rate-limited HTTP tasks"
- "deferred execution"
- "retry task endpoint"
- "queue background work"
- "throttle downstream service"

### Not to be confused with (MANDATORY)
Pub/Sub - Tasks controls execution of individual tasks, while Pub/Sub distributes events to subscribers.

▎ Choose Cloud Tasks if you need controlled task execution with retries, rate limits, and HTTP targets / Choose Pub/Sub if you need event fanout or streaming ingestion to multiple consumers.

### Security & FAQ Insights
- Supports scheduled tasks and retry policies.
- OIDC tokens can authenticate calls to Cloud Run/Functions.
- Use for backpressure when downstream services have strict rate limits.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Cloud Scheduler

### Definition & Purpose
Cloud Scheduler is a managed cron service that triggers HTTP endpoints, Pub/Sub topics, or App Engine jobs on schedules. It integrates with Cloud Run, Cloud Functions, Pub/Sub, IAM/OIDC, and App Engine; deployment model is fully managed scheduling.

### Exam Triggers
- "cron job"
- "trigger Cloud Run every hour"
- "scheduled Pub/Sub message"
- "managed scheduler"

### Not to be confused with (MANDATORY)
Cloud Composer - Scheduler triggers simple scheduled actions, while Composer orchestrates complex DAGs.

▎ Choose Cloud Scheduler if you only need a cron-like trigger for one endpoint or message / Choose Cloud Composer if you need multi-step dependency orchestration with Airflow DAGs.

### Security & FAQ Insights
- Use authenticated HTTP targets with OIDC where possible.
- Time zone and retry settings affect behavior.
- Not a workflow orchestration engine.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Storage Transfer Service

### Definition & Purpose
Storage Transfer Service moves and synchronizes data into Cloud Storage from other clouds, HTTP/S endpoints, or file systems. It integrates with Cloud Storage, AWS S3, Azure Blob, agents for POSIX file systems, scheduling, and IAM; deployment model is managed online transfer service.

### Exam Triggers
- "transfer S3 to Cloud Storage"
- "scheduled bucket sync"
- "online data transfer"
- "HTTP to GCS"
- "file system transfer agent"

### Not to be confused with (MANDATORY)
Transfer Appliance - Storage Transfer Service moves data online over networks, while Transfer Appliance moves data physically offline.

▎ Choose Storage Transfer Service if network-based transfer or recurring sync to Cloud Storage is feasible / Choose Transfer Appliance if network bandwidth is insufficient for the data volume or timeline.

### Security & FAQ Insights
- Supports scheduled recurring transfers.
- Agents support file system transfers.
- Preserve/metadata behavior depends on source and destination capabilities.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Transfer Appliance

### Definition & Purpose
Transfer Appliance is a physical device for offline bulk data transfer into Google Cloud when network transfer is impractical. It integrates with Cloud Storage import workflows, encryption, and logistics processes; deployment model is Google-shipped physical hardware.

### Exam Triggers
- "offline data transfer"
- "network too slow"
- "large dataset to Cloud Storage"
- "physical appliance"

### Not to be confused with (MANDATORY)
Storage Transfer Service - Transfer Appliance is offline physical transfer, while Storage Transfer Service is online network transfer.

▎ Choose Transfer Appliance if offline transfer is faster or required due to bandwidth constraints / Choose Storage Transfer Service if network transfer is feasible and recurring synchronization is needed.

### Security & FAQ Insights
- Plan encryption, shipping, and chain-of-custody.
- Best for very large one-time migrations.
- Not for continuous synchronization.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Migrate to Virtual Machines

### Definition & Purpose
Migrate to Virtual Machines migrates VM workloads from VMware, AWS, Azure, or other environments to Compute Engine with replication and cutover workflows. It integrates with Compute Engine, VPC, IAM, Cloud Logging, and migration waves; deployment model is managed VM migration service.

### Exam Triggers
- "lift and shift VMs"
- "migrate VMware to Compute Engine"
- "continuous replication"
- "cutover VM migration"

### Not to be confused with (MANDATORY)
Database Migration Service - Migrate to VMs moves servers, while DMS migrates databases.

▎ Choose Migrate to Virtual Machines if you need to rehost entire VMs to Compute Engine / Choose Database Migration Service if you need database migration or CDC to Cloud SQL/AlloyDB.

### Security & FAQ Insights
- Maps to lift-and-shift migration strategies.
- Test clones and cutover windows reduce risk.
- Post-migration modernization may use managed services or containers.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Database Migration Service

### Definition & Purpose
Database Migration Service migrates databases to Cloud SQL and AlloyDB with continuous replication for supported engines. It integrates with Cloud SQL, AlloyDB, source databases, connectivity options, and IAM; deployment model is managed database migration.

### Exam Triggers
- "migrate MySQL PostgreSQL SQL Server"
- "CDC to Cloud SQL"
- "database migration to AlloyDB"
- "minimal downtime database cutover"

### Not to be confused with (MANDATORY)
Migrate to Virtual Machines - DMS migrates databases, while Migrate to VMs migrates whole servers.

▎ Choose Database Migration Service if you need managed database migration with replication/CDC / Choose Migrate to Virtual Machines if you need to rehost full VM workloads.

### Security & FAQ Insights
- Network connectivity and firewall rules are common migration blockers.
- Schema compatibility must be assessed for heterogeneous migrations.
- Use private connectivity for secure migration paths where possible.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

# Part 6 - Security, Compliance & Operations

## Cloud KMS

### Definition & Purpose
Cloud KMS manages cryptographic keys for CMEK, asymmetric/symmetric encryption, rotation, and audit across Google Cloud services. It integrates with Cloud Storage, BigQuery, Persistent Disk, Cloud SQL, Secret Manager, HSM/EKM options, IAM, and Cloud Audit Logs; deployment model is fully managed key management with optional HSM/EKM tiers.

### Exam Triggers
- "CMEK"
- "customer-managed encryption key"
- "key rotation"
- "Cloud HSM"
- "External Key Manager"

### Not to be confused with (MANDATORY)
Secret Manager - KMS manages encryption keys, while Secret Manager stores and versions secret values.

▎ Choose Cloud KMS if you need to control encryption keys or CMEK for services / Choose Secret Manager if you need to store API keys, passwords, or credentials as secrets.

### Security & FAQ Insights
- IAM on keys controls cryptographic operations.
- CMEK can affect service availability if keys are disabled or unavailable.
- Cloud HSM and EKM address stricter key custody requirements.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Secret Manager

### Definition & Purpose
Secret Manager stores, versions, and controls access to secrets such as API keys, passwords, and certificates. It integrates with IAM, Cloud KMS, Cloud Run, Cloud Functions, GKE, Cloud Build, and audit logs; deployment model is fully managed secrets storage.

### Exam Triggers
- "store API keys"
- "secret versions"
- "access secret from Cloud Run"
- "avoid env plaintext secrets"
- "secret rotation"

### Not to be confused with (MANDATORY)
Cloud KMS - Secret Manager stores secrets, while KMS manages encryption keys.

▎ Choose Secret Manager if applications need secure secret storage, versioning, and runtime retrieval / Choose Cloud KMS if you need customer-managed encryption keys or cryptographic operations.

### Security & FAQ Insights
- Grant secret accessor narrowly to runtime service accounts.
- Use versions for rotation and rollback.
- Audit secret access with Cloud Audit Logs.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Identity-Aware Proxy (IAP)

### Definition & Purpose
IAP provides identity-aware access to web apps and TCP forwarding to VMs without a traditional VPN or bastion. It integrates with IAM, Cloud Load Balancing, OAuth, Chrome Enterprise/BeyondCorp patterns, and SSH/RDP TCP forwarding; deployment model is fully managed zero-trust access proxy.

### Exam Triggers
- "access app without VPN"
- "SSH to VM without public IP"
- "BeyondCorp"
- "IAP TCP forwarding"
- "identity-aware access"

### Not to be confused with (MANDATORY)
Cloud VPN - IAP grants identity-aware app/VM access, while VPN connects networks.

▎ Choose Identity-Aware Proxy (IAP) if users need authenticated access to apps or VMs without broad network connectivity / Choose Cloud VPN if networks need private IP routing between environments.

### Security & FAQ Insights
- IAP relies on IAM roles for access.
- Useful for removing public SSH/RDP exposure.
- For web apps, IAP usually sits behind supported load balancers.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Security Command Center

### Definition & Purpose
Security Command Center centralizes security posture, threat findings, vulnerability information, compliance insights, and risk management. It integrates with IAM, Cloud Asset Inventory, Event Threat Detection, Security Health Analytics, Sensitive Data Protection, and SIEM exports; deployment model is managed security posture platform.

### Exam Triggers
- "central security dashboard"
- "misconfiguration findings"
- "threat detection"
- "Security Health Analytics"
- "organization-wide security posture"

### Not to be confused with (MANDATORY)
Cloud Monitoring - SCC focuses on security posture/threats, while Monitoring focuses on operational metrics and alerts.

▎ Choose Security Command Center if you need centralized security findings, posture management, or threat visibility / Choose Cloud Monitoring if you need performance metrics, uptime checks, or operational alerting.

### Security & FAQ Insights
- Premium/Enterprise tiers add advanced detection and risk capabilities.
- Use organization-level activation for broad coverage.
- Findings can feed SIEM/SOAR workflows.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Sensitive Data Protection

### Definition & Purpose
Sensitive Data Protection discovers, classifies, masks, tokenizes, and de-identifies sensitive data such as PII across datasets and streams. It integrates with Cloud Storage, BigQuery, Pub/Sub, Dataflow, Cloud Functions, and Security Command Center; deployment model is fully managed data inspection and de-identification.

### Exam Triggers
- "detect PII"
- "de-identify data"
- "tokenize sensitive data"
- "DLP scan BigQuery or Cloud Storage"
- "mask data before sharing"

### Not to be confused with (MANDATORY)
VPC Service Controls - Sensitive Data Protection identifies/protects sensitive content, while VPC Service Controls creates service perimeters to reduce exfiltration.

▎ Choose Sensitive Data Protection if you need to discover, classify, mask, tokenize, or de-identify sensitive data / Choose VPC Service Controls if you need perimeter controls around supported services/projects.

### Security & FAQ Insights
- Templates standardize inspection and de-identification rules.
- Can be used in streaming pipelines through Dataflow/Pub/Sub.
- Does not replace IAM or perimeter controls.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Assured Workloads

### Definition & Purpose
Assured Workloads creates governed environments with compliance controls such as data residency, personnel access restrictions, and encryption settings for regulated workloads. It integrates with folders, IAM, Organization Policy, Cloud KMS, Access Transparency, and compliance regimes; deployment model is managed compliance environment orchestration.

### Exam Triggers
- "regulated workload"
- "data residency"
- "FedRAMP IL4 compliance controls"
- "support personnel restrictions"
- "compliance folder"

### Not to be confused with (MANDATORY)
Organization Policy - Assured Workloads packages compliance controls into governed environments, while Organization Policy is the individual constraint mechanism.

▎ Choose Assured Workloads if you need a managed compliance environment for regulated workloads / Choose Organization Policy if you need to configure individual resource constraints yourself.

### Security & FAQ Insights
- Controls are applied at folder/workload environment level.
- Available regimes and regions vary.
- Often paired with Access Transparency and Access Approval.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Access Transparency

### Definition & Purpose
Access Transparency logs actions taken by Google personnel when they access customer resources for support or operations. It integrates with Cloud Logging, Assured Workloads, IAM, and compliance workflows; deployment model is managed audit visibility.

### Exam Triggers
- "logs of Google staff access"
- "support personnel access audit"
- "transparency logs"
- "compliance evidence"

### Not to be confused with (MANDATORY)
Access Approval - Access Transparency records Google access, while Access Approval requires customer approval before certain access occurs.

▎ Choose Access Transparency if you need audit logs showing when Google personnel accessed data/resources / Choose Access Approval if you need to approve or reject Google personnel access before it occurs.

### Security & FAQ Insights
- Useful for compliance and regulator evidence.
- Availability depends on edition/service support.
- It does not itself block access; it records it.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Access Approval

### Definition & Purpose
Access Approval lets customers explicitly approve or reject Google personnel access requests before access to supported resources. It integrates with IAM, notifications, Cloud KMS signing keys, Assured Workloads, and Access Transparency; deployment model is managed access governance.

### Exam Triggers
- "approve Google support access"
- "customer approval before access"
- "regulated support workflow"
- "Access Approval request"

### Not to be confused with (MANDATORY)
Access Transparency - Access Approval gates access before it happens, while Access Transparency logs access after it happens.

▎ Choose Access Approval if you need explicit customer approval before Google personnel can access supported resources / Choose Access Transparency if you only need audit visibility into Google personnel access.

### Security & FAQ Insights
- Often used with Assured Workloads for regulated environments.
- Approval workflows must be operationally staffed to avoid support delays.
- Does not replace IAM for customer principals.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Cloud Monitoring

### Definition & Purpose
Cloud Monitoring collects metrics, dashboards, uptime checks, alerting policies, and SLO monitoring for Google Cloud and hybrid systems. It integrates with Cloud Logging, Managed Service for Prometheus, Cloud Trace, Error Reporting, Pub/Sub notifications, and Ops Agent; deployment model is fully managed observability.

### Exam Triggers
- "metrics dashboards alerts"
- "uptime checks"
- "SLO monitoring"
- "Managed Service for Prometheus"
- "Ops Agent"

### Not to be confused with (MANDATORY)
Cloud Logging - Monitoring focuses on metrics and alerts, while Logging stores and routes logs.

▎ Choose Cloud Monitoring if you need metrics, dashboards, uptime checks, alerting, or SLOs / Choose Cloud Logging if you need log collection, search, sinks, or audit logs.

### Security & FAQ Insights
- Alert policies can notify channels or trigger incident workflows.
- Use SLO/error budget concepts for reliability scenarios.
- Ops Agent collects VM system/application telemetry.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud Logging

### Definition & Purpose
Cloud Logging stores, searches, routes, and exports platform, audit, network, and application logs. It integrates with Cloud Monitoring, Log Router sinks, BigQuery, Cloud Storage, Pub/Sub, Cloud Audit Logs, and Error Reporting; deployment model is fully managed logging.

### Exam Triggers
- "central logs"
- "log sinks"
- "export logs to BigQuery"
- "Cloud Audit Logs"
- "Log Router"

### Not to be confused with (MANDATORY)
Cloud Monitoring - Logging stores events/log entries, while Monitoring turns metrics into dashboards and alerts.

▎ Choose Cloud Logging if you need log collection, audit log analysis, retention, routing, or exports / Choose Cloud Monitoring if you need metrics dashboards and alert policies.

### Security & FAQ Insights
- Admin Activity audit logs are generally always on; Data Access logs often need explicit enablement.
- Aggregated sinks route logs at folder/org level.
- Log volume and retention affect cost.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and migration/security traps.

---

## Cloud Trace and Error Reporting

### Definition & Purpose
Cloud Trace collects distributed request traces and latency data, while Error Reporting aggregates application exceptions. They integrate with Cloud Run, App Engine, GKE, Compute Engine, Cloud Logging, and OpenTelemetry; deployment model is managed application diagnostics.

### Exam Triggers
- "distributed tracing"
- "latency breakdown"
- "application exceptions"
- "Error Reporting groups stack traces"
- "OpenTelemetry"

### Not to be confused with (MANDATORY)
Cloud Monitoring - Trace/Error Reporting diagnose request paths and exceptions, while Monitoring provides broad metrics/alerts.

▎ Choose Cloud Trace and Error Reporting if you need request-level tracing or grouped exception diagnostics / Choose Cloud Monitoring if you need general metrics, uptime checks, dashboards, or SLO alerts.

### Security & FAQ Insights
- Instrumentation may be required depending on runtime.
- Trace sampling controls volume.
- Useful for root cause analysis and performance optimization.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

# Part 7 - DevOps, API Management, Cost & Governance

## Cloud Build

### Definition & Purpose
Cloud Build is a serverless CI/CD service that runs build steps from source triggers or build configs. It integrates with Artifact Registry, Cloud Deploy, GitHub/Cloud Source Repositories, Secret Manager, Cloud Run, GKE, and IAM; deployment model is fully managed build execution.

### Exam Triggers
- "cloudbuild.yaml"
- "serverless CI"
- "build container image"
- "Git trigger"
- "deploy from pipeline"

### Not to be confused with (MANDATORY)
Cloud Deploy - Cloud Build builds/tests artifacts, while Cloud Deploy manages progressive delivery to runtimes.

▎ Choose Cloud Build if you need CI builds, tests, image creation, or automation steps / Choose Cloud Deploy if you need release promotion and deployment pipeline management.

### Security & FAQ Insights
- Use private pools for network isolation or custom worker environments.
- Service account permissions determine what builds can deploy.
- Store artifacts in Artifact Registry.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Artifact Registry

### Definition & Purpose
Artifact Registry stores container images and language packages such as Maven, npm, Python, and apt repositories. It integrates with Cloud Build, GKE, Cloud Run, Binary Authorization, IAM, vulnerability scanning, and regional repositories; deployment model is fully managed artifact repository.

### Exam Triggers
- "container image registry"
- "Maven npm Python packages"
- "replace Container Registry"
- "store build artifacts"
- "vulnerability scanning"

### Not to be confused with (MANDATORY)
Cloud Storage - Artifact Registry stores deployable software artifacts with package semantics, while Cloud Storage stores general objects.

▎ Choose Artifact Registry if you need a managed repository for container images or packages / Choose Cloud Storage if you need generic object storage or data lake files.

### Security & FAQ Insights
- Prefer Artifact Registry over legacy Container Registry.
- Repository location affects latency/residency.
- Use IAM and vulnerability scanning for supply-chain security.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Cloud Deploy

### Definition & Purpose
Cloud Deploy manages continuous delivery pipelines, promotion stages, approvals, and rollouts to GKE and Cloud Run. It integrates with Skaffold, Cloud Build, Artifact Registry, GKE, Cloud Run, IAM, and audit logs; deployment model is fully managed delivery orchestration.

### Exam Triggers
- "progressive delivery"
- "promote dev staging prod"
- "manual approval"
- "deploy to GKE or Cloud Run"
- "release pipeline"

### Not to be confused with (MANDATORY)
Cloud Build - Cloud Deploy releases artifacts through environments, while Cloud Build creates/tests artifacts.

▎ Choose Cloud Deploy if you need controlled delivery, promotions, approvals, and rollout management / Choose Cloud Build if you need CI/build/test execution rather than release promotion.

### Security & FAQ Insights
- Uses delivery pipelines and targets.
- Manual approvals support governance gates.
- Works naturally with Cloud Run and GKE deployment flows.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Infrastructure Manager

### Definition & Purpose
Infrastructure Manager automates Terraform-based infrastructure provisioning on Google Cloud. It integrates with Terraform configurations, Cloud Storage/state concepts, IAM, Cloud Build-style automation, and audit logging; deployment model is managed IaC deployment service.

### Exam Triggers
- "Terraform on Google Cloud"
- "managed infrastructure deployment"
- "versioned IaC"
- "provision repeatable environments"

### Not to be confused with (MANDATORY)
Config Connector - Infrastructure Manager runs Terraform deployments, while Config Connector manages GCP resources as Kubernetes custom resources.

▎ Choose Infrastructure Manager if teams want managed Terraform-based infrastructure provisioning / Choose Config Connector if teams want GitOps/Kubernetes-native resource management through CRDs.

### Security & FAQ Insights
- Terraform is the expected modern IaC answer more often than Deployment Manager.
- Least-privilege deployment service accounts matter.
- State and drift governance should be planned.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Config Connector

### Definition & Purpose
Config Connector lets teams manage Google Cloud resources as Kubernetes custom resources. It integrates with GKE, IAM service accounts, Config Sync/GitOps, and many Google Cloud APIs; deployment model is Kubernetes-native declarative resource management.

### Exam Triggers
- "manage GCP resources as Kubernetes CRDs"
- "GitOps infrastructure"
- "Config Connector"
- "Kubernetes-native IaC"

### Not to be confused with (MANDATORY)
Infrastructure Manager - Config Connector is Kubernetes/GitOps-native, while Infrastructure Manager is Terraform-based.

▎ Choose Config Connector if a Kubernetes platform team wants to manage cloud resources through CRDs and GitOps / Choose Infrastructure Manager if the organization standardizes on Terraform for IaC.

### Security & FAQ Insights
- Works well with Config Sync and Policy Controller patterns.
- Requires careful IAM for the controller service account.
- Not the default choice for non-Kubernetes teams.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Apigee

### Definition & Purpose
Apigee is a full lifecycle API management platform for exposing, securing, transforming, monetizing, and analyzing enterprise APIs. It integrates with IAM/identity providers, Cloud Load Balancing, Cloud Armor, analytics, developer portals, and hybrid/runtime options; deployment model is managed enterprise API management.

### Exam Triggers
- "enterprise API management"
- "developer portal"
- "monetize APIs"
- "API analytics quotas transformation"
- "large API program"

### Not to be confused with (MANDATORY)
API Gateway - Apigee is enterprise API management, while API Gateway is lightweight managed API fronting for serverless/backends.

▎ Choose Apigee if you need enterprise API governance, monetization, portals, analytics, or complex policies / Choose API Gateway if you need a simpler managed API endpoint for Cloud Run/Functions or basic auth/throttling.

### Security & FAQ Insights
- Apigee is broader and more complex than simple gateway use cases.
- Hybrid options support enterprise deployment constraints.
- Common trap: do not choose API Gateway for full API product monetization/governance.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## API Gateway

### Definition & Purpose
API Gateway provides lightweight managed API fronting for services such as Cloud Run, Cloud Functions, and App Engine. It integrates with OpenAPI specs, IAM, API keys/JWTs, Cloud Logging, and serverless backends; deployment model is fully managed API gateway.

### Exam Triggers
- "simple managed API"
- "OpenAPI config"
- "front Cloud Run or Cloud Functions"
- "API key JWT"
- "serverless API"

### Not to be confused with (MANDATORY)
Apigee - API Gateway is lightweight API fronting, while Apigee is enterprise API management with portals, analytics, and monetization.

▎ Choose API Gateway if you need a simple managed API endpoint for serverless/backend services / Choose Apigee if you need enterprise API lifecycle management, developer portals, or monetization.

### Security & FAQ Insights
- Good for simpler APIs and serverless exposure.
- Use Cloud Armor/load balancing where deeper edge protection is required.
- Not a replacement for full Apigee governance.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through governance, cost, or specialized architecture constraints.

---

## Cloud Billing and Budgets

### Definition & Purpose
Cloud Billing manages billing accounts, project billing links, budgets, alerts, exports, and cost visibility. It integrates with Resource Manager, BigQuery billing export, labels, budgets, Cloud Monitoring notifications, and Recommender; deployment model is fully managed billing control plane.

### Exam Triggers
- "billing account linked to projects"
- "budgets and alerts"
- "BigQuery billing export"
- "chargeback showback"
- "cost by labels"

### Not to be confused with (MANDATORY)
Recommender - Billing/Budgets reports and alerts on spend, while Recommender suggests concrete optimizations.

▎ Choose Cloud Billing and Budgets if you need spend visibility, billing exports, budgets, or cost allocation / Choose Recommender if you need automated rightsizing or best-practice recommendations.

### Security & FAQ Insights
- Billing accounts can pay for multiple projects.
- Labels support cost allocation but must be applied consistently.
- Budgets alert but do not automatically redesign architecture.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

## Recommender

### Definition & Purpose
Recommender provides machine-generated recommendations for cost, performance, reliability, security, and IAM least privilege. It integrates with Compute Engine rightsizing, IAM recommendations, idle resource detection, Security Command Center, and console/API workflows; deployment model is fully managed recommendation engine.

### Exam Triggers
- "rightsizing recommendations"
- "idle VM disks"
- "IAM recommender"
- "optimize cost performance security"
- "automated recommendations"

### Not to be confused with (MANDATORY)
Cloud Billing and Budgets - Recommender suggests actions, while Billing/Budgets tracks spend and alerts on thresholds.

▎ Choose Recommender if you need optimization recommendations for resources or permissions / Choose Cloud Billing and Budgets if you need cost reporting, exports, or budget alerts.

### Security & FAQ Insights
- Review recommendations before applying in production.
- IAM recommendations help reduce overly broad roles.
- Use with Monitoring/Billing data for continuous optimization.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; know the main triggers, limits, and closest alternatives.

---

# Exam Coverage Check

Coverage verified against the official Professional Cloud Architect exam areas: designing and planning cloud solution architecture; managing and provisioning infrastructure; designing for security and compliance; analyzing and optimizing technical/business processes; managing implementation; and ensuring reliability. This version explicitly covers the major PCA service families: Resource Manager/Cloud Identity/IAM/Service Accounts/Org Policy/IAM Deny; VPC/Shared VPC/Peering/NCC/Interconnect/VPN/Load Balancing/CDN/DNS/NAT/Armor/PSC/PGA/VPC Service Controls/PSA/Serverless VPC Access/firewall policies/Flow Logs; Compute Engine/MIG/GKE/Cloud Run/App Engine/Cloud Functions; Cloud Storage/Persistent Disk/Hyperdisk/Filestore/Cloud SQL/AlloyDB/Spanner/Firestore/Bigtable/Memorystore/BigQuery/Looker; Pub/Sub/Dataflow/Dataproc/Composer/Tasks/Scheduler; Storage Transfer/Transfer Appliance/Migrate to VMs/DMS; KMS/Secret Manager/IAP/SCC/Sensitive Data Protection/Assured Workloads/Access Transparency/Access Approval; Cloud Monitoring/Logging/Trace/Error Reporting/Billing/Budgets/Recommender; Cloud Build/Artifact Registry/Cloud Deploy/Infrastructure Manager/Config Connector; Apigee/API Gateway/Vertex AI.

Reference: Google Cloud Professional Cloud Architect exam guide and official Google Cloud product documentation.
