## ARM Template Explanation

This ARM template deploys a **simple environment with 2 virtual machines (VMs) in Azure** with the minimum configuration required for a lab. IIS is **not installed** to **reduce costs** and keep the deployment simple using only 2 VMs.

The main goal of this template is to **provide a base environment ready to test Azure Load Balancer and Application Gateway**.

---

### 1. Parameters

Parameters make the template **flexible and reusable**, without modifying the code. They define resource names, passwords, and network configurations.

* **virtualMachines_az104_06_vm0_name / vm1_name**: Names of the two virtual machines.
* **virtualNetworks_az104_06_vnet1_name**: Name of the virtual network (VNet).
* **networkInterfaces_az104_06_nic0_name / nic1_name**: Names of the network interfaces associated with each VM.
* **networkSecurityGroups_az104_06_nsg1_name**: Name of the network security group (NSG).
* **adminPassword**: Secure password for VM administrators.

> Using parameters allows deploying the template in different resource groups or regions without modifying the main file.

---

### 2. Resources

The template creates resources **in a logical order**, using `dependsOn` dependencies to ensure that each resource is created before others that rely on it.

#### a) Network Security Group (NSG)

* **Type**: `Microsoft.Network/networkSecurityGroups`
* Contains basic security rules:

  * **RDP (port 3389)**: Allows remote access to Windows VMs.
  * **HTTP (port 80)**: Allowed in case web testing is needed.
* Each rule has a defined priority and direction (`Inbound` or `Outbound`) to control traffic flow.

#### b) Virtual Network (VNet)

* **Type**: `Microsoft.Network/virtualNetworks`
* Creates **a VNet with address space 10.60.0.0/22**, divided into **2 subnets**:

  * **subnet0**: For the first VM
  * **subnet1**: For the second VM
* Allows enabling private endpoint and network policies as needed.
* DDoS protection is **disabled** to simplify the lab and reduce costs.

#### c) Network Interfaces (NICs)

* **Type**: `Microsoft.Network/networkInterfaces`
* Creates **2 NICs**, one for each VM, connected to the respective subnets.
* Each NIC is associated with the previously created NSG, ensuring security rules are automatically applied to the VM.

#### d) Virtual Machines (VMs)

* **Type**: `Microsoft.Compute/virtualMachines`
* Creates **2 Windows Server 2019 virtual machines**.
* Each VM includes:

  * **HardwareProfile**: Size `Standard_D2s_v3`
  * **StorageProfile**: OS disk of 127 GB from Windows Server 2019 Datacenter image
  * **OSProfile**: Admin user (`localadmin`) and Azure VM agent
  * **NetworkProfile**: Connection to the corresponding NIC

> These VMs represent the servers used in the lab for traffic balancing and routing tests.

---

### 3. Deployment Flow

1. **NSG is created** with basic security rules.
2. **VNet is created** with its subnets.
3. **NICs are created**, associated with subnets and NSG.
4. **VMs are created**, connected to their respective NICs.

> Dependencies ensure that Azure deploys each resource in the correct order, avoiding creation errors.

---

### 4. Additional Notes

* The template **does not install IIS or applications**, to reduce complexity and costs.
* It can be **easily modified** to add more VMs, subnets, or NSG rules if needed.
* It provides a **minimal lab environment** sufficient to test **Load Balancer and Application Gateway** with HTTP traffic.
