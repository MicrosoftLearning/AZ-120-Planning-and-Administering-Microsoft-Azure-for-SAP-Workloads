# Demonstration: Back up Azure file shares

In this demonstration, we will explore backing up a file share in the Azure portal.

>**Note:** This desmonstration requires an Azure file share and a storage account that can be used by the vault. 

## Create a Recovery Services vault

1. In the Azure portal, type Recovery Services and click **Recovery Services vaults**.
3. Click **Add**.
4. Provide a **Name**, **Subscription**, **Resource group**, and **Location**. 
5. Your new vault should be in the same location as the file share. 
5. Click **Create**. It can take several minutes for the Recovery Services vault to be created. Monitor the status notifications in the upper right-hand area of the portal. Once your vault is created, it appears in the list of Recovery Services vaults. 
6. If after several minutes the vault is not added, click **Refresh**.

## Configure the vault

1. Open your recovery services vault. 
2. Click **Backup** and create a new backup instance. 
3. From the **Where is your workload running?** drop-down menu, select **Azure**.
4. From the **What do you want to backup?** menu, select **Azure FileShare**.
5. Click **Backup**.
6. From the list of Storage accounts, **select a storage account**, and click **OK**. Azure searches the storage account for files shares that can be backed up. If you recently added your file shares, allow a little time for the file shares to appear.
7. From the File Shares list, **select one or more of the file shares** you want to backup, and click **OK.**
8. On the Backup Policy page, choose **Create New backup policy** and provide Name, Schedule, and Retention information. Click **OK**.
9. When you are finished configuring the backup click **Enable backup**. 

## Explore Recovery Services vault information

1. Explore the **Backup items** blade. There is information on backed up items and replicated items.
2. Explore the **Backup policies** blade. You can add or delete backup policies. 
3. Explore the **Backup jobs** blade. Here you can review the status of your backup jobs.