---
lab:
    title: '06a - Overview of prerequisites for deployment of Azure Center for SAP solutions (ACSS)'
    module: 'Design and implement an infrastructure to support SAP workloads on Azure'
---

# AZ 1006 Module: Design and implement an infrastructure to support SAP workloads on Azure
# AZ-1006 1-day course lab: Overview of prerequisites for deployment of SAP workloads with Azure Center for SAP solutions (ACSS)

Estimated Time: 100 minutes

All tasks in this AZ-1006 1-day course lab are performed from the Azure portal

## Objectives

After completing this lab, you will be able to:

- Implement prerequisites for deploying SAP workloads in Azure by using Azure Center for SAP solutions

## Instructions

### Exercise 1: Implement prerequisites for deploying SAP workloads in Azure by using Azure Center for SAP solutions

Duration: 60 minutes

In this exercise, you will review and implement prerequisites for deploying SAP workloads in Azure by using Azure Center for SAP solutions. This will include the following activities:

- Creating a Microsoft Entra user-assigned managed identity to be used by Azure Center for SAP solutions for Azure Storage access during its deployment.
- Creating the Azure virtual network that will host all of the Azure virtual machines included in the deployment.
- Creating an Azure Storage General Purpose v2 account that will be associated with the Azure Center for SAP solutions used for the deployment
- Granting the Microsoft Entra user-assigned managed identity that will be used to perform the deployment access to the Azure subscription and the Azure Storage General Purpose v2 account
- Creating an Azure Premium file shares account that will be used to implement SAP Transport Directory
- Creating and configuring a network security group (NSG) that will be used to restrict outbound access from subnets of the virtual network that will host the deployment.
- Creating an Azure Firewall resource in the virtual network that will host the deployment to restrict outbound access from subnets of the virtual network that will host the deployment.
- Creating and configuring an Azure route table to be used within subnets of the virtual network that will host the deployment to route the traffic via the Azure Firewall resource.
- Creating an Azure Bastion resource to secure connectivity to Azure VMs from the internet.
- Creating an Azure virtual machine (VM) to be used for SAP software installation as part of an Azure Center for SAP solutions deployment.
- Connecting to the Azure VM by using Azure Bastion and configuring it for the SAP software installation. 
- Deleting all Azure resources provisioned in this lab.

These activities correspond to the following tasks of this exercise:

- Task 1: Create a Microsoft Entra user-assigned managed identity
- Task 2: Create the Azure virtual network
- Task 3: Create an Azure Bastion resource
- Task 4: Create an Azure Storage General Purpose v2 account
- Task 5: Configure authorization of the Microsoft Entra user-assigned managed identity
- Task 6: Create an Azure Premium file shares account
- Task 7: Create and configure a network security group
- Task 8: Create an Azure virtual machine
- Task 9: Configure the Azure VM
- Task 10: Remove Azure resources

#### Task 1: Create a Microsoft Entra user-assigned managed identity

In this task, you will create a Microsoft Entra user-assigned managed identity to be used by Azure Center for SAP solutions for Azure Storage access during its deployment.

1. From the lab computer, start a web browser, navigate to the Azure portal at `https://portal.azure.com`, and authenticate by using a Microsoft Account or Microsoft Entra ID account with the Owner role in the Azure subscription you will be using in this lab.
1. In the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Managed Identities**.
1. On the **Managed Identities** page, select **+ Create**.
1. On the **Basics** tab of the **Create User Assigned Managed Identity** page, specify the following settings and then select **Review + Create**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Region|the name of the Azure region which you will use for the ACSS deployment|
   |Name|**acss-infra-MI**|

1. On the **Review** tab, wait for the validation process to complete and select **Create**.

   >**Note**: Do not wait for the provisioning process to complete but instead proceed to the next task. The provisioning should take just a few seconds.

   >**Note**: In one of the upcoming tasks, you will authorize access of the managed identity to the storage account hosting the SAP installation media to accommodate installing SAP software through the Azure Center for SAP solutions.

#### Task 2: Create the virtual network

In this task, you will create the Azure virtual network that will host all of the Azure virtual machines included in the deployment. In addition, within the virtual network, you will create the following subnets:

- AzureFirewallSubnet - intended for deployment of Azure Firewall
- AzureBastionSubnet - intended for deployment of Azure Bastion
- dmz - intended for deployment of the Azure VM that will be used to deploy SAP software
- app - intended for hosting the SAP application and SAP Central Services instance servers
- db - intended for hosting the SAP database tier

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Virtual networks**. 
1. On the **Virtual networks** page, select **+ Create**.
1. On the **Basics** tab of the **Create virtual network** page, specify the following settings and select **Next**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Virtual network name|**acss-infra-VNET**|
   |Region|the name of the same Azure region you used in the previous task of this exercise|

1. On the **Security** tab, accept the default settings and select **Next**.

   >**Note**: You could provision at this point both Azure Bastion and Azure Firewall, however you will provision them separately once the virtual network is created.

1. On the **IP addresses** tab, specify the following settings and then select **Review + create**:

   |Setting|Value|
   |---|---|
   |IP address space|**10.0.0.0/16 (65,536 addresses)**|

1. In the list of subnets, select the trash bin icon to delete the **default** subnet.
1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Subnet purpose|**Azure Firewall**|
   |Starting address|**10.0.0.0**|

   >**Note**: This will automatically assign to the subnet the name **AzureFirewallSubnet** and set its size to **/26 (64 addresses)**.

1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Name|**dmz**|
   |Starting address|**10.0.0.128**|
   |Size|**/26 (64 addresses)**|

   >**Note**: This subnet will be used to host the Azure VM you will use to install the SAP software through the Azure Center for SAP solutions.

1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Subnet purpose|**Azure Bastion**|
   |Starting address|**10.0.1.0**|
   |Size|**/24 (256 addresses)**|

   >**Note**: This will automatically assign to the subnet the name **AzureBastionSubnet**.

1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Name|**app**|
   |Starting address|**10.0.2.0**|
   |Size|**/24 (256 addresses)**|

1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

   |Setting|Value|
   |---|---|
   |Name|**db**|
   |Starting address|**10.0.3.0**|
   |Size|**/24 (256 addresses)**|

1. On the **IP addresses** tab, select **Review + create**:
1. On the **Review + create** tab, wait for the validation process to complete and then select **Create**.

   >**Note**: Do not wait for the provisioning process to complete but instead proceed to the next task. The provisioning should take just a few seconds.

#### Task 3: Create an Azure Bastion resource

In this task, you will create an Azure Bastion resource to secure connectivity to Azure VMs from the internet.

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Bastions**. 
1. On the **Bastions** page, select **+ Create**.
1. On the **Basics** tab of the **Bastions** page, specify the following settings and select **Next : Advanced >**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Name|**acss-infra-BASTION**|
   |Region|the name of the same Azure region you used earlier in this exercise|
   |Tier|**Basic**|
   |Instance count|**2**|
   |Virtual network|**acss-infra-VNET**|
   |Subnet|**AzureBastionSubnet (10.0.1.0/24)**|
   |Public IP address|**Create new**|
   |Public IP address name|**acss-bastion-PIP**|

1. On the **Advanced** tab, review the available options without making any changes and then select **Review + create**.
1. On the **Review + create** tab, wait for the validation process to complete and then select **Create**.

   >**Note**: Do not wait for the provisioning to complete, but instead, proceed to the next task. The provisioning might take about 5 minutes.

#### Task 4: Create an Azure Storage General Purpose v2 account

In this task, you will create an Azure Storage General Purpose v2 account that will be associated with the Azure Center for SAP solutions used for the deployment. This storage account will be used to host the SAP installation media to accommodate installing SAP software through the Azure Center for SAP solutions.

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Storage accounts**.
1. On the **Storage accounts** page, select **+ Create**.
1. On the **Basics** tab of the **Create a storage account** page, specify the following settings and select **Next: Advanced >**.

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of letters and digits|
   |Region|the name of the same Azure region you used earlier in this exercise|
   |Performance|**Standard**|
   |Redundancy|**Geo-redundant storage (GRS)**|
   |Make read access to data available in the event of regional availability|Disabled|

1. On the **Advanced** tab, review the available options, accept the defaults, and select **Next: Networking >**.
1. On the **Networking** tab, perform the following actions and then select **Review**.

   1. Select **Enable public access from selected virtual networks and IP addresses**.
   1. In the **Virtual networks** section, ensure that the **Virtual network subscription** drop-down list displays the name of the Azure subscription you are using in this lab.
   1. In the **Virtual networks** section, in the **Virtual network** drop-down list, select **acss-infra-VNET**.
   1. In the **Subnets** drop-down list, select the **app**, **db**, and **dmz** subnets.

1. On the **Review** tab, wait for the validation process to complete and select **Create**.

   >**Note**: Wait for the provisioning process to complete. The provisioning should take less than 1 minute.

1. On the **Your deployment is complete** page, select **Go to resource**.
1. On the storage account page, in the vertical navigation menu on the left side, in the **Data storage** section, select **Containers**.
1. Select **+ Container**.
1. In the **New container** pane, in the **Name** text box, enter **sapbits** and select **Create**.

   >**Note**: The **sapbits** container will host the SAP installation media.

#### Task 5: Configure authorization of the Microsoft Entra user-assigned managed identity

In this task, you will use an Azure role-based access control (RBAC) role assignment to grant the Microsoft Entra user-assigned managed identity that will be used to perform the deployment access to the Azure subscription and the Azure Storage General Purpose v2 account created in the previous task.

1. In the Azure portal, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Managed Identities**.
1. On the **Managed Identities** page and select the **acss-infra-MI** entry.
1. On the **acss-infra-MI** page, in the vertical navigation menu on the left side, select **Azure role assignments**.
1. On the **Azure role assignments** page, select **+ Add role assignment (Preview)**.
1. On the **+ Add role assignment (Preview)** pane, specify the following settings and select **Save**:

   |Setting|Value|
   |---|---|
   |Scope|**Subscription**|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Role|**Azure Center for SAP solutions service role**|

1. Back on the **Azure role assignments** page, select **+ Add role assignment (Preview)**.
1. On the **+ Add role assignment (Preview)** pane, specify the following settings and select **Save**:

   |Setting|Value|
   |---|---|
   |Scope|**Storage**|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource|The name of the Azure Storage account you created in the previous task|
   |Role|**Reader and Data Access**|

#### Task 6: Create an Azure Premium file shares account

In this task, you will create an Azure Premium file shares account that will be used to implement SAP Transport Directory.

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Storage accounts**.
1. On the **Storage accounts** page, select **+ Create**.
1. On the **Basics** tab of the **Create a storage account** page, specify the following settings and select **Next: Advanced >**.

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of letters and digits|
   |Region|the name of the same Azure region you used earlier in this exercise|
   |Performance|**Premium**|
   |Premium account type|**File shares**|
   |Redundancy|**Zone-redundant storage (ZRS)**|

1. On the **Advanced** tab, disable the **Require secure transfer for REST API operations** setting and select **Next: Networking >**.

   >**Note**: The NFS protocol does not support encryption and relies on network-level security instead. This setting must be disabled for NFS to work.

1. On the **Networking** tab, perform the following actions and then select **Review**.

   1. Select **Enable public access from selected virtual networks and IP addresses**.
   1. In the **Virtual networks** section, ensure that the **Virtual network subscription** drop-down list displays the name of the Azure subscription you are using in this lab.
   1. In the **Virtual networks** section, in the **Virtual network** drop-down list, select **acss-infra-VNET**.
   1. In the **Subnets** drop-down list, select the **app**, **db**, and **dmz** subnets.

   >**Note**: In general, you should avoid allowing access to your internal resources form perimeter subnets. In this case, the only reason to do so is to allow for validating this access later in this lab. 

1. On the **Review** tab, wait for the validation process to complete and select **Create**.

   >**Note**: Wait for the provisioning process to complete. The provisioning should take less than 1 minute.

1. On the **Your deployment is complete** page, select **Go to resource**.
1. On the storage account page, in the vertical navigation menu on the left side, in the **Data storage** section, select **File shares** and then select **+ File share**.
1. On the **Basics** tab of the **New file share** page, specify the following settings and then select **Review + create**:

   |Setting|Value|
   |---|---|
   |Name|**trans**|
   |Provisioned capacity|**128**|
   |Protocol|**NFS**|
   |Root Squash|*No Root Squash**|

1. On the **Review + create** tab, wait for the validation process to complete and then select **Create**.

   >**Note**: Wait for the provisioning of the file share to complete. The provisioning should take just a few seconds.

1. On the **Connect to this NFS share from Linux** page, in the **Select your linux distribution** drop-down list, select **SUSE** in the Linux distribution drop-down list, and review the sample commands to mount this NFS share.

#### Task 7: Create and configure a network security group

In this task, you will create and configure a network security group (NSG) that will be used to restrict outbound access from subnets of the virtual network that will host the deployment. You can accomplish this by blocking connectivity the internet but explicitly allowing connections to the following services:

- SUSE or Red Hat update infrastructure endpoints
- Azure Storage
- Azure Key Vault
- Microsoft Entra ID
- Azure Resource Manager

>**Note**: In general, you should consider using Azure Firewall instead of NSGs to secure network connectivity for your SAP deployment. This lab covers both options.

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Network security groups**.
1. On the **Network security groups** page, select **+ Create**.
1. On the **Basics** tab of the **Create network security group** page, specify the following settings and then select **Review + create**:

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|The name of a **new** resource group **acss-infra-RG**|
   |Name|**acss-infra-NSG**|
   |Region|the name of the same Azure region you used earlier in this exercise|

1. On the **Review + create** tab, wait for the validation process to complete and select **Create**.

   >**Note**: Wait for the provisioning process to complete. The provisioning should take less than 1 minute.

1. On the **Your deployment is complete** page, select **Go to resource**.

   >**Note**: By default, the built-in rules of network security groups allow all outbound traffic, all traffic within the same virtual network, as well as all traffic between peered virtual networks. From the security standpoint, you should consider restricting this default behavior. The proposed configuration restricts outbound connectivity to the internet and Azure. You can also use NSG rules to restrict connectivity within a virtual network.

1. On the **acss-infra-NSG** page, in the vertical navigation menu on the left side, in the **Settings** section, select **Outbound security rules**.
1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. On the **Add outbound security rule** pane, specify the following settings and select **Add**:

   >**Note**: The following rule should be added to explicitly allow connectivity to Red Hat update infrastructure endpoints.

   >**Note**: To identify the IP addresses to use for RHEL, refer to [Prepare network for infrastructure deployment](https://learn.microsoft.com/en-us/azure/sap/center-sap-solutions/prepare-network#allowlist-suse-or-red-hat-endpoints)

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**IP addresses**|
   |Destination IP addresses/CIDR ranges|**13.91.47.76,40.85.190.91,52.187.75.218,52.174.163.213,52.237.203.198**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**300**|
   |Name|**AllowAnyRHELOutbound**|
   |Description|**Allow outbound connectivity to RHEL update infrastructure endpoints**|

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   >**Note**: The following rule should be added to explicitly allow connectivity to SUSE update infrastructure endpoints.

   >**Note**: To identify the IP addresses to use for SUSE, refer to [Prepare network for infrastructure deployment](https://learn.microsoft.com/en-us/azure/sap/center-sap-solutions/prepare-network#allowlist-suse-or-red-hat-endpoints)

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**IP addresses**|
   |Destination IP addresses/CIDR ranges|**52.188.224.179,52.186.168.210,52.188.81.163,40.121.202.140**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**305**|
   |Name|**AllowAnySUSEOutbound**|
   |Description|**Allow outbound connectivity to SUSE update infrastructure endpoints**|

   >**Note**: The following rule should be added to explicitly allow connectivity to Azure Storage.

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Service Tag**|
   |Destination service tag|**Storage**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**400**|
   |Name|**AllowAnyCustomStorageOutbound**|
   |Description|**Allow outbound connectivity to Azure Storage**|

   >**Note**: You could replace the **Storage** service tag with one which is region-specific, such as **Storage.EastUS**.

   >**Note**: The following rule should be added to explicitly allow connectivity to Azure Key Vault.

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Service Tag**|
   |Destination service tag|**AzureKeyVault**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**500**|
   |Name|**AllowAnyCustomKeyVaultOutbound**|
   |Description|**Allow outbound connectivity to Azure Key Vault**|

   >**Note**: The following rule should be added to explicitly allow connectivity to Microsoft Entra ID.

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Service Tag**|
   |Destination service tag|**AzureActiveDirectory**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**600**|
   |Name|**AllowAnyCustomEntraIDOutbound**|
   |Description|**Allow outbound connectivity to Microsoft Entra ID**|

   >**Note**: The following rule should be added to explicitly allow connectivity to Azure Resource Manager.

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Service Tag**|
   |Destination service tag|**AzureResourceManager**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Allow**|
   |Priority|**700**|
   |Name|**AllowAnyCustomARMOutbound**|
   |Description|**Allow outbound connectivity to Azure Resource Manager**|

   >**Note**: The last rule should be added to explicitly block connectivity to the internet.

1. On the **acss-infra-NSG \| Outbound security rules** page, select **+ Add**.
1. In the **Add outbound security rule** pane, specify the following settings and select **Add**:

   |Setting|Value|
   |---|---|
   |Source|**Any**|
   |Source port ranges|*|
   |Destination|**Service Tag**|
   |Destination service tag|**Internet**|
   |Service|**Custom**|
   |Destination port ranges|*|
   |Protocol|**Any**|
   |Action|**Deny**|
   |Priority|**1000**|
   |Name|**DenyAnyCustomInternetOutbound**|
   |Description|**Deny outbound connectivity to Internet**|

   >**Note**: Finally, you need to assign the NSG to the relevant subnets of the virtual network that will host the SAP deployment.

1. In the **Add outbound security rule** pane, in the vertical navigation menu on the left side, in the **Settings** section, select **Subnets**.
1. On the **acss-infra-NSG \| Subnets** page, select **+ Associate**.
1. In the **Associate subnet** pane, in the **Virtual network** drop-down list, select **acss-intra-VNET (acss-infra-RG)**, in the **Subnet** drop-down list, select **app**, and then select **OK**.
1. In the **Associate subnet** pane, in the **Virtual network** drop-down list, select **acss-intra-VNET (acss-infra-RG)**, in the **Subnet** drop-down list, select **db**, and then select **OK**.

#### Task 8: Create an Azure virtual machine

In this task, you will create an Azure virtual machine (VM) that will be used for SAP software installation as part of an Azure Center for SAP solutions deployment. 

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Virtual machines**. 
1. On the **Virtual machines** page, select **+ Create** and, in the drop-down menu, select **Azure virtual machine**.
1. On the **Basics** tab of the **Create a virtual machine** page, specify the following settings and select **Next: Disks >** (leave all other settings with their default value):

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|**acss-infra-RG**|
   |Virtual machine name|**acss-infra-vm0**|
   |Region|the name of the same Azure region you used earlier in this exercise|
   |Availability options|**No infrastructure redundancy required**|
   |Security type|**Trusted launch virtual machine**|
   |Image|**Ubuntu Server 20.04 LTS - x64 Gen2**|
   |VM architecture|**x64**|
   |Run with Azure Spot Discount|disabled|
   |Size|**Standard_B2ms**|
   |Authentication type|**Password**|
   |Username|any valid user name|
   |Password|any complex password of your choice|
   |Public inbound ports|**None**|
   
    > **Note**: Make sure you remember the username and password you specified. You will need it later in this lab.

1. On the **Disks** tab, accept the default values and select **Next: Networking >**.
1. On the **Networking** tab, specify the following settings and select **Next: Management >** (leave all other settings with their default value):

   |Setting|Value |
   |---|---|
   |Virtual network|**acss-infra-VNET**|
   |Subnet|**dmz**|
   |Public IP address|**None**|
   |NIC network security group|**None**|
   |Delete NIC when VM is deleted|enabled|
   |Load balancing Options|**None**|

1. On the **Management** tab, leave all settings with their default value and select **Next: Monitoring >**
1. On the **Monitoring** tab, set **Boot diagnostics** to **Disable** and select **Next: Advanced >** (leave all other settings with their default value)
1. On the **Advanced** tab, select **Review + create** (leave all settings with their default value).
1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. The provisioning might take about 3 minutes.

#### Task 9: Configure the Azure VM

In this task, you will connect to the Azure VM by using Azure Bastion and configure it for the SAP software installation. 

> **Note**: Before you start this task, ensure that the Azure Bastion provisioning has completed.

> **Note**: Ensure that your web browser does not block pop-up windows and, if so, disable the pop-up blocker functionality.

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Virtual machines**. 
1. On the **Virtual machines** page, select the **acss-infra-vm0** entry.
1. On the **acss-infra-vm0** page, in the toolbar, select **Connect** and, in the drop-down menu, select **Connect via Bastion**.
1. On the **acss-infra-vm0 \| Bastion** page, ensure that the **Authentication Type** is set to **VM Password**, in the **Username** and **Password** text boxes, enter the username and password you set when provisioning the Azure VM, ensure that the **Open in new browser tab** checkbox is enabled, and then select **Connect**.

   > **Note**: This should open another web browser window tab displaying the shell session running in the Azure VM.

   > **Note**: To prepare the Ubuntu server for the upload of SAP installation media, you will install Azure CLI.

1. In the newly opened browser tab, within the shell session, run the following command to install Azure CLI:

   ```bash
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
   ```

1. Within the shell session, run the following command to install PIP3:

   ```bash
   sudo apt install python3-pip
   ```

1. Within the shell session, run the following command to install Ansible 2.13.19:

   ```bash
   sudo pip3 install ansible-core==2.13.9
   ```

1. Within the shell session, run the following command to install Ansible galaxy collection modules:

   ```bash
   sudo ansible-galaxy collection install ansible.netcommon:==5.0.0 -p /opt/ansible/collections
   sudo ansible-galaxy collection install ansible.posix:==1.5.1 -p /opt/ansible/collections
   sudo ansible-galaxy collection install ansible.utils:==2.9.0 -p /opt/ansible/collections
   sudo ansible-galaxy collection install ansible.windows:==1.13.0 -p /opt/ansible/collections
   sudo ansible-galaxy collection install community.general:==6.4.0 -p /opt/ansible/collections
   ```

1. Within the shell session, run the following command to clone the SAP automation samples repository from GitHub:

   ```bash
   git clone https://github.com/Azure/SAP-automation-samples.git
   ```

1. Within the shell session, run the following command to clone the SAP automation repository from GitHub:

   ```bash
   git clone https://github.com/Azure/sap-automation.git
   ```

1. Within the shell session, run the following command to terminate the session:

   ```bash
   logout
   ```

1. When prompted, select **Close**.

#### Task 10: Remove Azure resources

In this task, you will remove all Azure resources provisioned in this lab.

1. On the lab computer, in the web browser window displaying the Azure portal, select the **Cloud Shell** icon to open the Cloud Shell pane. If needed, select **Bash** to start a Bash shell session. 

   > **Note**: If this is the first time you are launching Cloud Shell in the Azure subscription you were using in this lab, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1. In the Cloud Shell pane, run the following command to delete the resource group **acss-infra-RG** and all of its resources.

   ```cli
   az group delete --name 'acss-infra-RG' --no-wait --yes
   ```

   > **Note**: The command executes asynchronously (as determined by the --nowait parameter), so while the shell prompt will appear immediately after invoking it, a few minutes will pass before the resource group and its resources are actually removed.

1. Close the Cloud Shell pane.
