# Lab 02b – Manage Governance with Azure Policy (AZ-104)

## Summary
In this lab, I focused on implementing governance in Azure using **Azure Policy**, **resource tagging**, and **resource locks**.  
The goal was to enforce organizational standards, improve cost tracking, and protect critical resources from accidental modification or deletion.

---

## Business Scenario (Roleplay)

In my scenario, the organization has significantly expanded its Azure footprint over the past year.  
During a recent internal audit, multiple resources were found without key information such as owner, project, or cost center.

To improve governance and operational control, the organization decided to:
- Standardize the use of resource tags
- Enforce tagging through Azure Policy
- Automatically remediate non-compliant resources
- Protect critical resources using locks

These actions align with the **Microsoft Well-Architected Framework** and the **Cloud Adoption Framework**.

---

## Lab Objectives

- Create and apply tags to resource groups
- Enforce tagging using Azure Policy
- Automatically inherit tags from the resource group
- Remediate compliance issues using policy remediation
- Configure and validate resource locks
- Clean up resources after completion

---

## Task 1 – Create the resource group and assign tags

First, I created the resource group where the entire lab would be executed.

- **Resource group name:** `az104-rg2`
- **Region:** East US

![Create resource group](screenshots/1.1.png)

After the resource group was created, I navigated to the **Tags** section and added the following tag:

- **Name:** Cost Center  
- **Value:** 000  

![Add Cost Center tag](screenshots/1.2.png)

---

## Task 2 – Enforce tagging using Azure Policy

From the Azure portal, I navigated to **Policy**, and under **Definitions**, searched for the built-in policy:

**Require a tag and its value on resources**

![Policy definition search](screenshots/2.1.png)

I assigned the policy and configured the **scope** to target the `az104-rg2` resource group.

![Select policy scope](screenshots/2.2.png)

Next, I configured the policy assignment identification details and ensured policy enforcement was enabled.

![Policy assignment details](screenshots/2.3.png)

Before creating the assignment, I reviewed the configuration to confirm it was correct.

![Review policy assignment](screenshots/2.4.png)

Once the policy was created, I navigated to **Compliance** to verify that the policy was active and correctly applied.

![Policy compliance status](screenshots/2.5.png)

To validate enforcement, I attempted to create a **Storage Account** inside the resource group.

![Create storage account attempt](screenshots/2.6.png)

The deployment failed because the required tag was missing, confirming that the policy was working as expected.

![Policy validation failed](screenshots/2.7.png)  
![Policy error details](screenshots/2.8.png)

I then proceeded to **delete the policy assignment**.

![Delete policy assignment](screenshots/2.9.png)  
![Policy removed](screenshots/2.10.png)

After removing the policy, I retried creating the storage account and was able to deploy it successfully, as the restriction was no longer in place.

---

## Task 3 – Inherit tags automatically from the resource group

Next, I went back to **Policy → Assignments → Assign policy** and selected the definition:

**Inherit a tag from the resource group if missing**

I configured the scope to the `az104-rg2` resource group, provided a name and description, and enabled enforcement.

![Assign inherit tag policy](screenshots/3.1.png)

In the **Remediation** section, I enabled **Create a remediation task** and selected the corresponding policy.  
I also configured the tag name parameter correctly.

![Enable remediation task](screenshots/3.2.png)

Before creating the assignment, I reviewed the configuration.

![Review remediation settings](screenshots/3.3.png)

Next, I created a new storage account within the resource group.

![Create storage account](screenshots/3.4.png)

At this point, I identified an issue:  
The resource group tag was defined as **“Centro de costes”**, while the policy assignment used **“Cost Center”**.  
Due to this mismatch, the policy did not apply correctly.

![Tag mismatch issue](screenshots/3.5fallo.png)

To fix the issue, I added the **Cost Center** tag to the resource group so it matched the policy assignment exactly.

![Fix tag name](screenshots/3.6fix.png)

After repeating the process with the corrected configuration, the policy successfully inherited the tag and applied it automatically to the resource.

![Policy applied successfully](screenshots/3.7.png)

---

## Task 4 – Configure and test resource locks

Finally, I navigated to the resource group and opened **Settings → Locks**.  
I created a lock with the following configuration:

- **Name:** rg-lock  
- **Lock type:** Delete  

![Create delete lock](screenshots/4.1.png)

I then attempted to delete the storage account inside the resource group.

![Attempt to delete resource](screenshots/4.2.png)

The deletion was blocked successfully, confirming that the lock was applied correctly and the resource was protected.

![Deletion blocked](screenshots/4.4.png)

---

## Cleanup

To avoid unnecessary costs and keep the environment clean, I deleted all resources created during the lab.  
I first removed the lock and then deleted the resource group, ensuring that no resources or charges remained.

---
