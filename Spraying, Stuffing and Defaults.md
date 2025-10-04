## Password Spraying:

- ==`Password spraying`== is a type of brute-force attack in which attacker attempts to use a single password across many different user accounts.
- Depending on the target system, different tools may used to carry out password spraying attacks.
	- For web application ==`Burpsuite`== is strong tool 
	- For Active Directory environments, tool such as  [NetExec](https://github.com/Pennyw0rth/NetExec) or  [Kerbrute](https://github.com/ropnop/kerbrute) are commonly used:
		`netexec <protocol> <target_ip/subnets> -u <user_list> -p 'password123!'`

## Credential Stuffing:

- Credential stuffing is another type of brute-force attack in which an attacker uses stolen credentials from one service to attempt access on other.
- The user may use the same `username:password` or both across multiple platforms.
- Tool:
	- `hydra`
		- command:
			- `hydra -C user_pass.list ssh://<target_ip>`

## Default Credential:

-  Many systems--such as routers, firewall  and databases--come with default credentials.
- Several lists of known default credentials are available online, there are also dedicated tools that automate the process.
- One widely used example is [Default Credentials Cheat Sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) which cab be install with ==`pip3`==
	- `pip3 install defaultcred-cheat-sheet`
	- Once installed use ==`creds`== command to search for known default credentials associated with a specific product or vendors
		- Command:
			- `creds search linksys`
		
- In addition publicly available lists and tools, default credentials can often be found in product documentation.

- After researching the default credentials online, we can combine them into new list, formatted as `username:password` and reuse previously mentioned `hydra` command to attempt access.



[[Introduction to Password Attacks]]
