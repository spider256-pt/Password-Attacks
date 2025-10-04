- Every computer have services installed to manage, edit or create content.
- All these services are hosted using specific permissions and are assigned to specific users.
- Apart from web application, these services include:
	- `FTP`
	- `SMB`
	- `NFS`
	- `IMAP/POP3`
	- `SSH`
	- `MySQL/MSSQL`
	- `RDP`
	- `WinRM`
	- `VNC`
	- `Telnet`
	- `SMTP`
	- `LDAP`
- To know more about this use Footprinting module.

## WinRM:

- ==`WinRM`== Windows Remote Management is the Microsoft implementation of the `Web Services Management Protocol(WS-Management)`.
- It is a network protocol based on the XML web services using the `Simple Object Access protocol(SOAP)` which is used for remote management of Windows Systems.
- It takes care of the communication between `Web-Based Enterprise Management(WBEM)` and `Windows Management Instrumentation(WMI)` which can call the `Distributed Componet Object Model (DCOM)`
- For security reasons, WinRM must be activated and configured manually in Windows 10/11.
- It uses the TCP port `5985(HTTP)` and `5986(HTTPS)`
- A handy tool that can be use for our password attacks is `NetExec`.
	- Which can also be used for other protocols such as:
		- `SMB`
		- `LDAP`
		- `MSSQL`, and others
	
	- #### NetExec:
		- ##### Installation:
			- `apt-get -y install netexec`
		
		- ##### NetExec Menu Options:
			- Run the tool with `-h` flag which will show general usage:
				- `netexec -h` 
			
			- ##### NetExec Protocol-Specific Help:
				- `netexec <protocol_name> -h` 
			
			- ##### NetExec Usage:
				
				- `netexec <proto> <target_ip> -u <user or userlist> -p <password or passwordlist>` 

- Another handy tool to communicate with WinRM services is ==`Evil-WinRM`==:
	
	- #### Evil-WinRM:
		- ##### Installation:
			- `sudo gem install evil-winrm`
		
		- ##### Evil-WinRM Usage:
			- `evil-winrm -i <target_ip> -u <username> -p <password>`
				- if the login is successful, a terminal session is initiated using `MS-PSRP` which simplifies the operation and execution of commands.

## SSH(Secure Shell):

- ==`Secure Shell(SSH)`== is more secure way to connect to a remote host to execute system commands or transfer files from a host to a server.
- The SSH server runs on TCP `port 22`.
- The service uses three different cryptography operation/methods:
	- `symmetric encryption`
	- `asymmetric encryption`
	- `hashing`
	
	- #### Hydra-SSH:
		- ==`Hydra`== can be used to brute-force SSH. This is covered in-depth in the `login brute-forcing`
			- Command:
				- `hydra -L <user_list> -P <password_list> ssh://<target_ip>` 
			
			- After getting user and password after executing hydra command login to `ssh` server using `ssh` command:
				- `ssh user@Ip_add`

- ## Remote Desktop Protocol(RDP):
	
	- ==`RDP`== is a network protocol that allows remote access to windows System.
	- It uses TCP port `3389`
	- RDP provides both administrative/support staff with remote access to windows host within an organization.
	- The RDP is an application layer protocol.
		
		- ##### Hydra-RDP:
			
			- `hydra -L <user_list> -P <password_list> rdp://<targert_ip>`
			- `xfreerdp /v:<target_ip> /u:<user_name> /p:<password>`

- This can also be used for other services like smb:
	- `hydra -L <user_list> -P <passoword_list> smb://<target_ip>` or `msfconsole`
	- To get into smb server:
		- `use smbclient`
			- `smbclient -u <user> \\\\<target_ip>\<share_name>`
		- `use netexec`
			- `netexec smb <target_ip> -u <user> -p <password> --share`

[[Introduction to Password Attacks]]