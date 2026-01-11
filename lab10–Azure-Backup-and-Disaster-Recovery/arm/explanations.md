## ARM Template Explanation

This ARM template deploys a **lab environment with 1 virtual machine (VM) in Azure** with the minimum configuration necessary for connectivity and remote administration testing. The template includes a **VNet, subnet, NIC, public IP, and NSG**, so the VM is accessible via RDP.

The main goal of this template is to **provide a base environment ready for testing Azure infrastructure and VM management**, without installing additional applications to keep it simple and cost-effective.

---

### 1. Parameters

Parameters allow the template to be **flexible and reusable** without modifying the code. They define resource names, passwords, network configuration, and VM settings.

* **vmNamePrefix**: Prefix for the virtual machine name.
* **nicNamePrefix**: Prefix for the network interface associated with the VM.
* **pipNamePrefix**: Prefix for the public IP address.
* **virtualNetworkName**: Name of the virtual network (VNet).
* **addressPrefix**: Address range of the VNet.
* **subnet0Name / subnet0Prefix**: Name and range of the subnet where the VM will connect.
* **nsgName**: Name of the Network Security Group (NSG) controlling traffic.
* **adminUsername / adminPassword**: Administrator credentials for the VM.
* **imagePublisher / imageOffer / imageSKU**: OS image for the VM (Windows Server 2019 Datacenter in this case).
* **vmSize**: VM size (Standard_D2s_v3 by default).

> With parameters, the template can be deployed to different resource groups or regions without modifying the main template.

---

### 2. Resources

The template creates resources **in a logical order**, using dependencies (`dependsOn`) to ensure each resource is created before another that depends on it.

#### a) Network Security Group (NSG)

* **Type**: `Microsoft.Network/networkSecurityGroups`
* Contains a basic security rule:

  * **RDP (port 3389)**: Allows remote access to Windows from any IP address.

> The NSG is assigned to the VM’s NIC so security rules are applied automatically.

---

#### b) Virtual Network (VNet)

* **Type**: `Microsoft.Network/virtualNetworks`
* The template creates **a VNet with the 10.0.0.0/24 range**, divided into **one subnet**:

  * **subnet0**: For the deployed VM, with 10.0.0.0/26 range

> The VNet provides an isolated and controlled network space for the VM.

---

#### c) Public IP Address (PIP)

* **Type**: `Microsoft.Network/publicIpAddresses`
* Creates **a static public IP**, allowing the VM to be accessed from the Internet via RDP.

---

#### d) Network Interface (NIC)

* **Type**: `Microsoft.Network/networkInterfaces`
* Creates **one NIC**, connected to subnet `subnet0`, associated with the NSG and public IP.

> This ensures the VM has both internal and external connectivity while applying defined security rules.

---

#### e) Virtual Machine (VM)

* **Type**: `Microsoft.Compute/virtualMachines`
* Creates **a VM with Windows Server 2019 Datacenter**, configured with:

  * **HardwareProfile**: Size `Standard_D2s_v3`
  * **OSProfile**: Admin user (`localadmin`) and secure password
  * **StorageProfile**: Managed OS disk created from the Windows Server 2019 Datacenter image
  * **NetworkProfile**: Connection to the previously created NIC

> The VM represents the base server for the lab, ready for remote management and infrastructure testing.

---

### 3. Deployment Flow

1. **Create the VNet** with its defined subnet.
2. **Create the NSG** with RDP rules.
3. **Create the public IP** for the VM.
4. **Create the NIC**, associated with the subnet, NSG, and public IP.
5. **Create the VM**, connected to the NIC and ready for use.

> Thanks to the `dependsOn` dependencies, Azure ensures that each resource is deployed in the correct order, preventing creation errors.

---

### 4. Additional Notes

* The template **does not install any applications or services**, reducing cost and complexity.
* It can be **easily expanded** to add more VMs, subnets, NSG rules, or load balancers as needed.
* It is a **minimal test environment**, sufficient to **manage VMs, test connectivity, and configure basic network services** in Azure.
