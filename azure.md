
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

. Azure Monitor = Monitoring platform.
. Application Insights = Application Performance Monitoring (APM) component within Azure Monitor.

#### Q3: What is Log Analytics?

Service used to store and analyze log data using KQL queries.

#### Q4: What is a Data Collection Rule (DCR)?

Configuration that defines what data is collected, transformed, and where it is sent.

#### Q5: What are the types of Azure Monitor alerts?

1. Metric Alerts
2. Log Alerts
3. Activity Log Alerts
4. Smart Detection Alerts
