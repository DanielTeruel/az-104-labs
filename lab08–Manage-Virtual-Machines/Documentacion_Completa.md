# Lab 08 – Manage Virtual Machines (AZ-104)

## Overview

In this lab, we explore the deployment and management of Azure Virtual Machines, comparing **standalone virtual machines** with **Virtual Machine Scale Sets (VMSS)**.  
Key concepts such as **Availability Zones**, **vertical scaling**, **automatic horizontal scaling**, and VM creation using **Azure Portal, PowerShell, and Azure CLI** are covered.

---

## Business Scenario

The organization wants to evaluate different approaches to deploy and scale virtual machines in Azure.  
First, individual virtual machines are deployed across **Availability Zones** to improve availability.  
Later, a **Virtual Machine Scale Set** is implemented to automatically scale resources based on workload demand.

---

## Lab Objectives

- Deploy virtual machines across Availability Zones  
- Scale compute and storage resources  
- Create and configure a VM Scale Set  
- Configure autoscaling rules  
- Create virtual machines using PowerShell and Azure CLI  
- Clean up resources after the lab  

---

## Task 1 – Create Virtual Machines in Availability Zones

As usual, I start by creating a **Resource Group** where all lab resources will be grouped.

![Create Resource Group](screenshots/1.1.png)

Next, I navigate to **Virtual Machines** and click **Create**.  
During VM creation, I select the Resource Group and configure **two virtual machines**, each deployed in a **different Availability Zone** within the same region.

![Create Virtual Machines](screenshots/1.2.png)

I select **Standard security**, the **Windows Server 2025 Datacenter x64 Gen2** image, disable **Azure Spot** to avoid interruptions, and choose the **Standard D2s_v3** size, which is sufficient for this lab.  
I configure a username and password and disable hibernation.

![Basic VM Configuration](screenshots/1.3.png)

In the disk configuration, I disable host encryption, select a **128 GB Standard SSD**, enable disk deletion when the VM is deleted, and leave the encryption key managed by Azure.

![Disk Configuration](screenshots/1.4.png)

For networking, I select the VNet and subnet, do not open any public inbound ports, configure the public IP to be deleted with the NIC, and do not use a load balancer.

![Networking Configuration](screenshots/1.5.png)

I disable boot diagnostics since they are not required for this lab.

![Monitoring Settings](screenshots/1.6.png)

I review the configuration and create the virtual machines.

![Review and Create](screenshots/1.7.png)

Once deployment is complete, both virtual machines are visible, each deployed in a different Availability Zone.

![VMs Created](screenshots/2.1.png)

---

## Task 2 – Scale Compute and Storage Resources

I select **az104-vm1** and navigate to **Availability + scale → Size**, where I upgrade the SKU from **D2s_v3** to **D2ds_v4**.

![Resize VM](screenshots/2.2.png)

The operation completes successfully, which is confirmed via portal notifications.

![Resize Notification](screenshots/2.3.png)

Next, I add a **data disk** from **Settings → Disks**, creating a **32 GB HDD** disk.

![Add Data Disk](screenshots/2.4.png)

The disk is successfully attached to the VM.

![Disk Attached](screenshots/2.5.png)

I then detach the disk using the detach icon to modify and reuse it.

![Detach Disk](screenshots/2.6.png)

From the Azure portal home page, I select the disk resource.

![Select Disk](screenshots/2.7.png)

I upgrade the disk type from **HDD** to **Standard SSD** and save the changes.

![Upgrade Disk](screenshots/2.8.png)

Finally, I reattach the existing disk to the virtual machine.

![Attach Existing Disk](screenshots/2.9.png)

---

## Task 3 – Create and Configure a Virtual Machine Scale Set

I search for **Virtual Machine Scale Sets** and click **Create**.

![Create VMSS](screenshots/3.1.png)

I configure the Resource Group, scale set name, region, Availability Zones, **Uniform** orchestration mode, Standard security type, and manual capacity.

![VMSS Basic Configuration](screenshots/3.2.png)

I set a base instance count of **2**, select **Windows Server 2025 Datacenter Gen2**, choose **Standard D2s_v3**, disable hibernation, and configure credentials.

![Instance Configuration](screenshots/3.3.png)

I create a new VNet named **vmss-vnet8** with address space **10.81.0.0/20** and subnet **10.81.0.0/24**.

![Network Configuration](screenshots/3.4.png)

I create a **Network Security Group** with a rule allowing **HTTP traffic** from any source for load balancer testing.

![NSG Rule](screenshots/3.5.png)

I configure the NIC, enable a public IP, and associate the VNet, subnet, and NSG.

![NIC Configuration](screenshots/3.6.png)

I configure a **public Load Balancer**, create it, and select it for the scale set.

![Load Balancer](screenshots/3.7.png)

I disable boot diagnostics and leave the remaining settings as default. After reviewing, I create the scale set.

![Create VMSS](screenshots/3.9.png)

Once deployed, I confirm the VM Scale Set has been created successfully.

![VMSS Created](screenshots/3.10.png)

---

## Task 4 – Configure VM Scale Set Autoscaling

Inside the VMSS, under **Availability + scale → Scaling**, I change the scaling mode to **Automatic**.

I configure a rule to **increase instances** when average CPU usage exceeds **70% for 10 minutes**, increasing instance count by **50%**, with a **5-minute cooldown**.

![Scale Out Rule](screenshots/4.1.png)

I configure a second rule to **decrease instances** when CPU usage drops below **30% for 10 minutes**, reducing instance count by **50%**.

![Scale In Rule](screenshots/4.2.png)

I configure instance limits to prevent uncontrolled scaling.

![Instance Limits](screenshots/4.3.png)

I set the maximum number of instances to **5**, which is sufficient for this lab.

![Maximum Instances](screenshots/4.6.png)

On new Azure subscriptions, an error may appear indicating **Microsoft.Insights** is not registered.  
To resolve this, I run the following Azure CLI commands:

```bash
az provider register --namespace Microsoft.Insights
