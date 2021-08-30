# Demonstration: Create and manage file shares and snapshots

>**Note**: These steps require a storage account. 

## Create a file share and upload a file

1. Access your storage account, and click **Files**.
2. Click **+ File share** and give your new file share a **Name** and a **Quota**.
3. After your file share is created **Upload** a file. 
4. Notice the ability to **Add a directory**, **Delete share**, and edit the **Quota**.

## Manage snapshots

1. Access your file share.
1. Select **Create Snapshot**.
1. Select **View Snapshots** and verify your snapshot was created.
1. Click the snapshot and verify it includes your uploaded file.
1. Click the file that is part of the snapshot and review the **File properties**. 
1. Notice the choices to **Download** and **Restore** the snapshot file. 
1. Access the file share and delete the file you previously uploaded.
1. **Restore** the file from the snapshot. 
 
## Create a file share (PowerShell)

1. Create a context for your storage account and key The context encapsulates the storage account name and account key.

    ```PowerShell
    $storageContext = New-AzStorageContext storage-account-name storage-account-key
    ```

2. Create the file share. The name of your file share must be all lowercase.

    ```PowerShell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

## Mount a file share (PowerShell)

1. Run the following commands from a regular (i.e. not an elevated) PowerShell session to mount the Azure file share. Remember to replace **your-resource-group-name**, **your-storage-account-name**, **your-file-share-name**, and **desired-drive-letter** with the proper information.

    ```PowerShell
    $resourceGroupName = "your-resource-group-name"
    $storageAccountName = "your-storage-account-name"
    $fileShareName = "your-file-share-name"

    # These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    $storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
    $fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
        $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
    }

    if ($fileShare -eq $null) {
        throw [System.Exception]::new("Azure file share not found")
    }

    # The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
    # storage-account.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
    # used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
    # hosts for Azure file shares (and other storage resources).
    $password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
    New-PSDrive -Name desired-drive-letter -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
    ```

2. When finished, you can dismount the file share by running the following command:

    ```PowerShell
    Remove-PSDrive -Name desired-drive-letter
    ```