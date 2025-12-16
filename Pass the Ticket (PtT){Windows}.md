- Another method for moving laterally in an Active Directory Environment is called a Pass the Ticket(PtT) attack.
- In this the attacker use stolen ===`Kerberos tickerts`== to move laterally instead of an NTLM password hash.
## For Windows:

- ### Kerberos protocol refresher:
	
	- The Kerberos authentication system is ticket-based.
	- Kerberos keeps all the tickets on the local system and presents each service only the specific ticket for that service , preventing ticket from being used for another purpose.
		
		- `TGT{Ticket Granting Ticket}`: It is the first ticket obtained on a Kerberos system.
			- It permits client to obtain additional Kerberos tickets or TGS.
		
		- `TGS{Ticket Granting Service}`:  It is required by users who want to use a service. 
			- These tickets allow services to verify the user's identity.
	
	- When user request TGT, they must authenticate to the domain controller by encrypting the current timestamp with their password hash.

- ### Pass the Ticket(PtT) attack:
	
	- The attacker must need a valid Kerberos Ticket to perform a Pass the Ticket attack.
	- It can be: 
		- TGS to allow access to a particular resource
		- TGT which can be used to request service ticket to access any resource the user has privileges.
		- Tools:
			- `Mimikatz`
			- `Rubeus`
	
	- ### Harvesting Kerberos ticket from Windows:
		
		- #### Mimikatz.exe:
		
			- On Windows, tickets are processed and stored by the `LSASS{Local Security Authority Subsystem Service}`process.
			- To get a ticket from Windows System, Communicate with LSASS and request it.
			- A non-administrative user, can get only his ticket, but a local administrator can collect everything.
			- A attacker can harvest all ticket from a system using the ==`Mimikatz`== module `sekurlsa::tickets /export`. 
			- The result is a list of files with the extension `.kirbi` which contain the tickets.
			- `The tickets that end with $ corresponds to computer account`.
			- `User ticket have the user's namem followed by an @ that separates the service name and the domain`
			- Example:
				- `[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi`
				- `[0;3e7]-0-2-40a50000-DC01$@cifs-DC01.inlanefreight.htb.kirbi`
				- `A ticket with the service krbtgt, it corresponds to TGT of that account.`
				- `Using mimikatz version 2.2.0 20220919, if run sekurlsa::ekeys it represent all hashes as des_cbc_md4 on some Windows 10 versions`.
				-  `Exported tickets (sekurlsa::tickets /export) do not work correctly due to the wrong encryption`
		
		- #### Rubeus - Export Tickets:
			
			- Rubeus and the option dump is a another tool that can be used to export tickets(if running as a local administrator) 
			- Rubeus dump will not give a file but it will print the ticket encoded in Base64 format
			- `/nowrap` is a option that is used for easier copy-paste
			- This is a common ay to retrieve tickets from a computer.
			- Another advantage of abusing Kerberos tickets is the ability to forge our on tickets. {OverPass the Hash} technique
		
				- #### Pass the Key aka, OverPass the Hash:
					
					- The traditional Pass the Hash(PtH) technique involves reusing an NTLM password hash that doesn't touch Kerberos.
					- The Pass the Key aka, OverPass the Hash approach converts a hash/key for a domain-joined user into a full TGT.
					- To forge a ticket user hash is required.
					
					- #### Using Mimikatz:
						
						- Mimikatz to dump all users Kerberos encryption keys using the module `sekurlsa::ekeys`
						- `Command`:
							- `mimikatz.exe`
							- `privilege::debug`
							- `sekurlsa::ekeys`
								- It will list all the hash like AES256 and RC4_HMAC
							
							- ` sekurlsa::pth /domain:<domain_name> /user:<user_name> /<hash_type>:<hash_value>`
							- This will create a new cmd.exe window can be used for request access to any service.
					
					- ## Using Rubeus:
						
						- `Command:`
							- `c:\tools> Rubeus.exe asktgt /domain:<domain_name> /user:<user_name> /aes256:<hash_value> /nowrap`
						- After getting some Kerberos tickets, now it can be used to move laterally within an environment.
						- After retrieving the ticket in Base64 format.
						- Use the flag ==`/ptt`== to submit the ticket (TGT or TGS) to current logon session. 
						- `Command`:
							
							- `Rubeus.exe asktgt /domain:<domain_name> /user:<user_name> /<hash_type>:<hash_value> /ptt`
						
						- Another way is to import the ticket into the current session using `.kirbi` file from the disk.
							
							- `Rubeus.exe /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi`
						
						- We can also use the Base64 output from Rubeus or convert a ==`.kirbi`== to base64 to perform the Pass the Ticket attack. 
						- PowerShell can be used to convert a ==`.kirbi`== to base64
						- Command:
							
							- #### Convert `.kirbi` to Base64 Format:
								- `[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi")) <Ticket>
								
							- Base64 string can also be used instead of the filename. 
							- Command:
								
								- `Rubeubs.exe ptt /ticket:<ticket_string>` 
					
					- ## Using Mimikatz:
					
						- Mimikatz can be used to Pass the Ticket.
						- Module `kerberos::ptt` and the `.kirbi` file that contains the ticket that can be imported.
						- Command:
							
							- `mimikatz.exe`
							- `privilege::debug`
							- `kerberos::ptt "<File_path>"`
							- ==`Mimikatz module `misc` to launch a new command prompt window with the imported ticket using the `misc::cmd` command.`==

- ## Pass the Ticket with PowerShell Remoting:
	
	-  PowerShell Remoting allow a user to run Scripts or commands on a remote computer.
	- Enabling PowerShell Remoting creates both HTTP and HTTPS listeners on port TCP/5985 for HTTP and TCP/5986 for HTTPS.
	- To create a PowerShell Remoting session on a remote computer, the user must have administrative permissions.
	
	- ## Mimikatz - PowerShell Remoting with Pass the Ticket:
		
		- To use PowerShell Remoting, Use Mimikatz to import the ticket and then open PowerShell console and connect to the target machine.
		- Command:
			- `mimikatz.exe`
			- `privilege::debug`
			- `kerberos::ptt "<file_path>"`
			- `exit`
			- `powershell`
			- `Enter-PSSession -ComputerName <target_system_name>`
	
	- ## Rubeus - PowerShell Remoting with Pass the Ticket:
		
		-  Rubeus has the option ==`createneonly`==, which creates a sacrificial process/logon session.
		- The process is hidden by default but can be specify the flag `/show` to display the process and the result is the equivalent of ==`runas /netonly`== 
		- `Command:`
			
			-  `Rubeus.exe createneonly /program:"C:\Windows\System32\cmd.exe" /show`
			- This command will open a new cmd window. From that window we can execute Rubeus to request a new TGT with the option `/ptt` to import ticket for the current session and connect to the DC using  PowerShell Remoting.
			- `Rubeus.exe asktgt /user:<user_name> /domain:<domain_name> /aes256:<hash_value> /ptt`
