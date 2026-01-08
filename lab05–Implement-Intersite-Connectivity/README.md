# Lab 05 – Implement Intersite Connectivity (AZ-104)

Notes and documentation for Lab 05 of AZ-104.

## Overview

In this lab, I worked with **Azure Virtual Network connectivity** to enable communication between resources located in different virtual networks. I deployed virtual machines in separate VNets, tested connectivity, configured **VNet Peering**, and implemented a **User Defined Route (UDR)** to control traffic flow.

The objective was to understand how to connect segmented networks in Azure and how to control routing between subnets and virtual networks.

## Business Scenario

The organization separates core services from other departments, such as the manufacturing area.
However, in some scenarios, applications and services in both environments need to communicate securely.

To achieve this, the organization needs to:

* Enable secure connectivity between separate virtual networks.
* Validate connectivity between critical workloads.
* Control network traffic routing using custom routes.

## What I Learned

* How to deploy virtual machines in different virtual networks
* How to test network connectivity using Network Watcher
* How to configure **Virtual Network Peering (VNet Peering)**
* How to verify connectivity using PowerShell
* How to create and configure **User Defined Routes (UDR)**
* How to associate route tables to subnets to control traffic flow

## Full Documentation

For step-by-step instructions with screenshots, see [Lab05 Full Documentation](Lab05_Full_Documentation.md)      
For the Spanish version, see [Lab05 Documentación Completa](Lab05_Documentación_completa.md)
