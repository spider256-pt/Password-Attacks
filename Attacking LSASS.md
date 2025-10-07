- Acquiring copies if the SAM database to extract and crack password hashes, we will be also benefit from targeting the ==`Local System Authority Subsystem Service (LSASS)`==. 
- ==`LSASS`== is a core Windows process responsible for:
	- enforcing security policies
	- handling user authentication
	- storing sensitive credential material in memory

- ###### Upon initial logon, LSASS will:
	- Cache credentials locally in memory 
	- Create access tokens 
	- Enforce security policies 
	- Write to Windows' security log

## Dumping LSASS process memory:

-  First create a copy of the contents of LSASS process memory via the generation of a memory dump.
- Creating a dump file let to extract credentials offline.
- Offline mode gives more flexibility in the speed.

	- #### Task Manager method:
		- Open Task manager
		- Select the `Processes` tab
		- Find and right click the ==`Local System Authority Process`==
		- select create dump file
			
			- A file named `lsass.DMP` will created and saved in `%temp%`.
			- And then use this in the same way as the the `SAM`, `SYSTEM` and `SECURITY` file to crack the dumped hash if `lsass.DMP`.
	
	- #### Rundll32.exe & Comsvcs.dll method:
	
		- The Task Manager method is dependent on the GUI-based interactive session with a target.
		- Alternative method to dump LSASS process memory through a command-line utility called `rundll32.exe`.
			- It is way faster than the task manager method and more flexible.
			- Modern anti-virus tools recognize this method as malicious activity.
				- Determine the `ProcessID(PID)` is assigned to ==`lsass.exe`== 
				
				- ###### Finding LSASS's PID in CMD:
				
					- `tasklist /svc`
						- This command can be use to found `lsass.exe` and its process ID.
							![[Pasted image 20251007102344.png]]
					
				- ###### Finding LSASS's PID in PowerShell:
					
					-  Use Get-Process `lsass` and see the process ID in the Id field.
						![[Pasted image 20251007103328.png]]

				- ###### Creating a dump file using Powershell(rundll32.exe):
					
					- ` rundll32 C:\windows\system32\comsvcs.dll, Minidump 672 C:\lsass.dmp full`
					- with this command:
						- We are running `rundll32.exe` to call an exported function `comsvcs.dll` which also calls `Minidump` function to dump the LSASS process memory to specified directory `(c:\lsass.dmp)`.

## Using Pypykatz to extract credentials:

- Once `lsass` file is dump, ==`pypykatz`== is a tool can be use to extract credentials from the `.dmp` file.
- `Pypykatz` is a implementation of ==`Mimikatz`== written entirely in ==`python`==.
- Python allows us to run it on Linux-based machine.
- `Mimikatz` only runs on Windows systems. 
	
	- ### Running Pypykatz:
		
		- After receiving the file from the windows use the following command:
			- `pypykatz lsa minidump <path_itis_saved_id/lsass.dmp>`
			
			- After the command the results will throw some essential information:
				
				- ###### MSV:
					- It is an authentication package in Windows that LSA calls on to validate logon attempts against the SAM database.
					- `pypykatz` will some essentials like:
						- SID
						- Username
						- Domain
						- NT & SHA1 password hash
				
				- ###### WDIGEST:
					- It is an older authentication protocol by default in Windows XP-Windows 8 and Windows Server 2003- Windows Server 2012
					- LSASS caches credentials used by WDIGEST in clear text
					- Modern WDIGEST is disabled by default.
				
				- ###### Kerberos:
					- It is a network authentication protocol used by `Active Directory(AD)` in Windows Domain Environment.
					- Domain user accounts are granted tickets upon authentication with Active Directory.
						- This tickets is used to allow the user to access shared resources on the network that they have been granted.
					- LSASS caches `passwords`, `ekeys`, `tickets` and `pins`
				
				- ###### DPAPI:
					- `Mimikatz `and `pypykatz` can be extract the DPAPI `masterkey` for logged-on users whose data is present in LSASS process memory.
					- These key can be used to decrypt the secret. 