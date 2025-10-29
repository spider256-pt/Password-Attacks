- Nearly all corporate environments include network shares used by the employees to store and share the files across teams.
- This can be unintentionally become a goldmine for the attackers.

#### Common Credential Patterns:

- It is important to understand the different types of pattern and file format that often reveal sensitive information.
	
	- Look for the keywords within the files such as:
		- `passwd`
		- `user`
		- `token` 
		- `key` 
		- `secret`
	- Search for files with extension commonly associated with stored credential, such as:
		- `.ini`
		- `.cfg`
		- `.env`
		- `.xlsx`
		- `.ps1`
		- `.bat`
	- Watch for files with "interesting" names that include term like:
		- `config`
		- `user`
		- `passw`
		- `cred`
		- `initial`
	- Keywords should be localized based on the target

- How tools like:
	- `MANSPIDER`
	- `Snaffler`
	- `SnafflePy` and 
	- `NetExec` can be use to automate and enhance the process of credential hunting.

## Hunting from Windows:

- ### Snaffler
	
	- The first tool is [Snaffler](https://github.com/SnaffCon/Snaffler). This is a C# program that run on a ==`domain-joined`== machine.
	- It automatically identifies accessible network shares and searches for interesting files.
	- `Command`:
		- `Snaffler.exe -s`
	- The tool output may return "==`False Positive`==" result so a manual review is typically required.

- ### PowerHuntShares:
	
	- ==`PowerHuntShares`== is a powerful tool. A PowerShell script that doesn't necessarily need to be run on a domain-joined machine.
	- The most useful features is that it generates an HTML report upon completion, providing an `easy-to-us UI` for reviewing the results.
	- `Command`: `To run a basic scan using PowerHuntSHares`
		- `Invoke-HuntSMBShares -Threads 100 -OutputDirectory c:\Users\Public`

## Hunting from Linux:

- ### MANSPIDER:
	
	- If attacker don't have access to a domain-joined computer simply prefer to search for files remotely, tools like [MANSPIDER](https://github.com/blacklanternsecurity/MANSPIDER) allows to scan SMB shares from Linux,
	- Best to run the ==`MANSPIDER`== using the official docker container to avoid dependency issues.
	- MANSPIDER offers many parameters that can be configured to fine-tune the search.
	- `Command`:
		- `docker run --rm -v ./manspider:/root/.manspider balcklanternsecurity/manspider <target_address> -c '<string>' -u <username> -p '<password>'`

- ### NetExec:
	
	- NetExec can also be used to search through network shares using the --spider option.
	- A basic scan of network shares for files containing the string `"passw"` can be run like so:
	- `Command`:
		- `nxc smb <target_ip> -u <user_name> -p <password> --spider IT --content  --pattern "passw"`


