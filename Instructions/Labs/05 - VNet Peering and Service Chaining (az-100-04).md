---
lab:
    title: 'VNet Peering and Service Chaining'
    module: 'Module 05 - Intersite Connectivity'
---

# Lab: VNet Peering and Service Chaining
  
All tasks in this lab are performed from the Azure portal except for Exercise 2 Task 3, Exercise 3 Task 1, and Exercise 3 Task 2, which include steps performed from a Remote Desktop session to an Azure VM

### Scenario
  
Adatum Corporation wants to implement service chaining between Azure virtual networks in its Azure subscription. 


### Objectives
  
After completing this lab, you will be able to:

- Create Azure virtual networks and deploy Azure VM

- Configure VNet peering.

- Implement custom routing

- Validate service chaining


### Exercise 0: Prepare the Azure environment
  
The main tasks for this exercise are as follows:

1. Create the first virtual network hosting two Azure VMs 

1. Create the second virtual network in the same region hosting a single Azure VM 

#### Task 1: Create the first virtual network hosting two Azure VMs by using an Azure Resource Manager template

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. Create resource group **az1000401-RG** in **Region** close to you (Eg: East US)

1. In Azure portal, create new **Virtual Network** with below configuration

   - Name: **az1000401-vnet1**
   - Vnet address: **10.104.0.0/16**
   - Subnets: **subnet0 (10.104.0.0/24)**, **subnet1 (10.104.1.0/24)**
   - Region: Resource Group location
   - Resource Group: **az1000401-RG**

1. In Azure portal create new Virtual Machine with below configuration:

   - Vm1Name: az1000401-vm1
   - Region: Same as Resource Group location
   - Vm Size: use Standard_DS1_v2 or Standard_DS2_v2
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Virtual Network Name: az1000401-vnet1
   - Subnet name: subnet0
   - NIC name: az1000401-nic1

1. In Azure portal create second Virtual Machine with below configuration:

   - Vm1Name: az1000401-vm2
   - Region: Same as Resource Group location
   - Vm Size: use Standard_DS1_v2 or Standard_DS2_v2
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Virtual Network Name: az1000401-vnet1
   - Subnet name: subnet1
   - NIC name: az1000401-nic2

   > **Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **Note**: Do not wait for the deployment to complete but proceed to the next task. You will use the network and the virtual machines included in this deployment in the second exercise of this lab.


#### Task 2: Create the second virtual network in the same region hosting a single Azure VM 

1. Create resource group **az1000402-RG** in **Region** selected in previous task (Eg: East US)

1. In Azure portal, create new **Virtual Network** with below configuration

   - Name: **az1000402-vnet2**
   - Vnet address: **10.204.0.0/16**
   - Subnets: **subnet0 (10.204.0.0/24)**
   - Region: Resource group location
   - Resource Group: **az1000402-RG**

1. In Azure portal create new Virtual Machine with below configuration:

   - Vm1Name: az1000402-vm3
   - Region: Same as Resource Group location
   - Vm Size: use Standard_DS1_v2 or Standard_DS2_v2
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Virtual Network Name: az1000402-vnet2
   - Subnet name: subnet0
   - Resource Group: **az1000402-RG**
   - NIC name: az1000402-nic3


   > **Note**: Do not wait for the deployment to complete but proceed to the next task. You will use the network and the virtual machines included in this deployment in the second exercise of this lab.

> **Result**: After you completed this exercise, you have created two Azure virtual networks and initiated deployments of three Azure VM.


### Exercise 1: Configure VNet peering 

The main tasks for this exercise are as follows:

1. Configure VNet peering for the first virtual network

1. Configure VNet peering for the second virtual network


#### Task 1: Configure VNet peering for the first virtual network
  
1. In the Azure portal, navigate to the **az1000401-vnet1** virtual network blade.

1. From the **az1000401-vnet1** virtual network blade, display its **Peerings** blade.

1. From the **az1000401-vnet1 - Peerings** blade, click **+ Add** to create a VNet peering with the following settings:

    - Name: **az1000401-vnet1-to-az1000402-vnet2**

    - Virtual network deployment model: **Resource manager**

    - Subscription: the name of the Azure subscription you are using in this lab

    - Virtual network: **az1000402-vnet2**

    - Name of peering from az1000402-vnet2 to az1000401-vnet1: **az1000402-vnet2-to-az1000401-vnet1**

    - Allow virtual network access from az1000401-vnet1 to az1000402-vnet2: **Enabled**

    - Allow virtual network access from az1000402-vnet2 to az1000401-vnet1: **Enabled**
    
     - Allow forwarded traffic from az1000401-vnet1 to az1000402-vnet2: **disabled**

    - Allow gateway transit from az1000402-vnet2 to az1000401-vnet1: **disabled**
    
    - Allow gateway transit: unchecked

> **Note**: Because you have administrative access to both virtual networks, the portal is configuring both directions (from vnet1 to vnet2, AND vnet2 to vnet1) in a single action. From the CLI, PowerShell, or REST API, these tasks must be performed independently. 


### Exercise 2: Implement custom routing
  
The main tasks for this exercise are as follows:

1. Enable IP forwarding for a network interface of an Azure VM

1. Configure user defined routing 

1. Configure routing in an Azure VM running Windows Server 2016


#### Task 1: Enable IP forwarding for a network interface of an Azure VM
  
   > **Note**: Before you start this task, ensure that the template deployments you started in Exercise 0 have completed. 

1. In the Azure portal, navigate to the blade of the second Azure VM **az1000401-vm2**.

1. From the **az1000401-vm2** blade, display its **Networking** blade. 

1. From the **az1000401-vm2 - Networking** blade, display the blade of the network adapter (**az1000401-nic2**) of the Azure VM.

1. From the **az1000401-nic2** blade, display its **IP configurations** blade.

1. From the **az1000401-nic2 - IP configurations** set IP forwarding to **Enabled**, and then click **Save**.

   > **Note**: The Azure VM **az1000401-vm2**, which network interface you configured in this task, will function as a router, facilitating service chaining between the two virtual networks.


#### Task 2: Configure user defined routing 

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Route table**.

1. Select **Route table**, and then click **Create**.

1. From the **Create route table** blade, create a new route table with the following settings:

    - Name: **az1000402-rt1**

    - Subscription: the name of the Azure subscription you use for this lab

    - Resource group: **az1000402-RG**

    - Location: the same Azure region in which you created the virtual networks
  
    - Virtual network gateway route propagation: **Disabled**

1. In the Azure portal, navigate to the **az1000402-rt1** blade.

1. From the **az1000402-rt1** blade, display its **Routes** blade. 

1. From the **az1000402-rt1 - Routes** blade, add to the route table a route with the following settings: 

    - Route name: **custom-route-to-az1000401-vnet1**

    - Address prefix: **10.104.0.0/16**

    - Next hop type: **Virtual appliance**

    - Next hop address: **10.104.1.4**

   > **Note**: **10.104.1.4** is the IP address of the network interface of **az1000401-vm2**, which will provide service chaining between the two virtual networks.

1. From the **az1000402-rt1** blade, display its **Subnets** blade. 

1. From the **az1000402-rt1 - Subnets** blade, associate the route table **az1000402-rt1** with **subnet0** of **az1000402-vnet2**.


#### Task 3: Configure routing in an Azure VM running Windows Server 2016

1. In the Azure portal, navigate to the blade of the **az1000401-vm2** Azure VM. 

1. From the **Overview** pane of the **az1000401-vm2** blade, generate an RDP file and use it to connect to **az1000401-vm2**.

1. When prompted, authenticate by specifying the following credentials:

    - User name: **Student**

    - Password: **Pa55w.rd1234**

1. Within the Remote Desktop session to **az1000401-vm2**, from **Server Manager**, select **Manage**  use the **Add Roles and Features Wizard** 
1. Click **Next** twice, ensure **az1000401-vm2** is selected and click **Next**,  select the **Remote Access** server role then click **Next** three times, Select the **Routing** role service, select **Add Features**  and all required features. Select **Next** three times, click **Install**. Click **Close** when the installation is complete.

   > **Note**: If you receive an error message **There may be a version mismatch between this computer and the destination server or VHD** once you select the **Remote Access**  checkbox on the **Server Roles** page of the **Add Roles and Features Wizard**, clear the checkbox, click **Next**, click **Previous** and select the **Remote Access**  checkbox again.

1. Within the Remote Desktop session to **az1000401-vm2**, from Server Manager, select **Tools** start the **Routing and Remote Access** console. 

1. In the **Routing and Remote Access** console, right click on the server name and select **Configure and Enable Routing and Remote Access**, Select **Next** use the **Custom configuration** then **Next**, enable **LAN routing** then **Next**, click **Finish** and the click **Start Service**.

1. Within the Remote Desktop session to **az1000401-vm2**, start the **Windows Firewall with Advanced Security** console and enable **File and Printer Sharing (Echo Request - ICMPv4-In)** inbound rule for all profiles.

> **Result**: After completing this exercise, you have implemented custom routing between peered Azure virtual networks. 


### Exercise 3: Validating service chaining

The main tasks for this exercise are as follows:

1. Configure Windows Firewall with Advanced Security on the target Azure VM

1. Test service chaining between peered virtual networks


#### Task 1: Configure Windows Firewall with Advanced Security on the target Azure VM

1. In the Azure portal, navigate to the blade of the **az1000401-vm1** Azure VM. 

1. From the **Overview** pane of the **az1000401-vm1** blade, generate an RDP file and use it to connect to **az1000401-vm1**.

1. When prompted, authenticate by specifying the following credentials:

    - User name: **Student**

    - Password: **Pa55w.rd1234**

1. Within the Remote Desktop session to **az1000401-vm1**, open the **Windows Firewall with Advanced Security** console and enable **File and Printer Sharing (Echo Request - ICMPv4-In)** inbound rule for all profiles.


#### Task 2: Test service chaining between peered virtual networks
  
1. In the Azure portal, navigate to the blade of the **az1000402-vm3** Azure VM. 

1. From the **Overview** pane of the **az1000402-vm3** blade, generate an RDP file and use it to connect to **az1000402-vm3**.

1. When prompted, authenticate by specifying the following credentials:

    - User name: **Student**

    - Password: **Pa55w.rd1234**

1. Once you are connected to **az1-1000402-vm3** via the Remote Desktop session, start **Windows PowerShell**.

1. In the **Windows PowerShell** window, run the following:

   ```pwsh
   Test-NetConnection -ComputerName 10.104.0.4 -TraceRoute
   ```

   > **Note**: **10.104.0.4** is the IP address of the network interface of the first Azure VM **az1000401-vm1**

1. Verify that test is successful and note that the connection was routed over **10.104.1.4**

   > **Note**: Without custom routing in place, the traffic would flow directly between the two Azure VMs. 
> **Result**: After you completed this exercise, you have validated service chaining between peered Azure virtual networks.

## Exercise 4: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1000401-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1000401-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.

1. Repeat the steps for **az1000402-RG** resource group
