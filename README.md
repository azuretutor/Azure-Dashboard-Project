# Azure-Dashboard-Project

**Architecture Overview**

This project demonstrates how to design and deploy a **custom Azure Dashboard** that provides a single-pane-of-glass view into cloud workloads — including performance, cost, and security metrics — for Azure resources.


**High-Leve Architecture**

[Azure Resources]\
      |\
      v\
[Azure Monitor] ——→ [Log Analytics Workspace]\
      |\
      ├── [Azure Metrics & Insights]\
      |\
      ├── [Azure Resource Graph Queries]\
      |\
      ├── [Azure Cost Management]\
      |\
      └── [Microsoft Defender for Cloud]\
      |\
      v\
[Azure Dashboard (Portal Visualization Layer)]


**Key Components**

| Component          | Purpose        |
|-----------------   | -------------- |
| **Azure Monitor**          | Collects telemetry data (metrics, logs, activity) from all Azure resources. |
| **Log Analytics Workspace** | Centralized data store for queries and visualizations. |
| **Azure Resource Graph** |Provides fast, efficient resource queries across subscriptions. |
| **Microsoft Defender for Cloud** | Adds security posture and compliance data.|
| **Azure Dashboard** | Visual interface built within the Azure Portal to display key metrics and charts. |



**Configuration Steps**

**Step 1: Create a Log Analytics Workspace**
1. in the Azure Portal, search for **Log Analytics Workspaces**.
2. Click **Create** and specify:
   * Subscription and Resource Group
   * Workspace name (e.g, Cloud-Territory)
   * Region (same as your primary resources)
3. Once created, link your Azure resources (VMs, storage, etc.) to this workspace for data collection.

**Step 2: Enable Diagnostic Settings**
1. Go to each Azure resource (e.g., VM, App Service, Storage Account)
2. Under **Monitoring** > **Diagnostic Settings**, click **Add Diagnostic Setting**.
3. Select **Send to Log Analytics workspace**.
4. Choose relevant log categories (e.g., performance counters, metrics, audit logs).


**Step 3: Configure Data Collection Rules (Optional)**
For more control over metrics, create **Data Collection Rules (DCRs)**:
* Navigate to **Azure Monitor** > **Data Collection Rules**.
* Define sources (VMs, Containers) and destinations (Log Analytics workspace)
* Apply filtering and transformation rules for cleaner data ingestion.


**Step 4: Create REsource Graph Queries**

Use **Azure Resource Graph Explorer** to query metadata about resources.

Example query:

Resources\
| where type =~ 'microsoft.compute/virtualmachines'\
| project name, location, resourceGroup, properties.hardwareProfile.vmSize

Save and pin these queries to your dashboard for dynamic updates.

**Step 5: Build KQL Queries for Insights**

Use **Log Analytics** to build queries that power visualizations.

Example — **CPU Utilization (VMs)**:

Perf\
| where ObjectName == "Processor" and CounterName == "% Processor Time"\
| summarize AvgCPU = avg(CounterValue) by bin(TimeGenerated, 5m), Computer

Example — **Security Recommendations**:

SecurityRecommendation\
| summarize count() by RecommendationDisplayName, Severity\
| order by count_ desc

Example — **Cost Analysis**:

Usage\
| summarize TotalCost = sum(PreTaxCost) by ServiceName, bin(UsageDate, 1d)\
| order by TotalCost desc
