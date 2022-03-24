# AZ 120 Module 2: Explore the foundations of IaaS for SAP on Azure
# Lab 1a: Implement Linux clustering on Azure VMs

Estimated Time: 90 minutes

All tasks in this lab are performed from the Azure portal (including the Bash Cloud Shell session)  

   > **Note**: When not using Cloud Shell, the lab virtual machine must have Azure CLI installed [**https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows**](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows) and include an SSH client e.g. PuTTY, available from [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

Lab files: none

## Scenario
  
In preparation for deployment of SAP HANA on Azure, Adatum Corporation wants to explore the process of implementing clustering on Azure VMs running the SUSE distribution of Linux.

## Objectives
  
After completing this lab, you will be able to:

- Provision Azure compute resources necessary to support highly available SAP HANA deployments

- Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation

- Provision Azure network resources necessary to support highly available SAP HANA deployments

## Requirements

- A Microsoft Azure subscription with the sufficient number of available DSv3 vCPUs (2 x 4) and DSv2 (1 x 1) vCPUs

- A lab computer with an Azure Cloud Shell-compatible web browser and access to Azure

## Exercise 1: Provision Azure compute resources necessary to support highly available SAP HANA deployments

Duration: 30 minutes

In this exercise, you will deploy Azure infrastructure compute components necessary to configure Linux clustering. This will involve creating a pair of Azure VMs running Linux SUSE in the same availability set.

### Task 1: Deploy Azure VMs running Linux SUSE

1. From the lab computer, start a Web browser, and navigate to the Azure portal at https://portal.azure.com

1. If prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you will be using for this lab.

1. In the Azure portal, use the **Search resources, services, and docs** text box at the top of the Azure portal page to search for and navigate to the **Proximity placement groups** blade and, on the **Proximity placement groups** blade, select **+ create**.

1. On the **Basics** tab of the **Create Proximity Placement Groups** blade, specify the following settings and select **Review + create**:

   - Subscription: *the name of your Azure subscription*

   - Resource group: Resource group: *the name of a new resource group* **az12001a-RG**

   > **Note**: Consider using **East US** or **East US2** regions for deployment of your resources. 

   - Region: *an Azure region where you can deploy Azure VMs*

   - Proximity placement group name: **az12001a-ppg**

1. On the **Review + create** tab of the **Create Proximity Placement Groups** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less than a minute.

1. In the Azure portal, use the **Search resources, services, and docs** text box at the top of the Azure portal page to search for and navigate to the **Virtual machines** blade, then, on the **Virtual machines** blade, select **+ Create** and, in the drop-down menu, select **Azure virtual machine**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Disks >** (leave all other settings with their default value):

   - Subscription: *the name of your Azure subscription*

   - Resource group: *the name of the resource group you used earlier in this task*

   - Virtual machine name: **az12001a-vm0**

   - Region: *the same Azure region you chose when creating the proximity placement group*

   - Availability options: **Availability set**

   - Availability set: *a new availability set named* **az12001a-avset** *with 2 fault domains and 5 update domains*

   - Image: **SUSE Enterprise Linux for SAP 12 SP5 - BYOS - Gen 1**
   
   > **Note**: To locate the image, click the **See all images** link, on the **Select an image** blade, in the search text box, type **SUSE Enterprise Linux for SAP 12 BYOS** and, in the list of results, click **SUSE Enterprise Linux for SAP 12 SP5 - BYOS**.

   - Azure Spot Instance: **No**

   - Size: **Standard D4s v3**

   - Authentication type: **Password**

   - Username: **student**

   - Password: **Pa55w.rd1234**

1. On the **Disks** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Networking >** (leave all other settings with their default value):

   - OS disk type: **Premium SSD**

   - Encryption type: **(Default) Encryption at rest with a platform-managed key**

1. On the **Networking** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Management >** (leave all other settings with their default value):

   - Virtual network: *a new virtual network named* **az12001a-RG-vnet**

   - Address space: **192.168.0.0/20**

   - Subnet name: **subnet-0**

   - Subnet address range: **192.168.0.0/24**

   - Public IP address: *a new IP address named* **az12001a-vm0-ip**

   - NIC network security group: **Advanced**

   > **Note**: This image has preconfigured NSG rules

   - Accelerated networking: **On**

   - Place this virtual machine behind an existing load balancing solutions: **No**

1. On the **Management** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Advanced >** (leave all other settings with their default value):

   - Enable basic plan for free: **No**

   > **Note**: This setting is not available if you have already selected the Azure Security Center plan.

   - Boot diagnostics: **Enable with managed storage account (recommended)**

   - OS guest diagnostics: **Off**

   - System assigned managed identity: **Off**

   - Enable auto-shutdown: **Off**

1. On the **Advanced** tab of the **Create a virtual machine** blade, specify the following settings and select **Review + create** (leave all other settings with their default value):

   - Proximity placement group: **az12001a-ppg**

1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less about 3 minutes.

1. In the Azure portal, use the **Search resources, services, and docs** text box at the top of the Azure portal page to search for and navigate to the **Virtual machines** blade, then, on the **Virtual machines** blade, select **+ Create** and, in the drop-down menu, select **Virtual machine**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Disks >** (leave all other settings with their default value):

   - Subscription: *the name of your Azure subscription*

   - Resource group: *the name of the resource group you used earlier in this task*

   - Virtual machine name: **az12001a-vm1**

   - Region: *the same Azure region you chose when creating the first Azure VM*

   - Availability options: **Availability set**

   - Availability set: **az12001a-avset**

   - Image: **SUSE Enterprise Linux for SAP 12 SP5 - BYOS - Gen 1**
   
   > **Note**: To locate the image, click the **See all images** link, on the **Select an image** blade, in the search text box, type **SUSE Enterprise Linux for SAP 12 BYOS** and, in the list of results, click **SUSE Enterprise Linux for SAP 12 SP5 - BYOS**.

   - Azure Spot Instance: **No**

   - Size: **Standard D4s v3**

   - Authentication type: **Password**

   - Username: **student**

   - Password: **Pa55w.rd1234**

1. On the **Disks** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Networking >** (leave all other settings with their default value):

   - OS disk type: **Premium SSD**

   - Encryption type: **(Default) Encryption at rest with a platform-managed key**

1. On the **Networking** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Management >** (leave all other settings with their default value):

   - Virtual network: **az12001a-RG-vnet**

   - Subnet: **subnet-0 (192.168.0.0/24)**

   - Public IP address: *a new IP address named* **az12001a-vm1-ip**

   - NIC network security group: **Advanced**

   > **Note**: This image has preconfigured NSG rules

   - Accelerated networking: **On**

   - Place this virtual machine behind an existing load balancing solutions: **No**

1. On the **Management** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Advanced >** (leave all other settings with their default value):

   - Enable basic plan for free: **No**

   > **Note**: This setting is not available if you have already selected the Azure Security Center plan.

   - Boot diagnostics: **Enable with managed storage account (recommended)**

   - OS guest diagnostics: **Off**

   - System assigned managed identity: **Off**

   - Enable auto-shutdown: **Off**

1. On the **Advanced** tab of the **Create a virtual machine** blade, specify the following settings and select **Review + create** (leave all other settings with their default value):

   - Proximity placement group: **az12001a-ppg**

1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less about 3 minutes.


### Task 2: Create and configure Azure VMs disks

1. In the Azure Portal, start a Bash session in Cloud Shell. 

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

1. In the Azure portal, navigate to the blade of the first Azure VM you provisioned in the previous task (**az12001a-vm0**).

1. From the **az12001a-vm0** blade, navigate to the **az12001a-vm0 \| Disks** blade.

1. On the **az12001a-vm0 \| Disks** blade, select **Attach existing disks** and attach data disk with the following settings to az12001a-vm0:

   - LUN: **0**

   - Disk name: **az12001a-vm0-DataDisk0**

   - Resource group: *the name of the resource group you used earlier in this task*

   - HOST CACHING: **Read-only**

1. Repeat the previous step to attach the remaining 7 disks with the prefix **az12001a-vm0-DataDisk** (for the total of 8). Assign the LUN number matching the last character of the disk name. Set HOST CACHING of the disk with LUN **1** to **Read-only** and, for all the remaining ones, set HOST CACHING to **None**.

1. Save your changes. 

1. In the Azure portal, navigate to the blade of the second Azure VM you provisioned in the previous task (**az12001a-vm1**).

1. From the **az12001a-vm1** blade, navigate to the **az12001a-vm1 \| Disks** blade.

1. From the **az12001a-vm1 \| Disks** blade, attach data disks with the following settings to az12001a-vm1:

   - LUN: **0**

   - Disk name: **az12001a-vm1-DataDisk0**

   - Resource group: *the name of the resource group you used earlier in this task*

   - HOST CACHING: **Read-only**

1. Repeat the previous step to attach the remaining 7 disks with the prefix **az12001a-vm1-DataDisk** (for the total of 8). Assign the LUN number matching the last character of the disk name. Set HOST CACHING of the disk with LUN **1** to **Read-only** and, for all the remaining ones, set HOST CACHING to **None**.

1. Save your changes. 

> **Result**: After you completed this exercise, you have provisioned Azure compute resources necessary to support highly available SAP HANA deployments.


## Exercise 2: Configure operating system of Azure VMs running Linux to support a highly available SAP HANA installation

Duration: 30 minutes

In this exercise, you will configure operating system and storage on Azure VMs running SUSE Linux Enterprise Server to accommodate clustered installations of SAP HANA.

### Task 1: Connect to Azure Linux VMs

1. In the Azure Portal, start a Bash session in Cloud Shell. 

1. In the Cloud Shell pane, run the following command to set the value of the variable `RESOURCE_GROUP_NAME` to the name of the resource group containing the resources you provisioned in the previous exercise:

   ```cli
   RESOURCE_GROUP_NAME='az12001a-RG'
   ```

1. In the Cloud Shell pane, run the following command to identify the public IP address of the first Azure VM you deployed in the previous exercise:

   ```cli
   PIP=$(az network public-ip show --resource-group $RESOURCE_GROUP_NAME --name az12001a-vm0-ip --query ipAddress --output tsv)
   ```

1. In the Cloud Shell pane, run the following command to establish an SSH session to the IP address you identified in the previou step:

   ```cli
   ssh student@$PIP
   ```

1. When prompted whether you are sure to continue connecting, type `yes` and press the **Enter** key. 

1. When prompted for the password, type `Pa55w.rd1234` and press the **Enter** key. 

1. Open another Cloud Shell Bash session by clicking the **Open new session** icon in the Cloud Shell toolbar.

1. In the newly opened Cloud Shell Bash session, repeat all of the steps in this tasks to connect to the **az12001a-vm1** Azure VM via its IP address **az12001a-vm0-ip**.


### Task 2: Configure storage of Azure VMs running Linux

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, run the following command to elevate privileges: 

   ```cli
   sudo su -
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, run the following command to identify the mapping between the newly attached devices and their LUN numbers:
   
   ```cli
   lsscsi
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, create physical volumes for 6 (out of 8) data disks by running:
   
   ```cli
   pvcreate /dev/sdc
   pvcreate /dev/sdd
   pvcreate /dev/sde
   pvcreate /dev/sdf
   pvcreate /dev/sdg
   pvcreate /dev/sdh
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, create volume groups by running:
   
   ```cli
   vgcreate vg_hana_data /dev/sdc /dev/sdd
   vgcreate vg_hana_log /dev/sde /dev/sdf
   vgcreate vg_hana_backup /dev/sdg /dev/sdh
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, create logical volumes by running:

   ```cli
   lvcreate -l 100%FREE -n hana_data vg_hana_data
   lvcreate -l 100%FREE -n hana_log vg_hana_log
   lvcreate -l 100%FREE -n hana_backup vg_hana_backup
   ```

   > **Note**: We are creating a single logical volume per each volume group

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, format the logical volumes by running:

   ```cli
   mkfs.xfs /dev/vg_hana_data/hana_data -m crc=1
   mkfs.xfs /dev/vg_hana_log/hana_log -m crc=1
   mkfs.xfs /dev/vg_hana_backup/hana_backup -m crc=1
   ```

   > **Note**: Starting with SUSE Linux Enterprise Server 12, you have the option to use the new on-disk format (v5) of the XFS file system, which offers automatic checksums of XFS metadata, file type support, and an increased limit on the number of access control lists per file. The new format applies automatically when using YaST to create the XFS file systems. To create an XFS file system in the older format for compatibility reasons, use the mkfs.xfs command without the `-m crc=1` option. 

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, partition the **/dev/sdi** disk by running:

   ```cli
   fdisk /dev/sdi
   ```

1. When prompted, type, in sequence, `n`, `p`, `1` (followed by the **Enter** key each time) press the **Enter** key twice, and then type `w` to complete the write.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, partition the **/dev/sdj** disk by running:

   ```cli
   fdisk /dev/sdj
   ```

1. When prompted, type, in sequence, `n`, `p`, `1` (followed by the **Enter** key each time) press the **Enter** key twice, and then type `w` to complete the write.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, format the newly created partition by running (type `y` and press the **Enter** key when prompted for confirmation)	:

   ```cli
   mkfs.xfs /dev/sdi -m crc=1 -f
   mkfs.xfs /dev/sdj -m crc=1 -f
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, create the directories that will serve as mount points by running:

   ```cli
   mkdir -p /hana/data
   mkdir -p /hana/log
   mkdir -p /hana/backup
   mkdir -p /hana/shared
   mkdir -p /usr/sap
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, display the ids of logical volumes by running:

   ```cli
   blkid
   ```

   > **Note**: Identify the **UUID** values associated with the newly created volume groups and partitions, including **/dev/sdi** (to be used for **/hana/shared**) and **dev/sdj** (to be used for **/usr/sap**).


1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, open **/etc/fstab** in the vi editor (you are free to use any other editor) by running:

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

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, mount the new volumes by running:

   ```cli
   mount -a
   ```

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, verify that the mount was successful by running:

   ```cli
   df -h
   ```

1. Switch to the Cloud Shell Bash session to az12001a-vm1 and repeat all of the steps in this tasks to configure storage on **az12001a-vm1**.


### Task 3: Enable cross-node password-less SSH access

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -tdsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running: 

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm1, create a file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, paste the key you generated on az12001a-vm0.

1. Save the changes and close the editor.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm1, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -tdsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running: 

   ```cli
   cat /root/.ssh/id_dsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Cloud Shell pane containing the SSH session to az12001a-vm0 and create a file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, paste the key you generated on az12001a-vm1.

1. Save the changes and close the editor.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -t rsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running: 

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Cloud Shell pane containing the SSH session to **az12001a-vm1** and open the file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, starting from a new line, paste the key you generated on az12001a-vm0.

1. Save the changes and close the editor.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm1, generate passphrase-less SSH key by running:

   ```cli
   ssh-keygen -t rsa
   ```

1. When prompted, press **Enter** three times and then display the public key by running: 

   ```cli
   cat /root/.ssh/id_rsa.pub
   ```

1. Copy the value of the key into Clipboard.

1. Switch to the Cloud Shell pane containing the SSH session to az12001a-vm0 and open the file **/root/.ssh/authorized\_keys** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /root/.ssh/authorized_keys
   ```

1. In the editor window, starting from a new line, paste the key you generated on az12001a-vm1.

1. Save the changes and close the editor.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, open the file **/etc/ssh/sshd\_config** in the vi editor (you are free to use any other editor) by running:

   ```cli
   vi /etc/ssh/sshd_config
   ```

1. In the **/etc/ssh/sshd\_config** file, locate the **PermitRootLogin** and **AuthorizedKeysFile** entries, and configure them as follows (remove the leading **#** character if needed:

   ```cli
   PermitRootLogin yes
   AuthorizedKeysFile  /root/.ssh/authorized_keys
   ```

1. Save the changes and close the editor.

1. In the Cloud Shell pane, in the SSH session to az12001a-vm0, restart sshd daemon by running:

   ```cli
   systemctl restart sshd
   ```

1. Repeat the previous four steps on az12001a-vm1.

1. To verify that the configuration was successful, in the Cloud Shell pane, in the SSH session to az12001a-vm0, establish an SSH session as **root** from az12001a-vm0 to az12001a-vm1 by running: 

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

1. To verify that the configuration was successful, in the Cloud Shell pane, in the SSH session to az12001a-vm1, establish an SSH session as **root** from az12001a-vm1 to az12001a-vm0 by running: 

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

> **Result**: After you completed this exercise, you have configured operating system of Azure VMs running Linux to support a highly available SAP HANA installation


## Exercise 3: Provision Azure network resources necessary to support highly available SAP HANA deployments

Duration: 30 minutes

In this exercise, you will implement Azure Load Balancers to accommodate clustered installations of SAP HANA.


### Task 1: Configure Azure VMs to facilitate load balancing setup.

   > **Note**: Since you will be setting up a pair of Azure Load Balancer of the Stardard SKU, you need to first remove the public IP addresses associated with network adapters of two Azure VMs that will be serving as the load-balanced backend pool.

1. In the Azure portal, navigate to the blade of the **az12001a-vm0** Azure VM.

1. From the **az12001a-vm0** blade, navigate to the **az12001a-vm0 \| Networking** blade and, on the **az12001a-vm0 \| Networking** blade, select the entry representing the public IP address **az12001a-vm0-ip** associated with its network adapter.

1. On the **az12001a-vm0-ip** blade, select **Dissociate** to disconnect the public IP address from the network interface and then select **Delete** to delete it.

1. In the Azure portal, navigate to the blade of the **az12001a-vm1** Azure VM.

1. From the **az12001a-vm1** blade, navigate to the **az12001a-vm1 \| Networking** blade and, on the **az12001a-vm1 \| Networking** blade, select the entry representing the public IP address **az12001a-vm1-ip** associated with its network adapter.

1. On the **az12001a-vm1-ip** blade, select **Dissociate** to disconnect the public IP address from the network interface and then select **Delete** to delete it.

1. In the Azure portal, navigate to the blade of the **az12001a-vm0** Azure VM.

1. From the **az12001a-vm0** blade, navigate to the **az12001a-vm0 \| Networking** blade. 

1. From the **az12001a-vm0 \| Networking** blade, select the entry representing the network interface of the az12001a-vm0. 

1. From the blade of the network interface of the az12001a-vm0, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1. On the **ipconfig1** blade, set the private IP address assignment to **Static** and save the change.

1. In the Azure portal, navigate to the blade of the **az12001a-vm1** Azure VM.

1. From the **az12001a-vm1** blade, navigate to the **az12001a-vm1 \| Networking** blade. 

1. From the **az12001a-vm1 \| Networking** blade, navigate to the network interface of the az12001a-vm1. 

1. From the blade of the network interface of the az12001a-vm1, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1. On the **ipconfig1** blade, set the private IP address assignment to **Static** and save the change.


### Task 2: Create and configure Azure Load Balancers handling inbound traffic

1. In the Azure portal, use the **Search resources, services, and docs** text box at the top of the Azure portal page to search for and navigate to the **Load balancers** blade and, on the **Load balancers** blade, select **+ Create**.

1. From the **Basics** tab of the **Create load balancer** blade, specify the following settings and select **Review + create** (leave others with their default values):

   - Subscription: *the name of your Azure subscription*

   - Resource group: *the name of the resource group you used earlier in this lab*

   - Name: **az12001a-lb0**

   - Region: *the same Azure region where you deployed Azure VMs in the first exercise of this lab*

   - SKU: **Standard**
   
   - Type: **Internal**

1. Click **Next: Frontend IP Configuration**. On the **Frontend IP configuration** screen, click **Add a frontend IP configuration** and then click **Add**.
   - Name: frontend1
   
   - Virtual network: **az12001a-RG-vnet**

   - Subnet: **subnet-0**

   - IP address assignment: **Static**

   - IP address: **192.168.0.240**

   - Availability zone: **Zone redundant**

1. Select **Review + create**, and then select **Create**.

   > **Note**: Wait until the load balancer is provisioned. This should take less than a minute. 

1. In the Azure portal, navigate to the blade displaying the properties of the newly provisioned **az12001a-lb0** load balancer. 

1. On the **az12001a-lb0** blade, select **Backend pools**, select **+ Add**, and, on the **Add backend pool** specify the following settings (leave others with their default values):

   - Name: **az12001a-lb0-bepool**

   - IP version: **IPv4**

   - Virtual machine: **az12001a-vm0**  IP Configuration: **ipconfig1 (192.168.0.4)**

   - Virtual machine: **az12001a-vm1**  IP Configuration: **ipconfig1  (192.168.0.5)**

1. On the **az12001a-lb0** blade, select **Health probes** select **+ Add**, and, on the **Add health probe** blade, specify the following settings (leave others with their defaults):

   - Name: **az12001a-lb0-hprobe**

   - Protocol: **TCP**

   - Port: **62500**

   - Interval: **5** *seconds*

   - Unhealthy threshold: **2** *consecutive failures*

1. On the **az12001a-lb0** blade, select **Load balancing rules**, select **+ Add**, and, on the **Add load balancing rule** blade, specify the following settings (leave others with their defaults):

   - Name: **az12001a-lb0-lbruleAll**

   - IP Version: **IPv4**

   - Frontend IP address: **192.168.0.240 (LoadBalancerFrontEnd)**

   - HA Ports: **Enabled**

   - Backend pool: **az12001a-lb0-bepool (2 virtual machines)**

   - Health probe:**az12001a-lb0-hprobe (TCP:62500)**

   - Session persistence: **None**

   - Idle timeout (minutes): **4**

   - TCP reset: **Disabled**

   - Floating IP (direct server return): **Enabled**

### Task 3: Create and configure Azure Load Balancers handling outbound traffic

1. In the Azure Portal, start a Bash session in Cloud Shell. 

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

1. In the Azure portal, navigate to the blade displaying the properties of the newly created Azure Load Balancer **az12001a-lb1**.

1. On the **az12001a-lb1** blade, click **Backend pools**.

1. On the **az12001a-lb1 \| Backend pools** blade, click **az12001a-lb1-bepool**.

1. On the **az12001a-lb1-bepool** blade, specify the following settings and click **Save**:

   - Virtual network: **az12001a-rg-vnet (2 VM)**

   - Virtual machine: **az12001a-vm0**  IP Configuration: **ipconfig1 (192.168.0.4)**

   - Virtual machine: **az12001a-vm1**  IP Configuration: **ipconfig1 (192.168.0.5)**

### Task 4: Deploy a jump host

   > **Note**: Since two clustered Azure VMs are no longer directly accessible from Internet, you will deploy an Azure VM running Windows Server 2019 Datacenter that will serve as a jump host. 

1. From the lab computer, in the Azure portal, use the **Search resources, services, and docs** text box at the top of the Azure portal page to search for and navigate to the **Virtual machines** blade, then, on the **Virtual machines** blade, select **+ Create** and, in the drop-down menu, select **Azure virtual machine**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Disks >** (leave all other settings with their default value):

   - Subscription: *the name of your Azure subscription*

   - Resource group: *the name of the resource group you used earlier in this lab*

   - Virtual machine name: **az12001a-vm2**

   - Region: *the same Azure region where you deployed Azure VMs in the first exercise of this lab*

   - Availability options: **No infrastructure redundancy required**

   - Image: **Windows Server 2019 Datacenter - Gen 2**

   - Size: **Standard DS1 v2*** or similar*

   - Azure Spot Instance: **No**

   - Username: **Student**

   - Password: **Pa55w.rd1234**

   - Public inbound ports: **Allow selected ports**

   - Selected inbound ports: **RDP (3389)**

   - Would you like to use an existing Windows Server license?: **No**

1. On the **Disks** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Networking >** (leave all other settings with their default value):

   - OS disk type: **Standard HDD**

   - Encryption type: **(Default) Encryption at rest with a platform-managed key**

1. On the **Networking** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Management >** (leave all other settings with their default value):

   - Virtual network: **az12001a-RG-vnet**

   - Subnet name: **subnet-0 (192.168.0.0/24)**

   - Public IP address: *a new IP address named* **az12001a-vm2-ip**

   - NIC network security group: **Basic**

   - Public inbound ports: **Allow selected ports**

   - Select inbound ports: **RDP (3389)**

   - Accelerated networking: **Off**

   - Place this virtual machine behind an existing load balancing solutions: **No**

1. On the **Management** tab of the **Create a virtual machine** blade, specify the following settings and select **Next: Advanced >** (leave all other settings with their default value):

   - Enable basic plan for free: **No**

   > **Note**: This setting is not available if you have already selected the Azure Security Center plan.

   - Boot diagnostics: **Enable with managed storage account (recommended)**

   - OS guest diagnostics: **Off**

   - System assigned managed identity: **Off**

   - Enable auto-shutdown: **Off**

   - Enable backup: **Off**

   - Guest OS updates: **Manual updates**

1. On the **Advanced** tab of the **Create a virtual machine** blade, select **Review + create** (leave all other settings with their default value):

1. On the **Review + create** tab of the **Create a virtual machine** blade, select **Create**.

   > **Note**: Wait for the provisioning to complete. This should take less about 3 minutes.

1. Connect to the newly provisioned Azure VM via RDP. 

1. Within the RDP session to az12001a-vm2, download PuTTY from [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Ensure that you can establish SSH session to both az12001a-vm0 and az12001a-vm1 via their private IP addresses (192.168.0.4 and 192.168.0.5, respectively). 

> **Result**: After you completed this exercise, you have provisioned Azure network resources necessary to support highly available SAP HANA deployments


## Exercise 4: Remove lab resources

Duration: 10 minutes

In this exercise, you will remove resources provisioned in this lab.

#### Task 1: Open Cloud Shell

1. At the top of the portal, click the **Cloud Shell** icon to open Cloud Shell pane and choose Bash as the shell.

1. In the Cloud Shell pane, run the following command to set the value of the variable `RESOURCE_GROUP_PREFIX` to the prefix of the name of the resource group containing the resources you provisioned in this lab:

   ```cli
   RESOURCE_GROUP_PREFIX='az12001a-'
   ```

1. In the Cloud Shell pane, run the following command to list all resource groups you created in this lab:

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv
   ```

1. Verify that the output contains only the resource group you created in this lab. This resource group with all of their resources will be deleted in the next task.

#### Task 2: Delete resource groups

1. In the Cloud Shell pane, run the following command to delete the resource group and their resources.

   ```cli
   az group list --query "[?starts_with(name,'$RESOURCE_GROUP_PREFIX')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. Close the Cloud Shell pane.

> **Result**: After you completed this exercise, you have removed the resources used in this lab.
