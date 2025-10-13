- Once getting to a target Windows machine through the GUI or CLI, incorporating credential hunting into the approach can provide significant advantages
- ==`Credential Hunting`== is the process of performing detailed searches across the file system and through various application to discover credentials. 

## Search-centric:

- Many of the tools available to the users in Windows have search functionality.
- These are search-centric features built into most applications and operating systems, so they can be used for the user advantage.
- Default credential can found in various files.
- ##### Key terms to search for:
	- `Passwords`
	- `Passphrases`
	- `Keys`
	- `Username`
	- `User account`
	- `Creds`
	- `Users` 
	- `Passkeys`
	- `configuration`
	- `dbcredential`
	- `dbpassword`
	- `pwd`
	- `Login`
	- `Credential`

- ### Search Tools:
	
	- With access to the GUI, it is worthy attempting to use `Windows search` to find files on the target using some of the keywords mentioned above.
		- By default it will search various OS settings and the files system for files and applications containing the key term entered in the search bar.
	
	- #### LaZagne: 
		- Can use ==`LaZagne`== a third-party tool to take advantage to quickly discover credentials that web browser or other installed applications may insecurely store.
		- `Lazagne`is made up of modules which each target different software when looking for passwords.
		

| Module   | Description                                                                                                                               |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| browser  | Extracts passwords from various browsers including:<br>         1. Chrome<br>         2. Firefox<br>         3. Edge<br>         4. Opera |
| chats    | Extracts passwords from various chat applications including skype                                                                         |
| mails    | searches through mailboxes for passwords                                                                                                  |
| memory   | Dumps passwords from memory, targeting KeePass and LSASS                                                                                  |
| sysadmin | Extracts passwords from the configuration files of various sysadmin tools like OpenVPN and WinSCP                                         |
| windows  | Extracts Windows-specific credentials targeting LSA secrets, Credential Manager, and more                                                 |
| wifi     | Dumps Wifi Credentials                                                                                                                    |
##### Command:
- `start LaZagne.exe all`
- Can include the option `-vv`

#### findstr: 

-  [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr) is a tool to search from patterns across many types of files. 
- Can use common key terms can also use variations of this command to discover credentials on a Windows target:
	- Command:
		- `findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml`