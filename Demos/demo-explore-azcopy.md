# Demonstration: Explore AzCopy

## Download AzCopy

First, download the AzCopy V10 executable file to any directory on your computer. AzCopy V10 is just an executable file, so there's nothing to install.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

These files are compressed as a zip file (Windows and Mac) or a tar file (Linux). To download and decompress the tar file on Linux, see the documentation for your Linux distribution.

## Run AzCopy

For convenience, consider adding the directory location of the AzCopy executable to your system path for ease of use. That way you can type `azcopy` from any directory on your system.

If you choose not to add the AzCopy directory to your path, you'll have to change directories to the location of your AzCopy executable and type `azcopy` or `.\azcopy` in Windows PowerShell command prompts.

As an owner of your Azure Storage account, you aren't automatically assigned permissions to access data. Before you can do anything meaningful with AzCopy, you need to decide how you'll provide authorization credentials to the storage service. 

## Authorize AzCopy

You can provide authorization credentials by using Azure Active Directory (AD), or by using a Shared Access Signature (SAS) token.

Use this table as a guide:

| Storage type | Currently supported method of authorization |
|--|--|
|**Blob storage** | Azure AD & SAS |
|**Blob storage (hierarchical   namespace)** | Azure AD & SAS |
|**File storage** | SAS only |

### Option 1: Use Azure Active Directory

This option is available for blob Storage only. By using Azure Active Directory, you can provide credentials once instead of having to append a SAS token to each command.  

> **Note:** In the current release, if you plan to copy blobs between storage accounts, you'll have to append a SAS token to each source URL. You can omit the SAS token only from the destination URL. For examples, see [Copy blobs between storage accounts](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-data).

To authorize access by using Azure AD, see [Authorize access to blobs with AzCopy and Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-authorize-azure-active-directory).

### Option 2: Use a SAS token

You can append a SAS token to each source or destination URL that use in your AzCopy commands.

This example command recursively copies data from a local directory to a blob container. A fictitious SAS token is appended to the end of the container URL.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

To learn more about SAS tokens and how to obtain one, see [Grant limited access to Azure Storage resources using shared access signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

> **Note:** The [Secure transfer required](storage-require-secure-transfer.md) setting of a storage account determines whether the connection to a storage account is secured with Transport Layer Security (TLS). This setting is enabled by default.   

## Explore the help

1. To see a list of commands, type `azcopy -h` and then press the ENTER key.
2. To learn about a specific command, include the name of the command (for example: `azcopy list -h`) and then press the ENTER key.

![Inline help](Images/azcopy-inline-help.png)

## Download a blob from Blob storage to the file system

>**Notes:**
>- This example requires an Azure storage account with a blob container and blob file. You will also need to capture parameters in a text editor like Notepad.
>- This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

1. Access the Azure portal.
2. Access your storage account with the blob you want to download.
3. Drill down to the blob of interest, and view the file **Properties**.
4. Copy the **URL** information. This will be the source path *https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>*.
5. Locate a local destination directory. This will be the *<local-file-path>* value. A filename is also required.
6. Construct the command using your values:

    ```
    azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'
    ```
    
7. If you have errors, read them carefully and make corrections.
8. Verify the blob was downloaded to your local directory. 

## Upload files to Azure blob storage

>**Notes:**
>- This example continues from the previous example and requires a local directory with files.
>- This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

1. The *<local-file-path>* source for the command will be a local directory with files. 
2. The *https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>* destination for the command will the blob URL used in the previous example. Be sure to remove the filename, just include the storage account and container. 
3. Construct the command using your values.

    ```
    azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'
    ```

5. If you have errors, read them carefully and make corrections.
6. Verify your local files were copied to the Azure container. 
7. Notice there are switches to recurse subdirectories and pattern match.
