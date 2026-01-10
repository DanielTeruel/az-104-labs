# Lab 06 – Implementing Network Traffic Management (AZ-104)

## Introduction

In this lab, we explore **network traffic management in Azure**, implementing and testing two key solutions:
**Azure Load Balancer (Layer 4)** and **Azure Application Gateway (Layer 7)**.

We deploy a base infrastructure using **ARM templates**, balance HTTP traffic between multiple virtual machines, and configure **route-based routing rules** to distribute images and videos from specific servers.

For more information about the ARM template used in this lab, check the [ARM Files](./arm/) folder, which includes:

* [`template.json`](./arm/template.json)
* [`parameters.json`](./arm/parameters.json)
* [`explanations.md` (English)](./arm/explanations.md)
* [`explicaciones.md` (Spanish)](./arm/explicaciones.md)

---

## Business Scenario

The organization has a public website receiving traffic from the Internet.
Incoming requests need to be distributed among multiple virtual machines to improve **availability, resilience, and scalability**.

Additionally, the organization needs to serve different types of content:

* **Images** from a specific server
* **Videos** from another server

To achieve this, a **basic Azure Load Balancer** is implemented for simple traffic distribution, and an **Azure Application Gateway** is deployed for advanced application-level routing.

---

## Lab Objectives

* Deploy infrastructure using ARM templates
* Configure a public Azure Load Balancer
* Validate traffic distribution between virtual machines
* Configure an Azure Application Gateway
* Implement route-based routing rules
* Check the status of backend servers
* Clean up resources after completing the lab

---

## Task 1 – Deploy the infrastructure using a custom template

Go to **Azure Portal → Deploy a custom template** and select **Create your own template in the editor**.

![Deploy custom template](screenshots/1.1.png)

In the editor, paste the provided ARM template code.

### ARM Template Summary

The ARM template deploys the **base lab environment**, which includes:

* 2 Windows Server 2019 virtual machines
* 1 virtual network (VNet) with 2 subnets
* 2 network interfaces (NICs), one per VM
* 1 network security group (NSG) with basic RDP and HTTP rules

> Provides a minimal test environment ready to deploy Load Balancer and Application Gateway.

> To review the full template and detailed explanation, check: [template.json](./arm/template.json) and [explanations.md](./arm/explanations.md).

![ARM Template](screenshots/1.2.png)

Next, upload the corresponding parameters file.

> Parameters file: [parameters.json](./arm/parameters.json)

![ARM Parameters](screenshots/1.3.png)

Once the template and parameters are loaded, review the values, select the **resource group**, and set an **administrator password** to access the virtual machines.

![Parameter review](screenshots/1.4.png)

During deployment validation, an error appeared in my case due to **subscription limitations**, since the **IIS Custom Script Extension** was not allowed in the region used.
For this reason, automatic IIS installation was removed from the template.

![Deployment error](screenshots/1.5.png)

Once IIS installation was removed, validation completed successfully.

![Validation successful](screenshots/1.6.png)

Later, we manually installed the IIS service from **Cloud Shell (PowerShell)** using a custom script.

![IIS installation](screenshots/2.11.png)
![IIS installed](screenshots/2.12.png)

---

## Task 2 – Configure an Azure Load Balancer

Create an **Azure Load Balancer** from the Azure Portal, selecting the resource group, **Standard SKU**, **Public**, and **Regional scope**.

![Create Load Balancer](screenshots/2.1.png)

Add a **public IP** for the Load Balancer frontend called **az104-fe**, and the public IP **az104-lbpip**.

![Frontend IP](screenshots/2.2.png)

Verify that the frontend is correctly configured with its public IP.

![Frontend configured](screenshots/2.3.png)

Next, create the **backend pool** called **az104-be**, selecting the **VNet created in Task 1** and adding the previously deployed virtual machines.

![Backend pool](screenshots/2.4.png)
![Backend details](screenshots/2.5.png)
![VMs in backend](screenshots/2.6.png)

Review the configuration and create the Load Balancer.

![Create Load Balancer](screenshots/2.7.png)

Once created, access the resource and add a **load balancing rule**.

![Add rule](screenshots/2.8.png)

Configure the rule **az104-lbrule**, using **TCP**, port **80**, the configured frontend and backend, and a **health probe** called **az104-hp** with a 5-second interval.
Disable session persistence.

![Load balancing rule](screenshots/2.9.png)

Configure **outbound rules (SNAT)** to allow backend members to access the Internet.

![Outbound rules](screenshots/2.10.png)

When accessing the public IP from a browser, the webpage displays:

`Hello World from <VM name>`

![Hello World](screenshots/2.13.png)

By sending multiple requests, you can see the response alternates between both VMs, confirming that load balancing works correctly.

![Load Balancer test](screenshots/2.14.png)

---

## Task 3 – Configure an Azure Application Gateway

Go to **Virtual Networks** and review the VNet created earlier along with its existing subnets.

![Existing subnets](screenshots/3.1.png)

Add a new subnet called **subnet-appgw** with the range **10.60.3.224/27**.

![Create AppGW subnet](screenshots/3.2.png)

Create an **Application Gateway** named **az104-appgw**, with **Standard V2 SKU**, **2 instances**, no HTTP/2, IPv4, and select the VNet and subnet **subnet-appgw**.

![Create Application Gateway](screenshots/3.4.png)

Configure a **standard static public IP** for the frontend.

![Frontend AppGW](screenshots/3.5.png)

Add both VMs to the main backend pool **az104-appgwbe**.

![Main backend](screenshots/3.6.png)

Create a backend pool specifically for **videos** called **az104-videobe**, assigning **VM1**.

![Video backend](screenshots/3.7.png)

Create another backend pool for **images**, assigning **VM0**.

![Image backend](screenshots/3.8.png)

Configure the rule **az104-gwrule** and the listener **az104-listener**, listening on **HTTP port 80**.

![Listener](screenshots/3.9.png)

Configure the backend HTTP settings called **az104-http**.

![HTTP settings](screenshots/3.10.png)

At the bottom, add route-based rules:

![Route rules](screenshots/3.11.png)

* `/image/*` → **az104-imagebe**

![Image route](screenshots/3.12.png)

* `/video/*` → **az104-videobe**

![Video route](screenshots/3.13.png)

Verify the rules are correctly configured.

![Rules summary](screenshots/3.14.png)

Review the entire Application Gateway configuration and proceed to create it.

![Final review](screenshots/3.15.png)
![Create AppGW](screenshots/3.16.png)

Once deployed, in **Monitoring → Backend Health**, verify all backends respond with status **200**.

![Backend health](screenshots/3.17.png)

Accessing:

* `http://<public-IP>/image` → displays **VM0**

![Test images](screenshots/3.19.png)

* `http://<public-IP>/video` → displays **VM1**
  
![Test videos](screenshots/3.20.png)

This confirms that the **Application Gateway works correctly with route-based routing**.

---

## Resource Cleanup

To avoid unnecessary costs, delete all resources created during the lab by removing the **resource group**.

![Delete resources](screenshots/3.21.png)

Or using PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg6
```
