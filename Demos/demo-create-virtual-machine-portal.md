# Demonstration: Create a virtual machine in the portal

In this demonstration, we will create and access a Windows virtual machine in the portal.

## Create the virtual machine

1. Choose **Create a resource** in the upper left-hand corner of the Azure portal.
2. In the search box above the list of Azure Marketplace resources, search for **Windows Server 2016 Datacenter**. After locating the image, click **Create**.
3. In the **Basics** tab, under **Project details**, make sure the correct subscription is selected and then choose to **Create new** resource group. Type *myResourceGroup* for the name.

    ![Create a new resource group for your VM](Images/AZ103_Demo_Creating_VMs1.png)

4. Under **Instance details**, type *myVM* for the **Virtual machine name** and choose *East US* for your **Location**. Leave the other defaults.

    ![Instance details section](Images/AZ103_Demo_Creating_VMs2.png)

5. Under **Administrator account**, provide a username, such as *azureuser* and a password. The password must be at least 12 characters long and meet the defined complexity requirements.

    ![Enter your username and password](Images/AZ103_Demo_Creating_VMs3.png)

6. Under **Inbound port rules**, choose **Allow selected ports** and then select **RDP (3389)** and **HTTP** from the drop-down.

    ![Open ports for RDP and HTTP](Images/AZ103_Demo_Creating_VMs4.png)

7. Move to the **Management** tab, and under **Monitoring** turn **Off** Boot Diagnostics. This will eliminate validation errors. 
8. Leave the remaining defaults and then select the **Review + create** button at the bottom of the page. Wait for the validation, then click **Create**. 

    ![Review and create](Images/AZ103_Demo_Creating_VMs5.png)

## Connect to the virtual machine

Create a remote desktop connection to the virtual machine. These directions tell you how to connect to your VM from a Windows computer. On a Mac, you need to install an RDP client from the Mac App Store.

1. Select the **Connect** button on the virtual machine properties page.
2. In the **Connect to virtual machine** page, keep the default options to connect by DNS name over port 3389 and click **Download RDP file**.
3. Open the downloaded RDP file and select **Connect** when prompted.
4. In the **Windows Security** window, select **More choices** and then **Use a different account**. Type the username as localhost\username, enter password you created for the virtual machine, and then select **OK**.
5. You may receive a certificate warning during the sign-in process. Select **Yes** or **Continue** to create the connection.

## Install web server

To observe your VM in action, install the IIS web server. Open a PowerShell prompt on the VM and run the following command:

```PowerShell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

When done, close the RDP connection to the VM.

## View the IIS welcome page

In the portal, select the VM and in the overview of the VM, use the **Click to copy** button to the right of the public IP address to copy it and paste it into a browser tab. The default IIS welcome page will open.

![IIS default site](Images/AZ103_Demo_Creating_VMs6.png)

## Clean up resources

>**Note:** When no longer needed, you can delete the resource group, virtual machine, and all related resources. To do so, select the resource group for the virtual machine, select **Delete**, then confirm the name of the resource group to delete.