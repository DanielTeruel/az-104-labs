# Lab 06 – Implement Network Traffic Management (AZ-104)

Notes and documentation for Lab 06 of AZ-104.

## Overview

In this lab, I explored **network traffic management in Azure**, implementing and testing two key solutions:
**Azure Load Balancer (Layer 4)** for basic traffic distribution and **Azure Application Gateway (Layer 7)** for advanced application-level routing.

The lab included deploying a base infrastructure using **ARM templates**, balancing HTTP traffic between multiple virtual machines, and configuring **route-based routing rules** to serve different types of content from specific servers.

## Business Scenario

The organization has a public website receiving traffic from the Internet.
Incoming requests need to be distributed across multiple virtual machines to improve **availability, resilience, and scalability**.

Additionally, the organization needs to serve different types of content:

* **Images** from a specific server
* **Videos** from another server

A Load Balancer is used for basic traffic distribution, and an Application Gateway is deployed for route-based content delivery.

## What I Learned

* How to deploy a base infrastructure using ARM templates
* How to create and configure an **Azure Load Balancer** with frontends, backend pools, and health probes
* How to configure an **Azure Application Gateway** with listeners, backend pools, HTTP settings, and route-based rules
* How to verify backend health and test content routing
* How to troubleshoot deployment issues and manually configure services like IIS when needed
* How to clean up resources efficiently to avoid unnecessary costs

## Full Documentation

For step-by-step instructions with screenshots, see [Lab06 Full Documentation](Lab06_Full_Documentation.md)
For the Spanish version, see [Lab06 Documentación Completa](Lab06_Documentacion_completa.md)
