<p align="center">
</p>

<h1>Network File Sharing & Permissions</h1>
<p>
This tutorial walks you through the process of setting up a Domain Controller (DC) and a Client in Microsoft Azure, showcasing how to configure network connections and test functionality in a controlled virtual environment. By deploying a Windows Server as a Domain Controller (DC-1) and a Windows 10 machine as a client (Client-1), we’ll explore how to configure private networks, assign static IPs, and test connectivity between virtual machines.

Through this step-by-step guide, you’ll gain practical experience in:

- Establishing a domain environment in Azure.
- Configuring DNS settings and testing network connectivity.
- Troubleshooting and ensuring seamless communication between virtual machines.

Let's dive in. 

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory

<h2>Operating Systems Used</h2>

- Windows Server 2022
- Windows 10

<h2>Overview</h2>

  - Step 1: Create Sample File Shares with Various Permissions
  - Step 2: Test Access to File Shares as a Normal User
  - Step 3: Create an “ACCOUNTANTS” Security Group and Assign Permissions
  - Step 4: Add <someuser> to the “ACCOUNTANTS” Group and Test Access Again
  - Step 5: Ping "mainframe" from Client-1


<h2>Exercise Steps</h2>

<h4>Step 1: Create Sample File Shares with Various Permissions</h4>

<img src="https://i.imgur.com/wIJMPG7.png" height="80%" width="80%" alt=""/>

- Use your domain administrator account (mydomain.com\jane_admin) to log into the domain controller (DC-1).
- This account has sufficient privileges to create folders and manage file shares.
- Create Folders on the C:\ Drive:
  - Navigate to the C:\ drive on DC-1.
    - Create four folders:
      - read-access
      - write-access
      - no-access
      - accounting
  - Set Permissions and Share the Folders:
    - Right-click each folder, go to Properties, and then the Sharing tab.
    - Click Advanced Sharing > Share this folder, then click Permissions:
      - For read-access: Add the group Domain Users and assign the Read permission.
      - For write-access: Add the group Domain Users and assign both Read and Write permissions.
      - For no-access: Add the group Domain Admins and assign Read and Write permissions (do not include Domain Users).
      - Leave the accounting folder permissions unchanged for now.

<h4>Step 2: Test Access to File Shares as a Normal User</h4>

<img src="https://i.imgur.com/ErW8Wss.png" height="80%" width="80%" alt=""/>

Log in to Client-1:

Use a normal user account (mydomain\<someuser>) to log into Client-1.
This account represents a typical user in the domain.

- Navigate to the Shared Folders:
  - Open the Run dialog (Windows + R).
  - Type \\dc-1 and press Enter.
  - This opens a network view showing all shared folders on DC-1.
- Test Access:
  - Try to open each folder:
  - For read-access: You should be able to open and view files (if any) but cannot create new files.
  - For write-access: You should be able to open, view, and create files or folders.
  - For no-access: Access should be denied since you are not a member of the Domain Admins group.

- Evaluate Results:
  - Confirm that access aligns with the permissions you set.
  - Does the behavior make sense based on the permissions applied? (Yes, normal users only have access where Domain Users is explicitly granted permissions.)

<h4>Step 3: Create an “ACCOUNTANTS” Security Group and Assign Permissions</h4>

<img src="https://i.imgur.com/HPGrSp5.png" height="80%" width="80%" alt=""/>

- Log in to DC-1:
  - Use the domain administrator account (mydomain.com\jane_admin) again.
- Create a Security Group:
  - Open Active Directory Users and Computers.
  - Navigate to the Organizational Unit (OU) where you want to create the group.
  - Right-click > New > Group.
  - Name the group ACCOUNTANTS and ensure the group scope is Global and group type is Security.
- Assign Permissions to the “accounting” Folder:
  - Right-click the accounting folder > Properties > Sharing tab > Advanced Sharing > Permissions.
  - Add the ACCOUNTANTS group and assign both Read and Write permissions.
- Test Access as <someuser>:
  - While still logged in to Client-1 as <someuser>, navigate to the \\dc-1 shared folders.
- Attempt to open the accounting folder:
  - You should be denied access because <someuser> is not yet a member of the ACCOUNTANTS group.

<h4>Step 4: Add <someuser> to the “ACCOUNTANTS” Group and Test Access Again</h4>

<img src="https://i.imgur.com/hPFLhH7.png" height="80%" width="80%" alt=""/>

- Add <someuser> to the Group:
- Go back to DC-1.
- In Active Directory Users and Computers, find the user <someuser>.
- Right-click the user > Properties > Member Of tab > Add.
- Add the user to the ACCOUNTANTS group and click OK.
- Log Out of Client-1:
  - Log out of Client-1 as <someuser> and log back in.
  - This ensures the new group membership is applied (group memberships are updated at login).
- Test Access Again:
  - Navigate to \\dc-1 and try to open the accounting folder.
  - This time, access should be granted, and <someuser> should be able to read and write files in the accounting folder.
- Verify Results:
  - Confirm that the updated permissions work as expected.
