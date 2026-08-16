# active-directory-lab
Active Directory home lab focused on users, groups, permissions, Group Policy, and troubleshooting.


#Overview
I built an Active Directory home lab using Windows Server 2016 and Windows 10 virtual machines. I configured the server as a domain controller to centrally manage users, computers, security groups, and organizational units for a simulated company. I created departmental file shares, assigned access using group-based permissions and least privilege, and used Group Policy to automatically map network drives for appropriate users. I also joined client machines to the domain, practiced remote administration using Windows Remote Desktop, configured password and account lockout policies, and troubleshot issues involving DNS, Group Policy, authentication, and shared-folder access.

## Initial Configuration

To begin the lab, I created a virtualized Windows environment using Windows Server 2016 and Windows 10. The Windows Server VM was configured with a static IP address so it could reliably provide domain and DNS services to client machines.

I installed the **Active Directory Domain Services (AD DS)** role on Windows Server 2016 and promoted the server to a **domain controller** for the `cat.com` domain. This established the server as the central system for managing users, computers, groups, authentication, and Group Policy within the lab environment.

Because Active Directory relies heavily on DNS, I configured the Windows 10 client to use the domain controller as its DNS server. I then joined the Windows 10 machine to the `cat.com` domain and verified that it could communicate with and authenticate against the domain controller.

After the client was successfully joined to the domain, I tested the environment by logging in with a domain user account and using commands such as `whoami`, `nslookup`, and `ping` to verify domain authentication, DNS resolution, and network connectivity.

#Run Inital commands to confirm connecivity

This screenshot verifies that the Windows 10 client is logged in with the domain account cat\doug and authenticated by the SERVER2016 domain controller. It also confirms that DNS resolves Server2016 to the IP address 10.1.10.2 and that the client can successfully communicate with the server using ping. 

Verified the Windows 10 client’s network configuration, including its cat.com DNS suffix, static IPv4 address, default gateway, and domain-controller DNS server at 10.1.10.2. 


#Idenity managment % organization


Organized the simulated company’s Active Directory environment using department-based Organizational Units and security groups. Users were structured by department, while groups such as CAT-FIN, CAT-IT, and CAT-MAR were used to assign access based on job role. 


#Secure file sharing & Least privledge

Created centralized departmental folders within the CompanyShares directory and shared them over the network using paths such as \\SERVER2016\Sales. 

Verified least-privilege access by confirming that a Marketing user could open the Marketing share but was denied access to the Executive share. 


Secure File Sharing & Least Privilege
Configured a centralized company file share ensuring departments could only access their required data.
Structure: Created departmental folders within a main CompanyShares directory.
Least Privilege: Permissions were assigned exclusively through Security Groups (e.g., only the CAT-Executive group has Modify access to the Executive folder).

Group Policy & Centralized Configuration
Configured Group Policy to centrally manage user settings across the domain. Created and linked a Finance mapped-drive GPO to the Finance OU, allowing Finance users to automatically receive the department’s shared folder as the F: drive when they signed in.


Created and linked a Group Policy Object to the Finance OU to automatically map the Finance network share as the F: drive for department users. 


Created and linked a Group Policy Object to the Finance OU to automatically map the department’s shared folder as the F: drive for Finance users. Verified the policy on a Windows 10 client using gpresult /r and confirmed that the mapped drive appeared in File Explorer after the user signed in. 
