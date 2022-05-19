---
lab:
    title: '01 - Lab prerequisites'
    module: 'Module 01 - Lab prerequisites'
---


# AZ 120: Lab prerequisites

## vCPU core requirements

-   To complete the last lab of this course, you will need a Microsoft Azure subscription with at least 28 vCPU available in the Azure region that supports availability zones where the Azure VMs deployed in this lab will reside.

    -   4 x Standard_DS1_v2 (1 vCPUs each) = 4

    -   6 x Standard_D4s_v3 (4 vCPUs each) = 24

    > **Note**: Consider using **East US** or **East US2** regions for deployment of your resources.

    > **Note**: To identify the Azure regions that support availability zones, refer to [https://docs.microsoft.com/en-us/azure/availability-zones/az-overview]<https://docs.microsoft.com/en-us/azure/availability-zones/az-overview>

While the vCPU requirements for the first three labs of this course are lower, we recommend that you request increase of quotas to satisfy requirements for all of the labs, since the process of increasing quotas might take some time (even though quota increase requests are typically completed during the same business day).

## Before the hands-on lab

Timeframe: 30 minutes

### Task 1: Validate sufficient number of vCPU cores

1.  In the Azure portal at <http://portal.azure.com>, 

1.  In the Azure Portal, start a PowerShell session in Cloud Shell. 

    > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1.  In the Azure portal, in the **Cloud Shell** pane, at the PowerShell prompt, run the following: where `<Azure_region>` designates the target Azure region that you intend to use for this lab (e.g. `eastus`):

    ```powershell
    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv3Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv2Family'}
    ``` 

    > **Note**: To identify the names of Azure regions, in the **Cloud Shell**, at the Bash prompt, run `(Get-AzLocation).Location`
   
1.  Review the current value and the limit entries in the output of the commands executed in the previous step and ensure that you have sufficient number of vCPUs in the target Azure region.

1.  If the number of vCPUs is not sufficient, in the Azure portal, navigate back to the subscription blade, and click **Usage + quotas**. 

1.  On the subscription's **Usage + quotas** blade, click **Request Increase**.

1.  On the **Problem description** blade, specify the following:

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
