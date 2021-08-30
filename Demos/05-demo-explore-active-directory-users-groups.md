# Demonstration: Explore Actve Directory users and groups

>**Note**: Depending on your subscription not all areas of the Active Directory blade will be available.

## Determine domain information

1. Access the Azure portal, and navigate to the **Azure Active Directory** blade.
2. Make a note of your available domain name. For example, usergmail.onmicrosoft.com.

## Explore user accounts

1. Select the **Users** blade.
2. Select **New user**. 
3. Notice the selection to create a **New guest user**.
4. Create a **New user**. Replace your domain. 

    + **Name**: *Chris Green*
    + **Address**: *chris@your domain*
    + **Profile information**: Enter first and last name. 
    + **Directory Role** - *User*.

5. Review the **User Settings** blade.
6. Review the **Audit Logs** blade.

## Explore group accounts

1. Select the **Groups** blade.
2. Add a **New group**. 

    + **Group type**: *Security*
    + **Group name**: *Managers*
    + **Membership type**: *Assigned*
    + **Members**: Add *Chris Green* to the group. 

3. Under **Settings** review the **General** blade.
4. Under **Activity** review the **Audit Logs** blade.

## Explore PowerShell for group management

1. Create a new group called **Developers**.

    ```
    New-AzADGroup -DisplayName Developers -MailNickname Developers
    ```

2. Retrieve the **Developers** group **ObjectId **.

    ```
    Get-AzADGroup
    ```

3. Retrieve the user **ObjectId** for the member to add.

    ```
    Get-AzADUser
    ```

4. Add the user to the group. Replace **groupObjectId** and **userObjectId**.

    ```
    Add-AzADGroupMember -MemberUserPrincipalName ""myemail@domain.com"" -TargetGroupDisplayName ""MyGroupDisplayName""
    ```

5. Verify the members of the group. Replace **groupObjectId**.

    ```
    Get-AzADGroupMember -GroupDisplayName "MyGroupDisplayName"
    ```