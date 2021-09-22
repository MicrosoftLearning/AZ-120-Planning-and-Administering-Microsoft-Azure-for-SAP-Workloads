# Demonstration: Connect to Linux virtual machines

In this demonstration, we will create a Linux machine and access the machine with SSL.

>**Note:** Ensure port 22 is open for the connection to work. 

## Create the SSH Keys

1. Download the PuTTY tool. This will include [PuTTYgen](https://putty.org/). 
2. Once installed, locate and open the **PuTTYgen** program.
3. In the **Parameters** option group choose **RSA**.
4. Click the **Generate** button.
5. Move your mouse around the blank area in the window to generate some randomness.
6. Copy the text of the **Public key for pasting into authorized keys file**.
7. Optionally you can specify a **Key passphrase** and then **Confirm passphrase.** You will be prompted for the passphrase when you authenticate to the VM with your private SSH key. Without a passphrase, if someone obtains your private key, they can sign in to any VM or service that uses that key. We recommend you create a passphrase. However, if you forget the passphrase, there is no way to recover it.
8. Click **Save private key**.
9. Choose a location and filename and click **Save**. You will need this file to access the VM. 

## Create the Linux machine and assign the public SSH key

1. In the portal create a Linux machine of your choice.
2. Choose **SSH Public Key** for the **Authentication type** (instead of **Password** ).
3. Provide a **Username**.
4. Paste the public SSH key from PuTTY into the **SSH public key** text area. Ensure the key validates with a checkmark. 
5. Create the VM. Wait for it to deploy.
6. Access the running VM. 
7. From the **Overview** blade, click **Connect**.
8. Make a note of your login information including user and public IP address.

## Access the server using SSH

1. Open the **PuTTY** tool.
2. Enter **username@publicIpAddress** where username is the value you assigned when creating the VM and publicIpAddress is the value you obtained from the Azure portal.
3. Specify **22** for the **Port**.
4. Choose **SSH** in the **Connection Type** option group.
5. Navigate to **SSH** in the Category panel, then click **Auth**.
6. Click the **Browse** button next to **Private key file for authentication**.
7. Navigate to the  private key file saved when you generated the SSH keys and click **Open**.
8. From the main PuTTY screen click **Open.**
9. You will now be connected to your server command line. 