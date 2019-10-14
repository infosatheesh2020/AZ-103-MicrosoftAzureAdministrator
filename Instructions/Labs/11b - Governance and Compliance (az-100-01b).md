---
lab:
    title: 'Governance and Compliance'
    module: 'Module 11 - Governance and Compliance'
---

# Lab: Implementing governance and compliance with Azure initiatives and resource locks

All tasks in this lab are performed from the Azure portal

### Scenario
  
Adatum Corporation wants to use Azure policies and initiatives in order to enforce resource tagging in its Azure subscription. Once the environment is compliant, Adatum wants to prevent unintended changes by implementing resource locks.

### Objectives
  
After completing this lab, you will be able to:

-  Implement Azure tags by using Azure policies and initiatives

-  Implement Azure resource locks


### Exercise 1: Implement Azure tags by using Azure policies and initiatives

The main tasks for this exercise are as follows:

1. Provision Azure resources by using Azure Portal

1. Implement an initiative and policy that evaluate resource tagging compliance.

1. Implement a policy that enforces resource tagging compliance.

1. Evaluate tagging enforcement and tagging compliance.

1. Implement remediation of resource tagging non-compliance.

1. Evaluate effects of the remediation task on compliance.


#### Task 1: Provision Azure resources by using Azure Portal

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. Create a new resource group with below configuration

   - Name: az1000101b-RG
   - Location: the name of the Azure region which is closest to the lab location and where you can provision Azure VMs(Eg: East US)

1. In the Azure portal, create a Virtual Network with below configuration:

   - Virtual Network Name: az1000101b-vnet1
   - Subnet name: subnet0
   - Location: same as resource group location
   - Resource Group Name: az1000101b-RG
   - Vnet Address space: 10.102.0.0/16
   - Subnet Address space: 10.102.0.0/24

1. Create a Virtual Machine with below configuration

   - Vnet: az1000101b-vnet1
   - Subnet: subnet0
   - Location: same as resource group location
   - Resource Group: az1000101b-RG
   - Name: az1000101b-vm1
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Vm Size: Standard_DS1_v2
   - NIC name: az1000101b-nic1
   - publicIPAddressName: az1000101b-pip1
   - Tags: "environment":"lab"
   - Image: Windows 2016 Datacenter

1. Create a storage account with below settings

   - Name prefix: az1000101b
   - storageAccountSku: Standard_LRS
   - storageAccountKind: Storage V2
   - Location: same as resource group location
   - Resource Group Name: az1000101b-RG
   - Tags: "environment":"lab"

   > **Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **Note**: Do not wait for the deployment to complete before you proceed to the next step.
   
1. In the Azure portal, navigate to the **Tags** blade.

1. From the **Tags** blade, display all resources with the **environment** tag set to the value **lab**. Note that only some of the resources deployed in the previous task have this tag assigned.

   > **Note**: At this point, only some of the resources have been provisioned, however, you should see at least a few without tags assigned to them.


#### Task 2: Implement a policy and an initiative that evaluate resource tagging compliance.

1. In the Azure portal, navigate to the **Policy** blade.

1. From the **Policy** blade, navigate to the **Policy - Definitions** blade.

1. From the **Policy Definitions** blade, display the **Require tag and its value** policy definition.

1. From the **Require tag and its default value** policy definition blade, use the duplicate the definition feature to create a new policy with the following settings:

    - Definition location: the name of the subscription you are using in this lab

    - Name: **az10001b - Audit tag and its value**

    - Description: **Audits a required tag and its value. Does not apply to resource groups.**

    - Category: the name of a new category **Lab**

    - Policy rule: in the existing policy rule, change the **effect** from **deny** to **audit**, such that the policy definition has the following content:

   ```json
   {
     "mode": "indexed",
     "policyRule": {
       "if": {
         "not": {
           "field": "[concat('tags[', parameters('tagName'), ']')]",
           "equals": "[parameters('tagValue')]"
         }
       },
       "then": {
         "effect": "audit"
       }
     },
     "parameters": {
       "tagName": {
         "type": "String",
         "metadata": {
           "displayName": "Tag Name",
           "description": "Name of the tag, such as 'environment'"
         }
       },
       "tagValue": {
         "type": "String",
         "metadata": {
           "displayName": "Tag Value",
           "description": "Value of the tag, such as 'production'"
         }
       }
     }
   }
   ```

1. From the **Policy - Definitions** blade, navigate to the **New Initiative definition** blade.

1. From the **New Initiative definition** blade, create a new initiative definition with the following settings:

    - Definition location: the name of the subscription you are using in this lab

    - Name: **az10001b - Tagging initiative**

    - Description: **Collection of tag policies.**

    - Category: **Lab**

    - AVAILABLE DEFINITIONS: search for and select **az10001b - Audit tag and its value**

        - Tag Name: **environment**

        - Tag Value: **lab**

1. Navigate to the **Policy - Assignments** blade.

1. From the **Policy - Assignments** blade, navigate to the **Assign initiative** blade and create a new initative assignment with the following settings:

    - Scope: the name of the subscription you are using in this lab

    - Exclusions: none 

    - Initiative definition: **az10001b - Tagging initiative**

    - Assignment name: **az10001b - Tagging initiative assignment**

    - Description: **Assignment of az10001b - Tagging initiative**

    - Assigned by: the default value

    - Create a Managed Identity: **unchecked**

1. Navigate to the **Policy - Compliance** blade. Note that **COMPLIANCE STATE** is set to either **Not registered** or **Not started**.

   > **Note**: On average, it takes about 10 minutes for a compliance scan to start. Rather than waiting for the compliance scan, proceed to the next task. You will review the compliance status later in this exercise.


#### Task 3: Implement a policy that enforces resource tagging compliance.

1. Navigate to the **Policy - Definitions** blade.

1. From the **Policy - Definitions** blade, navigate to the **az10001b - Tagging initiative** blade.

1. From the **az10001b - Tagging initiative** blade, navigate to its **Edit initiative definition** blade.

1. Add the built-in policy definition named **Require tag and its value** to the initiative and set its parameters to the following values:

    - Tag Name: **environment**

    - Tag Value: **lab**

   > **Note**: At this point, your initiative contains two policies. The first of them evaluates the compliance status and the second one enforces tagging during deployment. 


#### Task 4: Evaluate tagging enforcement and tagging compliance.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **New** blade, search Azure Marketplace for **Virtual Network**.

1. Deploy a new virtual network without proving any tags during creation.

   > **Note**: The deployment will fail. This is expected.

1. You will be presented with the message indicating validation erors. Review the error details, indicating that deployment of VNET was disallowed by the policy **Require tag and its value** which is included in the **az10001b - Tagging initiative assignment**.

1. Navigate to the **Policy - Compliance** blade. Identify the entry in the **COMPLIANCE STATE** column.

1. Navigate to the **az10001b - Tagging initiative assignment** blade and review the summary of the compliance status.

1. Display the listing of resource compliance and note which resources have been identified as non-compliant. 

   > **Note**: You might need to click **Refresh** button on the **Policy - Compliance** blade in order to see the update to the compliance status. 


#### Task 5: Implement remediation of resource tagging non-compliance.

1. In the Azure portal, navigate to the **az10001b - Tagging initiative** blade.

1. From the **az10001b - Tagging initiative** blade, navigate to its **Edit initiative definition** blade.

1. Add the built-in policy definition named **Append tag and its default value** to the initiative and set its parameters to the following values:

    - Tag Name: **environment**

    - Tag Value: **lab**

1. Delete the custom policy definition named **az10001b - Audit tag and its value** from the initiative.

1. Delete the built-in policy definition named **Require tag and its value** from the initiative and save the changes.

   > **Note**: At this point, your initiative contains a single policy that automatically remediates tagging non-compliance during deployment of new resources and provides evaluation of compliance status.

1. From the Azure Portal, navigate to all resources listed in the resource group and add tag information (**environment:lab**) so that all resources are tagged correct.

1. In the Azure portal, navigate to the **Tags** blade.

1. From the **Tags** blade, display all resources with the **environment** tag set to the value **lab**. Verify that all resources in the resource group **az1000101b-RG** are listed.


#### Task 6: Evaluate effects of the remediation task on compliance.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **New** blade, search Azure Marketplace for **Virtual Network**.

1. Deploy a new virtual network without proving any tags during creation.

   > **Note**: The deployment will succeed this time. This is expected.

1. In the Azure portal, navigate to the **Tags** blade.

1. From the **Tags** blade, display all resources with the **environment** tag set to the value **lab**. Note that all the resources deployed now have this tag with the same value automatically assigned.

   > **Note**: You should see that all of the resources have tags assigned to them.

1. Navigate to the **Policy - Compliance** blade. Identify the entry in the **COMPLIANCE STATE** column.

1. Navigate to the **az10001b - Tagging initiative assignment** blade. Identify the entry in the **COMPLIANCE STATE** column. If the column contains the **Not started** entry, wait until it the compliance scan runs. 

   > **Note**: You might need to wait for up to 10 minutes and click **Refresh** button on the **Policy - Compliance** blade in order to see the update to the compliance status. 

   > **Note**: Do not wait until the status is listed as compliant but instead proceed to the next exercise. 

> **Result**: After you completed this exercise, you have implemented an initiative and policies that evaluate, enforce, and remediate resource tagging compliance. You also evaluated the effects of policy assignment. 


### Exercise 2: Implement Azure resource locks
  
The main tasks for this exercise are as follows:

1. Create resource group-level locks to prevent accidental changes

1. Validate functionality of the resource group-level locks


#### Task 1: Create resource group-level locks to prevent accidental changes

1. In the Azure portal, navigate to the **az1000101b-RG** resource group blade.

1. From the **az1000101b-RG** resource group blade, display the **az1000101b-RG - Locks** blade.

1. From the **az1000101b-RG - Locks** blade, add a lock with the following settings:

    - Lock name: **az1000101b-roLock**

    - Lock type: **Read-only**


#### Task 2: Validate functionality of the resource group-level locks

1. In the Azure portal, navigate to the **az1000101b-vm1** virtual machine blade.

1. In the Azure portal, navigate to the **az1000101b-vm1** virtual machine blade.

1. From the **az1000101b-vm1** virtual machine blade, navigate to the **az1000101b-vm1 - Tags** blade.

1. Try setting the value of the **environment** tag to **dev**. Note that this time the operation fails. The resulting error message indicates that the resource refused tag assignment, with resource lock being the likely reason.

1. Navigate to the blade of the storage account created in the **az1000101b-RG - Locks** resource group. 

1. From the storage account blade, navigate to its **Access keys** blade. Note the resulting error message stating that you cannot access the data plane because a read lock on the resource or its parent.

1. In the Azure portal, navigate to the **az1000101b-RG** resource group blade.

1. From the **az1000101b-RG** resource group blade, navigate to its **Tags** blade.

1. From the **Tags** blade, attempt assigning the **environment** tag with the value **lab** to the resource group and note the error message.


> **Result**: After you completed this exercise, you have created a resource group-level lock to prevent accidental changes and validated its functionality. 
