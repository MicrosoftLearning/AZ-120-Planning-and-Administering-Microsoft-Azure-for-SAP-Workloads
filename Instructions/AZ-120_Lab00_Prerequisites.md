---
lab:
    title: '00 - Lab prerequisites'
    module: 'Module 00 - Lab prerequisites'
---

# AZ 120: Lab prerequisites

## vCPU core requirements

> **Important**: The vCPU core requirements depend on the labs you intend to implement.

- To complete lab 04b - Implement SAP architecture on Azure VMs running Windows, you will need a Microsoft Azure subscription with at least 28 vCPU available in the Azure region that supports availability zones where the Azure VMs deployed in this lab will reside.

    - 4 x Standard_DS1_v2 (1 vCPUs each) = 4
    - 6 x Standard_D4s_v3 (4 vCPUs each) = 24

    > **Note**: Consider using **East US** or **East US2** regions for deployment of your resources.

    > **Note**: To identify the Azure regions that support availability zones, refer to <https://docs.microsoft.com/en-us/azure/availability-zones/az-overview>

- To complete lab 05 - Automate deployment by using Azure Center for SAP solutions, you will need a Microsoft Azure subscription with the following vCPU availability in the Azure region where the Azure VMs deployed in this lab will reside.

    - 4 x Standard_E4ds_v4 (4 vCPUs each) or 4 X Standard_D4ds_v4 (4 vCPUs each) = 8
    - 2 x Standard_M64ms (64 vCPUs each) = 128

>**Note**: To minimize the vCPU and memory requirements, you can use Standard_M32ts (32 vCPUs and 192 GiB of memory each) instead of Standard_M64m.

>**Note**: While the vCPU requirements for the first three labs of this course are lower, we recommend that you request increase of quotas to satisfy requirements for all of the labs, since the process of increasing quotas might take some time (even though quota increase requests are typically completed during the same business day).

## Before the hands-on lab

Timeframe: 30 minutes

### Task 1: Validate sufficient number of vCPU cores for the lab Implement SAP architecture on Azure VMs running Windows

1. From the lab computer, start a Web browser, and navigate to the Azure portal at `https://portal.azure.com`.
1. In the Azure Portal, start a PowerShell session in Cloud Shell. 

    > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1. In the Azure portal, in the **Cloud Shell** pane, at the PowerShell prompt, run the following: where `<Azure_region>` designates the target Azure region that you intend to use for this lab (e.g., `eastus`):

    ```powershell
    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv3Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv2Family'}
    ``` 

    > **Note**: To identify the names of Azure regions, in the **Cloud Shell**, at the Bash prompt, run `(Get-AzLocation).Location`
   
1. Review the current value and the limit entries in the output of the commands executed in the previous step and ensure that you have sufficient number of vCPUs in the target Azure region.
1. If the number of vCPUs is not sufficient, in the Azure portal, navigate back to the subscription blade, and click **Usage + quotas**. 
1. On the subscription's **Usage + quotas** blade, click **Request Increase**.
1. On the **Problem description** blade, specify the following:

    -   Issue type: **Service and subscription limits (quotas)**
    -   Subscription: the name of the Azure subscription you will be using in this lab
    -   Quota type: **Compute/VM (cores/vCPUs) subscription limit increases**

1. Click **Manage Quota**.
1. Use the location dropdown menu to filter the results to the Azure region that you plan to use. It is recommended to use **East US** or **East US2**.
1. Locate the **Standard DSv3 Family vCPUs** quota type and select the edit pencil.
1. In the New Limit field, specify **40** and click **Save and Continue**.
1. Locate the **Total Regional vCPUs** quota type and select the edit pencil.
1. In the New Limit field, specify **40** and click **Save and Continue**.

   > **Note**: The quota increase request should be approved automatically. If the request is denied, proceed to open a support ticket to request the quota increase.

### Task 2: Validate sufficient number of vCPU cores for the lab Automate deployment by using Azure Center for SAP solutions

> **Note**: To complete this lab (as described), you will need a Microsoft Azure subscription with the vCPU quotas that accommodate deployment of the following VMs:

- 2 x Standard_E4ds_v4 (4 vCPUs and 32 GiB of memory each) or 2 X Standard_D4ds_v4 (4 vCPUs and 16 GiB of memory each) VMs for the ASCS tier
- 2 x Standard_E4ds_v4 (4 vCPUs and 32 GiB of memory each) or 2 X Standard_D4ds_v4 (4 vCPUs and 16 GiB of memory each) VMs for the application tier 
- 2 x Standard_M64ms (64 vCPUs and 1750 GiB of memory each) VMs for the database tier

1. From the lab computer, start a Web browser, and navigate to the Azure portal at `https://portal.azure.com`.
1. In the Azure Portal, select the **Cloud Shell** icon and start a PowerShell session in Cloud Shell. 
1. In the Azure portal, in the **Cloud Shell** pane, at the PowerShell prompt, run the following: where `<Azure_region>` designates the Azure region to which you intend to deploy resources in this lab (e.g., `eastus`):

    ```powershell
    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'standardEDSv4Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'standardDSv4Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'standardMSFamily'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'cores'}
    ```

    > **Note**: To identify the names of Azure regions, in the **Cloud Shell**, at the Bash prompt, run `(Get-AzLocation).Location`

1. Review the output to identify the current vCPU usage and the vCPU limit. Ensure that the difference between them is sufficient to accommodate vCPUs of Azure VMs that you will be deployed in this lab. Take into account both VM family-specific and total regional vCPU numbers. 
1. If the number of vCPUs is not sufficient, close the Cloud Shell pane, in the Azure portal, in the **Search** text box, search for and select **Quotas**.
1. On the **Quotas** page, select **Compute**.
1. On the **Quotas \| Compute** page, use the **Region** filter to select the Azure region to which you intend to deploy resources in this lab.
1. In the **Quota name** column, locate and select the VM SKU name that requires a quota increase. 
1. In the same row, check the entry in the **Adjustable** column. The next step depends on whether the column contains **Yes** or **No** entry.

   - If the entry is set to **Yes**, select the **Request adjustment** icon, on the **New Quota Request**, in the **New limit** text box, enter the new quota limit, and then select **Submit**.
   - If the entry is set to **No**, select the **Request access or get recommendations** icon and, the **Quota Recommendations** pane, select **Contact Support** option, and then select **Next**. 

1. On the **Problem description** tab of the **New support request** page, specify the following settings and then select **Next**:

    |Setting|Value|
    |---|---|
    |What is your issue related to?|**Azure services**|
    |Issue type|**Service and subscription limits (quotas)**|
    |Subscription|The name of the Azure subscription you are using in this lab|
    |Quota type|**Compute/VM (cores/vCPUs) subscription limit increases**|

1. On the **Additional details** tab, select **Enter details**.
1. On the **Quota details** tab, in the **Deployment model** drop-down list, select **Resource manager**, in the **Locations** drop-down list, select the target Azure region, in the **Quotas** drop-down list, select the Azure VM series you need to increase the quota limits for, in the **New limit** text box, enter the new quota limit, and then select **Save and Continue**.
1. Back on the **Additional details** tab, in the **Advanced diagnostic information** tab, select **Yes (Recommended)**.
1. In the **Support method** section, select either **Email** or **Phone** as your preferred contact method and then select **Next**.
1. On the **Review + create** tab, select **Create**.

    > **Note**: Wait until the request to increase quota limits is successfully completed before you start the lab Automate deployment by using Azure Center for SAP solutions.
