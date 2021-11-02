# intro to virtual machine monitoring

- [This repo is based on this Learn module](https://docs.microsoft.com/learn/modules/monitor-performance-using-azure-monitor-for-vms/)
- [Learn more about Azure monitor](https://azure.microsoft.com/services/monitor/#overview)

# What are Azure Monitor Logs and Azure Monitor VM Insights?

Azure Monitor Logs collects and organizes log data generated from Azure resources. Log data is stored in a Log Analytics workspace. Data living in the workspace can be queried for trend analysis, reporting, and alerting. Some examples of data captured include Windows event logs, Heartbeat logs, performance data, and Syslogs.

Azure Monitor VM Insights is a feature of Azure Monitor that relies on Azure Monitor Logs and uses a table named InsightsMetrics. You can query performance and usage for virtual machines by using that table.

## What is the relationship between all the Azure native monitoring tools?

 Azure Monitor becomes the service at the top, which spans across all monitoring tools, while everything else lives underneath. The service collects and analyzes data generated from Azure resources. Azure Monitor captures monitoring data from the following sources:

- Application
- Guest OS
- Azure resources
- Azure subscriptions
- Azure tenant

Data collected by Azure Monitor is composed of metrics in Azure Monitor Metrics and logs in Azure Monitor Logs. Azure Monitor Metrics are lightweight numerical values stored in a time-series database that can be used for near real time alerting. 

Azure Monitor Metrics only store numeric data using a specific structure. Azure Monitor Logs can store Azure Monitor Metrics data and various other data types, each using their own structure.

The following image shows how applications, resources, workloads, tenant data, and custom sources flow into Azure Monitor (to either metrics or logs). When the data is in metrics or logs, there are many different ways to visualize, analyze, respond, integrate, and view overall resource health.

![flow of monitor data](img/1.png)

All Azure resources also automatically generate Azure platform logs, which are collected by Azure Monitor. Platform logs are resource logs, activity logs, and Azure Active Directory logs. Administrators might need to configure certain platform logs to be forwarded to one or more destinations (like Log Analytics) in order to be kept.

## Plan a Log Analytics workspace deployment

![ingested data of azure monitor](img/2.png)

One of the tasks involved with a Log Analytics deployment is picking the right design. Log Analytics workspaces now provide different levels of access control for the collected logs.


		

| Feature         | Description     
|--------------|-----------|
| Access mode | Involves how users access a Log Analytics workspace, plus defines data scope | 
| Access control mode      | Defines how permissions work for any given Log Analytics workspace  | 
| Table-level RBAC      | Provides a mechanism to define more granular data control inside a Log Analytics workspace with other permissions listed in the table  |



The best strategy is to limit the total number of workspaces required for daily operations. Reducing the number of workspaces will make administration and query experience easier and quicker. Multiple workspaces might still need to be a design consideration for certain companies.

## Azure collects compute monitoring data by using agents

Compute resources in Azure require agents to help collect monitoring data inside Log Analytics and Azure Monitor. Each agent allows customers to measure performance, responsiveness, and availability of guest operating systems and underlying workloads.

The following table lists each agent:

| Agent     | Description | Notes
|--------------|-----------|------------|
| Azure Monitor Agent | Collects monitoring data from guest operating systems on VMs and delivers data to Azure Monitor logs and/or metrics     | Over time, this agent will replace the Log Analytics Agent and Azure diagnostic extension listed below. While this agent introduces new capabilities, it does not support all the monitoring scenarios covered by earlier agents. You need to understand the tradeoffs before switching to the Azure Monitor agent.        |
| Log Analytics agent      | Collects logs and performance data for virtual machines in Azure, other clouds, or on-premises  | Allows for the onboarding of Azure Monitor VM Insights, Azure Security Center, and Azure Sentinel. The agent also works with Azure Automation accounts to onboard Azure Update Management and Azure Automation State Configuration, along with Azure Automation Change Tracking and Inventory.       |
| Azure diagnostics extension | Enables customers to receive extra data from guest operating systems and workloads living on compute resources | Data primarily captured with this extension will be sent to Azure Monitor Metrics. If necessary, this data could also be sent to a third-party tool by using Azure Event Hubs or sent to Azure Storage for archival. You could also collect boot diagnostics, which helps with investigations for virtual machine boot issues.
|Dependency agent |Collects discovered data about certain processes running on virtual machines | Maps all dependencies between virtual machines and any external process dependencies.

# An intro to Kusto Query Language 

Several Azure services use Azure Monitor Logs to store data and use the Kusto Query Language (KQL) to extract data. A Log Analytics workspace is the equivalent of a database inside Azure Data Explorer

A Kusto query is a read-only request. The request comes back in plain text, which makes the data easy to read. Each query uses schema entities that are organized into a hierarchy similar to SQL: databases, tables, and columns.

A query consists of references to actual tables and one or more query operators applied in sequence. When you look at a query, each reference is indicated visually by the use of a pipe character (|) to delimit operators. 

At least one statement in the query is a tabular expression statement, which is a statement that produces data arranged in columns and rows. Tabular operators will predominantly be the way you interact with and query monitoring data. Tabular operators such as count, distinct, evaluate, join, limit, and project are all relevant in formulating queries.

## Understand the schema and schema pane

The schema is a series of tables logically grouped together. The schema allows for an easy understanding behind how Log Analytics stores data. The schema displays on the schema pane located on the far left of a Log Analytics workspace. The schema is helpful when you craft queries.

## Building queries

Starting with a table name is the right way to configure log queries. Using a table name defines a clear scope for the query, which improves query performance and relevance of the results.

The KQL is case sensitive. Language keywords are typically written in lowercase, such as consume, count, distinct, evaluate, join, limit, and project. When you use names of tables or columns in a query, make sure to use the correct case. If you're ever stuck, the schema pane will show how to use correct casing for the table name.

```sql
Heartbeat | distinct Computer
```

The results now show only two virtual machines are reporting to the workspace. Heartbeat acts like an inventory of all virtual machines reporting to a specific workspace. The distinct operator produces a table with the distinct combination of columns requested within the input query.

# Demo

1. Create a log analytics workspace
2. Create two Ubuntu VMS.
3. Onboard VMs to workspace.
4. View data
