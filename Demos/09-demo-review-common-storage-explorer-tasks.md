# Demonstration: Review common Storage Explorer tasks

>**Notes**:
>- If you have an older version of the Storage Explorer, be sure to upgrade. These steps use version 1.6.2.
>- For the demonstration we will only do a basic storage account connection.

## Download and install Storage Explorer

1. Download and install [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 
2. After the installation, launch the tool.
3. Review the Release Notes and menu options.

## Connect to an Azure subscription

1. In Storage Explorer, select **Manage Accounts**, second icon top left. This will take you to the Account Management Panel.
2. The left pane now displays all the Azure accounts you've signed in to. To connect to another account, select **Add an account**.
3. If you want to sign into a national cloud or an Azure Stack, click on the Azure environment dropdown to select which Azure cloud you want to use. 
4. Once you have chosen your environment, click the **Sign in...** button. 
5. After you successfully sign in with an Azure account, the account and the Azure subscriptions associated with that account are added to the left pane. 
6. Select the Azure subscriptions that you want to work with, and then select **Apply**.
7. The left pane displays the storage accounts associated with the selected Azure subscriptions.

>**Note**: This next section requires an Azure storage account. 

## Attach an Azure storage account

1. Access the Azure portal, and your storage account.
2. Explore the choice for **Storage Explorer**.
3. Select **Access keys** and read the information about using the keys. 
4. To connect in Storage Explorer, you will need the **Storage account name** and **Key1** information.
5. In Storage Explorer, **Add an account**.
6. Paste your account name in the Account name text box, and paste your account key (the key1 value from the Azure portal) into the Account key text box, and then select **Next**.
7. Verify your storage account is available in the navigation pane. You may need to refresh the page. 
8. Right-click your storage account and notice the choices including **Open in portal**, **Copy primary key**, and **Add to Quick Access**.