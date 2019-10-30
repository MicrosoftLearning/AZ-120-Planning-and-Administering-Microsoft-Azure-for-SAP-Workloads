# AZ 120 Module 3: Implementing SAP on Azure
# Lab 3a: Implement SAP architecture on Azure VMs running Linux

Estimated Time: 120 minutes

All tasks in this lab are performed from the Azure portal (including the Bash Cloud Shell session)  

   > **Note**: When not using Cloud Shell, the lab virtual machine must have Azure CLI installed [**https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest**](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

Lab files: none

## Scenario
  
In preparation for deployment of SAP NetWeaver on Azure, Adatum Corporation wants to implement a demo that will illustrate highly available implementation of SAP NetWeaver on Azure VMs running the SUSE distribution of Linux.

## Objectives
  
After completing this lab, you will be able to:

-   Provision Azure resources necessary to support a highly available SAP NetWeaver deployment

-   Configure operating system of Azure VMs running Linux to support a highly available SAP NetWeaver deployment

-   Configure clustering on Azure VMs running Linux to support a highly available SAP NetWeaver deployment

## Requirements

-   A Microsoft Azure subscription with the sufficient number of available DSv3 vCPUs (2 x 4) and DSv2 (1 x 1) vCPUs in an Azure region that supports availability zones

-   A lab computer running Windows 10, Windows Server 2016, or Windows Server 2019 with access to Azure


## Exercise 1: Provision Azure resources necessary to support highly available SAP NetWeaver deployments

Duration: 30 minutes

In this exercise, you will deploy Azure infrastructure compute components necessary to configure Linux clustering. This will involve creating a pair of Azure VMs running Linux SUSE in the same availability set.

### Task 1: Create a virtual network that will host a highly available SAP NetWeaver deployment.

1.  From the lab computer, start a Web browser, and navigate to the Azure portal at https://portal.azure.com

1.  If prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you will be using for this lab and the the Global Administrator role in the Azure AD tenant associated with your subscription.

1.  In the Azure Portal, start a Bash session in Cloud Shell. 

    > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1.  In the Cloud Shell pane, run the following command to specify the Azure region that supports availability zones and where you want to create resources for this lab (replace `<region>` with the name of the Azure region):

    ```
    LOCATION='<region>'
    ```

1.  In the Cloud Shell pane, run the following command to create a resource group in the region you specified:

    ```
    RESOURCE_GROUP_NAME='az12003a-sap-RG'

    az group create --resource-group $RESOURCE_GROUP_NAME --location $LOCATION
    ```

1.  In the Cloud Shell pane, run the following command to create a virtual network with a single subnet in the resource group you created:

    ```
    VNET_NAME='az12003a-sap-vnet'

    VNET_PREFIX='10.3.0.0/16'

    SUBNET_NAME='sapSubnet'

    SUBNET_PREFIX='10.3.0.0/24'

    az network vnet create --resource-group $RESOURCE_GROUP_NAME --location $LOCATION --name $VNET_NAME --address-prefixes $VNET_PREFIX --subnet-name $SUBNET_NAME --subnet-prefixes $SUBNET_PREFIX
    ```

1.  In the Cloud Shell pane, run the following command to identify the Resource Id of the subnet of the newly created virtual network:

    ```
    az network vnet subnet list --resource-group $RESOURCE_GROUP_NAME --vnet-name $VNET_NAME --query "[?name == '$SUBNET_NAME'].id" --output tsv
    ```

1.  Copy the resulting value to Clipboard. You will need it in the next task.

### Task 2: Deploy Azure Resource Manager template provisioning Azure VMs running Linux SUSE that will host a highly available SAP NetWeaver deployment

1.  On the lab computer, start a browser and browse to [**https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md**](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)

    > **Note**: Make sure to use Microsoft Edge or a third party browser. Do not use Internet Explorer.

1.  On the page titled **SAP NetWeaver 3-tier compatible template using a Marketplace image - MD**, click **Deploy to Azure**. This will automatically redirect your browser to the Azure portal and display the **SAP NetWeaver 3-tier (managed disk)** blade.

1.  On the **SAP NetWeaver 3-tier (managed disk)** blade, click **Edit template**

1.  On the **Edit template** blade, locate the variable named **images**, locate the **SLES 12** section within the variable definition, and change the value of the `sku` key to `12-SP4`, so it looks as follows:

    ```
    "sku": "12-SP4", 
    ```

1.  Click **Save**. 

1.  Back on the **SAP NetWeaver 3-tier (managed disk)** blade, initiate deployment with the following settings:

    -   Subscription: *the name of your Azure subscription*

    -   Resource group: **az12003a-sap-RG**

    -   Location: *the same Azure region that you specified in the first task of this exercise*

    -   SAP System Id: **I20**

    -   Stack Type: **ABAP**

    -   Os Type: **SLES 12**

    -   Dbtype: **HANA**

    -   Sap System Size: **Demo**

    -   System Availability: **HA**

    -   Admin Username: **student**

    -   Authentication Type: **password**

    -   Admin Password Or Key: **Pa55w.rd1234**

    -   Subnet Id: *the value you copied into Clipboard in the previous task*

    -   Availability Zones: **1,2**

    -   Location: **[resourceGroup().location]**

    -   _artifacts Location: **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-3-tier-marketplace-image-md/**

    -   _artifacts Location Sas Token: *leave blank*

1.  Do not Wait for the deployment to complete but instead proceed to the next task. 

### Task 3: Deploy a jump host

   > **Note**: Since Azure VMs you deployed in the previous task are not accessible from Internet, you will deploy an Azure VM running Windows Server 2019 Datacenter that will serve as a jump host. 

1.  From the lab computer, in the Azure portal, click **+ Create a resource**.

1.  From the **New** blade, initiate creation of a new Azure VM based on the **Windows Server 2019 Datacenter** image.

1.  Provision a Azure VM with the following settings:

    -   Subscription: *the name of your Azure subscription*

    -   Resource group: *the name of a new resource group* **az12003a-dmz-RG**

    -   Virtual machine name: **az12003a-vm0**

    -   Region: *the same Azure region where you deployed Azure VMs in the previous tasks of this exercise*

    -   Availability options: **No infrastructure redundancy required**

    -   Image: **Windows Server 2019 Datacenter**

    -   Size: **Standard D2s v3**

    -   Username: **Student**

    -   Password: **Pa55w.rd1234**

    -   Public inbound ports: **Allow selected ports**

    -   Select inbound ports: **RDP (3389)**

    -   Already have a Windows license?: **No**

    -   OS disk type: **Standard HDD**

    -   Virtual network: **az12003a-sap-vnet**

    -   Subnet: *a new subnet named* **bastionSubnet (10.3.255.0/24)**

    -   Public IP: *a new IP address named* **az12003a-vm0-ip**

    -   NIC network security group: **Basic**

    -   Public inbound ports: **Allow selected ports**

    -   Select inbound ports: **RDP (3389)**

    -   Accelerated networking: **Off**

    -   Place this virtual machine behind an existing load balancing solutions: **No**

    -   Enable basic plan for free: **No**

    -   Boot diagnostics: **Off**

    -   OS guest diagnostics: **Off**

    -   System assigned managed identity: **Off**

    -   Login with AAD credentials (Preview): **Off**

    -   Enable auto-shutdown: **Off**

    -   Enable backup: **Off**

    -   Extensions: *None*

    -   Tags: *None*

1.  Wait for the provisioning to complete. This should take a few minutes.

> **Result**: After you completed this exercise, you have provisioned Azure resources necessary to support highly available SAP NetWeaver deployments


## Exercise 2: Configure Azure VMs running Linux to support a highly available SAP NetWeaver deployment

Duration: 30 minutes

In this exercise, you will configure Azure VMs running SUSE Linux Enterprise Server to accommodate a highly available SAP NetWeaver deployment.

### Task 1: Configure networking of the database tier Azure VMs.

   > **Note**: Before you start this task, ensure that the template deployments you initiated in the previous exercise have successfully completed. 

1.  From the lab computer, in the Azure portal, navigate to the blade of the **i20-db-0** Azure VM.

1.  From the **i20-db-0** blade, navigate to its **Networking** blade. 

1.  From the **i20-db-0 - Networking** blade, navigate to the network interface of the i20-db-0. 

1.  From the blade of the network interface of the i20-db-0, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1.  On the **ipconfig1** blade, set the private IP address to **10.3.0.20**, change its assignment to **Static** and save the change.

1.  In the Azure portal, navigate to the blade of the **i20-db-1** Azure VM.

1.  From the **i20-db-1** blade, navigate to its **Networking** blade. 

1.  From the **i20-db-1 - Networking** blade, navigate to the network interface of the i20-db-1. 

1.  From the blade of the network interface of the i20-db-1, navigate to its IP configurations blade and, from there, display its **ipconfig1** blade.

1.  On the **ipconfig1** blade, set the private IP address to **10.3.0.21**, change its assignment to **Static** and save the change.


### Task 2: Connect to the database tier Azure VMs.

1.  From the lab computer, in the Azure portal, navigate to the **az12003a-vm0** blade.

1.  From the **az12003a-vm0** blade, connect to the Azure VM az12003a-vm0 via Remote Desktop. 

1.  Within the RDP session to az12003a-vm0, in Server Manager, navigate to the **Local Server** view and turn off **IE Enhanced Security Configuration**.

1.  Within the RDP session to az12003a-vm0, download and install PuTTY from [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1.  Use PuTTY to connect via SSH to **i20-db-0** Azure VM. Acknowledge the security alert and, when prompted, provide the following credentials:

    -   Login as: **student**

    -   Password: **Pa55w.rd1234**

1.  Use PuTTY to connect via SSH to **i20-db-1** Azure VM with the same credentials.


### Task 3: Examine the storage configuration of the database tier Azure VMs.

1.  From within the PuTTY SSH session to i20-db-0 Azure VM, run the following command to elevate privileges: 

     ```
     sudo su -
     ```

1.  When prompted for the password, type **Pa55w.rd1234** and press the **Enter** key. 

1.  In the SSH session to i20-db-0, verify that all of the SAP HANA related volumes (including **/usr/sap**, **/hana/shared**, **/hana/backup**, **/hana/data**, and **/hana/logs**) are propertly mounted by running:

    ```
    df -h
    ```

1.  Repeat the previous steps on the i20-db-1 Azure VM.


### Task 4: Enable cross-node password-less SSH access

1.  In the SSH session to i20-db-0, open the file **/etc/ssh/sshd\_config** in the vi editor by running (alternatively, you can use any other editor):

    ```
    vi /etc/ssh/sshd_config
    ```

1.  In the **/etc/ssh/sshd\_config** file, locate the **PermitRootLogin** and **AuthorizedKeysFile** entries, and configure them as follows:
    ```
    PermitRootLogin yes
    AuthorizedKeysFile      /root/.ssh/authorized_keys
    ```

1.  Save the changes and close the editor.

1.  In the SSH session to i20-db-0, restart sshd daemon by running:

    ```
    systemctl restart sshd
    ```

1.  Repeat the previous four steps on the i20-db-1 Azure VM.

1.  In the SSH session to i20-db-0, generate passphrase-less SSH key by running:

    ```
    ssh-keygen -tdsa
    ```

1.  When prompted, press **Enter** three times and then display the key by running: 

    ```
    cat /root/.ssh/id_dsa.pub
    ```

1.  Copy the value of the key into Clipboard.

1.  In the SSH session to i20-db-1, create the directory **/root/.ssh/** by running:

    ```
    mkdir /root/.ssh
    ```

1.  In the SSH session to i20-db-1, open the file **/root/.ssh/authorized\_keys** in the vi editor by running:

    ```
    vi /root/.ssh/authorized_keys
    ```

1.  In the editor window, paste the key you generated on i20-db-1.

1.  Save the changes and close the editor.

1.  In the SSH session to i20-db-1, generate passphrase-less SSH key by running:

    ```
    ssh-keygen -tdsa
    ```

1.  When prompted, press **Enter** three times and then display the key by running: 

    ```
    cat /root/.ssh/id_dsa.pub
    ```

1.  Copy the value of the key into Clipboard.

1.  In the SSH session to i20-db-0, open the file **/root/.ssh/authorized\_keys** in the vi editor by running:

    ```
    vi /root/.ssh/authorized_keys
    ```

1.  In the editor window, paste the key you generated on i20-db-1.

1.  Save the changes and close the editor.

1.  In the SSH session to i20-db-0, generate passphrase-less SSH key by running:

    ```
    ssh-keygen -t rsa
    ```

1.  When prompted, press **Enter** three times and then display the key by running: 

    ```
    cat /root/.ssh/id_rsa.pub
    ```

1.  Copy the value of the key into Clipboard.

1.  In the SSH session to i20-db-1, open the file **/root/.ssh/authorized\_keys** in the vi editor by running:

    ```
    vi /root/.ssh/authorized_keys
    ```

1.  In the editor window, paste the key you generated on i20-db-0 starting from a new line.

1.  Save the changes and close the editor.

1.  In the SSH session to i20-db-1, generate passphrase-less SSH key by running:

    ```
    ssh-keygen -t rsa
    ```

1.  When prompted, press **Enter** three times and then display the key by running: 

    ```
    cat /root/.ssh/id_rsa.pub
    ```

1.  Copy the value of the key into Clipboard.

1.  In the SSH session to i20-db-0, open the file **/root/.ssh/authorized\_keys** in the vi editor by running:

    ```
    vi /root/.ssh/authorized_keys
    ```

1.  In the editor window, paste the key you generated on i20-db-1 starting from a new line.

1.  Save the changes and close the editor.

1.  To verify that the configuration on was successful, in the SSH session to i20-db-0, establish an SSH session as **root** from i20-db-0 to i20-db-1 by running: 

    ```
    ssh root@i20-db-1
    ```

1.  When prompted whether you are sure to continue connecting, type `yes` and press the **Enter** key. 

1.  Ensure that you are not prompted for the password.

1.  Close the SSH session from i20-db-0 to i20-db-1 by running: 

    ```
    exit
    ```

1.  In the SSH session to i20-db-1, establish an SSH session as **root** from i20-db-1 to i20-db-0 by running: 

    ```
    ssh root@i20-db-0
    ```

1.  When prompted whether you are sure to continue connecting, type `yes` and press the **Enter** key. 

1.  Ensure that you are not prompted for the password.

1.  Close the SSH session from i20-db-1 to i20-db-0 by running: 

    ```
    exit
    ```

### Task 5: Add YaST packages, update the Linux operating system, and install HA Extensions

1.  In the SSH session to i20-db-0, run the following to launch YaST:

    ```
    yast
    ```

1.  In **YaST Control Center**, select **Software -\> Add-On Products** and press **Enter**. This will load **Package Manager**.

1.  On the **Installed Add-on Products** screen, verify that **Public Cloud Module** is already installed. Then, press **F9** twice to return to the shell prompt.

1.  In the SSH session to i20-db-0, run the following to update operating system (when prompted, type **y** and press the **Enter** key):

    ```
    zypper update
    ```

1. In the SSH session to i20-db-0, run the following to update HA extensions dependencies (when prompted, type **y**, press the **Enter** key, read through the **SUSE End User License Agreement**, type **q**, type **yes** to agree with the terms of the licensing terms, and press the **Enter** key again).

    ```
    zypper install sle-ha-release fence-agents
    ```

1. Repeat the previous steps on i20-db-1.

> **Result**: After you completed this exercise, you have onfigured operating system of Azure VMs running Linux to support a highly available SAP NetWeaver deployment

## Exercise 3: Configure clustering on Azure VMs running Linux to support a highly available SAP NetWeaver deployment

Duration: 60 minutes

In this exercise, you will configure clustering on Azure VMs running Linux to support a highly available SAP NetWeaver deployment.

### Task 1: Configure clustering

1.  Within the RDP session to az12003a-vm0, in the PuTTY-based SSH session to i20-db-0, run the following to initiate configuration of an HA cluster on i20-db-0:

    ```
    ha-cluster-init
    ```

1.  When prompted, provide the following answers:

    -   Do you want to continue anyway (y/n)?: **y**

    -   /root/.ssh/id_rsa already exists - overwrite (y/n)? **n**

    -   Address for ring0 [10.3.0.20]: **ENTER**

    -   Port for ring0 [5405]: **ENTER**

    -   Do you wish to use SBD (y/n)?: **n**

    -   Do you wish to configure a virtual IP address (y/n)?: **n**

   > **Note**: The clustering setup generates an **hacluster** account with its password set to **linux**. You will chane it later in this task.

1.  Within the RDP session to az12003a-vm0, in the PuTTY-based SSH session to i20-db-1, run the following to join the HA cluster on i20-db-0 from i20-db-1:

    ```
    ha-cluster-join
    ```

1.  When prompted, provide the following answers:

    -   Do you want to continue anyway (y/n)? **y**

    -   IP address or hostname of existing node (e.g.: 192.168.1.1) \[\]: **i20-db-0**

    -   /root/.ssh/id\_rsa already exists - overwrite (y/n)? **n**

    -   /root/.ssh/id\_dsa already exists - overwrite (y/n)? **n**

    -   Address for ring0 [10.3.0.21]: **ENTER**

1.  In the PuTTY-based SSH session to i20-db-0, run the following to set the password of the **hacluster** account to **Pa55w.rd1234** (type the new password when prompted): 
    ```
    passwd hacluster

    ```

1.  Repeat the previous step on i20-db-1.

### Task 2: Review corosync configuration

1.  Within the RDP session to az12003a-vm0, in the PuTTY-based SSH session to i20-db-0, review content of the **/etc/corosync/corosync.conf** file by running:

    ```
    cat /etc/corosync/corosync.conf
    ```

1.  Note the `transport: udpu` entry and the `nodelist` section:
    ```
    [...]
       interface { 
           [...] 
       }
       transport:      udpu
    } 
    nodelist {
       node {
         ring0_addr:     10.3.0.20
         nodeid:     1
       }
       node {
         ring0_addr:     10.3.0.21
         nodeid:     2
       } 
    }
    logging {
        [...]
    ```

1.  Repeat the previous steps on i20-db-1.


### Task 3: Configure STONITH clustering options

1.  Within the RDP session to az12003a-vm0, in the PuTTY-based SSH session to i20-db-0, create a new file named **crm-defaults.txt** with the following content:

    ```
    property $id="cib-bootstrap-options" \
      no-quorum-policy="ignore" \
      stonith-enabled="true" \
      stonith-action="reboot" \
      stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
      resource-stickiness="1000" \
      migration-threshold="5000"
    op_defaults $id="op-options" \
      timeout="600"
    ```

1.  Save the changes and close the editor.

1.  In the PuTTY-based SSH session to i20-db-0, apply the settings in the newly created file by running:

    ```
    crm configure load update crm-defaults.txt
    ```

### Task 4: Identify the value of the Azure subscription Id and the Azure AD tenant Id

1.  From the lab computer, in the browser window, in the Azure portal at **https://portal.azure.com**, ensure that you are signed in with the user account that has the Global Administrator role in the Azure AD tenant associated with your subscription.

1.  In the Azure Portal, start a Bash session in Cloud Shell. 

1.  In the Cloud Shell pane, run the following command to identify the id of your Azure subscription and the id of the corresponding Azure AD tenant:

    ```
    az account show --query '{id:id, tenantId:tenantId}' --output json
    ```

1.  Copy the resulting values to Notepad. You will need it in the next task.


### Task 5: Create an Azure AD application for the STONITH device

1.  In the Azure portal, navigate to the Azure Active Directory blade.

1.  From the Azure Active Directory blade, navigate to the **App registrations** blade and then click **+ New registration**:

1.  On the **Register an application** blade, specify the following settings, and click **Register**:

    -   Name: **Stonith app**

    -   Supported account type: **Accounts in this organizational directory only**

1.  On the **Stonith app** blade, copy the value of **Application (client) ID** to Notepad. This will be referred to as **login_id** later in this exercise:

1.  On the **Stonith app** blade, click **Certificates & secrets**.

1.  On the **Stonith app - Certificates & secrets** blade, click **+ New client secret**.

1.  In the **Add a client secret** pane, in the **Description** text box, type **STONITH app key**, in the **Expires** section, leave the default **In 1 year**, and then click **Add**.

1.  Copy the resulting secret value to Notepad (this entry is displayed only once, after you click **Add**). This will be referred to as **password** later in this exercise:


### Task 6: Grant permissions to Azure VMs to the service principal of the STONITH app 

1.  In the Azure portal, navigate to the blade of the **i20-db-0** Azure VM

1.  From the  **i20-db-0** blade, display the **i20-db-0 - Access control (IAM)** blade.

1.  From the **i20-db-0 - Access control (IAM)** blade, add a role assignment with the following settings:

    -   Role: **Owner**

    -   Assign access to: **Azure AD user, group, or service principal**

    -   Select: **Stonith app**

1.  Repeat the previous steps to assign the Stonith app the Owner role to the **i20-db-1** Azure VM


### Task 7: Configure the STONITH cluster device 

1.  Within the RDP session to az12003a-vm0, in the PuTTY-based SSH session to i20-db-0, create a new file named **crm-fencing.txt** with the following content (where `subscription_id`, `tenant_id`, `login_id,` and `password` are placeholders for the values you identified in Exercise 3 Task 5:

    ```
    primitive rsc_st_azure_1 stonith:fence_azure_arm \
         params subscriptionId="subscription_id" resourceGroup="az12003a-sap-RG" tenantId="tenant_id" login="login_id" passwd="password"
    primitive rsc_st_azure_2 stonith:fence_azure_arm \
         params subscriptionId="subscription_id" resourceGroup="az12003a-sap-RG" tenantId="tenant_id" login="login_id" passwd="password"
    colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
    ```

1.  From the SSH session on s03-db-0, apply the settings in the file by running **crm configure load update crm-fencing.txt**:
    ```
    crm configure load update crm-fencing.txt
    ```

### Task 8: Review clustering configuration on Azure VMs running Linux by using Hawk

1.  Within the RDP session to az12003a-vm0, start Internet Explorer and navigate to **https://i20-db-0:7630**. This should display the SUSE Hawk sign-in page.

   > **Note**: Ignore **This site is not secure** message.

1.  On the SUSE Hawk sign in page, login by using the following credentials:

    -   Username: **hacluster**

    -   Password: **Pa55w.rd1234**

1.  Verify that the cluster status is healthy. If you are seeing a message indicating that one of two cluster nodes is unclean, restart that node from the Azure portal.

> **Result**: After you completed this exercise, you have configured clustering on Azure VMs running Linux to support a highly available SAP NetWeaver deployment


## Exercise 4: Remove lab resources

Duration: 10 minutes

In this exercise, you will remove resources provisioned in this lab.

#### Task 1: Open Cloud Shell

1. At the top of the portal, click the **Cloud Shell** icon to open Cloud Shell pane and choose Bash as the shell.

1. At the **Cloud Shell** command prompt at the bottom of the portal, type in the following command and press **Enter** to list all resource groups you created in this lab:

    ```
    az group list --query "[?starts_with(name,'az12003a-')]".name --output tsv
    ```

1. Verify that the output contains only the resource groups you created in this lab. These groups will be deleted in the next task.

#### Task 2: Delete resource groups

1. At the **Cloud Shell** command prompt, type in the following command and press **Enter** to delete the resource groups you created in this lab

    ```
    az group list --query "[?starts_with(name,'az12003a-')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. Close the **Cloud Shell** prompt at the bottom of the portal.


> **Review**: After you completed this exercise, you have removed the resources used in this lab.