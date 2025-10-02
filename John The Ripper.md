- ==`John The Ripper`== (aka JtR) is a well known penetration tool used for cracking password through various attacks including brute-force and dictionary.
- It is a open-source software initially developed for UNIX.
- `"Jumbo"` variant has performance optimization, additional features such as:
	- `Multilingual word-list`
	- `support 64-bit architectures`
- This version of JtR is able to crack passwords with great accuracy and speed.

- ## Cracking Modes:
	
	- ##### Single modes:
		
		- ==`Single crack mode`== is a rule-based cracking technique that is most useful when targeting Linux credentials.
		- It generates password candidates on the victims username, home directory name and GECOS values:
			- `Full name`
			- `room number`
			- `phone number, etc`
		- Single crack mode can generate candidate passwords and test them against the hash.
		
			- `<candidate_name> --single passwd`
			- `john --format=<hash_type> --single <hash_file>`

	- ##### Wordlist mode:
		
		- ==`Wordlist mode`==  is used to crack passwords with a dictionary attack.
		- It means it attempts all passwords in the wordlist against the password hash.
		
			- `john --wordlist=<wordlist_file> <hash_file>`
			- ###### To see the cracked hash password:
				- `john --pot=/tmp/empty.pot --wordlist=/usr/share/wordlists/rockyou.txt --format=<hash_format> <hash_file>`
				- the /tmp/empty.pot is used as the storage of cracked hash and password for JtR. If an hash is already exist then it wont show you the result for that particular hash if tried again so use ==`/tmp/<file_name>.pot `== to ignore the already cracked hashes and password.
		
		- The wordlist file or files are used for cracking password hashes must be in plane text forma.
		
			- `with one word per line format`
		
		- Multiple wordlists can be specified by separating them with a comma.
		- Rules, either custom or bult-in, can be specified by using the --rules argument.
	
	- ##### Incremental mode:
		
		- Incremental mode is a powerful, brute-force-style password cracking mode that generates candidate passwords based on a statistical model([Markov chains](https://en.wikipedia.org/wiki/Markov_chain))
		- It is designed to test all character combinations defined by a specific character set.
		- This is more time-consuming and most exhaustive
		- It generates password guesses dynamically and does not rely on predefined wordlist.
			- `john --incremental <hash_file>`
	

- ## Identifying hash Formats:
	
	- Passwords hashes may appear in an unknown format, and even John Ripper may not able to identify them completely.
	- To get an idea is to consult:
		-  [JtR's sample hash documentation](https://openwall.info/wiki/john/sample-hashes)
		- [list by PentestMonkey](https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats) ==`<recommended>`==
		- [hashID](https://github.com/psypanda/hashID)
	- They checks supplied hashes against a built-in to suggest potential format.
	- The ==`-j`== tag is used to list corresponding JtR format. 
		- `hashid -j <hash_string>`
	- The ==`--format`== argument can be specified to instruct `JtR` which format target hashes are. 

![[Pasted image 20251001154049.png]]

| Hash Format | Example Command                      | Description                              |
| ----------- | ------------------------------------ | ---------------------------------------- |
| afs         | `john --format=afs[...] <hash_file>` | AFS (Andrew File System) password hashes |
|             |                                      |                                          |

- ## Cracking Files:
	
	- It is also possible to crack password-protected or encrypted files with JtR
	- Multiple ==`"2john"`== tools come with JtR that can be used to process files and procedure hashes compatible with JtR.
		- `<tool> <file_to_crack> > file.hash`
		- `locate *2john*`

[[Introduction to Password Attacks]]