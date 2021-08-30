# Demonstration: Explore Azure Blob Storage

>**Note**: This demonstration requires a storage account.

## Create a container

1. Navigate to a storage account in the Azure portal.
2. In the left menu for the storage account, scroll to the **Blob service** section, then select **Blobs**.
3. Select the **+ Container** button.
4. Type a **Name** for your new container. The container name must be lowercase, must start with a letter or number, and can include only letters, numbers, and the dash (-) character. 
5. Set the level of public access to the container. The default level is Private (no anonymous access).
6. Select **OK** to create the container.

## Upload a block blob

1. In the Azure portal, navigate to the container you created in the previous section.
2. Select the container to show a list of blobs it contains. Since this container is new, it won't yet contain any blobs.
3. Select the **Upload** button to upload a blob to the container.
4. Expand the **Advanced** section.
5. Notice the **Authentication type**, **Blob type**, **Block size**, and the ability to **Upload to a folder**.
6. Notice the default **Authentication type** type is SAS.
4. Browse your local file system to find a file to upload as a block blob, and select **Upload**.
5. Upload as many blobs as you like in this way. You'll observe that the new blobs are now listed within the container.

## Download a block blob

You can download a block blob to display in the browser or save to your local file system. 

1. Navigate to the list of blobs that you uploaded in the previous section.
2. Right-click the blob you want to download, and select **Download**.

>**Note**: As you have time, explore using Azure Storage Explorer for managing blob storage. 