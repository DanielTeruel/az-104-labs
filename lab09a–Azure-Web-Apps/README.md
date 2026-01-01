# Lab 09a – Implement Web Apps (AZ-104)

Notes and documentation for Lab 09a of AZ-104.

## Overview
In this lab, I worked with **Azure Web Apps** to create, configure, and manage web applications using **deployment slots**.  
The lab covers creating an **App Service**, configuring staging and production slots, deploying code automatically from a **GitHub repository**, swapping slots, and testing **autoscaling** using **Azure Load Testing**.

The objective was to explore how Azure Web Apps can be deployed, tested, and automatically scaled in a real-world scenario while ensuring high availability and efficient performance.

## Business Scenario
The organization wants to migrate its websites from an **on-premises datacenter** to Azure to reduce hardware costs and improve scalability.  
The websites are currently running on Windows servers with PHP, and the hardware is nearing end-of-life.

A solution was implemented that allows:

- Testing updates in a **staging slot** before moving them to production  
- Deploying code automatically from a **GitHub repository**  
- Automatically scaling the App Service based on demand using **autoscaling** and load testing

## What I Learned
- How to create and configure an **Azure App Service** for PHP web applications  
- How to create and manage **deployment slots** for staging and production  
- How to configure **automatic deployment** from an external GitHub repository  
- How to perform a **swap** between staging and production slots  
- How to configure and test **autoscaling** of the App Service based on metrics  
- How to use **Azure Load Testing** to validate web application performance under load  
- How to monitor client and server metrics to ensure correct autoscaling behavior

## Full Documentation
For step-by-step instructions with screenshots in **English**, see [Lab09a Full Documentation](Lab09a_Full_Documentation.md)  
For the **Spanish version**, see [Lab09a Documentación Completa](Lab09a_Documentacion_Completa.md)
