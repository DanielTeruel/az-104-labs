# ARM Template Explained – Lab 11 (AZ-104)

This document provides a detailed explanation of the ARM template used in **Lab 11 – Implement Monitoring with Azure Monitor**.

The goal is to understand **what the template deploys**, **how it works**, and **why each resource is required**, following real-world Azure best practices.

---

## Overview

This ARM template deploys a complete test environment consisting of:

- A Windows Server virtual machine
- Networking components (VNet, subnet, NSG, public IP, NIC)
- A storage account used for boot diagnostics

The template is designed to support monitoring scenarios using **Azure Monitor and VM Insights**.

---

## Parameters

Parameters allow customization of the deployment without modifying the template code.

### Admin Credentials

- `adminUsername`  
  Defines the local administrator username for the virtual machine.

- `adminPassword`  
  Secure parameter used to store the administrator password.

### Virtual Machine Configuration

- `vmNamePrefix`  
  Prefix used to generate the virtual machine name.

- `vmSize`  
  Defines the size of the virtual machine (default: `Standard_D2s_v3`).

- `imagePublisher`, `imageOffer`, `imageSKU`  
  Define the Windows Server image used to deploy the VM.

---

## Networking Configuration

### Virtual Network and Subnet

The template creates a virtual network with the following configuration:

- Address space: `10.0.0.0/24`
- Subnet: `10.0.0.0/26`

This provides a basic but realistic network topology.

### Network Security Group (NSG)

An NSG is created with an inbound rule allowing:

- TCP traffic on port **3389 (RDP)**

This allows remote access to the virtual machine for administration and testing.

---

## Resource Loops (Copy)

The template uses the `copy` element to deploy resources dynamically:

- Network Interfaces
- Public IP Addresses
- Virtual Machines

Although only **one instance** is deployed, this design allows easy scaling by changing a single variable.

---

## Storage Account

A storage account is deployed to support:

- **Boot diagnostics** for the virtual machine

The storage account name is generated dynamically using:

- `uniqueString(subscription().subscriptionId)`

This guarantees global uniqueness.

---

## Virtual Machine Deployment

The virtual machine configuration includes:

- Windows Server 2019 Datacenter
- Managed OS disk
- Network interface association
- Boot diagnostics enabled

This configuration represents a typical production-ready VM setup.

---

## Dependencies

The template uses `dependsOn` to ensure correct deployment order, for example:

- The VM depends on the NIC and storage account
- The NIC depends on the VNet, NSG, and public IP

This prevents race conditions during deployment.

---

## Why This Template Is Used in Lab 11

This template provides a realistic environment to:

- Enable **VM Insights**
- Generate monitoring data
- Trigger alerts (such as VM deletion)
- Analyze logs and metrics using **KQL**

It serves as a foundation for all monitoring scenarios in this lab.

---
