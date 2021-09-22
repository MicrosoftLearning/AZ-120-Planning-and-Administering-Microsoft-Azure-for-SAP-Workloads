# Demonstration: Create storage accounts

## Create a storage account in the portal

1.  In the Azure portal, select **All services**. In the list of resources, type Storage Accounts. As you begin typing, the list filters based on your input. Select **Storage Accounts**.
2.  On the Storage Accounts window that appears, choose **Add**.
3.  Select the **subscription** in which to create the storage account.
4.  Under the Resource group field, select **Create new**. Enter a name for your new resource group.
5.  Enter a **name** for your storage account. The name you choose must be unique across Azure. The name also must be between 3 and 24 characters in length, and can include numbers and lowercase letters only.
6.  Select a **location** for your storage account, or use the default location.
7.  Leave these fields set to their default values:

     * Deployment model: **Resource Manager**
     * Performance: **Standard**
     * Account kind: **StorageV2 (general-purpose v2)**
     * Replication: **Locally redundant storage (LRS)**
     * Access tier: **Hot**

8.  Select **Review + Create** to review your storage account settings and create the account.
9.  Select **Create**.

## Create a storage account using PowerShell

Use the following code to create a storage account using PowerShell. Swap out the storage types and names to suit your requirements.

```PowerShell
Get-AzLocation | select Location 
$location = "westus" 
$resourceGroup = "storage-demo-resource-group" 
New-AzResourceGroup -Name $resourceGroup -Location $location 
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "storagedemo" -Location $location -SkuName Standard_LRS -Kind StorageV2 
```

## Create a storage account using Azure CLI

Use the following code to create a storage account using Azure CLI. Change the storage types and names to suit your requirements.

```PowerShell
az group create --name storage-resource-group --location westus 
az account list-locations --query "[].{Region:name}" --out table 
az storage account create --name storagedemo --resource-group storage-resource-group --location westus --sku Standard_LRS --kind StorageV2 
```