# Demonstration: Explore AzCopy

## Install the AzCopy tool

1. Download the Windows 8.1 version - [Get started with AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
2. Install and launch the tool.

## Explore the help

1. Launch the Microsoft Azure Storage AzCopy tool.
2. View the help.

    ```
    azcopy /?
    ```

3. Scroll to the top of the Help information and read about the **Common options**, like: source, destination, source key, and destination key.
4. Scroll down the **Samples** section. We will be trying several of these examples. Are any of these examples particularly interesting to you?

## Download a blob from Blob storage to the file system

>**Note**: This example requires an Azure storage account with blob container and blob file. You will also need to capture parameters in a text editor like Notepad. 

1. Access the Azure portal.
2. Access your storage account with the blob you want to download.
3. Select **Access keys** and copy the **Key Key1** value. This will be the *sourcekey:* value. 
4. Drill down to the blob of interest, and view the file **Properties**.
5. Copy the **URL** information. This will be the *source:* value.
6. Locate a local destination directory. This will be the *dest:* value. A filename is also required.
7. Construct the command using your values. 

    ```
    azcopy /source:sourceURL /dest:destinationdirectoryandfilename /sourcekey:"key"
    ``` 

8. If you have errors, read them carefully and make corrections.
9. Verify the blob was downloaded to your local directory. 

## Upload files to Azure blob storage

>**Note:** The example continues from the previous example and requires a local directory with files.

1. The *source:* for the command will be a local directory with files. 
2. The *dest:* will the blob URL used in the previous example. Be sure to remove the filename, just include the storage account and container. 
3. The *destkey:* will the key used in the previous example. 
4. Construct the command using your values.

    ```
    azcopy /source:source /dest:destinationcontainer /destkey:key
    ```

5. If you have errors, read them carefully and make corrections.
6. Verify your local files were copied to the Azure container. 
7. Notice there are switches to recurse subdirectories and pattern match.