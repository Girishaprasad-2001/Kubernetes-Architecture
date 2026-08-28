
## Azure App Service

Azure App Service is a fully managed Platform as a Service (PaaS) offering in Microsoft Azure that allows you to build, deploy, host, and scale web applications, REST APIs, and mobile back ends without managing servers or infrastructure. It supports .NET, Java, Node.js, Python, PHP, and custom containers on both Windows and Linux.

### Key Features

1. Fully managed hosting with automatic OS patching and infrastructure maintenance.
2. Auto-scaling based on demand.
3. Built-in CI/CD integration with GitHub, Azure DevOps, and other deployment tools.
4. Custom domains and SSL certificates for secure web hosting.
5. Authentication and authorization with Microsoft Entra ID, Google, Facebook, and other identity providers.
6. Monitoring and diagnostics through Azure Monitor and Application Insights.

### Components

1. Web Apps: Host websites and web applications.
2. API Apps: Build and host REST APIs.
3. WebJobs: Run background tasks and scheduled jobs.
4. App Service Environment (ASE): Isolated and highly secure hosting for enterprise workloads.

## Azure Monitor

Azure Monitor is Microsoft’s centralized monitoring and observability service that collects, analyzes, visualizes, and responds to telemetry from Azure, on-premises, hybrid, and multicloud resources. It brings together metrics, logs, traces, and events into a single monitoring platform.

### Core Components
##### 1. Metrics
Near real-time performance data
Examples:
CPU Utilization
Memory Usage
Disk IOPS
Network Throughput
Stored for fast querying and alerting.
##### 2. Logs
Detailed records collected from applications and infrastructure
Stored in Log Analytics Workspace
Queried using Kusto Query Language (KQL).

Example KQL:

```
AzureActivity
| where TimeGenerated > ago(24h)
| summarize count() by ResourceGroup
```
#### 3. Application Insights

Application Performance Monitoring (APM) service used to monitor applications:

Request rates
Response times
Failures
Dependencies
User behavior
Distributed tracing

Supports .NET, Java, Node.js, Python, and more.

#### 4. Alerts

Generate notifications when conditions are met:

Metric Alerts
Log Alerts
Activity Log Alerts
Smart Detection Alerts

Actions can include:

Email
SMS
Webhooks
Azure Functions
Logic Apps
#### 5. Dashboards & Workbooks
Interactive visualization
Custom reports
Performance dashboards
Operational monitoring views

### Q1: What is Azure Monitor?
 A centralized monitoring service for collecting, analyzing, and acting upon telemetry from Azure and hybrid environments.

#### Q2: Difference between Azure Monitor and Application Insights?

1. Azure Monitor = Monitoring platform.
2. Application Insights = Application Performance Monitoring (APM) component within Azure Monitor.

#### Q3: What is Log Analytics?

Service used to store and analyze log data using KQL queries.

#### Q4: What is a Data Collection Rule (DCR)?

Configuration that defines what data is collected, transformed, and where it is sent.

#### Q5: What are the types of Azure Monitor alerts?

1. Metric Alerts
2. Log Alerts
3. Activity Log Alerts
4. Smart Detection Alerts

## Azure Log Analytics

Log Analytics is an Azure service used to collect, store, query, and analyze log data from Azure resources, virtual machines, applications, and other connected services. It is a core component of Azure Monitor and stores data in a Log Analytics Workspace, where you can analyze it using Kusto Query Language (KQL).

### Why Use Log Analytics?

Log Analytics helps you:

1. Troubleshoot application and infrastructure issues.
2. Monitor resource health and performance.
3. Perform operational and security investigations.
4. Create dashboards, reports, and alerts from collected logs.
## Log Analytics Architecture
   ```
   Azure Resources
VMs
App Services
AKS
Azure SQL
Storage Accounts
     |
     v
Azure Monitor Agent / Resource Diagnostics
     |
     v
Log Analytics Workspace
     |
     v
KQL Queries
Alerts
Workbooks
Dashboards
```
### Log Analytics Workspace

A Log Analytics Workspace is the container where log data is stored and analyzed. Multiple Azure resources can send logs to the same workspace.

Common data sources include:

1. Azure Virtual Machines
2. Azure App Services
3. Azure SQL Database
4. AKS Clusters
5. Storage Accounts
6. Microsoft Entra ID Logs
7. Custom Applications

### What is Log Analytics?

A service within Azure Monitor that stores and analyzes log data using KQL queries.

### What is a Log Analytics Workspace?

A centralized repository where monitoring logs are collected and retained for analysis.

### What language is used in Log Analytics?

Kusto Query Language (KQL).

### What is the difference between Metrics and Logs?
Metrics: Lightweight, near real-time numerical data.
Logs: Detailed records used for deep analysis and troubleshooting.
#### What is the Heartbeat table used for?

To verify that VMs and connected servers are actively reporting to Azure Monitor.

## Azure Active Directory (Azure AD) / Microsoft Entra ID

Azure Active Directory (Azure AD) was renamed to Microsoft Entra ID in July 2023. It is Microsoft's cloud-based Identity and Access Management (IAM) service used to manage users, groups, applications, authentication, and access to Azure, Microsoft 365, and third-party applications

### What Does Microsoft Entra ID Do?

It helps organizations:

1. Authenticate users (verify identity).
2. Authorize access to resources and applications.
3. Provide Single Sign-On (SSO).
4. Enforce Multi-Factor Authentication (MFA).
5. Apply Conditional Access security policies.
6. Manage identities for users, devices, and applications.
   
### Q1: What is Azure AD?
 Azure AD, now called Microsoft Entra ID, is a cloud-based Identity and Access Management service that provides authentication and authorization for users, applications, and devices.

### Q2: What is SSO?
 Single Sign-On allows users to authenticate once and access multiple applications without logging in again.

### Q3: What is Conditional Access?
 A policy engine that grants or blocks access based on conditions such as user, device, location, and risk.

### Q4: What is MFA?
 A security mechanism requiring multiple verification methods before granting access.

#### Q5: Difference between Authentication and Authorization?

* Authentication = "Who are you?"
* Authorization = "What can you access?"

### Azure DNS

Azure DNS is a Microsoft Azure service that hosts and manages DNS domains using Azure infrastructure. DNS (Domain Name System) converts human-readable domain names (such as www.contoso.com) into IP addresses that computers use to communicate. Azure DNS supports both Public DNS and Private DNS zones.

#### Key Features
1. Host public internet-facing DNS domains.
2. Create private DNS zones for Azure Virtual Networks (VNets).
3. High availability using Azure's global DNS infrastructure.
4. Integration with Azure RBAC, Activity Logs, and Azure Portal.
5. Supports standard DNS record types such as A, AAAA, CNAME, MX, TXT, SRV, PTR, and SOA

### Common DNS Record Types

| Record | Purpose |
| :--- | :--- |
| **A** | Maps hostname to IPv4 address |
| **AAAA** | Maps hostname to IPv6 address |
| **CNAME** | Alias to another hostname |
| **MX** | Mail server record |
| **TXT** | Text verification records |
| **PTR** | Reverse DNS lookup |
| **SRV** | Service location |
| **SOA** | Zone authority information |

### What is Azure DNS?

Azure DNS is a hosting service for DNS domains that provides name resolution using Microsoft Azure infrastructure.

### Difference between Public DNS and Private DNS?

##### Public DNS

1. Accessible from the Internet
2. Used for websites and public applications

##### Private DNS

Accessible only within linked VNets
Used for internal resources and private endpoints
#### What is a DNS Zone?

A DNS Zone is a container that stores DNS records for a domain.

### What is an A Record?

Maps a hostname to an IPv4 address.

### What is Azure DNS Private Resolver?

A managed service that enables DNS resolution between Azure and on-premises networks without custom DNS servers.
