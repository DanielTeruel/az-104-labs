# Lab 04 – Implement Virtual Networks in Azure (AZ-104)

## Summary

In this lab, I worked with **Azure Virtual Networks (VNets)** to design and deploy a basic network architecture. During the exercise:

* I created virtual networks and subnets from the Azure portal.
* I exported and reused an ARM template to deploy a new VNet.
* I configured **Network Security Groups (NSG)** and **Application Security Groups (ASG)**.
* I implemented security rules to control inbound and outbound traffic.
* I configured **public and private DNS zones** and verified name resolution.

This lab allowed me to better understand how networking is structured in Azure and how to integrate security and name resolution within a cloud architecture.

## Business Scenario

The organization requires a segmented network infrastructure for different environments and services. Additionally, it is necessary to control traffic through security rules and provide both public and private DNS resolution for internal and external resources.

## Lab Objectives

* Create and configure virtual networks and subnets in Azure.
* Export an ARM template from an existing resource.
* Modify an ARM template and deploy a new virtual network.
* Create and configure an NSG and an ASG.
* Associate an NSG with a subnet and define security rules.
* Create and configure a public DNS zone.
* Create and configure a private DNS zone and link it to a VNet.
* Verify DNS name resolution.
* Apply best practices for resource cleanup.

---

## Task 1 – Create a virtual network with subnets using the portal

I started by creating a **virtual network (VNet)** in the desired resource group and region.

![1.1](screenshots/1.1.png)

I configured the address space **10.20.0.0/16** and created two subnets:

* **DatabaseSubnet** → 10.20.20.0/24
* **SharedServicesSubnet** → 10.20.10.0/24

![1.2](screenshots/1.2.png)

I reviewed the configuration and created the resource.

![1.3](screenshots/1.3.png)

Once the VNet was created, I went to **Automation → Export template** and downloaded the ARM template in ZIP format, which contained the files `template.json` and `parameters.json`.

![1.4](screenshots/1.4.png)

---

## Task 2 – Create a new virtual network using an ARM template

I opened the exported files in **Visual Studio Code** and made the following changes:

* Changed the network name from `CoreServicesVnet` to `ManufacturingVnet`.
* Changed the address space to **10.30.0.0/16**.
* Renamed the subnets:

  * `SharedServicesSubnet` → `SensorSubnet1` (10.30.20.0/24)
  * `DatabaseSubnet` → `SensorSubnet2` (10.30.21.0/24)
* Also updated the `parameters.json` file to match the new names.

Once the changes were ready, I went to the Azure portal and searched for **Deploy a custom template**. I uploaded the `template.json` file.

![2.3](screenshots/2.3.png)

Then I uploaded the `parameters.json` file.

![2.4](screenshots/2.4.png)

I selected the resource group and the name the new VNet would have.

![2.5](screenshots/2.5.png)

## ARM Templates Used

The complete templates used in this lab can be found here:

* 📄 [template.json](arm/template.json)
* 📄 [parameters.json](arm/parameters.json)

These files can be downloaded and reused directly to reproduce the lab.

I started the deployment, and when it finished, I verified that the new virtual network had been created correctly.

![2.6](screenshots/2.6.png)

I also confirmed that the subnets had been created correctly.

![2.7](screenshots/2.7.png)

---

## Task 3 – Create and configure an ASG and an NSG

First, I went to **Application Security Groups (ASG)** and created a new group.

![3.1](screenshots/3.1.png)

I selected the resource group, name, and region, and created it.

![3.2](screenshots/3.2.png)

Next, I created a **Network Security Group (NSG)**.

![3.3](screenshots/3.3.png)

Once created, I opened the NSG and in **Settings → Subnets**, I associated it with a subnet.

![3.4](screenshots/3.4.png)

In this case, I associated it with **SharedServicesSubnet**.

![3.5](screenshots/3.5.png)

Then, I went into the NSG rules configuration.

![3.6](screenshots/3.6.png)

I created an **inbound rule** that allows traffic from the ASG to the subnet on ports **80 and 443** using **TCP**, with priority **100**.

![3.7](screenshots/3.7.png)

Next, I created an **outbound rule** that **denies all traffic to the Internet**.

![3.8](screenshots/3.8.png)

I verified that the inbound rule correctly allows traffic from the ASG on ports 80 and 443.

![3.9](screenshots/3.9.png)

And I also confirmed that the outbound rule blocks any traffic to the Internet.

![3.10](screenshots/3.10.png)

---

## Task 4 – Configure public and private DNS zones

First, I created a **public DNS zone** called **contoso7.com**.

![4.1](screenshots/4.1.png)

Once created, I went to DNS record management.

![4.2](screenshots/4.2.png)

I added a new record:

* Name: **www**
* Type: **A**
* IP Address: **10.1.1.4**

![4.3](screenshots/4.3.png)

I verified name resolution using:

`nslookup www.contoso7.com ns1-06.azure-dns.com`

![4.4](screenshots/4.4.png)

Then, I created a **private DNS zone**.

![4.5](screenshots/4.5.png)

Once created, I added a **virtual network link**.

![4.6](screenshots/4.6.png)

I linked the private DNS zone to the **ManufacturingVnet**.

![4.7](screenshots/4.7.png)

I added an A record:

* Name: **sensorvm**
* IP Address: **10.1.1.4**

![4.8](screenshots/4.8.png)

I verified that the record was created successfully.

![4.9](screenshots/4.9.png)

And confirmed that the private DNS zone was correctly linked to the virtual network.

![4.10](screenshots/4.10.png)

---

## Resource Cleanup

To avoid unnecessary costs, I delete the Resource Group, which removes all associated resources.

![4.11](screenshots/4.11.png)

Azure Portal:

Resource Groups → Delete Resource Group

Azure PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg4
```

---
