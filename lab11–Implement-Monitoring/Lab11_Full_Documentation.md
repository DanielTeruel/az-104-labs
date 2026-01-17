# Lab 11 – Implement Monitoring with Azure Monitor (AZ-104)

## Introduction

In this lab, you explore the capabilities of **Azure Monitor** to monitor Azure resources, create alerts, and notify operations teams about critical events.

A base infrastructure is deployed using an **ARM template**, which defines the creation of a virtual machine and its associated resources.  
The template used in this lab can be reviewed directly in the repository:

- 📄 [template.json](https://github.com/DanielTeruel/az-104-labs/blob/main/lab11%E2%80%93Implement-Monitoring/arm/template.json)

After the deployment, **VM Insights** is enabled, **alert rules** and **action groups** are created, alerts are tested in real scenarios, and **alert processing rules** are configured to suppress notifications during maintenance windows.

Finally, **Azure Monitor Logs** and **KQL queries** are used to analyze metrics and events collected by the platform.

---

## Business scenario

The organization has migrated its infrastructure to Azure and needs to ensure that **administrators are notified of critical changes**, such as the deletion of virtual machines.

To achieve this, the organization requires a solution that allows:

- Real-time monitoring of virtual machines  
- Detection of significant events at the subscription level  
- Automatic notifications to the operations team  
- Suppression of alerts during planned maintenance periods  
- Centralized analysis of metrics and events using log queries  

This lab represents a real-world **proactive monitoring** scenario, which is essential for maintaining stability and security in production environments.

---

## Lab objectives

- Deploy infrastructure using **ARM templates**
- Configure **Azure Monitor** and **VM Insights**
- Create alert rules based on events
- Configure **action groups** with email notifications
- Test alerts by deleting a virtual machine
- Create alert processing rules for maintenance windows
- Query metrics and events using **Azure Monitor Logs (KQL)**

---

## Task 1 – Deploy infrastructure and enable Azure Monitor

I started by accessing the **Azure Portal** and selecting **Deploy a custom template**.

![1.1](screenshots/1.1.png)

Next, I created a custom template using the **editor** and deployed the ARM template used for this lab.

- 📄 [template.json](https://github.com/DanielTeruel/az-104-labs/blob/main/lab11%E2%80%93Implement-Monitoring/arm/template.json)

This template defines the infrastructure required for the lab, including:
- Test virtual machine  
- Virtual network and subnet  
- Network interface  
- Public IP address  
- Base configuration for monitoring  

![1.2](screenshots/1.2.png)

During the deployment, I entered the **username and password** in the **Instance details** section, which are required to create the virtual machine.

![1.3](screenshots/1.3.png)

Once the deployment completed, I verified that all **resources were successfully deployed** within the resource group.

![1.4](screenshots/1.4.png)

After that, I navigated to **Azure Monitor**.

![1.5](screenshots/1.5.png)

From Azure Monitor, I accessed the virtual machine information and the **Insights configuration**.

![1.6](screenshots/1.6.png)

In the overview section, I confirmed that the virtual machine **VM0 did not have monitoring enabled**.

![1.7](screenshots/1.7.png)

I selected **Enable**, reviewed the default configuration, and proceeded to enable monitoring.

![1.8](screenshots/1.8.png)

Finally, I confirmed that **monitoring was successfully enabled** for the virtual machine.

![1.9](screenshots/1.9.png)

---

## Task 2 – Create an alert rule

Within **Azure Monitor**, I navigated to **Alerts** and created a new **alert rule**.

![2.1](screenshots/2.1.png)

I selected the **subscription as the scope**, so the alert applies to all virtual machines.

![2.2](screenshots/2.2.png)

In the **Condition** section, I searched for and selected the signal **Delete Virtual Machine (Virtual Machines)**.

![2.3](screenshots/2.3.png)

In the **Alert logic** section, I left the default values, keeping **All selected**.

![2.4](screenshots/2.4.png)

Next, I configured the **actions** by creating an action group that would be triggered when the condition is met.

![2.5](screenshots/2.5.png)

---

## Task 3 – Configure action group and notifications

I created an **action group** named **Alert the operations team**.

![3.1](screenshots/3.1.png)

In the notifications configuration, I selected **email notification**, named it **VM was deleted**, and configured the appropriate email address.

![3.2](screenshots/3.2.png)

I reviewed the configuration and created the action group.

![3.3](screenshots/3.3.png)

After creating the action group, I assigned a **name and description to the alert rule** and completed its creation.

![3.4](screenshots/3.4.png)

I verified that the **alert rule was created successfully** and appeared as active in Azure Monitor.

![3.5](screenshots/3.5.png)

---

## Task 4 – Test the alert

To validate the configuration, I deleted the **VM0** virtual machine, which triggered the alert rule.

![4.1](screenshots/4.1.png)

> ⚠️ The email notification may take a few minutes to arrive.

---

## Task 5 – Configure an alert processing rule

To simulate a **planned maintenance** scenario, I created an **alert processing rule** to suppress notifications.

From **Alerts → Create → Alert processing rule**, I selected the **subscription scope**.

![5.1](screenshots/5.1.png)

I configured the rule to **suppress notifications**.

![5.2](screenshots/5.2.png)

I defined a specific time window for the rule, setting it to apply between **10:00 PM and 7:00 AM the next day**.

![5.3](screenshots/5.3.png)

I entered the **rule name**, **description**, and **resource group**.

![5.4](screenshots/5.4.png)

After reviewing the configuration, I created the alert processing rule.

![5.5](screenshots/5.5.png)

---

## Task 6 – Query data using Azure Monitor Logs

Finally, I used **Azure Monitor Logs** to query the collected data.

I navigated to **Azure Monitor → Logs** and selected the **subscription as the scope**.

![6.1](screenshots/6.1.png)

In the top-right corner, I switched the query mode to **KQL mode** and ran the **Count heartbeats** query.

![6.2](screenshots/6.2.png)

I then executed a custom KQL query to analyze virtual machine metrics.

After running the query, I observed the **heartbeat chart**, which clearly shows when the virtual machine stopped sending heartbeats, indicating that it was deleted (approximately at **3:45 PM**).

![6.3](screenshots/6.3.png)

---

## Cleanup

To avoid unnecessary costs, I deleted all resources created during the lab.

The easiest way to clean up is to delete the entire resource group.

### Azure Portal
Resource groups → Delete resource group

### Azure PowerShell
```powershell
Remove-AzResourceGroup -Name az104-rg11
