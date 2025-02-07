<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Configurations in Azure</h1>
This lab is a follow up to the lab where I installed Active Directory and created a domain controller. I will now be configuring Active Directory and allowing a client to join the domain as well as creating user accounts. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro (21H2)

<h2>Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/EFjZrUG.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/HSJk3BO.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Now that Active Directory is installed on the domain controller VM, you can begin organizing and managing users through Organizational Units (OUs) and assign user privileges.

Here are the steps to create new Organizational Units (OUs) and users:

1.) Open the Active Directory Users and Computers console:
- You can open this by searching for it in the Start menu or using the Server Manager.

2.) Create Organizational Units (OUs):
- In the console, right-click on the domain you created (in your case, ernestotest.com).
- Select New > Organizational Unit.
- Name the first OU as _EMPLOYEES and the second one as _ADMINS. These OUs will be used to organize your users later on.
- The naming convention will help with future scripts (like PowerShell scripts) that can target these specific OUs.

3.) Create a new user within the _ADMINS OU:
- Right-click on the _ADMINS OU and select New > User.
- Create a new user called Jane Doe. Fill in the required information, including the username (e.g., janedoe) and password.

4.) Assign administrative privileges:
- After creating Jane's account, right-click on the Jane Doe user and select Properties.
- In the Properties window, go to the Member Of tab.
- Click Add to add Jane to the Domain Admins security group. This will give her administrative privileges on the domain.
- Click OK to apply the changes.

5.) Log in as Jane with administrative privileges:
- From now on, you will use Jane's account to make further changes.
- Log off from your current session (as labuser) and log in as jane_admin with the appropriate password.

Now, with Jane Doe added to the Domain Admins group, she has administrative privileges, and you can proceed to make further configurations and changes as needed. The _EMPLOYEES OU can be used for general users who will not have admin privileges, and the _ADMINS OU will house users with higher access levels.

</p>
<br />

<p>
<img src="https://i.imgur.com/X6UGnsf.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Before the client VM can join the domain, you need to configure the DNS settings to point to the domain controller's private IP address. This ensures that the client VM can resolve domain names to the domain controller and successfully join the domain.

Follow these steps to configure the DNS settings for the client VM:

1.) Open the Azure portal and navigate to the client VM.

2.) In the Networking tab, click on the Network Interface associated with the client VM.

3.) In the Network Interface settings, locate the DNS servers section.

4.) Click Custom and enter the private IP address of the domain controller VM as the DNS server.
- This is the static IP address you assigned earlier to the domain controller.
- 
5.) Save the changes to update the DNS configuration.

Once the DNS settings are configured, restart the client VM to ensure the changes take effect.

After the restart, the client VM will be able to use the domain controller for DNS resolution and will be ready to join the domain in a future step.
</p>
<br />

<p>
<img src="https://i.imgur.com/b1gUew4.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/N0Mnfoq.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/DkPUJNR.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Now that the DNS settings have been configured, it's time to join the client VM to the domain you created earlier.

Here are the steps to join the client VM to the domain:

1.) Log in to the client VM using your local administrator account.

2.) Open the System Properties:
- In the client VM, right-click This PC and select Properties.
- In the left panel, click on Advanced system settings.
  
3.) Rename this PC and join the domain:
- In the System Properties window, under the Computer Name tab, click on Change.
- In the Computer Name/Domain Changes window, select Domain and enter the domain name you created earlier (e.g., ernestotest.com).
  
4.) Enter domain credentials: ernestotest.com\janedoe
- You will be prompted to enter domain credentials. Enter Jane Doe's credentials (since she is part of the Domain Admins group) in the format:
- Provide the corresponding password for Jane’s account.
  
5.) Restart the client VM:
- Once the domain join process is complete, you will receive a message saying the client has successfully joined the domain.
- Restart the client VM to complete the process.

6.) Verify on the domain controller:
- Log in to the domain controller VM and open the Active Directory Users and Computers console.
- Under the Computers section of your domain, you should now see the client VM listed as a computer that has successfully joined the domain.

The client VM is now a member of the domain, and you can proceed with domain management, user configurations, and other tasks within the domain environment.

</p>
<br />

<p>
<img src="https://i.imgur.com/jmR2LXa.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Before non-administrative users in the domain can use Remote Desktop to access the client computer, you need to enable Remote Desktop access for them. Here are the steps to allow Domain Users to access the client machine remotely:

1.) Log in to the client VM using an account with administrative privileges (in this case, Jane Doe).

2.) Open System Properties:
- Right-click This PC and select Properties.
- In the left pane, click Advanced system settings.

3.) Enable Remote Desktop:
- In the System Properties window, go to the Remote tab.
- Under the Remote Desktop section, select Allow remote connections to this computer.
- You will be prompted to select which users are allowed to connect remotely. Click Select Users.

4.) Add Domain Users:
- In the Remote Desktop Users dialog box, click Add.
- Type Domain Users (or any other specific group or user you'd like to allow remote access to the client machine).
- Click Check Names to ensure the domain users group is recognized and then click OK.

5.) Apply the Changes:
- Click OK on the Remote Desktop Users window, and then OK again on the System Properties window.

Now that Domain Users are granted access to Remote Desktop, they can log in to Client-1 using their credentials. Keep in mind that in a production environment, you could use Group Policy to apply this setting to multiple systems at once. However, for this lab, we are manually enabling it for now.

Non-administrative users are now able to connect to the client machine via Remote Desktop using their domain credentials.
</p>
<br />

<p>
<img src="https://i.imgur.com/HrI6BTq.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/c7LaN48.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Creating users can be done manually or through the use of a script. For this lab, I will be using a PowerShell script. The PowerShell script can be found <a href="https://github.com/AsiaPonder001/BunchofUsers/blob/main/README.md?plain=1)"> here. </a> On the domain controller, open PowerShell ISE as an administrator (and make sure you are logged in with an admin account on the domain controller). Create a new file and paste the script into ISE console. Run the script and observe the accounts being created. 
</p>
<br />

<p>
<img src="https://i.imgur.com/Xn5tQU2.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
After creating the users, Client-1 can now be signed in as one of the new users that were created from the PowerShell script. Pick a name and simply sign in to the client with the context of the domain. In my case, it is ernestotest.com\bon.rovej.
</p>
<br />

<h2>Lessons Learned</h2>

This lab has helped me learn how to set up Active Directory, join clients to the domain, and manage users and permissions. Although there's a lot of menu navigation in Active Directory, it’s not too difficult to understand once you get familiar with it. This lab has also set me up to dive deeper into topics like DNS settings and file permissions, which I plan to explore more in future labs. These areas will give me a better understanding of how everything works together in a network environment. I’m excited to continue learning and experimenting with different configurations.
