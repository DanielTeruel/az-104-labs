# Lab 05 – Implement Connectivity Between Networks (AZ-104)

## Summary

In this lab, I worked with **connectivity between virtual networks in Azure** to allow communication between resources located in different VNets. During this practice:

- I created two virtual machines in two different virtual networks.
- I verified the initial connectivity using **Network Watcher**.
- I configured **Virtual Network Peering (VNet Peering)**.
- I verified connectivity again using **PowerShell**.
- I created a **custom route (UDR)** to control traffic routing.
- I associated the route table to a specific subnet.

This lab allowed me to understand how networks are interconnected in Azure and how traffic flow between subnets and VNets can be controlled.

## Business Scenario

The organization separates core services (Core Services) from other departments, such as the manufacturing area. However, in some scenarios it is necessary for both environments to communicate. To achieve this, secure connectivity between separate virtual networks must be configured and traffic routing must be controlled.

## Lab Objectives

- Create two virtual machines in different virtual networks.
- Verify connectivity between VNets using Network Watcher.
- Configure virtual network peering (VNet Peering).
- Verify connectivity using PowerShell.
- Create a custom route table (UDR).
- Associate the route to a specific subnet.
- Apply best practices for resource cleanup.

---

## Task 1 – Create CoreServicesVM and its Virtual Network

As usual, I started by creating the **resource group** where all the lab resources will be hosted.

![1.1](screenshots/1.1.png)

Next, I went to **Virtual Machines** and created a new VM with the following configuration:

Configuration | Value
--- | ---
Resource group | az104-rg5
Virtual machine name | CoreServicesVM
Region | France Central
Availability options | No infrastructure redundancy required
Security type | Standard
Image | Windows Server 2025 Datacenter - x64 Gen2
Size | Standard_DS2_v3
Username | localadmin
Password | ********
Public inbound ports | None

![1.2](screenshots/1.2.png)

During the creation, I also created the virtual network where this VM will be connected.

![1.3](screenshots/1.3.png)

I configured the virtual network with the following parameters:

Configuration | Value
--- | ---
Name | CoreServicesVnet
Address space | 10.0.0.0/16
Subnet | Core
Subnet range | 10.0.0.0/24

![1.4](screenshots/1.4.png)

After that, I disabled **boot diagnostics**.

![1.5](screenshots/1.5.png)

Finally, I reviewed the configuration and created the virtual machine.

![1.6](screenshots/1.6.png)

---

## Task 2 – Create ManufacturingVM in Another Virtual Network

Next, I created a second virtual machine in another different virtual network with the following configuration:

Configuration | Value
--- | ---
Resource group | az104-rg5
Virtual machine name | ManufacturingVM
Region | France Central
Security type | Standard
Availability options | No infrastructure redundancy required
Image | Windows Server 2025 Datacenter - x64 Gen2
Size | Standard_DS2_v3
Username | localadmin
Password | ********
Public inbound ports | None

![2.1](screenshots/2.1.png)

During the creation, I configured a new virtual network with the following parameters:

Configuration | Value
--- | ---
Name | ManufacturingVnet
Address space | 172.16.0.0/16
Subnet | Manufacturing
Subnet range | 172.16.0.0/24

![2.2](screenshots/2.2.png)

---

## Task 3 – Test Connectivity Using Network Watcher

Once both virtual machines were created, I went to **Network Watcher → Connection troubleshoot** and configured a test:

- Source: CoreServicesVM  
- Destination: ManufacturingVM  
- Protocol: TCP  
- Port: 3389  

![3.1](screenshots/3.1.png)

When running the diagnostic, the connectivity test result was **Unreachable**, since the VNets were not yet peered.

![3.2](screenshots/3.2.png)

---

## Task 4 – Configure VNet Peering

I went to **Virtual networks → CoreServicesVnet → Peerings** and added a new peering.

![4.2](screenshots/4.2.png)

I configured the peering between **CoreServicesVnet** and **ManufacturingVnet**, allowing:

- Access between both VNets
- Forwarded traffic (traffic that did not originate in this VNet)

![4.3](screenshots/4.3.png)

I applied the configuration both from CoreServices to Manufacturing and from Manufacturing to CoreServices.

![4.4](screenshots/4.4.png)

Once created, I verified that in both VNets the peering status appeared as **Connected**.

![4.5](screenshots/4.5.png)

![4.6](screenshots/4.6.png)

---

## Task 5 – Test Connectivity Using PowerShell

Now I tested connectivity from **ManufacturingVM** using **Run Command → RunPowerShellScript**.

![5.1](screenshots/5.1.png)

I ran the following command:

```powershell
Test-NetConnection 10.0.0.4 -Port 3389
````

![5.2](screenshots/5.2.png)

As you can see, the result is:

```
TcpTestSucceeded = True
```

Therefore, connectivity between both VNets is working correctly.

![5.3](screenshots/5.3.png)

---

## Task 6 – Create a Custom Route (UDR)

Now I went to **CoreServicesVnet → Subnets** and added a new subnet:

| Configuration | Value       |
| ------------- | ----------- |
| Name          | perimeter   |
| Range         | 10.0.1.0/24 |

![6.1](screenshots/6.1.png)

![6.2](screenshots/6.2.png)

After that, I went to **Route tables** and created a new one.

![6.3](screenshots/6.3.png)

I created it with the corresponding name in the resource group.

![6.4](screenshots/6.4.png)

Once created, I entered it and added a new route.

![6.5](screenshots/6.5.png)

The route was configured as follows:

| Configuration       | Value             |
| ------------------- | ----------------- |
| Route name          | PerimetertoCore   |
| Destination         | IP addresses      |
| Destination address | 10.0.0.0/16       |
| Next hop            | Virtual appliance |
| Next hop address    | 10.0.1.7          |

![6.6](screenshots/6.6.png)

Then I went to **Settings → Subnets** and associated the route table to the subnet.

![6.7](screenshots/6.7.png)

I selected the **CoreServicesVnet** network and the **Core** subnet.

![6.8](screenshots/6.8.png)

In this way, the Core subnet is now associated with the custom route.

![6.9](screenshots/6.9.png)

---

## Resource Cleanup

To avoid unnecessary costs, I deleted the resource group, which removes all the resources associated with the lab.

From the Azure portal:

```
Resource group → Delete resource group
```

Or using PowerShell:

```powershell
Remove-AzResourceGroup -Name az104-rg5
```

```
