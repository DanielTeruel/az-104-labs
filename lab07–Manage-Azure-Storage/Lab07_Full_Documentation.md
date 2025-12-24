# Lab 07 – Manage Azure Storage (AZ-104)

## Overview
In this lab, I worked with **Azure Storage** to create and configure storage accounts, secure blob and file storage, manage lifecycle rules, and control access using network restrictions and authentication mechanisms such as SAS tokens.

The goal was to understand how Azure Storage can be optimized for cost, secured at multiple layers, and integrated with Azure networking features.

---

## Business Scenario (Roleplay)

In this scenario, the organization currently stores data on local file servers.  
Most of this data is accessed infrequently, and storage costs need to be optimized.

Additionally, the organization wants to:
- Secure access to storage using network restrictions
- Control access using authentication and authorization mechanisms
- Protect data using immutable storage policies
- Evaluate Azure Files as a replacement for on-premises file shares

---

## Lab Objectives

- Create and configure an Azure Storage Account
- Secure storage using network access controls
- Configure lifecycle management rules
- Create and secure blob containers
- Use SAS tokens for controlled access
- Create and configure Azure File shares
- Restrict access using virtual networks
- Clean up resources after the lab

---

## Task 1 – Create and configure an Azure Storage Account

To start the lab, I created a new **Resource Group** to contain all resources used in this exercise.  
I named the resource group `az104-RG7`.

![Create resource group](screenshots/1.1.png)

Inside this resource group, I created a new **Storage Account** with the required configuration.

![Create storage account](screenshots/1.2.png)

After the storage account was created, I navigated to **Security + networking → Networking → Manage public access**.

![Manage public network access](screenshots/1.3.png)

Here, I enabled **Public network access** but restricted it to **selected networks only**, allowing access exclusively from my public IP address.

![Allow selected networks](screenshots/1.4.png)

Next, I navigated to **Data management → Lifecycle management**.

![Lifecycle management](screenshots/1.5.png)

I added a new lifecycle rule named **movetocool**, applying it to all blobs in the storage account.

![Add lifecycle rule](screenshots/1.6.png)

I configured the rule so that any blob **not modified for more than 30 days** would be moved from the **Hot** tier to the **Cool** tier, helping reduce storage costs.

![Move blobs to cool tier](screenshots/1.7.png)

---

## Task 2 – Create and configure secure Blob storage

I returned to the storage account and created a new **Blob Container** named `data`, ensuring that the **public access level** was set to **Private**.

![Create blob container](screenshots/2.1.png)

After creating the container, I navigated to **Settings → Access policy** and configured **Immutable Blob Storage** to retain blobs for **180 days**.

![Immutable blob policy](screenshots/2.2.png)

Next, I uploaded a PNG file to the container inside a new folder named `securitytest`, using the **Hot** access tier.  
During the upload, I could see that the immutable policy was already applied.

![Upload blob](screenshots/2.3.png)

Once uploaded, I attempted to access the file directly but received a message indicating that **public access was not permitted**.

![Public access denied](screenshots/2.4.png)

This occurred because the container is private and requires explicit authorization.  
To grant limited access, I generated a **SAS token** for the uploaded file, selecting the required permissions and copying the generated SAS URL.

![Generate SAS token](screenshots/2.5.png)

By opening the SAS URL in the browser, I was able to access the file successfully, confirming that access was granted through both network rules and SAS permissions.

![Access blob with SAS](screenshots/2.6.png)

---

## Task 3 – Create and configure Azure File storage

Next, I navigated to **File shares** in the storage account.

![Navigate to file shares](screenshots/3.1.png)

I created a new file share named `share1`, keeping the default **Transaction optimized** access tier.

![Create file share](screenshots/3.2.png)

I disabled backups for this file share, as they were not required for this lab.

![Disable backup](screenshots/3.3.png)

After creation, I verified that the file share was created successfully.

![File share created](screenshots/3.4.png)

I then opened **Storage Browser**, selected the `share1` file share, and uploaded the same PNG file into it.

![Storage Browser](screenshots/3.5.png)  
![Upload file to file share](screenshots/3.6.png)

To further secure access, I created a **Virtual Network** named `vnet1`.

![Create virtual network](screenshots/3.7.png)

Within the virtual network configuration, I enabled a **Service Endpoint** for **Microsoft.Storage** on the default subnet.

![Configure service endpoint](screenshots/3.8.png)

I then returned to the storage account network settings, allowed access from the newly created virtual network, and removed my public IP address.

![Restrict network access to VNet](screenshots/3.9.png)

After this change, attempting to access the storage resources resulted in a **network access denied** message, confirming that access was now restricted to the virtual network only.

![Restrict network access to VNet](screenshots/3.10.png)

---

## Cleanup

To avoid unnecessary costs and leftover configurations, I removed all resources created during this lab.

The easiest way to clean up is to delete the entire resource group after removing any locks or dependencies.

### Azure Portal
Resource Group → Delete resource group

### Azure PowerShell
```powershell
Remove-AzResourceGroup -Name az104-RG7
