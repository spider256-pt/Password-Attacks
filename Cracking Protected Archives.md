- Besides standalone files, there are `archives` and `compressed files` too such as ZIP files which can be protected with a password.
- There are many types of archive files. Some of the more commonly encountered file extension include:
	- `tar`
	- `gz`
	- `rar`
	- `zip`
	- `vmdb/vmx`
	- `cpt`
	- `truecrypt`
	- `butlocker`
	- `kdbx`
	- `deb`
	- `7z`
	- `gzip`
- A comprehensive list of archive file types can be found on [FileInfo](https://fileinfo.com/filetypes/compressed)
- Rather than typing manually use this command to query the data using one-liner, {apply filters as needed}.
	- A comprehensive list of archive file types can be found on ==`FileInfo`==. 
	- Rather than typing them out manually, we can also query the data using a one-liner, apply filters as needed, and save the results to a file. At the time of writing, the website lists 365 archive file types.
		- `curl -s https://<website_name>/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt`
		- Not all archive types supports native password protection, in such case additional tools are often used to encrypt files. 
			- Example:
				- TAR file are commonly encrypted using openssl or gpg.
	
## Cracking ZIP files:

- The ZIP format is often heavily used in Windows environments to compress many files into one file. 
- use the tool `JtR` for doing it:
	- command used:
		- `zip2john <Zip_file.zip> > <file_name.hash>`
		- `cat <file_name.hash>`
	- Once extracted the hash, use JtR to crack it:
		- `john --wordlist=<path_of_the_wordlist> <file_name.hash>`
		- `john <file_name.hash> --show`

## Cracking OpenSSL encrypted GZIP files:

- `openssl` can be used to encrypt files in GZIP format.
- To determine the actual format of the file, use ==`file`== command, which provide detailed inforamation about the file.
	- `file <file_name>`
	- Example:
		```
		  file GZIP.gzip
	  
		  GZIP.gzip: openssl enc'd data with salted password
	  ```
	- When cracking OpenSSL encrypted files, may encounter various challenges, including numerous false positive or complete failure to identify the correct password.
		- To mitigate the problem:
		
			- use ==`openssl tool`== within a `for loop` that attempts to extract the contents directly, succeeding only if the correct password is found.
				- `for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done`
				- once the for loop has finished, check the current directory for newly extracted file

## Cracking BitLocker-encrypted drives:

- ==`BitLocker`== is a full-disk encryption features developed by Microsoft for the Windows operating system.
- It uses ==`AES`== encryption algorithm with either ==`128-bit or 256-bit`== key length.
- If the password or PIN used for BitLocker is forgotten, decryption can be still performed by using a recovery key--a `48 digit string` generated.
- To crack a BitLocker encrypted drive we can use a script called ==`bitlocaker2john`== to [four different hashes](https://openwall.info/wiki/john/OpenCL-BitLocker): 
	- `$bitlocker$0$`
	- `$bitlocker$1$`
	- `$bitlocker$2$`
	- `$bitlocker$3$`
- The first two corresponding to the Bitlocker  password, while other two represents the recovery key.
	- Focus cracking the password using the first hash(`$bitlocker$0$....`).
	- Command:
		- `bitlocker2john -i Backup.vhd > backup.hashes`
		- `grep "bitlocker\$0" backuo.hashes > backup.hash`
		- `cat backup.hash`
		- `$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f"`
	
	- Once a hash is generated either `JtR` or `hashcat` can be used to crack it.
		- command:
			- `hashcat -a 0 -m <preferred_mode> <hash_file>/<hash_value> <wordlist_path>`

## Mounting BitLocker-encrypted drives in Windows:

- The easiest method for mounting as BitLocker-encrypted virtual drive on Windows is to double-click the `.vhd` file.
- Since it can be encrypted, Windows will initially show an error. 
- After mounting simply double-click the BitLocker volume to be prompted for the password. 

## Mounting BitLocker-encrypted drives in Linux(or macOS):

- It is also possible to mount BitLocker-encrypted drives in Linux(or macOS).
- To do this, use a tool called [dislocker](https://github.com/Aorimn/dislocker)
- Using apt:
	- `apt-get install dislocker`
	- `sudo mkdir -p /media/bitlocker`
	- `mkdir -p /media/bitlockermount`
	- Then use `losetup` to configure the VHD as loop device, decrypt the drive using dislocker
		- And finally mount the decrypted volume:
			- `losetup -f -P Backup.vhd`
			- `dislocker /dev/loop0p2 -u123qwer -- /media/bitlocker`
			
				- `-u` is the flag for assigning the use password.
			
			- `mount -o loop /media/bitlocker/dislocker-file /media/bitlockernount`
			
		- if everything was done correctly, browse the files:
			- `cd /media/bitlockermount/`
			- `ls -la`
		
		- Analyzed the files on the mounted drive, we can unmount it using following commands:
			- `unmount /media/bitlockermount`
			- `unmount /media/bitlocker`


[[Introduction to Password Attacks]]