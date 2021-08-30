# Demonstration: Explore role assignments and role-based access control

## Locate Access Control blade

1. Access the Azure portal, and select a resource group. Make a note of what resource group you use. 
2. Select the **Access Control (IAM)** blade. 
3. This blade will be available for many different resources so you can control access.

## Review role permissions

1. Select the **Roles** tab (top).
2. Review the large number of built-in roles that are available.
3. Double-click a role, and then select **Permissions** (top).
4. Continue drilling into the role until you can view the **Read, Write, and Delete** actions for that role.
5. Return to the **Access Control (IAM)** blade.

## Add a role assignment

1. Select **Add role assignment**. 

    + **Role**: *Owner*
    + **Select**: *Managers*
    + **Save** your changes. 

2. Select **Check access**.
3. **Find** Chris Green.
4. Notice he is part of the Managers group and is an Owner. 
5. Notice that you can **Deny assignments**. 

## Explore PowerShell commands

1. Open the Azure Cloud Shell.
2. Select the PowerShell drop-down.
3. List role definitions.

    ```PowerShell
    Get-AzRoleDefinition | FT Name, Description
    ```

4. List the actions of a role.

    ```PowerShell
    Get-AzRoleDefinition owner | FL Actions, NotActions
    ```

5. List role assignments.

    ```PowerShell
    Get-AzRoleAssignment -ResourceGroupName <resource group name>
    ```