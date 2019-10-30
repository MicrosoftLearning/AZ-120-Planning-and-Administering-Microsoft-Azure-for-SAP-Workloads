
# AZ 120: Lab prerequisites

## vCPU core requirements

-   To complete the last lab of this course, you will need a Microsoft Azure subscription with at least 34 vCPU available in the Azure region that supports availability zones where the Azure VMs deployed in this lab will reside.

    -   5 x Standard_D2s_v3 (2 vCPUs each) = 10

    -   4 x Standard_D4s_v3 (4 vCPUs each) = 16

    -   2 x Standard_E4s_v3 (4 vCPUs each) = 8

    > **Note**: To identify the Azure regions that support availability zones, refer to [https://docs.microsoft.com/en-us/azure/availability-zones/az-overview]<https://docs.microsoft.com/en-us/azure/availability-zones/az-overview>

While the vCPU requirements for the first three labs of this course are lower, we recommend that you request increase of quotas to satisfy requirements for all of the labs, since the process of increasing quotas might take some time (even though quota increase requests are typically completed during the same business day).

## Before the hands-on lab

Timeframe: 120 minutes

### Task 1: Validate sufficient number of vCPU cores

1.  In the Azure portal at <http://portal.azure.com>, 

1.  In the Azure Portal, start a PowerShell session in Cloud Shell. 

    > **Note**: If this is the first time you are launching Cloud Shell in the current Azure subscription, you will be asked to create an Azure file share to persist Cloud Shell files. If so, accept the defaults, which will result in creation of a storage account in an automatically generated resource group.

1.  In the Azure portal, in the **Cloud Shell**, at the PowerShell prompt, run the following: where `<Azure_region>` designates the target Azure region that you intend to use for this lab (e.g. `westus2`):

    ```
    Get-AzVMUsage -Location westus2 | Where-Object {$_.Name.Value -eq 'StandardDSv3Family'}

    Get-AzVMUsage -Location westus2 | Where-Object {$_.Name.Value -eq 'StandardESv3Family'}

    ``` 

    > **Note**: To identify the names of Azure regions, in the **Cloud Shell**, at the Bash prompt, run `(Get-AzLocation).Location`
   
1.  Review the current value and the limit entries in the output of the commands executed in the previous step and ensure that you have sufficient number of vCPUs in the target Azure region.

1.  If the number of vCPUs is not sufficient, in the Azure portal, navigate back to the subscription blade, and click **Usage + quotas**. 

1.  On the subscription's **Usage + quotas** blade, click **Request Increase**.

1.  On the **Basic** blade, specify the following and click **Next**:

    -   Issue type: **Service and subscription limits (quotas)**

    -   Subscription: the name of the Azure subscription you will be using in this lab

    -   Quota type: **Compute/VM (cores/vCPUs) subscription limit increases**

1.  On the **Details** blade, click the **Provide details** blade. 

1.  On the **Quota details** blade, specify the following and click **Save and continue**:

    -   Deployment model: **Resource Manager**

    -   Location: the target Azure region you intend to use in this lab

    -   SKU family: **DSv3 Series** and **ESv3 Series**

1.  On the **Details** blade, specify the new limit for each SKU series and click **Next: Review + create**:

    -   Severity: **C - Minimal impact**

    -   Preferred contact method: choose your preferred option and provide contact details

1.  On the **Review + create** blade, click **Create**

   > **Note**: Quota increase requests are typically completed during the same business day.