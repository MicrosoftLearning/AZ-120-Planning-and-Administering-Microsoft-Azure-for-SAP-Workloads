# Demonstration: Explore Network Security Groups (NSGs) and service endpoints

## Access the NSGs blade

1. Access the Azure Portal.
2. Search for and access the **Network Security Groups** blade.
3. If you have virtual machines, you may already have NSGs. Notice the ability to filter the list.

## Add a new NSG

1. **+ Add** a network security group.

    + **Name**: *select a unique name*
    + **Subscription**: *select your subscription*
    + **Resource Group**: *create new or select an existing resource group*
    + **Location**: *your choice*

2. Click **Create**.

3. Wait for the new NSG to deploy.

## Explore inbound and outbound rules

1. Select your new NSG.
2. Notice the NSG can be associated with subnets and network interfaces (summary information above the rules).
3. Notice the three inbound and three oubound NSG rules.
4. Under **Settings** select **Inbound security rules**.
5. Notice you can use **Default rules** to hide the default rules.
6. **+ Add** a new inbound security rule.
7. Click **Basic** to change to the Advanced mode.
8. Use the **Service** drop-down to review the predefined services that are available.
9. When you make a service selection (like HTTPS) the port range (like 443) is automatically populated. This makes it easy to configure the rule.
10. Use the Information icon next to the Priority label to learn how to configure the priority.
11. Exit the rule without making any changes. 
12. As you have time, review adding an outbound security rule.