- ==`Credential Manager`== is a feature built into Windows since `Server 2008 R2` and `Windows 7`
- It allow user and applications to securely store credentials relevant to other systems and websites.
- Credential are stored in special encrypted folders on the computer under the user and system profiles.
	
	-  `%UserProfile%\AppData\Local\Microsoft\Vault\`
	- `%UserProfile%\AppData\Local\Microsoft\Credentials\`
	- `%UserProfile%\AppData\Roaming\Microsoft\Vault\`
	- `%ProgramData%\Microsoft\Vault\`
	- `%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\`

- Each vault folder contains a `Policy.vpol` file with AES keys (AES-128 or AES-256) that is protected by `DPAPI` 
- These AES keys are used to encrypt the credentials. 
- Newer version of Windows use `Credential Guard` to protect `DPAPI` master keys by storing them in secured memory enclaves  ([Virtualization-based Security](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-vbs))

- Microsoft often refers to the protected stores as `Credential Lockers`.
	- ==`Credential Manager`== is the user-facing feature/API, while the actual encrypted stores are the vault/locker folders.
	- There are 2 Windows Credential stores;

| Name                | Description                                                                                                                                                                                                      |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Web Credentials     | Credential associated with websites and online accounts.                                                                                                                                                         |
| Windows Credentials | Used to store login tokens for various services such as:<br>       a. OneDrive<br>	   b. Credential related to domain users<br>	   c. local network resources, <br>	   d. services <br>	   e. shared directories |
- It is possible to export Windows Vaults to ==`.crd`== files via control panel or with the command prompt:

	- `rundll32 keymgr.dll, KRShowKeyMgr`
	
		- After running the above command a pop up box will open
		 ![[Screenshot 2025-10-07 170014.png]]

		### Enumerating credentials with cmdkey:
		
		- Use ==`cmdkey`== to enumerate the Credentials store in the currents user's profile:
			-  Command:
				- `cmdkey /list`
				![[Screenshot 2025-10-07 170758.png]]
			- ###### Stored credentials are listed with the following format:
				- `Target`
				- `Type`
				- `User` 
				- `Persistence`
			- If a `Domain Password` account type is found then it can be used for interactive logon sessions
			- Whenever  a user come across an `interactive` `"Target"` type of credential use ==`runas`== to impersonate the stored user:
				- Command:
					- `/savecred /user:<user_name> cmd`

## Extracting Credentials with Mimikatz:

- There are many different tools that can be used to decrypt stored credentials. One of them is:
	- `Mimikatz`
- There are multiple ways to attack the credentials :
	- dump credentials from memory using the `sekurlsa` module.
	- Or manually decrypt credential using `dpapi` module.