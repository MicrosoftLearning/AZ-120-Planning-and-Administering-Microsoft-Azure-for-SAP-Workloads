---
lab:
    title: 'Lab 01: Implement Linux clustering on Azure VMs
    module: 'Implement Linux clustering on Azure VMs'
---

# Lab 01 - Implement Linux clustering on Azure VMs

## Lab introduction

After completing this lab, you will be able to:

- Provision Azure compute resources necessary to support highly available SAP HANA deployments.

- Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation.

- Provision Azure network resources necessary to support highly available SAP HANA deployments.

This lab requires:

- A Microsoft Azure subscription with the sufficient number of available DSv3 vCPUs (2 x 4) and DSv2 (1 x 1) vCPUs.

- A lab computer with an Azure Cloud Shell-compatible web browser and access to Azure.

All tasks in this lab are performed from the [Azure portal](https://portal.azure.com) (including the Bash Cloud Shell session)  

   > **Note**: When not using Cloud Shell, the lab virtual machine must have Azure CLI installed [**https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows**](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows) and include an SSH client e.g. PuTTY, available from [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## Estimated time: 90 minutes

## Lab scenario

In preparation for deployment of SAP HANA on Azure, Adatum Corporation wants to explore the process of implementing clustering on Azure VMs running the SUSE distribution of Linux.

## Interactive lab simulations

There are several interactive lab simulations that you might find useful for this topic. The simulation lets you to click through a similar scenario at your own pace. There are differences between the interactive simulation and this lab, but many of the core concepts are the same. An Azure subscription is not required.

- [TODO](https://TODO). TODO.
  
## Architecture diagram

![TODO](../media/az120-lab01-architecture.png)

TODO

## Job skills

- Task 1: Provision Azure compute resources necessary to support highly available SAP HANA deployments.
- Task 2: Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation.
- Task 3: Provision Azure network resources necessary to support highly available SAP HANA deployments.
- Task 4: Remove lab resources.
  
## Task 1: Provision Azure compute resources necessary to support highly available SAP HANA deployments

In this task, you will deploy Azure infrastructure compute components necessary to configure Linux clustering. This will involve creating a pair of Azure VMs running Linux SUSE in the same availability set and provisioning Azure Bastion.

### Deploy Azure VMs running Linux SUSE

1. From the lab computer, open a Web browser and navigate to the Azure portal at [https://portal.azure.com](https://portal.azure.com).

1. If prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you will be using for this lab.

1. At the top of the [Azure portal](https://portal.azure.com) page, use the **Search resources, services, and docs** text box to search for and navigate to the **Proximity placement groups** blade.

1. On the **Proximity placement groups** blade, select **+ create**.

1. On the **Basics** tab of the **Create Proximity Placement Groups** blade, specify the following settings, and then select **Review + create**:  <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription*  |
    | **Resource group** section | Select **Create new**, enter **az12001a-RG**, and then select **OK** |
    | **Region** | *the Azure region where you have sufficient vCPU quotas* |
    | **Proximity placement group name** | Select **az12001a-ppg** |
    | **Intent details** | **Standard D4s v3** |

   > **Note**: Consider using **East US** or **East US2** regions for deployment of your resources.

1. On the **Review + create** tab of the **Create Proximity Placement Groups** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less than a minute.

1. At the top of the [Azure portal](https://portal.azure.com) page, use the **Search resources, services, and docs** text box to search for and navigate to the **Virtual machines** blade.

1. Select **+ Create** and, on the drop-down menu, select **Azure virtual machine**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Disks >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription*  |
    | **Resource group** | *select the name of the resource group you used earlier in this task* |
    | **Virtual machine name** | *select* **az12001a-vm0** |
    | **Region** | *the **same Azure region** you chose when creating the proximity placement group* |
    | **Availability options** | *select* **Availability set** |
    | **Availability set** | *a new availability set named* **az12001a-avset** *with 2 fault domains and 5 update domains* |
    | **Security type** | *select* **Standard** |
    | **Image** | *select* **SUSE Enterprise Linux for SAP 15 SP3 - BYOS - x64 Gen 2** |
    | **Run with Azure Spot Discount** | **No** |
    | **Size** | **Standard D4s v3** |
    | **Authentication type** | **Password** |
    | **Username** | **student** |
    | **Password** | any complex password of your choice |

    > **Note**:
    > Make sure you remember the password you specified during deployment. You will need it later in this lab.<br><br>
    > To locate the image, click the **See all images** link, on the **Select an image** blade, in the search text box, type **SUSE Enterprise Linux** and, in the list of results, click **SUSE Enterprise Linux for SAP 15 SP3 - BYOS**, and then select **Generation 2**.

1. On the **Disks** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Networking >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **OS disk type** | **Premium SSD (locally-redundant storage)**  |
    | **Key management** | **Platform-managed key** |

1. On the **Networking** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Management >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **Virtual network** | *select* **Create new** *and create a new virtual network named* **az12001a-RG-vnet**, continue next steps in "Create virtual network."  |
    | **Address space** | *set the address space of the new virtual network to* **192.168.0.0/20** |
    | **Subnet name** | **subnet-0** |
    | **Subnet address range** | **192.168.0.0/24**, select "ok" to continue on "Create a virtual machine."|
    | **Public IP address** | **none** |
    | **NIC network security group** | **Advanced**  |
    | **Enable accelerated networking** | **On** |
    | **Load balancing Options** | **None** |

    > **Note**: This image has preconfigured NSG rules.

1. On the **Management** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Monitoring >** (leave all other settings with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Enable system assigned managed identity** | **Off** |
   | **Enable auto-shutdown** | **Off** |
   | **Enable basic plan for free** | **No**  |

   > **Note**: The **basic plan for free** setting is not available if you have already enabled Microsoft Defender for Cloud in your subscription.

1. On the **Monitoring** tab of the **Create a virtual machine** blade, select **Next: Advanced >** (leave all settings with their default values).

1. On the **Advanced** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Review + create** (leave all other settings with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Proximity placement group** | **az12001a-ppg** |

1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less about 3 minutes.

1. At the top of the [Azure portal](https://portal.azure.com) page, use the **Search resources, services, and docs** text box to search for and navigate to the **Virtual machines** blade.

1. On the **Virtual machines** blade, select **+ Create** and, on the drop-down menu, select **Azure virtual machine**. <TODO image for this step?>

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Disks >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **Subscription** | *the name of your Azure subscription*  |
    | **Resource group** | *select the name of the resource group you used earlier in this task* |
    | **Virtual machine name** | *select* **az12001a-vm1** |
    | **Region** | *the same Azure region you chose when creating the proximity placement group* |
    | **Availability options** | *select* **Availability set** |
    | **Availability set** | **az12001a-avset** |
    | **Security type** | *select* **Standard** |
    | **Image** | *select* ***SUSE Enterprise Linux for SAP 15 SP3 - BYOS - x64 Gen 2** |
    | **Run with Azure Spot Discount** | **No** |
    | **Size** | **Standard D4s v3** |
    | **Authentication type** | **Password** |
    | **Username** | **student** |
    | **Password** | the same password you specified during the first deployment |

   > **Note**: To locate the image, click the **See all images** link, on the **Select an image** blade, in the search text box, type **SUSE Enterprise Linux**. In the list of results, click **SUSE Enterprise Linux for SAP 15 SP3 - BYOS**, and then select **Generation 2**.

1. On the **Disks** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Networking >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **OS disk type** | **Premium SSD**  |
    | **Key management** | **Platform-managed key** |

1. On the **Networking** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Management >** (leave all other settings with their default values): <TODO image for this step?>

    | Setting | Value |
    |   --    |  --   |
    | **Virtual network** | **az12001a-RG-vnet** |
    | **Subnet** | **subnet-0 (192.168.0.0/24)** |
    | **Public IP address** | **none** |
    | **NIC network security group** | **Advanced**  |
    | **Enable accelerated networking** | **On** |
    | **Load balancing Options** | **None** |

   > **Note**: This image has preconfigured NSG rules.

1. On the **Management** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Next: Monitoring >** (leave all other settings with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Enable system assigned managed identity** | **Off** |
   | **Enable auto-shutdown** | **Off** |
   | **Enable basic plan for free** | **No**  |

   > **Note**: The **basic plan for free** setting is not available if you have already selected the Azure Security Center plan.

1. On the **Monitoring** tab of the **Create a virtual machine** blade, select **Next: Advanced >** (leave all settings with their default values).

1. On the **Advanced** tab of the **Create a virtual machine** blade, specify the following settings, and then select **Review + create** (leave all other settings with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Proximity placement group** | **az12001a-ppg** |

1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less about 3 minutes.

### Create and configure Azure VMs disks

1. In the [Azure portal](https://portal.azure.com), start a Bash session in Cloud Shell.

   > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1. In the Cloud Shell pane, run the following command to set the value of the variable `RESOURCE_GROUP_NAME` to the name of the resource group containing the resources you provisioned in the previous task:

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. In the Cloud Shell pane, run the following command to create the first set of 8 managed disks that you will attach to the first Azure VM you deployed in the previous task:

   ```cli
   LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

   for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
   ```

1. In the Cloud Shell pane, run the following command to create the second set of 8 managed disks that you will attach to the second Azure VM you deployed in the previous task:

   ```cli
   for I in {0..7}; do az disk create --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm1-DataDisk$I --size-gb 128 --location $LOCATION --sku Premium_LRS; done
   ```

1. In the [Azure portal](https://portal.azure.com), navigate to the blade of the first Azure VM you provisioned in the previous task (**az12001a-vm0**).

1. From the **az12001a-vm0** blade, navigate to the **az12001a-vm0 \| Disks** blade.

1. On the **az12001a-vm0 \| Disks** blade, select **Attach existing disks** and attach data disk with the following settings to az12001a-vm0: <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **LUN** | **0** |
   | **Disk name** | **az12001a-vm0-DataDisk0** |
   | **Resource group** | *select the name of the resource group you used earlier in this task* |
   | **HOST CACHING** | **Read-only** |

1. Repeat the previous step to attach the remaining 7 disks with the prefix **az12001a-vm0-DataDisk** (for the total of 8). Assign the LUN number matching the last character of the disk name. Set HOST CACHING of the disk with LUN **1** to **Read-only** and, for all the remaining ones, set HOST CACHING to **None**.

1. Save your changes.

1. In the [Azure portal](https://portal.azure.com), navigate to the blade of the second Azure VM you provisioned in the previous task (**az12001a-vm1**).

1. From the **az12001a-vm1** blade, navigate to the **az12001a-vm1 \| Disks** blade.

1. From the **az12001a-vm1 \| Disks** blade, attach data disks with the following settings to az12001a-vm1: <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **LUN** | **0** |
   | **Disk name** | **az12001a-vm1-DataDisk0** |
   | **Resource group** | *select the name of the resource group you used earlier in this task* |
   | **HOST CACHING** | **Read-only** |

1. Repeat the previous step to attach the remaining 7 disks with the prefix **az12001a-vm1-DataDisk** (for the total of 8). Assign the LUN number matching the last character of the disk name. Set HOST CACHING of the disk with LUN **1** to **Read-only** and, for all the remaining ones, set HOST CACHING to **None**.

1. Save your changes.

### Provision Azure Bastion

> **Note**:
> Azure Bastion allows for connection to the Azure VMs without public endpoints which you deployed in the previous task of this exercise, while providing protection against brute force exploits that target operating system level credentials.<br><br>
> Ensure that your browser has the pop-up functionality enabled.

1. In the browser window displaying the Azure portal, open another tab and navigate to the [Azure portal](https://portal.azure.com).

1. Open the **Cloud Shell** pane by selecting on the toolbar icon directly to the right of the search textbox. <TODO image?>

1. From the PowerShell session in the Cloud Shell pane, run the following to add a subnet named **AzureBastionSubnet** to the virtual network named **az12001a-RG-vnet** you created earlier in this exercise:

   ```powershell
   $resourceGroupName = 'az12001a-RG'
   $vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroupName -Name 'az12001a-RG-vnet'
   $subnetConfig = Add-AzVirtualNetworkSubnetConfig `
     -Name 'AzureBastionSubnet' `
     -AddressPrefix 192.168.15.0/24 `
     -VirtualNetwork $vnet
   $vnet | Set-AzVirtualNetwork
   ```

1. Close the Cloud Shell pane.

1. In the [Azure portal](https://portal.azure.com), search for and select **Bastions**.

1. On the **Bastions** blade, select **+ Create**.

1. On the **Basic** tab of the **Create a Bastion** blade, specify the following settings, and then select **Review + create**: <TODO image for this step?>

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|**az12001a-RG**|
   |Name|**az12001a-bastion**|
   |Region|the same Azure region to which you deployed the resources in the previous task of this exercise|
   |Tier|**Basic**|
   |Virtual network|**az12001a-RG-vnet**|
   |Subnet|**AzureBastionSubnet (192.168.15.0/24)**|
   |Public IP address|**Create new**|
   |Public IP name|**az12001a-RG-vnet-ip**|

1. On the **Review + create** tab of the **Create a Bastion** blade, select **Create**:

   > **Note**: Wait for the deployment to complete before you proceed to the next task of this exercise. The deployment might take about 5 minutes.

### Task 1 result

After you complete this task, you have provisioned Azure compute resources necessary to support highly available SAP HANA deployments.

## Task 2: Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation

In this task, you will configure operating system and storage on Azure VMs running SUSE Linux Enterprise Server to accommodate clustered installations of SAP HANA.

### Connect to Azure Linux VMs

1. From your lab computer, in the [Azure portal](https://portal.azure.com), search for and select **Virtual machines**.

1. On the **Virtual machines** blade, select the **az12001a-vm0** entry. This will open the **az12001a-vm0** blade.

1. Select **Connect**, and then, on the drop-down menu, select **Connect via Bastion**.

1. On the **Bastion** tab of the **az12001a-vm0** blade, provide the credentials you set when deploying the **az12001a-vm0** virtual machine.

1. Leave the **Authentication type** set to **VM Password**, leave the checkbox **Open in new browser tab** enabled, and then select **Connect**.

1. Repeat these steps to connect via Bastion to the **az12001a-vm1** Azure VM.

### Configure storage of Azure VMs running Linux

1. Within the Bastion session to the **az12001a-vm0** Azure VM, run the following command to elevate privileges:

   ```cli
   sudo su -
   ```

1. Run the following command to identify the mapping between the newly attached devices and their LUN numbers:

   ```cli
   lsscsi
   ```

1. Create physical volumes for 6 (out of 8) data disks by running:

   ```cli
   pvcreate /dev/sdc
   pvcreate /dev/sdd
   pvcreate /dev/sde
   pvcreate /dev/sdf
   pvcreate /dev/sdg
   pvcreate /dev/sdh
   ```

1. Create volume groups by running:

   ```cli
   vgcreate vg_hana_data /dev/sdc /dev/sdd
   vgcreate vg_hana_log /dev/sde /dev/sdf
   vgcreate vg_hana_backup /dev/sdg /dev/sdh
   ```

1. Create logical volumes by running:

   ```cli
   lvcreate -l 100%FREE -n hana_data vg_hana_data
   lvcreate -l 100%FREE -n hana_log vg_hana_log
   lvcreate -l 100%FREE -n hana_backup vg_hana_backup
   ```

   > **Note**: We are creating a single logical volume per each volume group.

1. Format the logical volumes by running:

   ```cli
   mkfs.xfs /dev/vg_hana_data/hana_data -m crc=1
   mkfs.xfs /dev/vg_hana_log/hana_log -m crc=1
   mkfs.xfs /dev/vg_hana_backup/hana_backup -m crc=1
   ```

   > **Note**: Starting with SUSE Linux Enterprise Server 12, you have the option to use the new on-disk format (v5) of the XFS file system, which offers automatic checksums of XFS metadata, file type support, and an increased limit on the number of access control lists per file. The new format applies automatically when using YaST to create the XFS file systems. To create an XFS file system in the older format for compatibility reasons, use the mkfs.xfs command without the `-m crc=1` option.

1. Partition the **/dev/sdi** disk by running:

   ```cli
   fdisk /dev/sdi
   ```

1. When prompted, type, in sequence, `n`, `p`, `1` (followed by the **Enter** key each time) press the **Enter** key twice, and then type `w` to complete the write.

1. Partition the **/dev/sdj** disk by running:

   ```cli
   fdisk /dev/sdj
   ```

1. When prompted, type, in sequence, `n`, `p`, `1` (followed by the **Enter** key each time) press the **Enter** key twice, and then type `w` to complete the write.

1. Format the newly created partition by running (type `y` and press the **Enter** key when prompted for confirmation):

   ```cli
   mkfs.xfs /dev/sdi -m crc=1 -f
   mkfs.xfs /dev/sdj -m crc=1 -f
   ```

1. Create the directories that will serve as mount points by running:

   ```cli
   mkdir -p /hana/data
   mkdir -p /hana/log
   mkdir -p /hana/backup
   mkdir -p /hana/shared
   mkdir -p /usr/sap
   ```

1. Display the ids of logical volumes by running:

   ```cli
   blkid
   ```

   > **Note**: Identify the **UUID** values associated with the newly created volume groups and partitions, including **/dev/sdi** (to be used for **/hana/shared**) and **dev/sdj** (to be used for **/usr/sap**).

1. Open **/etc/fstab** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /etc/fstab
   ```

1. In the editor, add the following entries to **/etc/fstab** (where `\<UUID of /dev/vg\_hana\_data-hana\_data\>`, `\<UUID of /dev/vg\_hana\_log-hana\_log\>`, `\<UUID of /dev/vg\_hana\_backup-hana\_backup\>`, `\<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)\>`, and `\<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdj)\>`, represent the ids you identified in the previous step):

   ```cli
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_data-hana_data> /hana/data xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_log-hana_log> /hana/log xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_backup-hana_backup> /hana/backup xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_hana_shared-hana_shared (/dev/sdi)> /hana/shared xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<UUID of /dev/vg_usr_sap-usr_sap (/dev/sdj)> /usr/sap xfs  defaults,nofail  0  2
   ```

1. Save the changes and close the editor.

1. Mount the new volumes by running:

   ```cli
   mount -a
   ```

1. Verify that the mount was successful by running:

   ```cli
   df -h
   ```

1. Switch to the Bastion session to az12001a-vm1 and repeat all of the steps in this tasks to configure storage on **az12001a-vm1**.

### Enable cross-node password-less SSH access

1. Within the Bastion session to the **az12001a-vm0** Azure VM, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -tdsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running:

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Bastion session to the **az12001a-vm1** Azure VM and create a file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, paste the key you generated on az12001a-vm0.

1. Save the changes and close the editor.

1. Within the Bastion session to the **az12001a-vm1** Azure VM, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -tdsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running:

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Bastion session to the **az12001a-vm0** Azure VM and create a file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, paste the key you generated on **az12001a-vm1**.

1. Save the changes and close the editor.

1. Within the Bastion session to the **az12001a-vm0** Azure VM, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -t rsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running:

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Bastion session to the **az12001a-vm1** Azure VM, and open the file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, starting from a new line, paste the key you generated on **az12001a-vm0**.

1. Save the changes and close the editor.

1. Within the Bastion session to the **az12001a-vm1** Azure VM, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -t rsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running:

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Bastion session to the **az12001a-vm0** Azure VM and open the file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, starting from a new line, paste the key you generated on **az12001a-vm1**.

1. Save the changes and close the editor.

1. Within the Bastion session to the **az12001a-vm0** Azure VM, open the file **/etc/ssh/sshd\_config** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /etc/ssh/sshd_config
   ```

1. In the **/etc/ssh/sshd\_config** file, locate the **PermitRootLogin** and **AuthorizedKeysFile** entries, and configure them as follows (remove the leading **#** character if needed):

   ```cli
   PermitRootLogin yes
   AuthorizedKeysFile  /root/.ssh/authorized_keys
   ```

1. Save the changes and close the editor.

1. Within the Bastion session to the **az12001a-vm0** Azure VM, restart sshd daemon by running:

   ```cli
   systemctl restart sshd
   ```

1. Repeat the previous four steps on **az12001a-vm1**.

1. To verify that the configuration was successful, in the Bastion session to the **az12001a-vm0** Azure VM,  establish an SSH session as **root** from az12001a-vm0 to az12001a-vm1 by running:

   ```cli
   ssh root@az12001a-vm1
   ```

1. When prompted whether you are sure to continue connecting, type `yes` and press the **Enter** key.

1. Ensure that you are not prompted for the password.

1. Close the SSH session from az12001a-vm0 to az12001a-vm1 by running:

   ```cli
   exit
   ```

1. Sign out from az12001a-vm0 by running the following twice:

   ```cli
   exit
   ```

1. To verify that the configuration was successful, in the Bastion session to **az12001a-vm1**, establish an SSH session as **root** from az12001a-vm1 to az12001a-vm0 by running:

   ```cli
   ssh root@az12001a-vm0
   ```

1. When prompted whether you are sure to continue connecting, type `yes` and press the **Enter** key.

1. Ensure that you are not prompted for the password.

1. Close the SSH session from az12001a-vm1 to az12001a-vm0 by running:

   ```cli
   exit
   ```

1. Sign out from az12001a-vm1 by running the following twice:

   ```cli
   exit
   ```

### Task 2 result

After you complete this task, you have configured an operating system of Azure VMs running Linux to support a highly available SAP HANA installation.

## Task 3: Provision Azure network resources necessary to support highly available SAP HANA deployments

In this task, you will implement Azure Load Balancers to accommodate clustered installations of SAP HANA.

### Configure Azure VMs to facilitate load balancing setup

<TODO images for these steps? Could use some rewrite but need to see the UI to write it>

1. In the [Azure portal](https://portal.azure.com), navigate to the blade of the **az12001a-vm0** Azure VM.

1. Navigate to the **az12001a-vm0 \| Networking** blade, and the select the entry representing the network interface of **az12001a-vm0**.

1. Navigate to the **IP configurations** blade, and then display its **ipconfig1** blade.

1. Set the private IP address assignment to **Static** and save the change.

### Create and configure Azure Load Balancers handling inbound traffic

1. At the top of the [Azure portal](https://portal.azure.com) page, use the **Search resources, services, and docs** text box to search for and navigate to the **Load balancers** blade.

1. On the **Load balancers** blade, select **+ Create**.

1. From the **Basics** tab of the **Create load balancer** blade, specify the following settings, and then select **Review + create** (leave others with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Subscription** | *the name of your Azure subscription* |
   | **Resource group** | *select the name of the resource group you used earlier in this lab* |
   | **Name** | **az12001a-lb0** |
   | **Region** | *the same Azure region where you deployed Azure VMs in the first exercise of this lab* |
   | **SKU** | **Standard** |
   | **Type** | **Internal** |

1. Click **Next: Frontend IP Configuration**.

1. On the **Frontend IP configuration** screen, click **Add a frontend IP configuration**, and then click **Add**. <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Name** | **frontend1** |
   | **Virtual network** | **az12001a-RG-vnet** |
   | **Subnet** | **subnet-0** |
   | **IP address assignment** | **Static** |
   | **IP address** | **192.168.0.240** |
   | **Availability zone** | **Zone redundant** |

1. Select **Review + create**, and then select **Create**.

   > **Note**: Wait until the load balancer is provisioned. This should take less than a minute.

1. In the [Azure portal](https://portal.azure.com), navigate to the blade displaying the properties of the newly provisioned **az12001a-lb0** load balancer.

1. On the **az12001a-lb0** blade, select **Backend pools**, select **+ Add**, and, on the **Add backend pool** specify the following settings (leave others with their default values): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Name** | **az12001a-lb0-bepool** |
   | **Virtual network** | **az12001a-RG-vnet** |
   | **Backend Pool Configuration** | **IP address** |
   | **IP address** | **192.168.0.4** Resource name: **az12001a-vm0** |
   | **IP address** | **192.168.0.5** Resource name: **az12001a-vm1** |

1. On the **az12001a-lb0** blade, select **Health probes**, and then select **+ Add**.

1. On the **Add health probe** blade, specify the following settings (leave others with their defaults): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Name** | **az12001a-lb0-hprobe** |
   | **Protocol** | **TCP** |
   | **Port** | **62500** |
   | **Interval** | **5** *seconds* |
   | **Unhealthy threshold** | **2** *consecutive failures* |

1. On the **az12001a-lb0** blade, select **Load balancing rules**, and then select **+ Add**.

1. On the **Add load balancing rule** blade, specify the following settings (leave others with their defaults): <TODO image for this step?>

   | Setting | Value |
   |   --    |  --   |
   | **Name** | **az12001a-lb0-lbruleAll** |
   | **IP Version** | **IPv4** |
   | **Frontend IP address** | **192.168.0.240 (LoadBalancerFrontEnd)** |
   | **HA Ports** | **Enabled** |
   | **Backend pool** | **az12001a-lb0-bepool (2 virtual machines)** |
   | **Health probe** | **az12001a-lb0-hprobe (TCP:62500)** |
   | **Session persistence** | **None** |
   | **Idle timeout (minutes)** | **4** |
   | **TCP reset** | **Disabled** |
   | **Floating IP (direct server return)** | **Enabled** |

### Create and configure Azure Load Balancers handling outbound traffic

1. In the [Azure portal](https://portal.azure.com), start a Bash session in Cloud Shell.

1. In the Cloud Shell pane, run the following command to set the value of the variable `RESOURCE_GROUP_NAME` to the name of the resource group containing the resources you provisioned in the first exercise of this lab:

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. In the Cloud Shell pane, run the following command to create the public IP address to be used by the second load balancer:

   ```cli
   LOCATION=$(az group list --query "[?name == '$RESOURCE_GROUP_NAME'].location" --output tsv)

   PIP_NAME='az12001a-lb1-pip'

   az network public-ip create --resource-group $RESOURCE_GROUP_NAME --name $PIP_NAME --sku Standard --location $LOCATION
   ```

1. In the Cloud Shell pane, run the following command to create the second load balancer:

   ```cli
   LB_NAME='az12001a-lb1'

   LB_BE_POOL_NAME='az12001a-lb1-bepool'

   LB_FE_IP_NAME='az12001a-lb1-fe'

   az network lb create --resource-group $RESOURCE_GROUP_NAME --name $LB_NAME --sku Standard --backend-pool-name $LB_BE_POOL_NAME --frontend-ip-name $LB_FE_IP_NAME --location $LOCATION --public-ip-address $PIP_NAME
   ```

1. In the Cloud Shell pane, run the following command to create the outbound rule of the second load balancer:

   ```cli
   LB_RULE_OUTBOUND='az12001a-lb1-ruleoutbound'

   az network lb outbound-rule create --resource-group $RESOURCE_GROUP_NAME --lb-name $LB_NAME --name $LB_RULE_OUTBOUND --frontend-ip-configs $LB_FE_IP_NAME --protocol All --idle-timeout 4 --outbound-ports 1000 --address-pool $LB_BE_POOL_NAME
   ```

1. Close the Cloud Shell pane.

1. In the [Azure portal](https://portal.azure.com), navigate to the blade displaying the properties of the newly created Azure Load Balancer **az12001a-lb1**.

1. On the **az12001a-lb1** blade, click **Backend pools**.

1. On the **az12001a-lb1 \| Backend pools** blade, click **az12001a-lb1-bepool**.

1. On the **az12001a-lb1-bepool** blade, specify the following settings and click **Save**: <TODO image?>

   | Setting | Value |
   |   --    |  --   |
   | **Virtual network** | **az12001a-rg-vnet (2 VM)** |
   | **Virtual machine** | **az12001a-vm0**  IP Configuration: **ipconfig1 (192.168.0.4)** |
   | **Virtual machine** | **az12001a-vm1**  IP Configuration: **ipconfig1 (192.168.0.5)** |

### Task 3 result

After you complete this task, you have provisioned Azure network resources necessary to support highly available SAP HANA deployments.

## Task 4: Remove lab resources

In this task, you will remove resources provisioned in this lab.

### List resource groups to be deleted

1. At the top of the [Azure portal](https://portal.azure.com) page, click the **Cloud Shell** icon to open Cloud Shell pane, and choose Bash as the shell.

1. In the Cloud Shell pane, run the following command to set the value of the variable `RESOURCE_GROUP_PREFIX` to the prefix of the name of the resource group containing the resources you provisioned in this lab:

   ```cli
   RESOURCE_GROUP_PREFIX='az12001a-'
   ```

1. In the Cloud Shell pane, run the following command to list all resource groups you created in this lab:

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv
   ```

1. Verify that the output contains only the resource group you created in this lab. This resource group with all of their resources will be deleted in the next task.

### Delete resource groups

1. In the Cloud Shell pane, run the following command to delete the resource group and their resources.

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. Close the Cloud Shell pane.

### Task 4 result

After you complete this task, you have removed the resources used in this lab.

## Key takeaways

Congratulations! Now that you have completed this lab, you know how to:

- Provision Azure compute resources necessary to support highly available SAP HANA deployments.

- Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation.

- Provision Azure network resources necessary to support highly available SAP HANA deployments.

## Learn more with self-paced training

- [TODO](https://TODO). TODO.
