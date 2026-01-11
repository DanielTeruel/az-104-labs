# Lab 10 – Implement Azure Backup and Disaster Recovery (AZ-104)

## Introduction

This lab explores how to protect data and ensure business continuity in Azure using **Azure Backup** and **Azure Site Recovery**.

A base infrastructure is deployed using **ARM templates**, which includes a virtual machine, virtual network, subnet, NIC, public IP, and network security group (NSG).
Then, VM-level backups are implemented, and cross-region replication is enabled for disaster recovery.

For more information about the ARM template used in this lab, check the **ARM Files** folder, which includes:

* 📄 [template.json](arm/template.json) – Defines the complete infrastructure of the VM, network, NIC, public IP, and NSG.
* 📄 [parameters.json](arm/parameters.json) – Parameter file that separates configuration values from the template, including VM names, size, network, and admin user.
* 📄 [explicaciones.md](arm/explicaciones.md) – Documentation in **Spanish**, with detailed explanations of each resource and deployment flow.
* 📄 [explanations.md](arm/explanations.md) – Documentation in **English**, adapted from the Spanish version for international audiences.

---

## Business Scenario

The organization needs to protect a critical virtual machine against:

* Data loss
* Accidental deletions
* Regional failures

A solution is required that allows:

* Performing **automatic backups**
* Retaining data for a defined period
* Replicating the infrastructure to a **secondary region** for disaster recovery

The lab reflects a real-world **business continuity** scenario where protecting and restoring critical workloads is essential to minimize incident impact.

---

## Lab Objectives

* Deploy infrastructure using **ARM templates**
* Create and configure a **Recovery Services Vault**
* Configure **Azure Backup** for a virtual machine
* Create and apply a **backup policy**
* Monitor Azure Backup using **diagnostics and storage accounts**
* Implement cross-region replication with **Azure Site Recovery**
* Apply **best practices for resource cleanup**

---

## Task 1 – Deploy Infrastructure Using an ARM Template

I started by accessing the **Azure Portal** and selecting **Deploy a custom template**.

For the initial deployment of the virtual machine, I used an **ARM template**, which allows defining infrastructure as code (*Infrastructure as Code*) and ensures **repeatable, consistent, and controlled deployments**.

![1.1](screenshots/1.1.png)

Next, I selected **Build your own template in the editor** and uploaded the ARM template for the lab.

* 📄 [template.json](arm/template.json)

The `template.json` file defines **the infrastructure structure**, including:

* Virtual network and subnet
* Network interfaces and public IPs
* Network security group (NSG)
* Virtual machine and its properties (SKU, image, size)

![1.2](screenshots/1.2.png)

Then I uploaded the **parameters** file, where the values that can vary between deployments are specified.

* 📄 [parameters.json](arm/parameters.json)

The `parameters.json` file allows **separating configuration values**, making the template reusable without modifying its logic.
It defines data such as:

* Virtual machine name
* VM size
* Virtual network and subnet
* Administrator user

![1.3](screenshots/1.3.png)

Finally, I reviewed the deployment details, selected a **secure password** for the virtual machine, and launched the deployment.

![1.4](screenshots/1.4.png)

---

## Task 2 – Create and Configure a Recovery Services Vault

Once the VM was deployed, I went to **Recovery Services vaults** and created a new vault.

![2.1](screenshots/2.1.png)

I selected the **resource group**, **region**, and **vault name**.

![2.2](screenshots/2.2.png)

I reviewed the configuration and created the Recovery Services Vault.

![2.3](screenshots/2.3.png)

Once created, I accessed **Settings → Properties** and located the **Backup Configuration** section, where I clicked **Update**.

![2.4](screenshots/2.4.png)

I verified that the replication type was set to **Geo-Redundant (GRS)** and that cross-region restore was disabled.

![2.5](screenshots/2.5.png)

Finally, I checked that **soft delete** was enabled with a retention period of **14 days**.

![2.6](screenshots/2.6.png)

---

## Task 3 – Configure Azure Backup for the Virtual Machine

From the Recovery Services Vault, I selected **Backup** and defined the workload type and resource type to protect.

![3.1](screenshots/3.1.png)

I selected the **Standard** policy subtype and created a new backup policy.

![3.2](screenshots/3.2.png)

I configured the new policy with the following values:

* Policy name: **az104-backup**
* Frequency: **Daily**
* Time: **00:00**
* Time zone: local time zone
* Snapshot retention: **2 days**

![3.3](screenshots/3.3.png)

Next, I selected the **VM0** virtual machine previously created to apply the backup policy.

![3.4](screenshots/3.4.png)

After reviewing the configuration, I created the resource and verified that the virtual machine appeared under **Protected Items → Backup Items**.

![3.5](screenshots/3.5.png)

I observed that the VM was not yet fully protected, as the first backup according to the policy had not yet run.

![3.6](screenshots/3.6.png)

To speed up the process, I accessed the VM details and executed a **manual backup**.

![3.7](screenshots/3.7.png)

After approximately **30 to 60 minutes**, the backup completed successfully, and the last backup status appeared as **Succeeded**.

![3.8](screenshots/3.8.png)

---

## Task 4 – Configure Azure Backup Monitoring

Next, I went to **Storage Accounts** and created a new account.

![4.1](screenshots/4.1.png)

I selected a **unique name**, standard tier, and configured **Geo-Redundant Storage (GRS)**.

![4.2](screenshots/4.2.png)

I reviewed the configuration and created the storage account.

![4.3](screenshots/4.3.png)

Then I returned to the Recovery Services Vault and accessed **Monitoring → Diagnostic Settings** to add a new configuration.

![4.5](screenshots/4.5.png)

I created a setting called **Logs and Metrics to storage** and selected the following metrics and logs:

* Azure Backup Reporting Data
* Addon Azure Backup Job Data
* Addon Azure Backup Alert Data
* Azure Site Recovery Jobs
* Azure Site Recovery Events

![4.6](screenshots/4.6.png)

Finally, I configured the previously created storage account as the destination, selecting **Archive to a storage account**.

![4.4](screenshots/4.7.png)

---

## Task 5 – Enable Replication with Azure Site Recovery

To finish, I created a new **Recovery Services Vault** intended for replication.

![5.1](screenshots/5.1.png)

I selected a **different target region** from the original VM region.

![5.2](screenshots/5.2.png)

In the following sections, I left the default values, created the **automation account**, and completed the setup.

![5.3](screenshots/5.3.png)

Once replication was enabled, I accessed **Protected Items → Replicated Items**, where I could see that the VM synchronization had started successfully.

---

## Cleanup

To avoid unnecessary costs and leftover configurations, I deleted all resources created during the lab.

The easiest way to perform cleanup is to delete the entire resource group.

### Azure Portal

Resource Group → Delete Resource Group

### Azure PowerShell

```powershell
Remove-AzResourceGroup -Name az104-RG10
```
¿Quieres que haga eso también?
