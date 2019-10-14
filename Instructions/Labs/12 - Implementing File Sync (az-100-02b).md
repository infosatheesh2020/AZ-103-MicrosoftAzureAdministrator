---
lab:
    title: 'Azure File Sync'
    module: 'Module 12 - Data Services'
---

# Lab: Implement Azure File Sync

All tasks in this lab are performed from the Azure portal, except for steps in Exercise 1 and Exercise 2 performed within a Remote Desktop session to an Azure VM.

### Scenario
  
Adatum Corporation hosts its file shares in on-premises file servers. Considering its plans to migrate majority of its workloads to Azure, Adatum is looking for the most efficient method to replicate its data to file shares that will be available in Azure. To implement it, Adatum will use Azure File Sync.

### Objectives
  
After completing this lab, you will be able to:

-  Deploy an Azure VM by using an Azure Portal

-  Prepare Azure File Sync infrastructure

-  Implement and validate Azure File Sync


### Exercise 0: Prepare the lab environment
  
The main tasks for this exercise are as follows:

1. Deploy an Azure VM by using Azure Portal


#### Task 1: Deploy an Azure VM by using Azure Portal

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. Create a new resource group with below configuration

   - Name: az1000201b-RG
   - Location: the name of the Azure region which is closest to the lab location and where you can provision Azure VMs (Eg: East US)

1. In the Azure portal, create a Virtual Network with below configuration:

   - Virtual Network Name: az1000201b-vnet1
   - Subnet name: subnet0
   - Location: same as resource group location
   - Resource Group Name: az1000201b-RG
   - Vnet Address space: 10.102.0.0/16
   - Subnet Address space: 10.102.0.0/24

1. Create a Virtual Machine with below configuration

   - Vnet: az1000201b-vnet1
   - Subnet: subnet0
   - Location: same as resource group location
   - Resource Group: az1000201b-RG
   - Name: az1000201b-vm1
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Vm Size: Standard_DS1_v2
   - NIC name: az1000201b-nic1
   - publicIPAddressName: az1000201b-pip1
   - Image: Windows Server 2016-Datacenter
   - Data Disk: 16 GB

   > **Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **Note**: Do not wait for the deployment to complete but proceed to the next exercise. You will use the virtual machine included in this deployment in the next exercise of this lab.

   > **Note**: Keep in mind that the purpose of Azure VM **az1000201b-vm1** is to emulate an on-premises file server in our scenario.

> **Result**: After you completed this exercise, you have initiated a  deployment of an Azure VM **az1000201b-vm1** that you will use in the next exercise of this lab.


### Exercise 1: Prepare Azure File Sync infrastructure

The main tasks for this exercise are as follows:

1. Create an Azure Storage account and a file share

1. Prepare Windows Server 2016 for use with Azure File Sync

1. Run Azure File Sync evaluation tool


#### Task 1: Create an Azure Storage account and a file share

1. In the Azure portal, navigate to the **New** blade.

1. From the **New** blade, search Azure Marketplace for **Storage account**.

1. Use the list of search results to navigate to the **Create storage account** blade.

1. From the **Create storage account** blade, create a new storage account with the following settings: 

    - Subscription: the same subscription you selected in the previous task

    - Resource group: the name of a new resource group **az1000202b-RG**

    - Storage account name: any valid, unique name between 3 and 24 characters consisting of lowercase letters and digits

    - Location: the name of the Azure region which you selected in the previous task

    - Performance: **Standard**

    - Account kind: **Storage (general purpose v1)**

    - Replication: **Locally-redundant storage (LRS)**

    - Secure transfer required: **Disabled**

    - Allow access from: **All networks**

    - Hierarchical namespace: **Disabled**

   > **Note**: Wait for the storage account to be provisioned then proceed to the next step.

1. In the Azure portal, navigate to the blade representing the newly provisioned storage account.

1. From the storage account blade, display the properties of its File Service.

1. From the storage account **Files** blade, create a new file share with the following settings:

    - Name: **az10002bshare1**

    - Quota: none


#### Task 2: Prepare Windows Server 2016 for use with Azure File Sync

   > **Note**: Before you start this task, ensure that the template deployment you started in Exercise 0 has completed. 

1. In the Azure portal, navigate to the **az1000201b-vm1** blade.

1. From the **az1000201b-vm1** blade, connect to the Azure VM via the RDP protocol and, when prompted to sign in, provide the following credentials:

    - Admin Username: **Student**

    - Admin Password: **Pa55w.rd1234**

1. Within the RDP session to the Azure VM, in Server Manager, navigate to **File and Storage Services**, locate the data disk attached to the Azure VM, initialize it as a **GPT** disk, and use **New Volume Wizard** to create a single volume occupying entire disk with the following settings:

    - Drive letter: **S**

    - File system: **NTFS**

    - Allocation unit size: **Default**

    - Volume label: **Data**

1. Within the RDP session, start a Windows PowerShell session as administrator. 

1. From the Windows PowerShell console, set up a file share by running the following:

   ```powershell
   $directory = New-Item -Type Directory -Path 'S:\az10002bShare'

   New-SmbShare -Name $directory.Name -Path $directory.FullName -FullAccess 'Administrators' -ReadAccess Everyone   

   Copy-Item -Path 'C:\WindowsAzure\*' -Destination $directory.FullName –Recurse
   ```

   > **Note**: To populate the file share with sample data, we use content of the *C:\\WindowsAzure* folder, which should contain about 100 MB worth of files

1. From the Windows PowerShell console, install the latest Az PowerShell module by running the following:

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > **Note**: When prompted, confirm that you want to proceed with the installation from PSGallery repository.


#### Task 3: Run Azure File Sync evaluation tool

1. Within the RDP session to the Azure VM, from the Windows PowerShell console, install the latest version of Package Management and PowerShellGet by running the following:

   ```powershell
   Install-Module -Name PackageManagement -Repository PSGallery -Force

   Install-Module -Name PowerShellGet -Repository PSGallery -Force
   ```

   > **Note**: When prompted, confirm that you want to proceed with the installation of the NuGet provider.

1. Restart the PowerShell session.

1. From the Windows PowerShell console, install the Azure File Sync PowerShell module by running the following:

   ```powershell
   Install-Module -Name Az.StorageSync -AllowClobber -Force
   ```

1. From the Windows PowerShell console, install the Azure File Sync PowerShell module by running the following:

   ```powershell
   Invoke-AzStorageSyncCompatibilityCheck -Path 'S:\az10002bShare'
   ```

1. Review the results and verify that no compatibility issues have been found.

> **Result**: After you completed this exercise, you have created an Azure Storage account and a file share, prepare Windows Server 2016 for use with Azure File Sync, and run Azure File Sync evaluation tool


### Exercise 2: Prepare Azure File Sync infrastructure

The main tasks for this exercise are as follows:

1. Deploy the Storage Sync Service

1. Install the Azure File Sync Agent

1. Register the Windows Server with Storage Sync Service

1. Create sync groups and a cloud endpoint

1. Create a server endpoint

1. Validate Azure File Sync operations


#### Task 1: Deploy the Storage Sync Service

1. Within the RDP session to the Azure VM, in Server Manager, navigate to the Local Server view and turn off temporarily **IE Enhanced Security Configuration**.

1. Within the RDP session to the Azure VM, start Internet Explorer, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using the same Microsoft account you used previously in this lab.

1. In the Azure portal, navigate to the **New** blade.

1. From the **New** blade, search Azure Marketplace for **Azure File Sync**.

1. Use the list of search results to navigate to the **Deploy Storage Sync** blade.

1. From the **Deploy Storage Sync** blade, create a Storage Sync Service with the following settings:

    - Name: **az1000202b-ss**

    - Subscription: the same subscription you selected in the previous task

    - Resource group: the name of a new resource group **az1000203b-RG**

    - Location: the name of the Azure region in which you created the storage account earlier in this exercise


#### Task 2: Install the Azure File Sync Agent.

1. Within the RDP session, start another instance of Internet Explorer, browse to Microsoft Download Center at [**https://go.microsoft.com/fwlink/?linkid=858257**](https://go.microsoft.com/fwlink/?linkid=858257) and download the Azure File Sync Agent Windows Installer file **StorageSyncAgent_V6_WS2016.msi**.

1. Once the download completes, run the Storage Sync Agent Setup wizard with the default settings to install Azure File Sync Agent.

1. After the Azure File Sync agent installation completes, the **Azure File Sync - Server Registration** wizard will automatically start.


#### Task 3: Register the Windows Server with Storage Sync Service

1. From the initial page of the **Azure File Sync - Server Registration** wizard, sign in by using the same Microsoft account you used previously in this lab.

1. On the **Choose a Storage Sync Service** page of the **Azure File Sync - Server Registration** wizard, specify the following settings to register:

    - Azure Subscription: the name of the subscription you are using in this lab

    - Resource group: **az1000203b-RG**

    - Storage Sync Service: **az1000202b-ss**

1. When prompted, sign in again by using the same Microsoft account you used previously in this lab.


#### Task 4: Create a sync group and a cloud endpoint

1. Within the RDP session to the Azure VM, in the Azure portal, navigate to the **az1000202b-ss** Storage Sync Service blade.

1. From the **az1000202b-ss** Storage Sync Service blade, navigate to the **Sync group** blade and create a new sync group with the following settings:

    - Sync group name: **az1000202b-syncgroup1**

    - Azure Subscription: the name of the subscription you are using in this lab

    - Storage account: the resource id of the storage account you created in the previous exercise

    - Azure File Share: **az10002bshare1**


#### Task 5: Create a server endpoint

1. Within the RDP session to the Azure VM, in the Azure portal, from the **az1000202b-ss** Storage Sync Service blade, navigate to the **az1000202b-syncgroup1** blade.

1. From the **az1000202b-syncgroup1** blade, navigate to the **Add server endpoint** blade and create a new server endpoint with the following settings:

    - Registered server: **az1000201b-vm1**

    - Path: **S:\\az10002bShare**

    - Cloud Tiering: **Enabled**

        - Always preserve the specified percentage of free space on the volume: **15**

        - Only cache files that were accessed or modified within the specified number of days: **30**

    - Offline Data Transfer: **Disabled**


#### Task 6: Validate Azure File Sync operations

1. Within the RDP session to the Azure VM, in the Azure portal, monitor the health status of the server endpoint **az100021b-vm1** on the **az1000202b-syncgroup1** blade, as it changes from **Provisioning** to **Pending** and, eventually, to a green checkmark.

   > **Note**: You should be able to proceed to the next step after a few minutes. 

1. In the Azure portal, navigate to the blade for the storage account you created earlier in the lab, switch to the **Files** tab and then click **az10002bshare1**.

1. On the **az10002bshare1** blade, click **Connect**.

1. From the **Connect** blade, copy into Clipboard the PowerShell commands that connect to the file share from a Windows computer.

1. Within the RDP session, start a Windows PowerShell ISE session. 

1. From the Windows PowerShell ISE session, open the script pane and paste into it the content of your local Clipboard.

1. Add the ` -Persist` switch to the end of the line containing the `New-PSDrive` cmdlet.

1. Execute the script and verify that its output confirms successful mapping of the Z: drive to the Azure Storage File Service share.

1. Within the RDP session, start File Explorer, navigate to the Z: drive, and verify that it contains the same content as S:\\az10002bShare

1. Display the Properties window of individual folders on the Z: drive, review the Security tab, and note that the entries represent NTFS permissions assigned to the corresponding folders on the S: drive.


> **Result**: After you completed this exercise, you have deployed the Storage Sync Service, installed the Azure File Sync Agent, registered the Windows Server with Storage Sync Service, created a sync group and a cloud endpoint, created a server endpoint, and validated Azure File Sync operations.
