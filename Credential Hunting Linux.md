Hunting the credential is the first step that can the attacker can do after having the access to the system. There are several sources that can provide us with credentials that can be categorized into four:

- ==`Files`== : includes configs, databases, notes, scripts, source code, cronjobs and SSH keys
- History:  includes logs, and common-line history
- Memory: includes cache, and in-memory processing  
- Key-rings: such as browser stored credentials

All these categories will increase the probability of successful find outs. With some ease.


## Files:

- The core principle of Linux is that everything is a file.
- So it allow the user to search, find and filter the appropriate files according to the requirements.
- Look for categories of file one by one:
	- Configuration files
	- Database
	- Scripts
	- Cronjobs
	- SSH Keys
		
		- ### Configuration Files:
			
			- These files are the core functionality of services on Linux distribution.
			- These file may contain some valuables(credentials) that can be leaked or spoofed by the attacker.
			- Usually the configuration files are marked by three file extensions:
				- `.config`
				- `.conf`
				- `.cnf`
				
				- #### Searching for configuration Files:
					
					- The most important part of any system enumeration is to obtain an overview of it.
					- So first step is to find all the possible configuration files on the system, then examine and analyze individually in more detail.
					- `Command:`
						
						- `for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done`
					- This command can reduce the search.
					- `Command`to run the scan directly for each file found with the specified file extension and output the contents.
						
						- `for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done`
				
				- #### Searching for databases:
					
					- For searching the ==`database`== the same method can be used:
					- Command:
					
						- `for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done`
				
				- #### Searching for notes:
					
					-  Finding ==`notes`== about specific process on the system often include lists of many different access points or even credentials
					- It is often challenging to find notes right away if it is stored some where on the system and not on the desktop or in its subfolders.
						- This is because they can be named anything and do not have to have specific file extension such as `.txt` 
						- So search for files including the `.txt` file extension and files that have no file extension at all.
						- `Command`:
							
							- `find /home/* -type f -name "*.txt" -o ! -name "*.*"`
				
				- #### Searching for Scripts:
					
					- ==`Scripts`== are files that often contain highly sensitive information and processes.
					- These also contain credentials that are necessary to be able to call up and execute the processes automatically.
					- `Command`:
						- `for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done`
				
				- #### Enumerating cronjobs:
					
					- ==`Cronjobs`== are independent execution of commands, programs, scripts.
					- These are divided into the system-wide area (`/etc/crontab`) and user-dependent executions.
						- There are areas that are divided into different time ranges(`/etc/cron.daily`, `/etc/cron.hourly`, `/etc/cron.monthly`, `/etc/cron.weekly`)
					- The scripts files used by `cron` can also be found in `/etc/cron.d/` for ==`Debian-based`== distributions.
				
				- #### Enumerating History Files:
					
					- All history files provide crucial inforamation about the current and past/historical course of processes.
					- In the history of the commands entered on Linux distribution that `use Bash as a standard shell`,
					- `Command`:
						- `tail -n5 /home/*/.bash*`
				
				- #### Enumerating Log files:
					
					-  An essential concept of linux Systems is log files that are stored in text files.
					- Many programs, especially all services and the system itself, write such files.
					- Can find system errors, detect problems regarding services or follow what the system is doing in the background.
					- The entire of log files can be divided into four categories:
						
						- `Application logs` 
						- `Event logs`
						- `Service logs`
						- `System logs`
					
					- `Command`:
						- `for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done`

## Memory and cache:

- ### Minipenguin:
	- Many application and process uses credentials for authentication and store them in either in memory or in files so that they can be reused.
	- In order to retrieve this type of information from Linux distributions, the tool [mimipenguin](https://github.com/huntergregal/mimipenguin) can come in action that makes whole process easier.
	- This tool do require root permissions
	- `Command`:
		- `python3 minipenguin.py`

- ### LaZagne:
	- ==`LaZagne`==, this tool allow to access far more resources and extract the credentials.
	- The password and hashes cab obtain from different sources but are not limited to:
		- `Wifi`
		- `Wpa_supplicant`
		- `Libsecret`
		- `Kwallet`
		- `Chromium-based`
		- `CLI`
		- `Mozilla`
		- `Thunderbird`
		- `Git`
		- `ENV variables`
		- `Grub`
		- `Fstab`
		- `AWS`
		- `Filezilla`
		- `Gftp`
		- `SSH`
		- `Apache`
		- `Shadow`
		- `Docker`
		- `Keepass`
		- `Mimipy`
		- `Sessions`
		- `Keyrings`
	- `Command`:
		- `python3 laZagne.py all`
	 
- ### Browser credentials:
	- Browser store the passwords saved by the users in an encrypted form locally on the system to be reused
	- LaZagne can also be used to dig out or to decrypt the password if it is stored in the browser:
		- `python3 laZagne.py browsers`