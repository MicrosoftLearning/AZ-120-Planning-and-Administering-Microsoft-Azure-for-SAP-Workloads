---
lab:
    title: '01b - Implement Windows clustering on Azure VMs'
    module: 'Module 01 - Explore the foundations of IaaS for SAP on Azure'
---

# AZ 120 Module 1: Explore the foundations of IaaS for SAP on Azure
# Lab 1b: Implement Windows clustering on Azure VMs

Estimated Time: 120 minutes

All tasks in this lab are performed from the Azure portal (including the PowerShell Cloud Shell session)  

   > **Note**: When not using Cloud Shell, the lab virtual machine must have Az PowerShell module installed [**https://docs.microsoft.com/en-us/powershell/azure/install-az-ps-msi**](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps-msi).

Lab files: none

## Scenario
  
In preparation for deployment of SAP NetWeaver on Azure, with SQL Server as the database management system, Adatum Corporation wants to explore the process of implementing clustering on Azure VMs running Windows Server 2022.

## Objectives
  
After completing this lab, you will be able to:

-   Provision Azure compute resources necessary to support highly available SAP NetWeaver deployments.

-   Configure operating system of Azure VMs running Windows Server 2022 to support a highly available SAP NetWeaver deployment.

-   Provision Azure network resources necessary to support highly available SAP NetWeaver deployments.

## Requirements

-   A Microsoft Azure subscription with the sufficient number of available DSv2 and Dsv3 vCPUs (one Standard_DS1_v2 VM with 1 vCPU and four Standard_D4s_v3 VMs with 4 vCPUs each) in the Azure region you intend to use for this lab

-   A lab computer with an Azure Cloud Shell-compatible web browser and access to Azure

> **Note**: Make sure that the Azure region you choose for deployment of your resources supports availability zones. For the list of such regions, refer to (https://docs.microsoft.com/en-us/azure/availability-zones/az-overview). Consider using **East US** or **East US2**.

## Exercise 1: Provision Azure compute resources necessary to support highly available SAP NetWeaver deployments

Duration: 50 minutes

In this exercise, you will deploy Azure infrastructure compute components necessary to configure Failover Clustering on Azure VMs running Windows Server 2022. This will involve deploying a pair of Active Directory domain controllers, followed by a pair of Azure VMs running Windows Server 2022. Each pair of the VMs will be placed in separate availability zones within the same virtual network. To automate the deployment of domain controllers, you will use an Azure Resource Manager QuickStart template available from <https://aka.ms/az120-1bdeploy>

### Task 1: Deploy a pair of Azure VMs running highly available Active Directory domain controllers by using a Bicep template

1. From the lab computer, start a Web browser, and navigate to the Azure portal at **https://portal.azure.com**.

1. If prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you will be using for this lab.

1. In the Azure Portal, start a PowerShell session in Cloud Shell. 

    > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1. In the Cloud Shell pane, run the following commands to create a shallow clone of the repository hosting the Bicep template you will use for deployment of a pair of Azure VMs running highly available Active Directory domain controllers and set the current directory to the location of that template and its parameter file:

    ```
    cd $HOME
    rm ./azure-quickstart-templates -rf
    git clone --depth 1 https://github.com/polichtm/azure-quickstart-templates
    cd ./azure-quickstart-templates/application-workloads/active-directory/active-directory-new-domain-ha-2-dc-zones/
    ```

1. In the Cloud Shell pane, run the following command to set the value of the variable `$rgName` to `az12001b-ad-RG`:

    ```
    $rgName = 'az12001b-ad-RG'
    ```

1. In the Cloud Shell pane, run the following command to set the value of the variable `$location` to the name of the Azure regions which supports availability zones and where you intend to deploy the lab VMs (replace the `<Azure_region>` placeholder with the name of that region):

    ```
    $location = '<Azure_region>'
    ```

1. In the Cloud Shell pane, run the following command to create a resource group named **az12001b-ad-RG** in the Azure region you chose:

    ```
    New-AzResourceGroup -Name $rgName -Location $location
    ```

1. In the Cloud Shell pane, run the following command to set the value of the variable `$deploymentName`:

    ```
    $deploymentName = 'az1201b-' + $(Get-Date -Format 'yyyy-MM-dd-hh-mm')
    ```

1. In the Cloud Shell pane, run the following commands to set the name of the administrative user account and its password (replace the `<username>` and `<password>` placeholders with the name of the administrative user account and the value of its password, respectively):

    ```
    $adminUsername = '<username>'
    $adminPassword = ConvertTo-SecureString '<password>' -AsPlainText -Force
    ```

    > **Note**: Make sure that the password satisfies the complexity requirements applicable to deployment of Azure VMs running Windows (the lenght of at least 12 characters containing lower and upper case letters, digits, and special characters).

1. In the Cloud Shell pane, run the following command to run the deployment:

    ```
    New-AzResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rgName -TemplateFile .\main.bicep -TemplateParameterFile .\azuredeploy.parameters.json -adminUsername $adminUsername -adminPassword $adminPassword -c
    ```

1. Review the output of the command and verify that it does not include any errors and warnings. When prompted, press the **Enter** key to proceed with the deployment.

    > **Note**: The deployment should take about 30 minutes. Wait for the deployment to complete before you proceed to the next task.

    > **Note**: If the deployment fails with an error including the statement `PowerShell DSC resource MSFT_xADDomainController failed to execute Set-TargetResource functionality with error message: Domain 'adatum.com' could not be found`, use the following steps to remediate this issue:

    - In the Azure portal, navigate to the blade of the **adVNET**, in the vertical navigation menu on the left side, in the **Settings** section, select **DNS servers**, on the **adVNET \| DNS servers** page, delete the **10.0.0.5** entry and then select **Save**.
      
    - Navigate to the blade of the **adBDC** VM, in the vertical navigation menu on the left side, in the **Settings** section, select **Extensions + applications**, in the **Extensions + applications** pane, select **PrepareBDC**, and, in the **Prepare BDC** pane, select **Uninstall**. 

    - Navigate back to the **adBDC** VM blade and restart the Azure VM.

    - Navigate to the **az1201b-ad-RG** blade, in the vertical navigation menu on the left side, in the **Settings** section, select **Deployments**.

    - On the **az1201b-ad-RG \| Deployments** blade, select the deployment which name starts with the **az1201b** prefix and, on the deployment blade, select **Redeploy**.

    - On the **Custom deployment** blade, in the **Admin Password** text box, enter the same password you used during the original deployment, select **Review + create**, and then select **Create**.

    - Do not wait for redeployment to complete but instead proceed to the next task. The redeployment should take about 3 minutes.

### Task 2: Deploy a pair of Azure VMs running Windows Server 2022 into different availability zones

1. From the lab computer, in the Azure portal, navigate to the **Virtual machines** blade, click **+ Create**, and, from the drop-down menu, select **Azure virtual machine**.

1. From the **Create a virtual machine** blade, initiate provisioning of a **Windows Server 2022 Datacenter: Azure Edition - Gen2** Azure VM with the following settings (leave all others with their default values):
    
    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription*  |
    | **Resource group** | *the name of a new resource group* **az12001b-cl-RG** |
    | **Virtual machine name** | **az12001b-cl-vm0** |
    | **Region** | *the same Azure region where you deployed the Azure VMs in the previous task* |
    | **Availability options** | **Availability zone** |
    | **Availability zone** | **Zone 1** |
    | **Security type** | **Standard** |
    | **Image** | *select* **Windows Server 2022 Datacenter: Azure Edition - Gen2** |
    | **Size** | **Standard D4s v3** |
    | **Username** | *the same username you specified when deploying the Bicep template earlier in this exercise* |
    | **Password** | *the same password you specified when deploying the Bicep template earlier in this exercise* |
    | **Public inbound ports** | **None** |
    | **Would you like to use an existing Windows Server license?** | **No** |
    | **OS disk type** | **Premium SSD** |
    | **Virtual network** | **adVNET** |
    | **Subnet name** | *a new subnet named* **clSubnet** |
    | **Subnet address range** | **10.0.1.0/24** |
    | **Public IP address** | **None** |
    | **NIC network security group** | **None**  |
    | **Enable accelerated networking** | **On** |
    | **Load balancing Options** | **None** |
    | **Enable system assigned managed identity** | **Off** |
    | **Login with Azure AD** | **Off** |
    | **Enable auto-shutdown** | **Off** |
    | **Patch orchestration option** | **Manual Updates** |
    | **Boot diagnostics** | **Disable** |
    | **Extensions** | *None* |
    | **Tags** | *None* |

1. Do not wait for the provisioning to complete but continue to the next step.

1. Provision another **Windows Server 2022 Datacenter: Azure Edition - Gen2** Azure VM with the following settings:
     
    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription*  |
    | **Resource group** | *the name of the resource group you used when deploying the first **Windows Server 2022 Datacenter: Azure Edition - Gen2** Azure VM in this task* |
    | **Virtual machine name** | **az12001b-cl-vm1** |
    | **Region** | *the same Azure region where you deployed the first **Windows Server 2022 Datacenter: Azure Edition - Gen2** Azure VM in this task* |
    | **Availability options** | **Availability zone** |
    | **Availability zone** | **Zone 2** |
    | **Security type** | **Standard** |
    | **Image** | *select* **Windows Server 2022 Datacenter: Azure Edition - Gen2** |
    | **Size** | **Standard D4s v3** |
    | **Username** | *the same username you specified when deploying the Bicep template earlier in this exercise* |
    | **Password** | *the same password you specified when deploying the Bicep template earlier in this exercise* |
    | **Public inbound ports** | **None** |
    | **Would you like to use an existing Windows Server license?** | **No** |
    | **OS disk type** | **Premium SSD** |
    | **Virtual network** | **adVNET** |
    | **Subnet name** | **clSubnet** |
    | **Public IP address** | **None** |
    | **NIC network security group** | **None**  |
    | **Enable accelerated networking** | **On** |
    | **Load balancing Options** | **None** |
    | **Login with Azure AD** | **Off** |
    | **Enable auto-shutdown** | **Off** |
    | **Patch orchestration option** | **Manual Updates** |
    | **Boot diagnostics** | **Disable** |
    | **Extensions** | *None* |
    | **Tags** | *None* |

1. Wait for the provisioning to complete. This should take a few minutes.

### Task 3: Create and configure Azure VMs disks

1. In the Azure Portal, start a PowerShell session in Cloud Shell. 

1. In the Cloud Shell pane, run the following command to set the value of the variable `$resourceGroupName` to the name of the resource group containing the resources you provisioned in the previous task:

    ```
    $resourceGroupName = 'az12001b-cl-RG'
    ```

1. In the Cloud Shell pane, run the following command to create the first set of 4 managed disks that you will attach to the first Azure VM you deployed in the previous task:

    ```
    $location = (Get-AzResourceGroup -Name $resourceGroupName).Location
    
    $zone = (Get-AzVM -ResourceGroupName $resourceGroupName -Name 'az12001b-cl-vm0').Zones

    $diskConfig = New-AzDiskConfig -Location $location -DiskSizeGB 128 -AccountType Premium_LRS -OsType Windows -CreateOption Empty -Zone $zone

    for ($i=0;$i -lt 4;$i++) {New-AzDisk -ResourceGroupName $resourceGroupName -DiskName az12001b-cl-vm0-DataDisk$i -Disk $diskConfig}
    ```

1. In the Cloud Shell pane, run the following command to create the second set of 4 managed disks that you will attach to the second Azure VM you deployed in the previous task:

    ```
    $zone = (Get-AzVM -ResourceGroupName $resourceGroupName -Name 'az12001b-cl-vm1').Zones
    
    $diskConfig = New-AzDiskConfig -Location $location -DiskSizeGB 128 -AccountType Premium_LRS -OsType Windows -CreateOption Empty -Zone $zone
        
    for ($i=0;$i -lt 4;$i++) {New-AzDisk -ResourceGroupName $resourceGroupName -DiskName az12001b-cl-vm1-DataDisk$i -Disk $diskConfig}
    ```

1. In the Azure portal, navigate to the blade of the first Azure VM you provisioned in the previous task (**az12001b-cl-vm0**).

1. From the **az12001b-cl-vm0** blade, navigate to the **az12001b-cl-vm0 - Disks** blade.

1. From the **az12001b-cl-vm0 - Disks** blade, attach data disks with the following settings to az12001b-cl-vm0:
   
   | Setting | Value |
   |   --    |  --   |
   | **LUN** | **0** |
   | **Disk name** | **az12001b-cl-vm0-DataDisk0** |
   | **Resource group** | *the name of the resource group you used when deploying the pair of **Windows Server 2022 Datacenter** Azure VMs in the previous task* |
   | **HOST CACHING** | **Read-only** |

1. Repeat the previous step to attach the remaining 3 disks with the prefix **az12001b-cl-vm0-DataDisk** (for the total of 4). Assign the LUN number matching the last character of the disk name. For the last disk (LUN **3**), set HOST CACHING to **None**.

1. Save your changes. 

1. In the Azure portal, navigate to the blade of the second Azure VM you provisioned in the previous task (**az12001b-cl-vm1**).

1. From the **az12001b-cl-vm1** blade, navigate to the **az12001b-cl-vm1 - Disks** blade.

1. From the **az12001b-cl-vm1 - Disks** blade, attach data disks with the following settings to az12001b-cl-vm1:
     
   | Setting | Value |
   |   --    |  --   |
   | **LUN** | **0** |
   | **Disk name** | **az12001b-cl-vm1-DataDisk0** |
   | **Resource group** | *the name of the resource group you used when deploying the pair of **Windows Server 2022 Datacenter** Azure VMs in the previous task* |
   | **HOST CACHING** | **Read-only** |

1. Repeat the previous step to attach the remaining 3 disks with the prefix **az12001b-cl-vm1-DataDisk** (for the total of 4). Assign the LUN number matching the last character of the disk name. For the last disk (LUN **3**), set HOST CACHING to **None**.

1. Save your changes. 

#### Task 4: Provision Azure Bastion 

> **Note**: Azure Bastion allows for connection to the Azure VMs (which you deployed in the previous task of this exercise) without using public endpoints, while providing protection against brute force exploits that target operating system level credentials.

> **Note**: To use Azure Bastion, ensure that your browser has the pop-up functionality enabled.

1. In the browser window displaying the Azure portal, open another tab and, in the browser tab, navigate to the [**Azure portal**](https://portal.azure.com).
1. In the Azure portal, open **Cloud Shell** pane by selecting on the toolbar icon directly to the right of the search textbox.
1. From the PowerShell session in the Cloud Shell pane, run the following to add a subnet named **AzureBastionSubnet** to the virtual network named **az12001a-RG-vnet** you created earlier in this exercise:

   ```powershell
   $resourceGroupName = 'az12001b-cl-RG'
   $vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroupName -Name 'adVNET'
   $subnetConfig = Add-AzVirtualNetworkSubnetConfig `
     -Name 'AzureBastionSubnet' `
     -AddressPrefix 10.0.255.0/24 `
     -VirtualNetwork $vnet
   $vnet | Set-AzVirtualNetwork
   ```

1. Close the Cloud Shell pane.
1. In the Azure portal, search for and select **Bastions** and, from the **Bastions** blade, select **+ Create**.
1. On the **Basic** tab of the **Create a Bastion** blade, specify the following settings and select **Review + create**:

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|**az12001b-cl-RG**|
   |Name|**az12001b-bastion**|
   |Region|the same Azure region to which you deployed the resources in the previous tasks of this exercise|
   |Tier|**Basic**|
   |Virtual network|**adVNET**|
   |Subnet|**AzureBastionSubnet (10.0.255.0/24)**|
   |Public IP address|**Create new**|
   |Public IP name|**adVNET-ip**|

1. On the **Review + create** tab of the **Create a Bastion** blade, select **Create**:

   > **Note**: Do not wait for the deployment to complete but instead you proceed to the next task of this exercise. The deployment might take about 5 minutes.

> **Result**: After you completed this exercise, you have provisioned Azure compute resources necessary to support highly available SAP NetWeaver deployments.

## Exercise 2: Configure operating system of Azure VMs running Windows Server 2022 Datacenter to support a highly available SAP NetWeaver installation

Duration: 40 minutes

### Task 1: Join Windows Server 2022 Datacenter VMs to the Active Directory domain.

   > **Note**: Before you start this task, ensure that the template deployment you initiated in the last task of the previous exercise has successfully completed. 

1. In the Azure Portal, navigate to the blade of the virtual network **adVNET**, which was provisioned automatically in the first exercise of this lab.

1. Display the **adVNET - DNS servers** blade and notice that the virtual network is configured with the private IP addresses assigned to the domain controllers deployed in the first exercise of this lab as its DNS servers.

1. In the Azure Portal, start a PowerShell session in Cloud Shell. 

1. In the Cloud Shell pane, run the following command to set the value of the variable `$resourceGroupName` to the name of the resource group containing the pair of **Windows Server 2022 Datacenter: Azure Edition - Gen2** Azure VMs you provisioned in the previous exercise:

    ```
    $resourceGroupName = 'az12001b-cl-RG'
    ```

1. In the Cloud Shell pane, run the following command to join the Windows Server 2022 Azure VMs you deployed in the second task of the previous exercise to the **adatum.com** Active Directory domain (replace the `<username>` and `<password>` placeholders with the name and password of the administrative user account you specified when deploying the Bicep template in the first exercise of this lab):

    ```
    $location = (Get-AzureRmResourceGroup -Name $resourceGroupName).Location

    $settingString = '{"Name": "adatum.com", "User": "adatum.com\\<username>", "Restart": "true", "Options": "3"}'

    $protectedSettingString = '{"Password": "<password>"}'

    $vmNames = @('az12001b-cl-vm0','az12001b-cl-vm1')

    foreach ($vmName in $vmNames) { Set-AzVMExtension -ResourceGroupName $resourceGroupName -ExtensionType 'JsonADDomainExtension' -Name 'joindomain' -Publisher "Microsoft.Compute" -TypeHandlerVersion "1.0" -Vmname $vmName -Location $location -SettingString $settingString -ProtectedSettingString $protectedSettingString }
    ```

1. Wait for the script to complete before proceeding to the next task.

### Task 2: Configure storage on Azure VMs running Windows Server 2022 to support a highly available SAP NetWeaver installation.

1. In the Azure Portal, navigate to the blade of the virtual virtual machine **az12001b-cl-vm0**, which you provisioned in the first exercise of this lab.

1. On the **az12001b-cl-vm0** blade, select **Connect**, in the drop-down menu, select **Connect via Bastion**, on the **Bastion** tab of the **az12001b-cl-vm0**, leave the **Authentication type** set to **VM Password**, provide the credentials you set when deploying the **az12001b-cl-vm0** virtual machine, leave the checkbox **Open in new browser tab** enabled, and select **Connect**.

    > **Note**: Make sure to sign in using the **ADATUM** domain account, rather than the operating system level account (i.e. ensure that the user name is preceded by the **ADATUM\\** prefix.

1. Within the Bastion session to az12001b-cl-vm0, in Server Manager, navigate to the **File and Storage Services** -> **Servers** node. 

1. Navigate to the **Storage Pools** view and verify that you see all the disks you attached to the Azure VM in the previous exercise.

1. Use the **New Storage Pools Wizard** to create a new storage pool with the following settings:
     
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **Data Storage Pool** |
    | **Physical Disks** | *select the 3 disks with disk numbers corresponding to the first three LUN numbers (0-2) and set their allocation to* **Automatic** |

    > **Note**: Use the entry in the **Chassis** column to identify the **LUN** number.

1. Use the **New Virtual Disk Wizard** to create a new virtual disk with the following settings:
     
    | Setting | Value |
    |   --    |  --   |
    | **Virtual Disk Name** | **Data Virtual Disk** |
    | **Storage Layout** | **Simple** |
    | **Provisioning** | **Fixed** |
    | **Size** | **Maximum size** |

1. Use the **New Volume Wizard** to create a new volume with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Server and Disk** | *accept the default values* |
    | **Size** | *accept the default values* |
    | **Drive letter** | **M** |
    | **File system** | **ReFS** |
    | **Allocation unit size** | **Default** |
    | **Volume label** | **Data** |

1. Back in the **Storage Pools** view, use the **New Storage Pools Wizard** to create a new storage pool with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **Log Storage Pool** |
    | **Physical Disks** | *select the last of 4 disks and set its allocation to* **Automatic** |

1. Use the **New Virtual Disk Wizard** to create a new virtual disk with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Virtual Disk Name** | **Log Virtual Disk** |
    | **Storage Layout** | **Simple** |
    | **Provisioning** | **Fixed** |
    | **Size** | **Maximum size** |

1. Use the **New Volume Wizard** to create a new volume with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Server and Disk** | *accept the default values* |
    | **Size** | *accept the default values* |
    | **Drive letter** | **L** |
    | **File system** | **ReFS** |
    | **Allocation unit size** | **Default** |
    | **Volume label** | **Log** |

1. Repeat the previous step in this task to configure storage on az12001b-cl-vm1.

### Task 3: Prepare for configuration of Failover Clustering on Azure VMs running Windows Server 2022 to support a highly available SAP NetWeaver installation.

1. Within the Bastion session to az12001b-cl-vm0, start a Windows PowerShell ISE session and install Failover Clustering and Remote Administrative tools features on both az12001b-cl-vm0 and az12001b-cl-vm1 by running the following:

    ```
    $nodes = @('az12001b-cl-vm1', 'az12001b-cl-vm0')

    Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools} 

    Invoke-Command $nodes {Install-WindowsFeature RSAT -IncludeAllSubFeature -Restart} 
    ```

    > **Note**: This will result in restart of the guest operating system of both Azure VMs.

1. On the lab computer, in the Azure Portal, click **+ Create a resource**.

1. From the **New** blade, initiate creation of a new **Storage account** with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription* |
    | **Resource group** | *the name of the resource group containing the pair of **Windows Server 2022 Datacenter** Azure VMs you provisioned in the previous exercise* |
    | **Storage account name** | *any unique name consisting of between 3 and 24 letters and digits* |
    | **Location** | *the same Azure region where you deployed the Azure VMs in the previous exercise* |
    | **Performance** | **Standard** |
    | **Redundancy** | **Locally-redundant storage (LRS)** |
    | **Connectivity method** | **Public endpoint (all networks)** |
    | **Require secure transfer for REST API operations** | **Enabled** |
    | **Large file shares** | **Disabled** |
    | **Soft delete for blobs, containers, and files** | **Disabled** |
    | **Hierarchical namespace** | **Disabled** |

### Task 4: Configure Failover Clustering on Azure VMs running Windows Server 2022 to support a highly available SAP NetWeaver installation.

1. In the Azure Portal, navigate to the blade of the virtual virtual machine **az12001b-cl-vm0**, which you provisioned in the first exercise of this lab.

1. From the **az12001b-cl-vm0** blade, connect to the virtual machine guest operating system by using Azure Bastion. When prompted to authenticate, provide the credentials of the administrative user account you specified when deploying the Bicep template in the first exercise of this lab.

1. Within the Bastion session to az12001b-cl-vm0, from the **Tools** menu in Server Manager, start **Active Directory Administrative Center**.

1. In Active Directory Administrative Center, create a new organizational unit named **Clusters** in the root of the adatum.com domain.

1. In Active Directory Administrative Center, move the computer accounts of **az12001b-cl-vm0** and **az12001b-cl-vm1** from the **Computers** container to the **Clusters** organizational unit.

1. Within the Bastion session to az12001b-cl-vm0, start a Windows PowerShell ISE session and create a new cluster by running the following:

    ```
    $nodes = @('az12001b-cl-vm0','az12001b-cl-vm1')

    New-Cluster -Name az12001b-cl-cl0 -Node $nodes -NoStorage -StaticAddress 10.0.1.6
    ```

1. Within the Bastion session to az12001b-cl-vm0, switch to the **Active Directory Administrative Center** console.

1. In Active Directory Administrative Center, navigate to the **Clusters** organizational unit and display its **Properties** window. 

1. In the **Clusters** organizational unit **Properties** window, navigate to the **Extensions** section, display the **Security** tab. 

1. On the **Security** tab, click the **Advanced** button to open the **Advanced Security Settings for Clusters** window. 

1. On the **Permissions** tab of the **Advanced Security Settings for Clusters** window, click **Add**.

1. In the **Permission Entry for Clusters** window, click **Select Principal**

1. In the **Select User, Service Account or Group** dialog box, click **Object Types**, enable the checkbox next to the **Computers** entry, and click **OK**. 

1. Back in the **Select User, Computer, Service Account or Group** dialog box, in the **Enter the object name to select**, type **az12001b-cl-cl0** and click **OK**.

1. In the **Permission Entry for Clusters** window, ensure that **Allow** appears in the **Type** drop-down list. Next, in the **Applies to** drop-down list, select **This object and all descendant objects**. In the **Permissions** list, select the **Create Computer objects** and **Delete Computer objects** checkboxes, and click **OK** twice.

1. Within the Windows PowerShell ISE session, install the Az PowerShell module by running the following:

    ```
    Install-PackageProvider -Name NuGet -Force

    Install-Module -Name Az -Force
    ```

1. Within the Windows PowerShell ISE session, authenticate by using your Azure AD credentials by running the following:

    ```
    Add-AzAccount
    ```

    > **Note**: When prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you are using for this lab.

1. Within the Windows PowerShell ISE session, set the Cloud Witness quorum of the new cluster by running the following:

    ```
    $resourceGroupName = 'az12001b-cl-RG'

    $cwStorageAccountName = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName)[0].StorageAccountName

    $cwStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $cwStorageAccountName).Value[0]

    Set-ClusterQuorum -CloudWitness -AccountName $cwStorageAccountName -AccessKey $cwStorageAccountKey
    ```

1. To verify the resulting configuration, within the Bastion session to az12001b-cl-vm0, from the **Tools** menu in Server Manager, start **Failover Cluster Manager**.

1. In the **Failover Cluster Manager** console, review the **az12001b-cl-cl0** cluster configuration, including its nodes, as well as is witness and network settings. Notice that the cluster does not have any shared storage.

1. Terminate the Bastion session to az12001b-cl-vm0.

> **Result**: After you completed this exercise, you have configured operating system of Azure VMs running Windows Server 2022 to support a highly available SAP NetWeaver installation


## Exercise 3: Provision Azure network resources necessary to support highly available SAP NetWeaver deployments

Duration: 30 minutes

In this exercise, you will implement Azure Load Balancers to accommodate clustered installations of SAP NetWeaver.

### Task 1: Configure Azure VMs to facilitate load balancing setup.

   > **Note**: Since you will be setting up a pair of Azure Load Balancer of the Stardard SKU, you need to first remove the public IP addresses associated with network adapters of two Azure VMs that will be serving as the load-balanced backend pool.

1. On the lab computer, in the Azure portal, navigate to the blade of the Azure VM **az12001b-cl-vm0**. 

1. From the **az12001b-cl-vm0** blade, navigate to the blade of the public IP address **az12001b-cl-vm0-ip** associated with its network adapter.

1. From the **az12001b-cl-vm0-ip** blade, first disassociate the public IP address from the network interface and then delete it.

1. In the Azure portal, navigate to the blade of the Azure VM **az12001b-cl-vm1**. 

1. From the **az12001b-cl-vm1** blade, navigate to the blade of the public IP address **az12001b-cl-vm1-ip** associated with its network adapter.

1. From the **az12001b-cl-vm1-ip** blade, first disassociate the public IP address from the network interface and then delete it.

1. In the Azure portal, navigate to the blade of the **az12001b-cl-vm0** Azure VM.

1. From the **az12001b-cl-vm0** blade, navigate to its **Networking** blade. 

1. From the **az12001b-cl-vm0 - Networking** blade, navigate to the network interface of the az12001b-cl-vm0. 

1. From the blade of the network interface of the az12001b-cl-vm0, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1. On the **ipconfig1** blade, set the private IP address assignment to **Static** and save the change.

1. In the Azure portal, navigate to the blade of the **az12001b-cl-vm1** Azure VM.

1. From the **az12001b-cl-vm1** blade, navigate to its **Networking** blade. 

1. From the **az12001b-cl-vm1 - Networking** blade, navigate to the network interface of the az12001b-cl-vm1. 

1. From the blade of the network interface of the az12001b-cl-vm1, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1. On the **ipconfig1** blade, set the private IP address assignment to **Static** and save the change.

### Task 2: Create and configure Azure Load Balancers handling inbound traffic

1. In the Azure portal, click **+ Create a resource**.

1. From the **New** blade, initiate creation of a new Azure Load Balancer with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription* |
    | **Resource group** | *the name of the resource group containing the pair of **Windows Server 2022 Datacenter** Azure VMs you provisioned in the first exercise of this lab* |
    | **Name** | **az12001b-cl-lb0** |
    | **Region** | the same Azure region where you deployed Azure VMs in the first exercise of this lab* |
    | **SKU** | **Standard** |
    | **Type** | **Internal** |
    | **Frontend IP name** | **frontend-ip1** |
    | **Virtual network** | **adVNET** |
    | **Subnet** | **clSubnet** |
    | **IP address assignment** | **Static** |
    | **IP address** | **10.0.1.240** |
    | **Availability zone** | **Zone-redundant** |

1. Wait until the load balancer is provisioned and then navigate to its blade in the Azure portal.

1. From the **az12001b-cl-lb0** blade, add a backend pool with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **az12001b-cl-lb0-bepool** |
    | **Virtual network** | **adVNET** |
    | **Backend Pool Configuration** | **IP address** |
    | **IP address** | **10.0.1.4** Resource Name **az1201b-cl-vm0** |
    | **IP address** | **10.0.1.5** Resource Name **az1201b-cl-vm1** |

1. From the **az12001b-cl-lb0** blade, add a health probe with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **az12001b-cl-lb0-hprobe** |
    | **Protocol** | **TCP** |
    | **Port** | **59999** |
    | **Interval** | **5** *seconds* |
    | **Unhealthy threshold** | **2** *consecutive failures* |

1. From the **az12001b-cl-lb0** blade, add a network load balancing rule with the following settings:
     
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **az12001b-cl-lb0-lbruletcp1433** |
    | **IP Version** | **IPv4** |
    | **Frontend IP address** | **10.0.1.240 (LoadBalancerFrontEnd)** |
    | **HA Ports** | **Disabled** |
    | **Protocol** | **TCP** |
    | **Port** | **1433** |
    | **Backend Port** | **1433** |
    | **Backend pool** | **az12001b-cl-lb0-bepool (2 virtual machines)** |
    | **Health probe** | **az12001b-cl-lb0-hprobe (TCP:59999)** |
    | **Session persistence** | **None** |
    | **Idle timeout (minutes)** | **4** |
    | **TCP reset** | **Disabled** |
    | **Floating IP (direct server return)** | **Enabled** |

### Task 3: Create and configure Azure Load Balancers handling outbound traffic

1. From the Azure Portal, start a PowerShell session in Cloud Shell. 

1. In the Cloud Shell pane, run the following command to set the value of the variable `$resourceGroupName` to the name of the resource group containing the pair of **Windows Server 2022 Datacenter** Azure VMs you provisioned in the first exercise of this lab:

    ```
    $resourceGroupName = 'az12001b-cl-RG'
    ```

1. In the Cloud Shell pane, run the following command to create the public IP address to be used by the second load balancer:

    ```
    $location = (Get-AzResourceGroup -Name $resourceGroupName).Location

    $pipName = 'az12001b-cl-lb0-pip'

    az network public-ip create --resource-group $resourceGroupName --name $pipName --sku Standard --location $location
    ```

1. In the Cloud Shell pane, run the following command to create the second load balancer:

    ```
    $lbName = 'az12001b-cl-lb1'

    $lbFeName = 'az12001b-cl-lb1-fe'

    $lbBePoolName = 'az12001b-cl-lb1-bepool'
   
    $pip = Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName -Name $pipName

    $feIpconfiguration = New-AzLoadBalancerFrontendIpConfig -Name $lbFeName -PublicIpAddress $pip

    $bePoolConfiguration = New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBePoolName

    New-AzLoadBalancer -ResourceGroupName $resourceGroupName -Location $location -Name $lbName -Sku Standard -BackendAddressPool $bePoolConfiguration -FrontendIpConfiguration $feIpconfiguration
    ```

1. Close the Cloud Shell pane.

1. In the Azure portal, navigate to the blade displaying the properties of the Azure Load Balancer **az12001b-cl-lb1**.

1. On the **az12001b-cl-lb1** blade, click **Backend pools**.

1. On the **az12001b-cl-lb1 - Backend pools** blade, click **az12001b-cl-lb1-bepool**.

1. On the **az12001b-cl-lb1-bepool** blade, specify the following settings and click **Save**:
    
    | Setting | Value |
    |   --    |  --   |
    | **Virtual network** | **adVNET (4 VM)** |
    | **Virtual machine** | **az12001b-cl-vm0**  IP ADDRESS: **ipconfig1** |
    | **Virtual machine** | **az12001b-cl-vm1**  IP ADDRESS: **ipconfig1** |

1. On the **az12001b-cl-lb1** blade, click **Health probes**.

1. From the **az12001b-cl-lb1 - Health probes** blade, add a health probe with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **az12001b-cl-lb1-hprobe** |
    | **Protocol** | **TCP** |
    | **Port** | **80** |
    | **Interval** | **5** *seconds* |
    | **Unhealthy threshold** | **2** *consecutive failures* |

1. On the **az12001b-cl-lb1** blade, click **Load balancing rules**.

1. From the **az12001b-cl-lb1 - Load balancing rules** blade, add a network load balancing rule with the following settings:
    
    | Setting | Value |
    |   --    |  --   |
    | **Name** | **az12001b-cl-lb1-lbharule** |
    | **IP Version** | **IPv4** |
    | **Frontend IP address** | *accept the default value* |
    | **HA Ports** | **Disabled** |
    | **Protocol** | **TCP** |
    | **Port** | **80** |
    | **Backend Port** | **80** |
    | **Backend pool** | **az12001b-cl-lb1-bepool (2 virtual machines)** |
    | **Health probe** | **az12001b-cl-lb1-hprobe (TCP:80)** |
    | **Session persistence** | **None** |
    | **Idle timeout (minutes)** | **4** |
    | **TCP reset** | **Disabled** |
    | **Floating IP (direct server return)** | **Disabled** |

> **Result**: After you completed this exercise, you have provisioned Azure network resources necessary to support highly available SAP NetWeaver deployments

## Exercise 4: Remove lab resources

Duration: 10 minutes

In this exercise, you will remove resources provisioned in this lab.

#### Task 1: Open Cloud Shell

1. At the top of the portal, click the **Cloud Shell** icon to open Cloud Shell pane and choose PowerShell as the shell.

1. In the Cloud Shell pane, run the following command to set the value of the variable `$resourceGroupName` to the name of the resource group containing the pair of **Windows Server 2022 Datacenter** Azure VMs you provisioned in the first exercise of this lab:

    ```
    $resourceGroupNamePrefix = 'az12001b-'
    ```

1. In the Cloud Shell pane, run the following command to list all resource groups you created in this lab:

    ```
    Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like "$resourceGroupNamePrefix*"} | Select-Object ResourceGroupName
    ```

1. Verify that the output contains only the resource groups you created in this lab. These groups will be deleted in the next task.

#### Task 2: Delete resource groups

1. In the Cloud Shell pane, run the following command to delete the resource groups you created in this lab

    ```
    Get-AzResourceGroup | Where-Object {$_.ResourceGroupName -like "$resourceGroupNamePrefix*"} | Remove-AzResourceGroup -Force  
    ```

1. Close the Cloud Shell pane.

> **Result**: After you completed this exercise, you have removed the resources used in this lab.
