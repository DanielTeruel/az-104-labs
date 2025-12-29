Perfecto 😎, voy a tomar **tus comentarios numerados con las capturas** y toda la información del Lab 03 que me diste y crear **un README.md completo, listo para copiar en GitHub**, con la estructura profesional que usamos antes, incluyendo **Overview, Business Scenario, What I Learned**, y las tareas numeradas con sus capturas de `screenshots/`.

Aquí tienes todo en un solo bloque:

````markdown
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

### Task 3 – Deploy a template using Azure Cloud Shell and PowerShell
1. **Set up Cloud Shell storage** – create a new storage account before accessing PowerShell.  
   ![3.1](screenshots/3.1.png)

2. **Upload template files** – in PowerShell Cloud Shell, switch to Classic view and upload `template.json` and `parameters.json`.  
   ![3.2](screenshots/3.2.png)

3. **Edit template** – update the default value of `disk_name` in lines 5–6.  
   ![3.3](screenshots/3.3.png)

4. **Edit parameters file** – update line 5 to match the template.  
   ![3.5](screenshots/3.5.png)

5. **Deploy template** using:
   ```powershell
   New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json
````

![3.6](screenshots/3.6.png)

6. **Verify disk creation**:

   ```powershell
   Get-AzDisk | ft
   ```

   ![3.7](screenshots/3.7.png)

7. **Switch to Bash** in Cloud Shell and edit `template.json` and `parameters.json`, changing the disk name to `"disk4"`.
   ![3.8](screenshots/3.8.png)
   ![3.9](screenshots/3.9.png)

---

### Task 4 – Deploy the template using Azure CLI (Bash)

1. Deploy the template:

   ```bash
   az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json
   ```

   ![4.1](screenshots/4.1.png)

2. Verify the deployment:

   ```bash
   az disk list --resource-group az104-rg3 --output table
   ```

   ![4.2](screenshots/4.2.png)
   ![4.3](screenshots/4.3.png)
   ![4.4](screenshots/4.4.png)

---

### Task 5 – Deploy a managed disk using Bicep

1. Upload `azuredeploydisk.bicep` to Cloud Shell and edit:

   * `managedDiskName` → `"az104-disk5"` (line 2)
   * `diskSizeInGiB` → `32` (line 7)
   * `sku name` → `StandardSSD_LRS` (line 26)
     ![5.1](screenshots/5.1.png)

2. Deploy the Bicep template:

   ```bash
   az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep
   ```

   ![5.2](screenshots/5.2.png)

3. Verify the disk creation:

   ```bash
   az disk list --resource-group az104-rg3 --output table
   ```

   ![5.3](screenshots/5.3.png)
   ![5.4](screenshots/5.4.png)
   ![5.5](screenshots/5.5.png)

---

## Cleanup

Delete the resource group to remove all lab resources and avoid unnecessary costs:

```powershell
# Azure PowerShell
Remove-AzResourceGroup -Name az104-rg3

# Azure CLI
az group delete --name az104-rg3
```

```

---

Si quieres, puedo hacer **la versión en español del README**, exactamente igual, para que tengas **README bilingüe listo para GitHub**, siguiendo la misma numeración de capturas.  

¿Quieres que haga esa versión ahora?
```
