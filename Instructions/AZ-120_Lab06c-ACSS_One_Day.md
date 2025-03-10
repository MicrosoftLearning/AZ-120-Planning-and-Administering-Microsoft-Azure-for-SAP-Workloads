---
lab:
    title: '06c - Overview of deployment Azure Center for SAP solutions'
    module: 'Design and implement an infrastructure to support SAP workloads on Azure'
---

# AZ 1006 Module: Design and implement an infrastructure to support SAP workloads on Azure
# AZ-1006 1-day course lab 6c: Overview of deployment with Azure Center for SAP solutions'

Estimated Time: 60 minutes

All tasks in this AZ-1006 1-day course lab are performed from the Azure portal

## Objectives

After completing this lab, you'll be able to:

- Deploy the infrastructure hosting SAP workloads in Azure by using Azure Center for SAP solutions

>**Important**: The prerequisites implemented in this exercise are *not* meant to represent the best practices for deploying SAP workloads in Azure by using Azure Center for SAP solutions. Their purpose is to minimize time, cost, and resources required to evaluate the mechanics of deploying SAP workloads in Azure by using Azure Center for SAP solutions and performing post-deployment management and maintenance tasks. Implementing the prerequisites includes the following activities:

- Creating a Microsoft Entra user-assigned managed identity to be used by Azure Center for SAP solutions for Azure Storage access during its deployment.
- Granting the Microsoft Entra user-assigned managed identity that is used to perform the deployment access to the Azure subscription
- Creating the Azure virtual network that hosts all of the Azure virtual machines included in the deployment.

These activities correspond to the following tasks of this exercise:

- Task 1: Create a Microsoft Entra user-assigned managed identity
- Task 2: Configure Azure role-based access control (RBAC) role assignments for the Microsoft Entra ID user-assigned managed identity
- Task 3: Create the Azure virtual network

## Instructions

### Exercise 1: Implement the minimum prerequisites for evaluating deployment of SAP workloads in Azure by using Azure Center for SAP solutions

Duration: 15 minutes

In this exercise, you implement the bare minimum prerequisites for evaluating deploying SAP workloads in Azure by using Azure Center for SAP solutions. This includes the following activities:

>**Important**: The prerequisites implemented in this exercise are *not* meant to represent the best practices for deploying SAP workloads in Azure by using Azure Center for SAP solutions. Their purpose is to minimize time, cost, and resources required to evaluate the mechanics of deploying SAP workloads in Azure by using Azure Center for SAP solutions and performing post-deployment management and maintenance tasks.

Implementing the prerequisites includes the following activities:

- Creating a Microsoft Entra user-assigned managed identity to be used by Azure Center for SAP solutions for Azure access during its deployment.
- Creating the Azure virtual network that hosts all of the Azure virtual machines included in the deployment.

These activities correspond to the following tasks of this exercise:

- Task 1: Create a Microsoft Entra user-assigned managed identity
- Task 2: Create the Azure virtual network

#### Task 1: Create a Microsoft Entra user-assigned managed identity

In this task, you create a Microsoft Entra user-assigned managed identity to be used by Azure Center for SAP solutions for Azure Storage access during its deployment.

1. From the lab computer, start a web browser, navigate to the Azure portal at `https://portal.azure.com`, and authenticate by using a Microsoft Account or Microsoft Entra ID account with the Owner role in the Azure subscription you use in this lab.
1. In the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Managed Identities**.
1. On the **Managed Identities** page, select **+ Create**.
1. On the **Basics** tab of the **Create User Assigned Managed Identity** page, specify the following settings and then select **Review + Create**:

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|The name of a **new** resource group **acss-infra-RG**|
    |Region|the name of the Azure region that you use for the ACSS deployment|
    |Name|**acss-infra-MI**|

1. On the **Review** tab, wait for the validation process to complete and select **Create**.
1. Wait for the provisioning process to complete and select **Go to resource** to prepare for the next task. The provisioning should take just a few seconds.

#### Task 2: Configure Azure role-based access control (RBAC) subscription contributor

1. Continue in the Azure portal on the Managed Identity overview page for **acss-infra-MI** from the completion of the last task.
1. On the **acss-infra-MI** Managed Identity page menu choose **Azure role assignments**.
1. On the **Azure role assignments** page, select **+ Add role assignment**.

1. On the **Add role assignment** tab of the **Add role assignment** pane, specify the following settings, and **save**:

    |Setting|Value|
    |---|---|
    |Scope|**Subscription**|
    |Subscription|The name of the Azure subscription used in this lab|
    |Role|**Contributor**|

#### Task 3: Configure Azure role-based access control (RBAC) role assignments for the Microsoft Entra ID user account that will be used to perform the deployment

##### Add Identity: "Azure Center for SAP solutions service role"

1. In the Azure portal, in the **Search** text box, search for and select **Subscriptions**.
1. On the **Subscriptions** page, select the entry representing the Azure subscription you will be using for this lab. 
1. On the page displaying the properties of the Azure subscription, select **Access control (IAM)**.
1. On the **Access control (IAM)** page, select **+ Add** and then, in the drop-down menu select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, in the listing of **Job function roles**, search for and select the **Azure Center for SAP solutions service role** entry, and the select **Next**.
1. On the **Members** tab of the **Add role assignment** page, for **Assign access to**, select **Managed Identity** and then click **+ Select members**.
1. In the **Select managed identities** pane, specify the following settings, and then click **Select**:

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Managed identity|**User-assigned managed identity**|
    |Select|**acss-infra-RG/subscriptions/...**|

1. Back on the **Members** tab, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

##### Add Identity: "Azure Center for SAP solutions administrator

1. Back on the **Access control (IAM)** page, select **+ Add** and then, in the drop-down menu select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, in the listing of **Job function roles**, search for and select the **Azure Center for SAP solutions administrator** entry, and the select **Next**.
1. On the **Members** tab
    - for **Assign access to**, select **User, Group, or Service principle**
    - click **+ Select members**.
1. In the **Select members** pane, in the **Select** text box, enter the name of the Microsoft Entra ID user account you used to access the Azure subscription you are using for this lab, select it in the list of results matching your entry, and then click **Select**.
1. Back on the **Members** tab, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

##### Add Identity: "Managed Identity Operator"

1. Back on the **Access control (IAM)** page, select **+ Add** and then, in the drop-down menu select **Add role assignment**.
1. On the **Role** tab of the **Add role assignment** page, in the listing of **Job function roles**, search for and select the **Managed Identity Operator** entry, and the select **Next**.
1. On the **Members** tab
    - for **Assign access to**, select **User, Group, or Service principle**
    - click **+ Select members**.
1. In the **Select members** pane, in the **Select** text box, enter the name of the Microsoft Entra ID user account you used to access the Azure subscription you are using for this lab, select it in the list of results matching your entry, and then click **Select**.
1. Back on the **Members** tab, select **Review + assign**.
1. On the **Review + assign** tab, select **Review + assign**.

#### Task 4: Create the virtual network

In this task, you create the Azure virtual network that hosts all of the Azure virtual machines included in the deployment. In addition, within the virtual network, you create the following subnets:

- bastion
- app - intended for hosting the SAP application and SAP Central Services instance servers
- db - intended for hosting the SAP database tier

1. On the lab computer, in the web browser window displaying the Azure portal, in the **Search** text box, search for and select **Virtual networks**.
1. On the **Virtual networks** page, select **+ Create**.
1. On the **Basics** tab of the **Create virtual network** page, specify the following settings and select **Next**:

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|**acss-infra-RG**|
    |Virtual network name|**acss-infra-VNET**|
    |Region|the name of the same Azure region you used in the previous task of this exercise|

1. On the **Security** tab, under **Azure Bastion** select the **checkbox** to **Enable Azure Bastion**.
1. Specify the following settings and select  **Next**.
    |Setting|Value|
    |---|---|
    |Azure Bastion host name|**acss-infra-vnet-bastion**|
    |Azure Bastion public IP address|**(New) acss-infra-bastion**|

1. On the **IP addresses** tab, specify the following settings and then select **add**:

    |Setting|Value|
    |---|---|
    |IP address space|**10.0.0.0/16 (65,536 addresses)**|

1. In the list of subnets, select the trash bin icon to **delete** the **default subnet**.

1. Select **+ Add a subnet**.
1. In the **Add a subnet** pane, specify the following settings and then select **Add** (leave others with their default values):

    |Setting|Value|
    |---|---|
    |Name|**acss-admin**|
    |Starting address|**10.0.0.0**|
    |Size|**/24 (256 addresses)**|

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

    >**Note**: Wait for ~3 minutes for the provisioning process to proceed partially before you proceed to the next task. The entire provisioning could take 25 minutes for Azure Bastion so we will **not wait**.

### Exercise 2: Deploy the infrastructure that hosts SAP workloads in Azure by using Azure Center for SAP solutions

Duration: 20 minutes

In this exercise, you perform deployment of Azure Center for SAP solutions. This includes the following activity:

- Use Azure Center for SAP solutions to deploy the infrastructure capable of hosting SAP workloads into an Azure subscription.

This activity corresponds to the following task of this exercise:

- Task 1: Create Virtual Instance for SAP (VIS) solutions

>**Note**: Following the successful deployment, you could proceed with installing SAP software by using Azure Center for SAP solutions. However, installing of SAP software is not included in this lab.

>**Note**: For information regarding installing SAP software by using Azure Center for SAP solutions, refer to the Microsoft Learn documentation that describes how to [Get SAP installation media](https://learn.microsoft.com/en-us/azure/sap/center-sap-solutions/get-sap-installation-media) and [Install SAP software](https://learn.microsoft.com/en-us/azure/sap/center-sap-solutions/install-software).

#### Task 1: Create Virtual Instance for SAP (VIS) solutions

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Azure Center for SAP Solutions**.
1. On the **Azure Center for SAP Solutions \| Overview** page, select **Create a new SAP system**.
1. On the **Basics** tab of the **Create Virtual Instance for SAP solutions** page, specify the following settings and select **Next : Virtual machines**

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|**acss-infra-RG**|
    |Name (SID)|**VI1**|
    |Region|the name of the Azure region hosting the ACSS-registered SAP deployment or another region in the same geography|
    |Environment type|**Non-production**|
    |SAP product|**S/4HANA**|
    |Database|**HANA**|
    |HANA scale method|**Scale up (recommended)**|
    |Deployment type|**Distributed**|
    |Virtual network|**acss-infra-VNET**|
    |Application subnet|**app**|
    |Database subnet|**db**|
    |Application OS image options|**Use a marketplace image**|
    |Application OS image|**Red Hat Enterprise Linux 8.4 for SAP Applications - x64 Gen2 latest**|
    |Database OS image options|**Use a marketplace image**|
    |Database OS image|**Red Hat Enterprise Linux 8.4 for SAP Applications - x64 Gen2 latest**|
    |SAP transport option|**Create a new SAP transport directory**|
    |Transport resource group|**acss-infra-RG**|
    |Storage account name|*blank*|
    |Authentication type|**SSH public**|
    |Username|**contososapadmin**|
    |SSH public key source|**Generate new key pair**|
    |Key pair name|**contosovi1key**|
    |SQP FQDN|**sap.contoso.com**|
    |Managed identity source|**Use existing user assigned managed identity**|
    |Managed identity name|**acss-infra-MI**|

    >**Note**: Make sure to select **Red Hat Enterprise Linux 8.4 for SAP Applications - x64 Gen2 latest**.

1. On the **Virtual machines** tab, specify the following settings:

    |Setting|Value|
    |---|---|
    |Generate Recommendation based on|**SAP Application Performance Standard (SAPS) - Select this option to provide SAPS for application tier and Database Memory size and click Generate Recommendations**|
    |SAPS for application tier|**1000**|
    |Memory size for database (GiB)|**128**|

1. Select **Generate Recommendation**.
1. Review the size and the number of VMs for the ASCS, application, and database virtual machines.

    >**Note**: If needed, adjust the recommended sizes by selecting the **See all Sizes** link for each set of virtual machines and choosing an alternative size. By default, the distributed deployment type with high availability as well as the application tier SAPS and database memory size specified above results in the following minimum VM SKU recommendations:
    - 1 x Standard_D4ds_v4 for the ASCS VMs (4 vCPUs and 16 GiB of memory each)
    - 1 x Standard_D4ds_v4 for the application VMs (4 vCPUs and 16 GiB of memory each)
    - 1 x Standard_E16ds_v5 for the database VMs (16 vCPUs and 128 GiB of memory each)

    >**Note**: If needed, you can request quota increase by selecting the **Request Quota** link for a specific SKU of virtual machines and submitting a quota increase request. The processing of a request typically takes a few minutes.

    >**Note**: Azure Center for SAP solutions enforces the usage of the SAP supported VM SKUs during deployment.

1. On the **Virtual machines** tab, in the **Data disks** section, select the **View and customize configuration** link.
1. On the **Database disk configuration** page, review the recommended configuration without making any changes and select **Close**.
1. Back on the **Virtual machines** tab, select **Next : Visualize Architecture**.
1. On the **Visualize Architecture** tab, review the diagram illustrating the recommended architecture and select **Review + create**.
1. On the **Review + create** tab, wait for the validation process to complete, select the checkbox confirm that you have ample quota available in the deployment region to avoid running into "Insufficient Quota" error, and select **Create**.
1. When prompted, in the **Generate new key pair** window, select **Download private key and create resource**.

    >**Note**: The private key required to connect to the Azure VMs included in the deployment will be downloaded to the computer from which you are running this lab.

    >**Note**: Wait only ~3 minutes for the provisioning process to proceed partially before you proceed to the next task. The entire provisioning could take 25 minutes, but we will **not wait**.

    >**Note**: Following the deployment, you could proceed to installing SAP software by using Azure Center for SAP solutions. In this lab, you will explore the capabilities of the Azure Center for SAP solutions without installing SAP software.

### Exercise 3: Explore a VIS for SAP workloads in Azure by using Azure Center for SAP solutions

Duration: 25 minutes

In this exercise you view the properties and functions within a Azure Center for SAP solutions VIS. You will also connect to a VM created by ACSS and explore the directories created.

#### Task 1: Review the ACSS VIS Page

1. Once The VIS deployment completes, view the **VI1 Virtual Instance for SAP solutions** page and explore the available information from the Virtual Instance for SAP solutions page menu, including:

    1. **Overview**
        - **Get Started** tab displays options to "Install SAP software" and to "Confirm already installed SAP software."
        - **Properties** tab displays the deployed virtual machines.
        - **Monitoring** tab displays "App + Central server VM CPU utilization," "Database disk IOPS consumption" and "Database VM CPU utilization."
    1. **Monitoring** > **Quality Insights**  on the Quality insights page:
        - **Virtual Machines**: explore Compute List, Compute Extensions and Compute+OS Disk.
        - **Configuration Checks**: explore the subitem choices: Accelerated Networking, Public IP, Backup, and Load Balancer.
    1. **Cost Management** > **Cost analysis**
        - Expand items under **Resource** column.

#### Task 2: Connect to DB VM and review ACSS configuration

1. In the Azure Portal, Select the Virtual Machines and select the database VM created in ACSS, **vi1dbvm**.
1. Select Connect > Azure Bastion and choose the following settings and teh select **connect**:
    - Authentication Type **SSH Private Key from Local File**
    - Username **contososapadmin** 
    - Local File ***private key you downloaded***
    
1. At the Bash prompt enter: `mount` and find the output as follows for mapping:

    ```output
    /dev/sdb1 on /mnt type ext4 (rw,relatime,x-systemd.requires=cloud-init.service,_netdev)
    /dev/mapper/vg_sap-lv_usrsap on /usr/sap type xfs (rw,relatime,attr2,inode64,noquota)
    /dev/mapper/vg_hana_shared-lv_hana_shared on /hana/shared type xfs (rw,relatime,attr2,inode64,noquota)
    /dev/mapper/vg_hana_backup-lv_hana_backup on /hana/backup type xfs (rw,relatime,attr2,inode64,noquota)
    /dev/mapper/vg_hana_data-lv_hana_data on /hana/data type xfs (rw,relatime,attr2,inode64,sunit=512,swidth=2048,noquota)
    /dev/mapper/vg_hana_log-lv_hana_log on /hana/log type xfs (rw,relatime,attr2,inode64,sunit=128,swidth=384,noquota)
    ```

1. At the prompt enter: `more /etc/fstab` and find output similar to the following that mapping for the SAP Media (`sapmedia`) share:

    ```output
    10.100.1.8:/vi2nfs9fbec656c6a60a7/sapmedia on /usr/sap/install type nfs4     (rw,relatime,vers=4.1,rsize=65536,wsize=65536,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.100.1.6,local_
    lock=none,addr=10.100.1.8)
    ```

1. At the prompt enter: `cd /usr/sap/install` to navigate to the directory that maps to `sapmedia`.

### Exercise 4 (optional): Maintain SAP workloads in Azure by using Azure Center for SAP solutions

Duration: 20 minutes

In this exercise, you review post-deployment management and monitoring of SAP workloads by using Azure Center for SAP solutions. This includes the following activities:

- Review prerequisites for backup of SAP workloads managed by Azure Center for SAP solutions
- Review prerequisites for disaster recovery of SAP workloads managed by Azure Center for SAP solutions
- Reviewing monitoring options available for SAP workloads managed by Azure Center for SAP solutions
- Deleting all of the Azure resources provisioned in this lab.

These activities correspond to the following tasks of this exercise:

- Task 1: Review prerequisites for backup of SAP workloads managed by Azure Center for SAP solutions
- Task 2: Review prerequisites for disaster recovery of SAP workloads managed by Azure Center for SAP solutions
- Task 3: Review monitoring options for SAP workloads managed by Azure Center for SAP solutions
- Task 4: Delete the Azure resources provisioned in this lab

#### Task 1: Review prerequisites for backup of SAP workloads managed by Azure Center for SAP solutions

>**Note**: When you configure Azure Backup at the VIS resource level in the Azure Center for SAP solutions, you can, in one step, enable backup for Azure VMs hosting the database, application servers, and SAP Central Services instance, and for the HANA DB. For the HANA DB backup, Azure Center for SAP solutions automatically runs the backup pre-registration script.

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Azure Center for SAP Solutions**.
1. On the **Azure Center for SAP Solutions \| Overview** page, in the vertical navigation menu on the left side, select **Virtual instances for SAP solutions** and, in the list of virtual instances, select the instance you deployed in the previous exercise.
1. On the virtual instance page, in the vertical navigation menu on the left side, in the **Operations** section, select **Backup (preview)**.
1. Note the message indicating that backup can't be set up since SAP software installation/registration for this SAP system isn't complete.

   >**Note**: This is expected. You will not able to set up backup this way until the installation of SAP software is completed. However, completing the setup also involves additional prerequisites, including creation of vaults and backup policies, which we'll review here. 

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Backup center**. 
1. On the **Backup center** page, in the vertical navigation menu on the left side, in the **Manage** section, select **Vaults**.
1. On the **Backup center \| Vaults** page, select **+ Vault**.
1. On the **Start: Create Vault** page, review available vault types, ensure that **Recovery services vault** (which supports **Azure virtual machines** and **SAP HANA in Azure VM** datasource types) is selected, and then select **Continue**.
1. On the **Basics** tab of the **Create Recovery Services vault** page, specify the following settings and select **Next : Redundancy**

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|The name of a **new** resource group **acss-mgmt-RG**|
    |Vault name|**acss-backup-RSV**|
    |Region|the name of the Azure region hosting the ACSS-registered SAP deployment|

1. On the **Redundancy** tab, specify the following settings and select **Next : Vault properties**

    |Setting|Value|
    |---|---|
    |Backup Storage Redundancy|**Geo-redundant**|
    |Cross Region Restore|**Enable**|

1. On the **Vault properties** tab, review the **Enable immutability** setting without enabling it and select **Next : Networking**
1. On the **Networking** tab, accept the default option to **Allow public access from all networks** and select **Review + create**
1. In this exercise **do not** select **Create** as we are only reviewing.
1. On the **Review + create** tab, wait for the validation process to complete and then return to the Azure Center for SAP solutions page (settings for backup will be lost).  

    >**Note**: The [Backup (preview)](https://learn.microsoft.com/azure/sap/center-sap-solutions/acss-backup-integration) feature of Azure Center for SAP solutions UI will become a preferred method for completing the backup configuration after it is released to *General Availability* from *Preview*.

    >**Note**: When configuring backup at the VIS level in the Azure Center for SAP solutions interface, you will be able to leverage the existing vault and its policies.

    >**Note**: Once backup is configured at the VIS level, you can monitor the status of backup jobs of Azure VMs and HANA DB from the VIS interface in the Azure portal.

#### Task 2: Review prerequisites for disaster recovery of SAP workloads managed by Azure Center for SAP solutions

>**Note**: While Azure Center for SAP solutions service is a zone redundant service, there is no Microsoft initiated failover in the event of a region outage. To remediate such scenario, you should configure disaster recovery for SAP systems deployed using Azure Center for SAP solutions by following guidance described in [Disaster recovery overview and infrastructure guidelines for SAP workload](https://learn.microsoft.com/en-us/azure/sap/workloads/disaster-recovery-overview-guide), which involves the use of Azure Site Recovery (ASR). In this task, you'll step through the process of implementing an ASR-based disaster recovery solution which relies on that guidance.

>**Note**: ASR is the recommended solution for application servers and SAP Central Services instances. For database servers, you should consider using native their replication functionality.

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Recovery Services vaults**.
1. On the **Recovery Services vaults** page, select **+ Create**.
1. On the **Basics** tab of the **Create Recovery Services vault** page, specify the following settings (leave others with their default values) and select **Next: Redundancy**.

    |Setting|Value|
    |---|---|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|The name of a **new** resource group **acss-dr-RG**|
    |Vault name|**acss-dr-RSV**|
    |Region|the name of the Azure region that is paired up with the ACSS-registered SAP deployment|

    >**Note**: To identify the region which is a paired up with the one hosting your production workloads, refer to MS Learn documentation describing [Azure paired regions](https://learn.microsoft.com/en-us/azure/reliability/cross-region-replication-azure#azure-paired-regions).

1. On the **Redundancy** tab, specify the following settings and select **Next : Vault properties**

    |Setting|Value|
    |---|---|
    |Backup Storage Redundancy|**Locally-redundant**|

1. On the **Vault properties** tab, review the **Enable immutability** setting without enabling it and select **Next : Networking**
1. On the **Networking** tab, accept the default option to **Allow public access from all networks** and select **Review + create**
1. On the **Review + create** tab, wait for the validation process to complete and select **Create**.

    >**Note**: Do not wait for the provisioning process to complete but instead proceed to the next step. The provisioning might take about 2 minutes.

    >**Note**: Now you will set up the disaster recovery environment in the paired up region in which you created the Recovery Services vault. This environment will include a virtual network that will host replicas of the Azure VMs currently hosted in the primary region where you provisioned Virtual Instance for SAP. 

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Recovery Services vaults**.
1. On the **Recovery Services vaults** page, select **acss-dr-RSV**.
1. On the **acss-dr-RSV** page, in the vertical navigation menu on the left side, in the **Getting started** section, select **Site Recovery**.
1. On the **acss-dr-RSV \| Site Recovery** page, in the **Azure virtual machines** section, select **1. Enable replication**. 
1. On the **Source** tab of the **Enable replication** page, specify the following settings and then select **Next**:

    |Setting|Value|
    |---|---|
    |Region|the name of the Azure region hosting your Virtual Instance for SAP (VIS)|
    |Subscription|The name of the Azure subscription used in this lab|
    |Resource group|**acss-vi-RG**|
    |Virtual machine deployment model|**Resource Manager**|
    |Disaster recovery between availability zones|**No**|

    >**Note**: The **Disaster recovery between availability zones** might be not configurable, depending on whether the source region supports availability zones.

1. On the **Virtual machines** tab, select the first four virtual machines in the list (**vi1appvm0**, **vi1appvm1**, **vi1ascsvm0**, and **vi1ascsvm0**) and then select **Next**.

    >**Note**: As mentioned earlier, the ASR-based replication will be applied to the application servers and SAP Central Services instances. Database servers will be maintained in sync by relying on the native database replication functionality.

1. On the **Replication settings** tab, perform the following actions:

    1. If needed, in the **Target location** drop-down list, select the Azure region in which you created the **acss-dr-RSV** Recovery Services vault.
    1. Ensure that the name of the Azure subscription used in this lab appears in the **Target subscription** drop-down list.
    1. In the **Target resource group** drop-down list, select **acss-dr-RG**.
    1. Below the **Failover virtual network** drop-down list, select **Create new**.
    1. In the **Create virtual network** pane, in the **Name** text box, enter **CONTOSO-VNET-asr**
    1. In the **Address space** section, in the **Address range** text box, replace the default entry with **10.10.0.0/16**.
    1. In the **Subnets** section, in the **Subnet name** text box, enter **app** and, in the **Address range** text box, enter **10.10.0.0/24**.
    1. Below the newly added subnet entry, in the **Subnets** section, in the **Subnet name** text box, enter **db** and, in the **Address range** text box, enter **10.10.2.0/24**.
    1. In the **Create virtual network** pane, select **OK**.
    1. Back on the **Enable replication** page, ensure that the **(new) app (10.10.0.0/24)** entry appears in the **Failover subnet** drop-down list.
    1. In the **Storage** section, select the **View/edit storage configuration** link.
    1. On the **Customize target settings** page, review the resulting configuration but don't make any changes and select **Cancel**.
    1. In the **Availability options** section, select the **View/edit availability options** link.
    1. On the **Availability options** page you have the option of implementing proximity placement groups for target resources but don't make any changes and select **Cancel**.

    >**Note**: You also have the option of configuring capacity reservation.

    >**Note**: You also have the option of configuring replication policy settings.

    >**Important**: Note that the IP address spaces differ between the virtual network in the primary and secondary regions. This is intentional, since it will allow connecting the two virtual network together, which is necessary in order to configure replication between database servers hosted in the two regions. Such connection can be established by using virtual network peering.

1. On the **Replication settings** tab of the **Enable replication** page, select **Next**.
1. On the **Manage** tab of the **Enable replication** page, select **Next**.
1. On the **Review** tab of the **Enable replication** page, Review the settings. For this exercise, we will **not enable** replication.

    >**Note**: Initial replication usually takes considerable time to complete. Considering the limited time allocated to this lab, refer to the instructor regarding any extra steps to be performed as part of this task. In absence of any specific guidance, proceed directly to the next task.

    >**Note**: You could provision at this point both Azure Bastion and Azure Firewall, however you should instead automate their provisioning as part of your disaster recovery failover procedure. This will minimize charges associated with maintaining the disaster recovery environment. The same should apply to other components of that environment that mirror the configuration of the primary Virtual Instance for SAP, such as Azure Premium file shares and custom routing.

#### Task 3: Review monitoring options for SAP workloads managed by Azure Center for SAP solutions

>**Note**: As with backup, you will not be able to fully experience the monitoring capabilities of Azure Center for SAP solutions. This requires installation of SAP software or registering an existing Azure Monitor for SAP solutions instance. Instead, in this task, you will step through the interface available in the Virtual Instance for SAP solutions to identify and review these capabilities.

1. On the lab computer, in the Microsoft Edge window displaying the Azure portal, in the **Search** text box, search for and select **Virtual Instances for SAP solutions**. 
1. On the **Virtual Instances for SAP solutions** page, review the summarized status information for the **VI1** instance, including the overall health and status visual indicators.
1. On the **Virtual Instances for SAP solutions** page, select **VI1**.
1. On the **VI1** page, in the vertical navigation menu on the left side, select **Overview** and then, in the pane on the right side, select **Monitoring**.
1. Review the monitoring telemetry displayed in the monitoring pane.

    >**Note**: The monitoring pane includes vCPU utilization graphs and metrics for application servers, database servers, and SAP Central Services instances. It also includes database disk IOPS statistic database servers. 

1. On the **VI1** page, in the vertical navigation menu on the left side, in the **Monitoring** section, select **Quality insights**.
1. On the **VI1 \| Quality insights \| Workbook 1** page, review the **Advisor Recommendation** tab, which is intended to provide recommendations for optimizing Virtual Instance for SAP solutions (VIS), Central server instance, App service instances, and databases.

    >**Note**: These recommendations require completing installation of SAP software.

1. On the **VI1 \| Quality insights \| Workbook 1** page, select the **Virtual Machine** tab and review the content of **Azure Compute**, **Compute List**, **Compute Extensions**, **Compute + OS Disk**, and **Compute + Data Disks** tabs.

    >**Note**: Each of these tabs should include actual data collected from the Azure VMs that are part of the Virtual Instance for SAP solutions.

1. On the **VI1 \| Quality insights \| Workbook 1** page, select the **Configuration Checks** tab and review the content of the **Accelerated Networking**, **Public IP**, **Backup**, and **Load Balancer** tabs. This content provides a quick overview of the performance and security related settings of compute and network components of the Virtual Instance for SAP solutions. The **Load Balancer** tab includes **Load Balancer Monitor** information that displays key load balancer metrics.
1. On the **VI1** page, in the vertical navigation menu on the left side, in the **Monitoring** section, select **Azure Monitor for SAP solutions**.
1. On the **VI1 \| Azure Monitor for SAP solutions** page, note the message stating that AMS can't be set up since SAP software installation\registration for VIS isn't complete.

    >**Note**: Once you install SAP software, you will be able to integrate it with a new or existing Azure Monitor for SAP solutions resource. Azure Monitor for SAP solutions relies on the Azure Monitor capabilities of Log Analytics and workbooks to provide a comprehensive monitoring of SAP workloads hosted on Azure VMs, including support for custom visualizations, queries, and alerts.
