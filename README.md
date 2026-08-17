# Overview
I built an Active Directory home lab using Windows Server 2016 and Windows 10 virtual machines. I configured the Windows Server as a domain controller to centrally manage users, computers, security groups, and organizational units for a simulated company. I created departmental file shares, assigned access using group-based permissions and least privilege, and used Group Policy to automatically map network drives for authorized users. I also joined Windows clients to the domain, practiced remote administration using Remote Desktop, configured password and account lockout policies, and troubleshot issues involving DNS, Group Policy, authentication, and shared-folder access.

# Lab Enviroment
Domain: cat.com
Domain Controller: Server2016
Server OS: Windows Server 2016
Client OS: Windows
Virtualization: VirtualBox
Server IP: 10.1.10.2
Services: Active Directory Domain Services, DNS, Group Policy

## Initial Configuration

Before entering Windows, I came up with a mock company called Cascade Adventure Travel. I created the departments the company would need and the users who would be part of the organization.

First, I created a virtualized Windows environment using Windows Server 2016 and Windows 10. I chose to configure the Windows Server VM with a static IP address so it could reliably provide domain and DNS services to client machines.

I installed the Active Directory Domain Services (AD DS) role on Windows Server 2016 and promoted the server to a domain controller for the cat.com domain. This established the server as the central system for managing users, computers, groups, authentication, and Group Policy within the lab environment.

![Active Directory Domain Services running on SERVER2016](ADDS_Domain_Controller.png)

Because Active Directory relies heavily on DNS, I configured the Windows 10 client to use the domain controller as its DNS server. I then joined the Windows 10 machine to the cat.com domain and verified that it could communicate with and authenticate against the domain controller.

After the client was successfully joined to the domain, I tested the environment by logging in with a domain user account and using commands such as whoami, nslookup, and ping to verify domain authentication, DNS resolution, and network connectivity.

![Terminal Commands Testing Connectivity](connectivity_Commands_1.png)

This screenshot verifies that the Windows 10 client is logged in with the domain account cat\doug and authenticated by the SERVER2016 domain controller. It also confirms that DNS resolves Server2016 to the IP address 10.1.10.2 and that the client can successfully communicate with the server using ping. 

**Commands used:**

- `whoami` — verified that I was logged in with a domain account (`CAT\doug`).
- `hostname` — confirmed the Windows 10 client name (`desktop2`).
- `echo %logonserver%` — confirmed the client was authenticating against `SERVER2016`.
- `nslookup SERVER2016` — verified DNS resolution for the domain controller.
- `ping SERVER2016` — confirmed network connectivity between the client and server.



## Identity Management & Organization

I organized the simulated company’s Active Directory environment using department-based Organizational Units (OUs) and security groups. Users were organized into OUs based on their department, including Executive, Finance, IT, Marketing, and Sales.

I also created departmental security groups such as `CAT-FIN`, `CAT-IT`, and `CAT-MAR` and added users to the appropriate groups based on their job role. These groups were later used to control access to departmental file shares, allowing permissions to be managed at the group level rather than individually for each user.

![Active Directory organizational units and security groups](Configured_userFolders_groups.png)

## Department File Shares and Permissions

I created centralized departmental folders under C:\CompanyShares, including folders for Executive, Finance, IT, Marketing, and Sales. Each folder was shared over the network using paths such as \\SERVER2016\Sales.

![File shared over netwrok \\SERVER2016\Sales](Shared_files.png)

I then assigned access using department-based security groups rather than giving permissions directly to individual users. For example, the CAT-FIN security group was granted Modify access to the Finance folder, allowing members to read, create, edit, and delete files without being given unnecessary Full Control.
![Confiremd permissions shared to Finance group](Finance_NTFS_Permissions.png)

To verify that the permission structure was enforcing least privilege, I tested access using a user from another department. The user could access their assigned departmental share but was denied access to the Executive share.
![Confiremd permissions shared to Finance group](Permissions_file_issue.png)

This confirmed that departmental resources were accessible to authorized users while access to unrelated folders remained restricted.


## Group Policy and Mapped Drives

Once users had access to the appropriate departmental folders, I used Group Policy to automatically map those shared folders as network drives on their accounts. Based on the Organizational Units and groups I had already created, I configured a drive mapping so authorized users would see their departmental share as a clickable drive in File Explorer.

![Confiremd permissions shared to Finance group](mapped_drives.png)

After configuring the policy, I used gpupdate /force on the Windows 10 client to refresh Group Policy. I then used gpresult /r to verify that the correct Group Policy Object had been applied to the user.

![Confiremd permissions shared to Finance group](group_policy_update.png)

This confirmed that the departmental network drive was being mapped automatically for the appropriate user.

## Security Policies

I configured account lockout and password policies to better secure the domain and user accounts.

Account Lockout Policy
Account lockout threshold: 3 failed attempts
Account lockout duration: 15 minutes
Reset account lockout counter after: 15 minutes

![Account LockOut Secuirty Policy](Account_Lockout.png)


I configured account lockout after three failed sign-in attempts to reduce the risk of repeated password-guessing attacks. The account remains locked for 15 minutes before the user can attempt to sign in again.

Password Policy
Minimum password length: 12 characters
Password complexity requirements: Enabled
Password history: 10 passwords remembered


![Passwrod Security Policy ](Password_policy.png)

I increased the minimum password length from 8 to 12 characters and enabled complexity requirements to encourage stronger passwords. I also configured password history to remember the previous 10 passwords, helping prevent users from immediately reusing old passwords.


## Troubleshooting and Validation

Throughout the lab, I encountered several configuration and access issues that required me to verify settings rather than only following the initial setup steps. These problems helped me better understand how DNS, Group Policy, security groups, and NTFS permissions work together in an Active Directory environment.

DNS Configuration Issue

At one point, the Windows 10 client was unable to correctly resolve domain resources because its DNS configuration was pointing to an external DNS server instead of the domain controller.

To troubleshoot the issue, I used commands such as:

ipconfig /all
nslookup
ping

I reviewed the client's network configuration and confirmed that the DNS server was incorrect. I changed the Windows 10 client to use SERVER2016 as its DNS server.

After correcting the DNS settings, the client was able to resolve domain resources correctly and communicate with the cat.com domain controller.

Department Folder Access Issue

I also encountered an issue where a user could not access the departmental folder they were supposed to have access to.

I checked the user's Active Directory group membership and compared it with the permissions configured on the departmental share. I found that the user was not a member of the correct department security group.

I added the user to the appropriate group and refreshed Group Policy using:

gpupdate /force

I then used:

gpresult /r

to confirm that the expected Group Policy Object was being applied.

After updating the user's group membership and refreshing policy, the user was able to access the correct departmental resources.

NTFS Permission Troubleshooting

While testing permissions, I also discovered that removing a user from a departmental security group did not always remove their access as expected. After reviewing the folder's security settings, I found that the user had also been granted permissions directly on the folder.

This helped me understand the difference between permissions inherited through a security group and permissions assigned directly to an individual user.

I corrected the folder permissions so access was controlled through the departmental security group rather than through direct user permissions. This made the permission structure easier to manage and better aligned with least privilege.

Permission Validation

After configuring the departmental shares, I intentionally tested access using users from different departments.

For example, a user who was not authorized to access the Executive share received an access denied message, while users assigned to the correct departmental group were able to access their own resources.

This confirmed that the security groups and NTFS permissions were restricting access as intended.


## Skills Practiced


Active Directory Domain Services
Windows Server administration
Domain controller configuration
Active Directory Users and Computers
Organizational Units
Security groups
NTFS permissions
Windows file sharing
Group Policy
Mapped network drives
DNS configuration
Domain joining
Authentication troubleshooting
gpupdate
gpresult
nslookup
ping
Windows security policies


## Project Summary

This project gave me hands-on experience with several key concepts involved in setting up and managing a Windows domain environment. I learned how organizations can use Windows Server, Active Directory, security groups, and Group Policy to centrally manage users, computers, and access to resources.

I practiced applying least privilege and role-based access by creating departmental groups, assigning permissions, and controlling access to shared network folders. I also configured account lockout and password policies to improve the security posture of the domain.

I created shared departmental folders, assigned access through security groups, and used Group Policy to automatically map those network drives to the correct users. I also practiced tasks such as password resets, account management, and remote access within the lab environment.

Overall, this project helped me better understand how users, computers, DNS, Active Directory, Group Policy, and file permissions work together to manage a workplace environment.
