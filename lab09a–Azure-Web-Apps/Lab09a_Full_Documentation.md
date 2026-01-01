# Lab 09a - Web Apps Implementation (AZ-104)

## Introduction

In this lab, we explore the deployment and management of **Azure Web Apps**, including the creation of **deployment slots**, continuous deployment from **GitHub**, and configuration of **auto-scaling**.  
Key concepts covered include: App Service Plan, deployment slots, slot swapping, metric-based auto-scaling, and load testing with **Azure Load Testing**.

---

## Business Scenario

The organization wants to migrate its websites from an **on-premises data center** to Azure, avoiding hardware costs while gaining scalability and availability.  
Currently, the sites are running on Windows servers with PHP, and the hardware is nearing end-of-life.  

A flexible cloud environment is required to test updates in a **staging** slot before deploying to **production**, and to **automatically scale** during demand spikes.

---

## Lab Objectives

- Create and configure an **App Service** in Azure to run web applications in PHP  
- Configure **deployment slots** for staging and production, allowing pre-deployment testing  
- Implement continuous deployment from an **external GitHub repository**  
- Perform a **swap** between staging and production slots  
- Configure and test **App Service auto-scaling** based on demand  
- Validate application functionality using **Azure Load Testing**  
- Clean up resources at the end of the lab

---

## Task 1 – Create the App Service and initial configuration

As usual, I start by creating a **Resource Group** to contain all necessary services.

![Resource Group creation](screenshots/1.1.png)

### Create the App Service

From the Azure portal main page, I searched for **App Services** and proceeded to create a new one.  
I configured the App Service with the following parameters:

- Unique name: `az104lab09`  
- Publish: **Code**  
- Runtime stack: **PHP 8.2**  
- Operating system: **Linux**  
- Initial pricing plan: **Basic B1**

![App Service configuration](screenshots/1.2.png)

After reviewing the configuration, I validated the settings and launched the web application creation.

![App Service created successfully](screenshots/1.3.png)

---

## Task 2 – Deployment Slots Configuration

Once the deployment was complete, I verified that the App Service was created correctly and operational.

![Error adding slot on B1](screenshots/1.4.png)

I attempted to add a **new deployment slot**, but the **B1 plan** does not allow this functionality.  

### Update App Service Plan

Therefore, I upgraded the **App Service Plan** to **Premium V3 – P0V3**, which enables the creation of deployment slots.

![Add staging slot](screenshots/1.5.png)

### Create the staging slot

Once the SKU was updated, I accessed:

`App Service → Deployment → Deployment Slots`

I added a new slot with the following characteristics:

- Slot name: **staging**  
- Clone configuration: **Do not clone configuration**

![Verify slots](screenshots/2.1.png)

After creation, I verified that I now had **two deployment slots**:

- Production  
- Staging

![Confirm slots created](screenshots/2.2.png)

---

## Task 3 – Deploy code from GitHub

### Configure Deployment Center

With the slots created, the next step was to configure **automatic code deployment**.

![Deployment Center configuration](screenshots/3.1.png)

Inside the **staging** slot, I accessed the **Deployment Center** and configured the following settings:

- Source: **External Git**  
- Repository: `https://github.com/Azure-Samples/php-docs-hello-world`  
- Branch: **master**

I saved the changes to start automatic deployment.

![Hello World verification in staging](screenshots/3.2.png)

---

## Task 4 – Slot Swap

Once deployment was complete, I selected the **default URL for the staging slot**.  
The page correctly displayed the **Hello World** message, confirming that the code had been successfully deployed.

![Swap between staging and production](screenshots/3.3.png)

After validating functionality in staging, I returned to **Deployment Slots** and selected the **Swap** option.

I configured the swap with default values:

- Source: **staging**  
- Target: **production**

I initiated the swap process to move the validated code to production.

![Confirmation in production](screenshots/4.1.png)

Once the swap was complete, I accessed the **default App Service URL in production**.  
The content that was previously visible in staging was now visible in production.

![Auto-scaling configuration](screenshots/4.2.png)

---

## Task 5 – Auto-scaling Configuration and Load Testing

With the application in production, I went to **App Service Plan → Scale Out** and configured **auto-scaling**, setting a **maximum burst of 2 instances**.

![Create Load Test](screenshots/5.1.png)

### Load Testing with Azure Load Testing

I searched for **Azure Load Testing** in the Azure portal.

![New load test resource](screenshots/5.2.png)

I created a new load test resource.

![URL-based test configuration](screenshots/5.3.png)

I configured a **URL-based test**, reviewed the settings, and launched creation.

![Add HTTP request to test](screenshots/5.4.png)

Inside the test plan, I added an **HTTP request** pointing to the **production App Service URL**.

![Run load test](screenshots/5.5.png)

I saved the configuration and ran the test.

![Client-side metrics](screenshots/5.6.png)

During execution, I observed **client-side metrics**, such as virtual users, latency, and request count.

![Server-side metrics](screenshots/5.7.png)

I also reviewed **server-side metrics**, validating the App Service behavior under load.

![Metrics and auto-scaling summary](screenshots/5.8.png)

These metrics confirmed that **auto-scaling** worked correctly under increased demand.

---

## Resource Cleanup

To avoid unnecessary costs, I deleted the **Resource Group**, which removed all resources associated with the lab.

### Azure Portal

- Navigate to **Resource Groups** → Select the lab's resource group → Click **Delete Resource Group**  
- Confirm deletion

![Resource Group deletion](screenshots/5.9.png)

### Azure PowerShell

```powershell
Remove-AzResourceGroup -Name az104-rg9
