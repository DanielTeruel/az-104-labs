# Lab 02a – Manage Subscriptions and RBAC (AZ-104)

## Overview
In this lab, I focused on managing Azure subscriptions and implementing Role-Based Access Control (RBAC) using Management Groups.  
The goal was to apply the principle of least privilege while centralizing access management at scale.

---

## Business Scenario (Roleplay)

In my scenario, the organization is growing and managing multiple Azure subscriptions.  
To simplify governance and access control, I organized subscriptions using Management Groups.

A centralized Help Desk team is responsible for:
- Managing virtual machines
- Creating and managing Azure support requests

However, this team must not:
- Register new Azure resource providers
- Modify resources beyond their responsibilities

To achieve this, I implemented a combination of built-in and custom RBAC roles at the Management Group level.

---

## Lab Objectives

- Create and configure Management Groups
- Assign built-in RBAC roles
- Create a custom RBAC role
- Review role assignments using Activity Log
- Clean up resources after completion

---

## Task 1 – Create and configure a Management Group

I navigated to **Management Groups** in the Azure Portal (using the central search bar).

![Create Management Group](screenshots/1.1.png)  

I created a new Management Group with the following configuration:
- **Management Group ID:** `az104-mg1`
- **Display Name:** `az104-mg1`

![Management Group creation in progress](screenshots/1.2.png)

After a few moments, I verified that the Management Group appeared in the hierarchy.

![Management Group visible in hierarchy](screenshots/1.3.png)

---

## Task 2 – Assign a built-in RBAC role

Inside the Management Group `az104-mg1`, I navigated to **Access Control (IAM)** and selected **Add → Add role assignment**.

![Add role assignment](screenshots/2.1.png)  

I assigned the **Virtual Machine Contributor** role to the Help Desk group.

![Select VM Contributor role](screenshots/2.2.png)  

This role allowed the team to manage virtual machines without granting access to the operating system or related networking and storage resources.

![Select Help Desk group](screenshots/2.3.png)  

I verified the assignment under **Access Control (IAM) → Role assignments**.

![Role assignment completed](screenshots/2.4.png)

---

## Task 3 – Create a custom RBAC role

From **Access Control (IAM)**, I selected **Add → Custom role**.

![Custom role creation start](screenshots/3.1.png)  

I configured the role with the following:
- **Role name:** `Custom Support Request`
- **Description:** A custom contributor role for support requests
- **Baseline permissions:** Clone role
- **Cloned role:** Support Request Contributor

![Select role to clone](screenshots/3.2.png)  

I excluded the permission:
- **Other: Register Support Resource Provider**

![Enter role details](screenshots/3.3.png)  

This permission was added as a **NotAction**, preventing the Help Desk team from registering new Azure resource providers.

![Exclude permissions step 1](screenshots/3.4.png)  

I confirmed that the assignable scope was set to the Management Group `az104-mg1` and reviewed the generated JSON before creating the role.

![Exclude permissions step 2](screenshots/3.5.png)  
![Review role JSON](screenshots/3.6.png)  

Finally, I confirmed that the custom role was created successfully.

![Custom role created](screenshots/3.7.png)

---

## Task 4 – Review role changes using Activity Log

I navigated to the **Activity Log** of the Management Group `az104-mg1` to review:
- Role assignments
- Creation of the custom RBAC role
- Other administrative changes performed during the lab

![Activity Log overview](screenshots/4.1.png)  
![Filter activity log](screenshots/4.2.png)  

---

## Cleanup

To avoid unnecessary costs and configuration leftovers, I deleted the Management Group once the lab was completed.

### Azure Portal
Management Group → Overview → Delete

![Filter activity log](screenshots/4.2.png)  

### Azure PowerShell
Alternatively, the Management Group can also be deleted using Azure PowerShell.
```powershell
Remove-AzManagementGroup -GroupName az104-mg1
```
Once the cleanup is completed, I navigated to the Management Groups panel to verify that the group had been successfully deleted.

![Custom role creation activity](screenshots/4.4.png)
