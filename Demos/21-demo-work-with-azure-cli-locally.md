# Demonstration: Work with Azure CLI locally

In this demonstration, you will install and use the CLI to create resources.

## Install the CLI on Windows

You install Azure CLI on the Windows operating system using the MSI installer:

1. Download the [MSI installer](https://aka.ms/installazurecliwindows), and in the browser security dialog box, click **Run**.
2. In the installer, accept the license terms, and then click **Install**.
3. In the **User Account Control** dialog, select **Yes**.

## Verify Azure CLI installation

You run Azure CLI by opening a Bash shell for Linux or macOS, or from the command prompt or PowerShell for Windows.

Start Azure CLI and verify your installation by running the version check:

```azurecli
az --version
 ```

>**Note**: Running Azure CLI from PowerShell has some advantages over running Azure CLI from the Windows command prompt. PowerShell provides more tab completion features than the command prompt.

## Login to Azure

Because you're working with a local Azure CLI installation, you'll need to authenticate before you can execute Azure commands. You do this by using the Azure CLI **login** command:

```azurecli
az login
```

Azure CLI will typically launch your default browser to open the Azure sign-in page. If this doesn't work, follow the command-line instructions and enter an authorization code at [https://aka.ms/devicelogin](https://aka.ms/devicelogin).

After a successful sign in, you'll be connected to your Azure subscription.

## Create a resource group

You'll often need to create a new resource group before you create a new Azure service, so we'll use resource groups as an example to show how to create Azure resources from the CLI.

Azure CLI **group create** command creates a resource group. You must specify a name and location. The *name* must be unique within your subscription. The *location* determines where the metadata for your resource group will be stored. You use strings like "West US", "North Europe", or "West India" to specify the location; alternatively, you can use single word equivalents, such as westus, northeurope, or westindia. The core syntax is:

```azurecli
az group create --name <name> --location <location>
```

## Verify the resource group

For many Azure resources,  Azure CLI provides a **list** subcommand to view resource details. For example, the Azure CLI **group list** command lists your Azure resource groups. This is useful to verify whether resource group creation was successful:

```azurecli
az group list
```

To get a more concise view, you can format the output as a simple table:

```azurecli
az group list --output table
```

If you have several items in the group list, you can filter the return values by adding a **query** option. Try this command:

```azurecli
az group list --query "[?name == '<rg name>']"
```

>**Note:** You format the query using **JMESPath**, which is a standard query language for JSON requests. Learn more about this powerful filter language at [http://jmespath.org/](http://jmespath.org/).