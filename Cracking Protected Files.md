- The use of file encryption is often neglected on both ==`private`== and ==`professional`== contexts.
- It remains standard practice to discuss ==`confidential`== topics or transmit ==`sensitive`== data via email.
- Encrypted files can still be cracked and accessed with the right combination of wordlists and tools.

- In many cases, ==`symmetric encryption`== algorithm such as ==`AES-256`== are used to securely store individual files or folders
	- In this method, the same key is used for both encryption and decryption
	
- For transmitting files, asymmetric encryption is typically employed, which uses distinct keys:
	- the sender encrypts the file with recipient's ==`public key`==
	- the recipient decrypts it using the corresponding ==`private key`== 

## Hunting for Encrypted Files:

- Many different extensions correspond to encrypted files-- a useful reference list can be found on [FileInfo](https://fileinfo.com/filetypes/encoded)
- The command might use to locate commonly encrypted files on a Linux System.

	- `for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done`
## Haunting for SSH keys:

- Certain files such as SSH keys, do not have standard file extension. 
- In such case the files can be identify files by the standard content such as `header` and `footer` values.
- SSH private keys always begin with  `-----BEGIN [...SNIP...] PRIVATE KEY-----`
	- tools like `grep` can be used to get the ssh private file.
	- Command to use:
	
		- `grep -rnE '^\--{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null`
	
- Some SSH key are encrypted with a passphrase. 
- With older PEM formats, it was possible to tell if an SSH key is encrypted based on the header, which contains the encryption method in use 
- Modern SSH keys, however, appear the same whether encrypted or not.
- One way to tell, whether an SSH key is encrypted or not, is by trying the key with 
	- ==`ssh-keygen`== command:
	
		- `ssh-keygen -yf ~/.ssh/<key>`
		
		- if the above command result in key then the the ssh key is not encrypted but if it ask for passphrase then its encrypted.

## Cracking encrypted SSH keys:

-  JtR has many different scripts for extracting hashes from files which we can then proceed to crack.
- For cracking encrypted ssh key we will be using:
	- `ssh2john.py`
	- Command:
	
		- `ssh2john.py SSH.private > ssh.hash`
		- `john --wordlist=rockyou.txt ssh.hash`
		- `john ssh.hash --show`


[[Introduction to Password Attacks]]




 
