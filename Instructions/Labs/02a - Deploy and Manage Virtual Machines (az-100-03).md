---
lab:
    title: 'Deploy and Manage Virtual Machines'
    module: 'Module 02 - Azure Virtual Machines'
---

# Lab: Deploy and Manage Virtual Machines

All tasks in this lab are performed from the Azure portal 


Lab files: 

-  **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip**

### Scenario
  
Adatum Corporation wants to implement its workloads by using Azure virtual machines (VMs) and Azure VM scale sets


### Objectives
  
After completing this lab, you will be able to:

-  Deploy Azure VMs by using the Azure portal

-  Configure networking settings of Azure VMs running Windows operating systems

-  Deploy and configure Azure VM scale sets


### Exercise 1: Deploy Azure VMs by using the Azure portal
  
The main tasks for this exercise are as follows:

1. Deploy an Azure VM running Windows Server 2016 Datacenter into an availability set by using the Azure portal


#### Task 1: Deploy an Azure VM running Windows Server 2016 Datacenter into an availability set by using the Azure portal

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Windows Server**. Select **Windows Server** from the search results list.

1. On the Windows Server page, use the drop-down menu to select **[smalldisk] Windows Server 2016 Datacenter**, and then click **Create**.

1. Use the **Create a virtual machine** blade to deploy a virtual machine with the following settings:

    - Subscription: the name of the subscription you are using in this lab

    - Resource group: the name of a new resource group **az1000301-RG**

    - Virtual machine name: **az1000301-vm0**

    - Region: **(US) East US** (or a region closer to you)

      > **Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

    - Availability options: **Availability set**

    - Availability set: Click **Create New**, and name the new availability set **az1000301-avset0** with **2** fault domains and **5** update domains. Click **OK**.

    - Image: **[smalldisk] Windows Server 2016 Datacenter**

    - Size: **Standard DS2_v2**

    - Username: **Student**

    - Password: **Pa55w.rd1234**

    - Public inbound ports: **None**

    - Already have a Windows license?: **No**

1. Click **Next: Disks >**.    

    - OS disk type: **Standard HDD**

1. Click **Next: Networking >**.

1. On the Networking tab, click **Create new** under Virtual Network. Use the virtual network name already assigned by default and specify the following:

    - Virtual network address range: **10.103.0.0/16**

    - Subnet name: **subnet0**

    - Subnet address range: **10.103.0.0/24**

1. Click **OK**.

1. Leave all other default values, and click **Review + create**.

1. Click **Create**.

   > **Note**: You will configure the network security group you create in this task in the second exercise of this lab

   > **Note**: Wait for the deployment to complete before you proceed to the next task. This should take about 5 minutes.


### Exercise 2: Configure networking settings of Azure VMs running Windows operating systems
  
The main tasks for this exercise are as follows:

1. Configure static private and public IP addresses of Azure VMs

1. Connect to an Azure VM running Windows Server 2016 Datacenter via a public IP address


#### Task 1: Configure static private and public IP addresses of Azure VMs

1. In the Azure portal, navigate to the **az1000301-vm0** blade.

1. From the **az1000301-vm0** blade, navigate to the **Networking** blade, displaying the configuration of the public IP address **az1000301-vm0-ip**, assigned to its network interface.

1. From the **Networking** blade, click the link representing the public IP address.

1. On the az1000301-vm0-ip blade, click **Configuration**.

1. Change the assignment of the public IP address to **Static**, and then click **Save**.

   > **Note**: Take a note of the public IP address assigned to the network interface of **az1000301-vm0**. You will need it later in this exercise.


#### Task 2: Connect to an Azure VM running Windows Server 2016 Datacenter via a public IP address

1. In the Azure portal, navigate to the **az1000301-vm0** blade.

1. From the **az1000301-vm0** blade, navigate to the **Networking** blade.

1. On the **az1000301-vm0 - Networking** blade, review the inbound port rules of the network security group assigned to the network interface of **az1000301-vm0**.

   > **Note**: The default configuration consisting of built-in rules block inbound connections from the internet (including connections via the RDP port TCP 3389)

1. Add an inbound security rule to the existing network security group with the following settings:

    - Source: **Any**

    - Source port ranges: **\***

    - Destination: **Any**

    - Destination port ranges: **3389**

    - Protocol: **TCP**

    - Action: **Allow**

    - Priority: **100**

    - Name: **AllowInternetRDPInBound**

1. In the Azure portal, display the **Overview** pane of the **az1000301-vm0** blade. 

1. From the **Overview** pane of the **az1000301-vm0** blade, click **Connect** and generate an RDP file and use it to connect to **az1000301-vm0**.

1. When prompted, authenticate by specifying the following credentials:

    - User name: **Student**

    - Password: **Pa55w.rd1234**



### Exercise 3: Deploy and configure Azure VM scale sets

The main tasks for this exercise are as follows:

1. Deploy an Azure VM scale set

1. Install IIS on a scale set VM by using DSC extensions



#### Task 1: Deploy an Azure VM scale set

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Virtual machine scale set**.

1. Use the list of search results to navigate to the **Create virtual machine scale set** blade.

1. Use the **Create virtual machine scale set** blade to deploy a virtual machine scale set with the following settings:

    - Virtual machine scale set name: **az1000303vmss0**

    - Operating system disk image: **Windows Server 2016 Datacenter**

    - Subscription: the name of the subscription you are using in this lab

    - Resource group: the name of a new resource group **az1000303-RG**

    - Location: the same Azure region you chose in the previous exercises of this lab

    - Availability zone: **None**

    - Username: **Student**

    - Password: **Pa55w.rd1234**

    - Instance count: **1**

    - Instance size: **DS2 v2**

    - Deploy as low priority: **No**

    - Use managed disks: **Yes**

    - Autoscale: **Disabled**

    - Choose Load balancing options: **Load balancer**

    - Public IP address name: **az1000303vmss0-ip**

    - Domain name label: type in the value of the &lt;custom-label&gt; you identified in the previous task

    - Virtual network: the name of a new virtual network **az1000303-vnet0** with the following settings:

        - Address range: **10.203.0.0/16**

        - Subnet name: **subnet0**

        - Subnet address range: **10.203.0.0/24**

    - Public IP address per instance: **Off**
    
    - Accelerated networking: **Off**
    
    - NIC network security group: **Basic**
    
    - Select inbound ports: **HTTP**
    
    - Boot diagnostics: **Off**
    
    - System assigned managed identity: **Off**

   > **Note**: Wait for the deployment to complete before you proceed to the next task. This should take about 5 minutes.


#### Task 2: Install IIS on a scale set VM by using DSC extensions

1. In the Azure portal, navigate to the **az1000303vmss0** blade.

1. From the **az1000303vmss0** blade, display its Extension blade.

1. From the **az1000303vmss0 - Extension** blade, add the **PowerShell Desired State Configuration** extension with the following settings:

   > **Note**: The DSC configuration module is available for upload from **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip**. The module contains the DSC configuration script that installs the Web Server (IIS) role.

    - Configuration Modules or Script: **"az-100-03_install_iis_vmss.zip"**

    - Module-qualified Name of Configuration: **az-100-03_install_iis_vmss.ps1\IISInstall**

    - Configuration Arguments: leave blank

    - Configuration Data PSD1 File: leave blank

    - WMF Version: **latest**

    - Data Collection: **Disable**

    - Version: **2.76**

    - Auto Upgrade Minor Version: **Yes**

1. Navigate to the **az1000303vmss0 - Instances** blade and initiate the upgrade of the **az1000303vmss0_0** instance.

   > **Note**: The update will trigger application of the DSC configuration script. Wait for upgrade to complete. This should take about 5 minutes. You can monitor the progress from the **az1000303vmss0 - Instances** blade.

1. Once the upgrade completes, navigate to the **Overview** blade. 

1. On the **az1000303vmss0-ip** blade, note the public IP address assigned to **az1000303vmss0**.

1. Start Microsoft Edge and navigate to the public IP address you identified in the previous step.

1. Verify that the browser displays the default IIS home page. 

> **Result**: After you completed this exercise, you have identified an available DNS name for an Azure VM scale set deployment, deployed an Azure VM scale set, and installed IIS on a scale set VM by using the DSC extension.

## Exercise 4: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1000301-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1000301-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.
