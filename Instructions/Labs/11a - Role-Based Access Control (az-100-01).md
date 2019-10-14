---
lab:
    title: 'Role-Based Access Control'
    module: 'Module 11 - Governance and Compliance'
---

# Lab: Role-Based Access Control 

All tasks in this lab are performed from the Azure portal 

### Scenario
  
Adatum Corporation wants to use Azure Role Based Access Control and Azure Policy to control provisioning and management of their Azure resources. It also wants to be able to automate and track provisioning and management tasks.

### Objectives
  
After completing this lab, you will be able to:

-  Configure delegation of provisioning and management of Azure resources by using built-in Role-Based Access Control (RBAC) roles and built-in Azure policies

-  Verify delegation by provisioning Azure resources as a delegated admin and auditing provisioning events


### Exercise 1: Configure delegation of provisioning and management of Azure resources by using built-in Role-Based Access Control (RBAC) roles and built-in Azure policies

The main tasks for this exercise are as follows:

1. Create Azure Active Directory (AD) users and groups

1. Create Azure resource groups

1. Delegate management of an Azure resource group via a built-in RBAC role

1. Assign a built-in Azure policy to an Azure resource group


#### Task 1: Create Azure AD users and groups

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab and is a Global Administrator of the Azure AD tenant associated with that subscription.

1. In the Azure portal, navigate to the **Azure Active Directory** blade 

1. From the **Azure Active Directory** blade, navigate to the **Custom domain names** blade and identify the primary DNS domain name associated the Azure AD tenant. Note its value - you will need it later in this task.

1. From the Azure AD **Custom domain names** blade, navigate to the **Users - All users** blade. 

1. From the **Users - All users** blade, create a new user with the following settings:

    - Name: **aaduser100011**

    - User name: **aaduser100011@&lt;DNS-domain-name&gt;** where &lt;DNS-domain-name&gt; represents the primary DNS domain name you identified earlier in this task.

    - Profile: **Not configured**

    - Properties: **Default**

    - Groups: **0 groups selected**

    - Directory role: **User**

    - Password: select the checkbox **Show Password** and note the string appearing in the **Password** text box. You will need it later in this lab.

1. From the **Users - All users** blade, navigate to the **Groups - All groups** blade. 

1. From the **Groups - All groups** blade, create a new group with the following settings:

    - Group type: **Security**

    - Group name: **az1001 Contributors**

    - Group description: **az1001 Contributors**

    - Membership type: **Assigned**

    - Members: **aaduser100011**


#### Task 2: Create Azure resource groups

1. In the Azure portal, navigate to the **Resource groups** blade.

1. From the **Resource groups** blade, create the first resource group with the following settings:

    - Resource group name: **az1000101-RG**

    - Subscription: the name of the subscription you are using in this lab

    - Resource group location: the name of the Azure region which is closest to the lab location and where you can provision Azure VMs.

   > **Note**: To identify Azure regions available in your subscription, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

1. From the **Resource groups** blade, create the second resource group with the following settings:

    - Resource group name: **az1000102-RG**

    - Subscription: the name of the subscription you selected in the previous step

    - Resource group location: the name of the Azure region you selected in the previous step


#### Task 3: Delegate management of an Azure resource group via a built-in RBAC role

1. In the Azure portal, from the **Resource groups** blade, navigate to the **az1000101-RG** blade.

1. From the **az1000101-RG** blade, display its **Access control (IAM)** blade.

1. From the **az1000101-RG - Access control (IAM)** blade, display the **Role assignments**  blade.

1. From the **Role assignments** blade, create the following **role assignment**:

    - Role: **Contributor**

    - Assign access to: **Azure AD user, group, or service principal**

    - Select: **az1001 Contributors**


#### Task 4: Assign a built-in Azure policy to an Azure resource group

1. From the **az1000101-RG** blade, display its **Policies** blade.

1. From the **Policy - Compliance** blade, display the **Assign policy** blade.

1. Assign the policy with the following settings:

    - Scope: **az1000101-RG**

    - Exclusions: leave the entry blank

    - Policy definition: **Allowed virtual machine SKUs**

    - Assignment name: **Allowed virtual machine SKUs**

    - Description: **Allowed selected virtual machine SKUs (Standard_DS1_v2)**

    - Assigned by: leave the entry set to its default value
	
    - Allowed SKUs: **Standard_DS1_v2**

    - Create a Managed Identity: leave the entry blank

> **Result**: After you completed this exercise, you have created an Azure AD user and an Azure AD group, created two Azure resource groups, delegated management of the first Azure resource group via the built-in Azure VM Contributor RBAC role, and assigned to the same resource group the built-in Azure policy restricting SKUs that can be used for Azure VMs.


### Exercise 2: Verify delegation by provisioning Azure resources as a delegated admin and auditing provisioning events
  
The main tasks for this exercise are as follows:

1. Attempt an automated deployment of a policy non-compliant Azure VM as a delegated admin

1. Perform an  automated deployment of a policy compliant Azure VM as a delegated admin

1. Review Azure Activity Log events corresponding to Azure VM deployments


#### Task 1: Attempt an automated deployment of a policy non-compliant Azure VM as a delegated admin

1. Launch another browser window in the Private mode.

1. In the new browser window, navigate to the Azure portal and sign in using the user account you created in the previous exercise. When prompted, change the password to a new value.

1. In the Azure portal, navigate to the **Resource groups** blade and note that you can view only the resource group **az1000101-RG**.

1. In the Azure portal, navigate to the **Create a resource** blade. 

1. From the **Create a resource** blade, search Azure Marketplace for **Template deployment**.

1. Use the list of search results to navigate to the **Deploy a custom template** blade.

1. On the **Custom deployment** blade, in the **Load a GitHub quickstart template** drop-down list, select the **101-vm-simple-linux** entry and navigate to the **Edit template** blade.

1. On the **Edit template** blade, navigate to the **Variables** section and locate the **vmSize** entry.

1. Note that the template is using hard-coded **Standard_A1** VM size.

1. Discard any changes you might have made to the template and navigate to the **Deploy a simple Ubuntu Linux VM** blade.

1. From the **Deploy a simple Ubuntu Linux VM** blade, initiate a template deployment with the following settings:

    - Subscription: the same subscription you selected in the previous exercise

    - Resource group: **az1000101-RG**

    - Location: the name of the Azure region which you selected in the previous exercise

    - Admin Username: **Student**

    - Admin Password: **Pa55w.rd1234**

    - Dns Label Prefix: the &lt;custom-label&gt; you identified in the previous task

    - Ubuntu OS Version: accept the default value

    - Location: accept the default value
    
1. Note that the initiation of the deployment fails. Navigate to the **Errors** blade and note that the deployment of the resource is not allowed by the policy **Allowed virtual machine SKUs**.   


#### Task 2: Perform an automated deployment of a policy compliant Azure VM as a delegated admin
 
1. From the **Deploy a simple Ubuntu Linux VM** blade, navigate to the **Edit template** blade.

1. On the **Edit template** blade, navigate back to the **Variables** section and locate the **vmSize** entry.

1. Replace the value **Standard_A1** with **Standard_DS1_v2** and save the change.

1. Initiate a deployment again. Note that this time validation is successful. 

1. Do not wait for the deployment to complete but proceed to the next task.


#### Task 3: Review Azure Activity Log events corresponding to Azure VM deployments

1. Switch to the browser window that you used in the previous exercise.

1. In the Azure portal, navigate to the **az1000101-RG** resource group blade.

1. From the **az1000101-RG** resource group blade, display its **Activity log** blade. 

1. In the list of operations, note the ones corresponding to the failed and successful validation events. 

1. Refresh the view of the blade and observe events corresponding to the Azure VM provisioning, including the final one representing the successful deployment.

> **Result**: After you completed this exercise, you have identified an available DNS name for an Azure VM deployment, attempted an automated deployment of a policy non-compliant Azure VM as a delegated admin, performed an automated deployment of a policy compliant Azure VM as the same delegated admin, and reviewed Azure Activity Log entries corresponding to both Azure VM deployments.

## Exercise 3: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1000101-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1000101-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.

1. Repeat the steps for **az1000102-RG** resource group

> **Result**: In this exercise, you removed the resources used in this lab.
