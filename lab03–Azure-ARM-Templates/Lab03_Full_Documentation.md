# Lab 03 – Manage Azure Resources by using Azure Resource Manager Templates (AZ-104)

## Introduction
In this lab, we learned how to automate Azure resource deployments using Azure Resource Manager (ARM) templates and Bicep templates. The lab covers different ways to deploy templates through the Azure portal, Cloud Shell with PowerShell, and the Azure CLI.

## Overview
We focused on deploying managed disks in Azure, exploring:
- Creating and exporting ARM templates
- Editing templates and redeploying resources
- Using Azure Cloud Shell with PowerShell and Bash
- Deploying resources using Bicep templates
- Verifying deployments and resource configurations

This exercise provides hands-on experience with infrastructure-as-code (IaC) practices, improving deployment consistency and reducing human errors.

## Business Scenario
Your team wants to automate and simplify Azure resource deployments. By using templates, your organization can reduce administrative overhead, minimize human error, and increase consistency across deployments.

## What I Learned
- How to create and configure managed disks in the Azure portal
- How to export ARM templates from deployed resources
- How to edit templates and redeploy resources
- How to deploy templates using Cloud Shell (PowerShell and Bash)
- How to deploy resources using Bicep templates
- How to verify deployments and clean up resources

---

## Lab Tasks

### Task 1 – Create a managed disk and export an ARM template
1. **Create a Resource Group** – as always, start by creating the resource group.  
   ![1.1](screenshots/1.1.png)

2. **Create a managed disk** – go to **Disks** and create a new 32GB Standard HDD disk.  
   ![1.2](screenshots/1.2.png)

3. **Review and create** the disk.  
   ![1.3](screenshots/1.3.png)

4. **Export ARM template** – go to **Automation → Export template**, review it, and click **Download**.  
   ![1.4](screenshots/1.4.png)

5. **Template files** – extract the ZIP containing `template.json` and `parameters.json`.  
   ![1.5](screenshots/1.5.png)

---

### Task 2 – Deploy a new managed disk using a custom template
1. Open **Deploy a custom template** in the portal and upload the downloaded template.  
   ![2.1](screenshots/2.1.png)

2. Edit the template:
   - Change the default value of `disk_name` to `"az104-disk2"`
   - Update the parameter name to `"disk_name"`  
   ![2.2](screenshots/2.2.png)

3. Edit `parameters.json` to match the template parameter name.  
   ![2.3](screenshots/2.3.png)

4. Deploy the resource.  
   ![2.4](screenshots/2.4.png)

5. Verify the deployment in the Azure portal.  
   ![2.5](screenshots/2.5.png)

6. Check the deployment under **Resource Group → Deployments**.  
   ![2.6](screenshots/2.6.png)  
   ![2.7](screenshots/2.7.png)

---

### Task 3 – Deploy a template
