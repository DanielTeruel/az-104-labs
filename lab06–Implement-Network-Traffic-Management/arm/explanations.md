## ARM Template Explanation

This ARM template deploys a **simple environment of 2 virtual machines (VMs) in Azure** with the minimal configuration needed for a lab, without installing IIS, and with the goal of **reducing costs** by using only 2 VMs.

### 1. Parameters

Parameters make the template **flexible**. They define names, passwords, and network resources.

* **virtualMachines_az104_06_vm0_name / vm1_name**: Names of the two VMs.
* **virtualNetworks_az104_06_vnet1_name**: Name of the virtual network (VNet).
* **networkInterfaces_az104_06_nic0_name / nic1_name**: Names of the network interfaces associated with each VM.
* **networkSecurityGroups_az104_06_nsg1_name**: Name of the network security group (NSG).
* **adminPassword**: Secure password for the VM administrators.

> Parameters allow the template to be reused without modifying it directly.

---

### 2. Resources

The template creates several resources **in a logical order**, using dependencies (`dependsOn`) so that each resource is created correctly:

#### a) Network Security Group (NSG)

* Type: `Microsoft.Network/networkSecurityGroups`
* Defines security rules:

  * Allows RDP (port 3389) to access Windows.
  * Allows HTTP (port 80) in case it is needed later.
* Each rule has a defined priority and direction (Inbound/Outbound).
