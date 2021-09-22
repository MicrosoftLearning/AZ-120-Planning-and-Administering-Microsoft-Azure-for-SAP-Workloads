# Demonstration: Work with PowerShell locally

In this demonstration, we will install Azure Az PowerShell module. The Az module is available from a global repository called the *PowerShell Gallery*. You can install the module onto your local machine through the **Install-Module** command. You need an elevated PowerShell shell prompt to install modules from the PowerShell Gallery. 

## Install the Az module

1. Open the **Start** menu, and type **Windows PowerShell**.
2. Right-click the **Windows PowerShell** icon, and select **Run as administrator**.
3. In the **User Account Control** dialog, select **Yes**.
4. Type the following command, and then press Enter. This command installs the module for all users by default. (It's controlled by the scope parameter.) AllowClobber overwrites the previous PowerShell module. 

    ```
    Install-Module -Name Az -AllowClobber
    ```

## Install NuGet (if needed)

1. Depending on the NuGet version you have installed you might get a prompt to download and install the latest version.
2. If prompted, install and import the NuGet provider.

## Trust the repository

1. By default, the PowerShell Gallery isn't configured as a trusted repository for PowerShellGet. The first time you use the PowerShell Gallery, you will be prompted.

    ```
    You are installing the modules from an untrusted repository. If you trust this repository, change its
    InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
    'PSGallery'?
    ```

2. As prompted, install the modules. 

## Connect to Azure and view your subscription information

1. Login to Azure

    ```
    Login-AzAccount
    ```

2. When prompted provide your credentials.
3. Verify your subscription information.

    ```
    Get-AzSubscription
    ```

## Create resources

1. Create a new resource group. Provide a different location if you like. The *name* must be unique within your subscription. The *location* determines where the metadata for your resource group will be stored. You use strings like "West US", "North Europe", or "West India" to specify the location; alternatively, you can use single word equivalents, such as westus, northeurope, or westindia. The core syntax is:

    ```
    New-AzResourceGroup -name <name> -location <location>
    ```

2. Verify your resource group. 
  
    ```
    Get-AzResourceGroup
    ```

3. Remove your resource group. When prompted, confirm. 

    ```
    Remove-AzResourceGroup -Name Test
    ```