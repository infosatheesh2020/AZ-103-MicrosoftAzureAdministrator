---
lab:
    title: 'Implement and Manage Storage'
    module: 'Module 03 - Azure Storage'
---

# Lab: Implement and Manage Storage

All tasks in this lab are performed from the Azure portal  except for Exercise 2 Task 2, which includes steps performed on a VM or on laptop computer.

Lab files: 

-  **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json**

-  **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json**

### Scenario
  
Adatum Corporation wants to leverage Azure Storage for hosting its data

### Objectives
  
After completing this lab, you will be able to:

-  Implement and use Azure Blob Storage

-  Implement and use Azure File Storage



### Exercise 1: Implement and use Azure Blob Storage

The main tasks for this exercise are as follows:

1. Create Azure Storage accounts

1. Review configuration settings of Azure Storage accounts

1. Manage Azure Storage Blob Service

1. Use a Shared Access Signature (SAS) key to access a blob


#### Task 1: Create Azure Storage accounts

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Storage account**.

1. Use the list of search results to navigate to the **Create storage account** blade.

1. From the **Create storage account** blade, create a new storage account with the following settings: 

    - Subscription: the same subscription you selected in the previous task

    - Resource group: the name of a new resource group **az1000202-RG**

    - Storage account name: any valid, unique name between 3 and 24 characters consisting of lowercase letters and digits

    - Location: the name of the Azure region which you selected in the previous task

    - Performance: **Standard**

    - Account kind: **Storage (general purpose v1)**

    - Replication: **Locally-redundant storage (LRS)**

1. Click **Review + create**, and then click **Create**.

1. Do not wait for the storage account to be provisioned but proceed to the next step.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Storage account**.

1. Use the list of search results to navigate to the **Create storage account** blade.

1. From the **Create storage account** blade, create a new storage account with the following settings: 

    - Subscription: the same subscription you selected in the previous task

    - Resource group: the name of a new resource group **az1000203-RG**

    - Storage account name: any valid, unique name between 3 and 24 characters consisting of lowercase letters and digits

    - Location: the name of an Azure region different from the one you chose when creating the first storage account

    - Performance: **Standard**

    - Account kind: **StorageV2 (general purpose v2)**

    - Access tier: **Hot**

    - Replication: **Geo-redundant storage (GRS)**

1. Click **Review + create**, then click **Create**.

1. Wait for the storage account to be provisioned. This should take less than a minute.


#### Task 2: Review configuration settings of Azure Storage accounts
  
1. In Azure Portal, navigate to the blade of the first storage account you created. 

1. With your storage account blade open, review the storage account configuration in the **Overview** section, including the performance, replication, and account kind settings.

1. Display the **Access keys** blade. Note that you have the option of copying the values of storage account name, as well as the values of key1 and key2. You also have the option to regenerate each of the keys.

1. Display the **Configuration** blade of the storage account.

1. On the **Configuration** blade, note that you have the option of performing an upgrade to **General Purpose v2** account, enforcing secure transfer, and changing the replication settings to either **Geo-redundant storage (GRS)** or **Read-access geo-redundant storage (RA-GRS)**. However, you cannot change the performance setting (this setting can only be assigned when the storage account is created).

1. Display the **Encryption** blade of the storage account. Note that encryption is enabled by default and that you have the option of using your own key.

   > **Note**: Do not change the configuration of the storage account. 

1. In Azure Portal, navigate to the blade of the second storage account you created. 

1. With your storage account blade open, review the storage account configuration in the **Overview** section, including the performance, replication, and account kind settings.

1. Display the **Configuration** blade of the storage account.

1. On the **Configuration** blade, note that you have the option of disabling the secure transfer requirement, setting the default access tier to **Cool**, and changing the replication settings to either **Locally-redundant storage (LRS)** or **Read-access geo-redundant storage (RA-GRS)**. In this case, you also cannot change the performance setting.

1. Display the **Encryption** blade of the storage account. Note that in this case encryption is also enabled by default and that you have the option of using your own key.


#### Task 3: Manage Azure Storage Blob Service

1. In the Azure portal, navigate to the **Blobs** blade of the first storage account you created. 

1. From the **Blobs** blade of the first storage account, create a new container named **az1000202-container** with the **Public access level** set to **Private (no anonymous access)**. 

1. From the **az1000202-container** blade, upload **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json** and **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json** into the container.





#### Task 4: Use a Shared Access Signature (SAS) key to access a blob

1. From the **Blobs** blade of the second storage account, navigate to the container **az1000202-container**, and then open the **az-100-02_azuredeploy.json** blade.

1. On the **az-100-02_azuredeploy.json** blade, copy the value of the **URL** property.

1. Open another Microsoft Edge window and navigate to the URL you copied in the previous step. 

   > **Note**: The browser will display the **ResourceNotFound**. This is expected since the container has the **Public access level** set to **Private (no anonymous access)**.

1. On the **az-100-02_azuredeploy.json** blade, generate a shared access signature (SAS) and the corresponding URL with the following settings:

    - Permissions: **Read**

    - Start date/time: specify the current date/time in your current time zone

    - Expiry date/time: specify the date/time 24 hours ahead of the current time

    - Allowed IP addresses: leave blank

    - Allowed protocols: **HTTP**

    - Signing key: **Key 1**

1. On the **az-100-02_azuredeploy.json** blade, copy **Blob SAS URL**.

1. From the previously opened Microsoft Edge window, navigate to the URL you copied in the previous step. 

   > **Note**: This time, you will be prompted whether you want to open or save **az-100-02_azuredeploy.json**. This is expected as well, since this time you are no longer accessing the container anonymously, but instead you are using the newly generated SAS key, which is valid for the next 24 hours.

1. Close the Microsoft Edge window displaying the prompt. 

> **Result**: After you completed this exercise, you have created two Azure Storage accounts, reviewed their configuration settings, created a blob container, uploaded blobs into the container, copied the container and blobs between the storage accounts, and used a SAS key to access one of the blobs. 


### Exercise 2: Implement and use Azure File Storage

The main tasks for this exercise are as follows:

1. Create an Azure File Service share

1. Map a drive to the Azure File Service share from an Azure VM or from laptop computer


#### Task 1: Create an Azure File Service share
  
1. In the Azure portal, navigate to the blade displaying the properties of the second storage account you created in the previous exercise.

1. From the storage account blade select Files under File Service.

1. From the storage account **Files** blade, create a new file share with the following settings:

    - Name: **az10002share1**

    - Quota: **5 GB**


#### Task 2: Map a drive to the Azure File Service share from an Azure VM or laptop computer

1. Navigate to the **az10002share1** blade and display the **Connect** blade.

1. From the **Connect** blade, copy into Clipboard the PowerShell commands that connect to the file share from a Windows computer.

1. Log into remote VM with right credentials through RDP or into the laptop computer

1. Within the RDP session/laptop computer, start a Windows PowerShell ISE session. 

1. From the Windows PowerShell ISE session, open the script pane and paste into it the content of your local Clipboard.

1. Paste the script into the PowerShell ISE session, add `` -Persist `` at the end of the script, execute the script, and verify that its output confirms successful mapping of the Z: drive to the Azure Storage File Service share.

1. Start File Explorer, navigate to the Z: drive and create a folder named **Folder1**.

1. In the File Explorer window, navigate to **Folder1** and create a text document named **File1.txt**. 

   > **Note**: Make sure that you take into account the default configuration of File Explorer that does not display known file extensions in order to avoid creating a file named **File1.txt.txt**.

1. From the PowerShell prompt, enter **Z:** to change the directory context to the mapped drive. 

1. From the PowerShell prompt, enter **dir** to list the contents of the drive. You should see the directory that you created from File Explorer.

1. From the PowerShell prompt, enter **cd Folder1** to change directories to the folder. Run the **dir** command again to list the file contents.

> **Result**: After you completed this exercise, you have created an Azure File Service share, mapped a drive to the file share from an Azure VM, and used File Explorer from the Azure VM to create a folder and a file in the file share.

## Exercise 3: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1000202-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1000202-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.

1. Repeat the steps for **az1000203-RG** resource group
