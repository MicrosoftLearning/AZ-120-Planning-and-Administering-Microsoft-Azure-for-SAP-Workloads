# Demonstration: Create an Azure Policy

## Assign a policy

1. Launch the Azure Policy service in the Azure portal by clicking **All services**, then searching for and selecting **Policy**. This service is under **Management and Governance**.
2. Select **Assignments** on the left side of the Azure Policy page. An assignment is a policy that has been assigned to take place within a specific scope.
3. Select **Assign Policy** from the top of the Policy - Assignments page.
4. Notice the **Scope** which determines what resources or grouping of resources the policy assignment gets enforced on.
5. Select the **Policy definition ellipsis** to open the list of available definitions. Take some time to review the built-in policy definitions.
6. Select **Require SQL Server version 12.0**. This policy ensures all SQL servers use version 12.0. If this policy definition is not available select something else.
7. Leave Create a **Managed Identity** unchecked. 
8. Click **Assign** to create your policy.

## Create and assign an initiative definition

1. Select **Definitions** under Authoring in the left side of the Azure Policy page.
2. Select **+ Initiative Definition** at the top of the page to open the Initiative definition page.
3. Provide a **Name** and **Description**.
4. **Create new** Category.
5. From the right panel **Add** the **Require SQL Server version 12.0** policy.
6. Add one additional policy of your choosing.
7. **Save** your changes and then **Assign** your initiative definition to your subscription.

## Check for compliance

1. Return to the Azure Policy service page.
2. Select **Compliance**.
3. Review the status of your policy and your definition. 