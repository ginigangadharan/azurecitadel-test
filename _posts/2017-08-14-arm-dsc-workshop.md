---
layout: article
title: Azure Arm and DSC Lab Guide
date: 2017-08-14
categories: workshops
author: Adam_Bohle
image:
  feature: Azure101.jpg
  teaser: Education.jpg
  thumb: 
comments: true
---


{% include toc.html %}

## Overview

In this workshop session we are going to construct an ARM template using Visual Studio, this ARM template will build out a simple network topology consisting of a single VNET with two subnets "FrontEndNet" and "DataBaseNet", we will add two Virtual Machines to this configuration to represent the front end Web Server and the backend Database VM. Building on from that we will make use of PowerShell Desired State Configuration (DSC) to set the Windows Guest OS and Application components up to handle the application which we want to run. This will be a simple web store applicaton for demonstraton purposes

Our Goal with this lab exercise is to show you how you can make use of ARM templates to automate the provisioning of your infrastructure and applications. With this knowledge you should feel more empowered to start using tools such as ARM Templates and DSC to enable Infrastructure as Code (IaC) in your projects. Lets get started.

## Pre-Requirements

We are going to need a few things up and running on our workstation in order to complete this lab

* An Azure Subscription
* A laptop or development workstation with the following software installed
    * Visual Studio 2017 Community or Enterprise Edition [Visual Studio Downloads](https://www.visualstudio.com/downloads/)
    * The latest Azure PowerShell cmdlets [Instructions regarding installing Azure PowerShell cmdlets](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.2.0)

**Please Ensure that you reboot your qorkstation after installing the SDK and the PowerShell cmdlets**

### Exercise 1

#### Configure the Automation Account

In order to allow Azure to apply our DSC settings to our Virtual Machines we are going to need to use an Automation Account, the Automation account acts as a repository for our Automation needs in Azure

##### Task 1: Create the Automation Account

1. Browse to the Azure Portal and authenticate at https://portal.azure.com/
2. Click **New** and type **Automation** in the search box. Choose **Automation** from the results

    ![](/images/ARM-Fig1.png)

3. Click **Create** on the Automation blade so that the **Add Automation Account** blade is displayed. Specify the following information and click **Create**.

    ![](/images/ARM-Fig2.png)

### Exercise 2

#### Upload DSC Configurations into Automation account

1. Firstly we will need to download the DSC Configuration files which we will use for the guest customisation, these can be found at the below link, extract these files to a location on your computer  **NOTE THIS NEEDS FIXING**

    [DSC Configuration Files](https://cloudworkshop.blob.core.windows.net/arm-hackathon/ARM_Hackathon_Guide_Student_Files.zip)

2. Click **Resource groups** > **Automation_RG** > **Automation-Acct** In the Configuration Management section click **DSC Configurations**

    ![](/images/ARM-Fig3.png)

3. Click **Add a configuration** to upload the .PS1 DSC files which we downloaded earlier, we will need to add the CloudShopSQL.ps1 and the CloudShopWeb.ps1 files individually
4. Once these have been added we will need click on each configuration and compile them

    ![](/images/ARM-Fig4.png)

   NOTE: The compile process may take 20-30 seconds to complete. 

### Summary

In this exercise, you configured an Automation account, and configured DSC configuration scripts that will be leveraged by the virtual machine resources. We ran the configuration scripts through the DSC compiler to ensure that they are good to run against our VMs which we will now start building

### Exercise 3

#### Define the vNet configuration which will host our Virtual Machines

Your first ARM template task is going to be creating a virtual network template using Visual Studio, we will then deploy the defined network configuration to Azure.

1. Open Visual Studio
2.	Choose **File**, **New Project**, and then choose **Cloud**, and then **Azure Resource Group**, name the solution **ArmLab** and click **OK**

   ![](/images/ARM-Fig5.png)

3. On the select Azure Template dialog box we will choose **Blank Template** and then click **OK**
4. In Solution Explorer, on the right hand side of the Visual Studio Screen click **azuredeploy.json** this will open the azuredeploy.json file in the center of the screen, you should see the below code in the center of the Visual Studio workspace

```json
   {
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {}
   }
```