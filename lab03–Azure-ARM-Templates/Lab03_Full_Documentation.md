
# Lab 03 – Manage Azure Resources by using Azure Resource Manager Templates (AZ-104)

## Introduction
In this lab, I learned how to automate Azure resource deployments using Azure Resource Manager (ARM) templates and Bicep templates. I explored different ways to deploy templates through the Azure portal, Cloud Shell with PowerShell, and the Azure CLI.

## Overview
The lab focused on deploying managed disks in Azure. I covered:
- Creating and exporting ARM templates
- Editing templates and redeploying resources
- Using Azure Cloud Shell with PowerShell and Bash
- Deploying resources using Bicep templates
- Verifying deployments and resource configurations

This exercise gave me hands-on experience with infrastructure-as-code (IaC) practices, improving deployment consistency and reducing human errors.

## Business Scenario
I wanted to explore how to automate and simplify Azure resource deployments. Using templates helped me reduce administrative overhead, minimize human error, and maintain consistency across deployments.

## What I Learned
- How to create and configure managed disks in the Azure portal
- How to export ARM templates from existing resources
- How to edit templates and redeploy resources
- How to deploy templates using Cloud Shell (PowerShell and Bash)
- How to deploy resources using Bicep templates
- How to verify deployments and clean up resources

---

## Lab Tasks

## Task 1 – Create a managed disk and export an ARM template
I started by creating the resource group for the lab.  

![1.1](screenshots/1.1.png)

I went to **Disks** and created a new 32GB Standard HDD disk.  

![1.2](screenshots/1.2.png)

I reviewed the configuration and created the disk.  

![1.3](screenshots/1.3.png)

After the deployment, I went to **Automation → Export template**, reviewed the ARM template, and downloaded the ZIP.  

![1.4](screenshots/1.4.png)

I extracted the ZIP and found `template.json` and `parameters.json` to use in the next deployment.  

![1.5](screenshots/1.5.png)

---

## Task 2 – Deploy a new managed disk using a custom template
I opened **Deploy a custom template** in the Azure portal and uploaded the downloaded template.  

![2.1](screenshots/2.1.png)

I edited the template:
- Changed the default value of `disk_name` to `"az104-disk2"`
- Updated the parameter name to `"disk_name"`  

![2.2](screenshots/2.2.png)

I also edited `parameters.json` to match the template parameter.  

![2.3](screenshots/2.3.png)

I deployed the new disk resource.  

![2.4](screenshots/2.4.png)

I verified that the disk appeared correctly in the Azure portal.  

![2.5](screenshots/2.5.png)

I checked the deployment details under **Resource Group → Deployments**.  

![2.6](screenshots/2.6.png)

![2.7](screenshots/2.7.png)

---

## Task 3 – Deploy a template using Azure Cloud Shell and PowerShell
Before opening PowerShell, I created a new storage account in Cloud Shell to store the template files I would need later.  

![3.1](screenshots/3.1.png)

I uploaded `template.json` and `parameters.json` to Cloud Shell, switched to Classic view, and made sure the files were visible in the file explorer.  

![3.2](screenshots/3.2.png)

I edited the template and updated the default value of `disk_name` in lines 5–6.  

![3.3](screenshots/3.3.png)

I also edited `parameters.json` line 5 to match the template.  

![3.5](screenshots/3.5.png)

I deployed the template using PowerShell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json
````

I confirmed that the disk was created successfully.

![3.6](screenshots/3.6.png)

I verified it using:

```powershell
Get-AzDisk | ft
```

![3.7](screenshots/3.7.png)

Then, I switched to Bash in Cloud Shell, edited `template.json` and `parameters.json`, and changed the disk name to `"disk4"`.

![3.8](screenshots/3.8.png)

![3.9](screenshots/3.9.png)

---

## Task 4 – Deploy the template using Azure CLI (Bash)

I deployed the template using:

```bash
az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json
```

![4.1](screenshots/4.1.png)

I listed the disks to verify the deployment:

```bash
az disk list --resource-group az104-rg3 --output table
```

![4.2](screenshots/4.2.png)

![4.3](screenshots/4.3.png)

![4.4](screenshots/4.4.png)

---

## Task 5 – Deploy a managed disk using Bicep

I uploaded `azuredeploydisk.bicep` to Cloud Shell and edited the parameters:

* `managedDiskName` → `"az104-disk5"` (line 2)
* `diskSizeInGiB` → `32` (line 7)
* `sku name` → `StandardSSD_LRS` (line 26)

![5.1](screenshots/5.1.png)

I deployed the Bicep template using:

```bash
az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep
```

![5.2](screenshots/5.2.png)

I listed the disks to confirm that the deployment was successful:

```bash
az disk list --resource-group az104-rg3 --output table
```

![5.3](screenshots/5.3.png)

![5.4](screenshots/5.4.png)

![5.5](screenshots/5.5.png)

---

## Cleanup

To avoid unnecessary costs, I deleted the resource group, which removed all associated resources:

```powershell
# Azure PowerShell
Remove-AzResourceGroup -Name az104-rg3
```

# Azure CLI
az group delete --name az104-rg3
```
