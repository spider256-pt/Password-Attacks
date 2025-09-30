- Passwords are commonly hashed when stored
- Hashing is a mathematical function which transforms an arbitrary number of input into a fixed-size output.
	- ==`MD5`== and ==`SHA-256`== are the most commonly used hashed.
	- Command Example:
		- `echo -n <string/password> | <hashvalue>`
- Hash function are designed to work in ==`one-direction`== .
- When attacker attempt to do this it is called ==`password cracking`==
- Common technique are to use:
	- ==`rainbow tables`==
	- ==`dictionary attacks`==
	- ==`brute-force attack`==

- ## Rainbow tables:
	
	- *Rainbow table* are large pre-compiled maps of input and output values for a given hash function.
	- These can be used to very quickly identify the password if its corresponding hash has already been mapped.
	- Rainbow tables are such a powerful attack, ==`salting`== is used
		- ==`salt`==: A salt is used cryptographic terms, is a random sequence of bytes added to a password before it hashed.
		- To maximize impact, salts should not be reused.
		- For all password stored in one database
			- `echo -n <passcode> | <hashvalue>`
		- A salt is not a secret value.
		- salt consisting of just one single byte would mean the 15 billion entries before would have to be 3.84 trillion(factor 256).

| Password  | MD5 Hash                         |
| --------- | -------------------------------- |
| 123456    | e10adc3949ba59abbe56e057f20f883e |
| 12345     | 827ccb0eea8a706c4c34a16891f84e7b |
| 123456789 | 25f9e794323b453885f5181f1b624d0b |
| password  | 5f4dcc3b5aa765d61d8327deb882cf99 |

- ## Brute-force attack:
	
	- A brute force attack involves attempting every possible combination of:
		- `letters`
		- `numbers`
		- `symbols`
		This process continues until the correct password is discovered. 
	- Can take a long-time, especially for long password.
	- Brute-forcing is the only password cracking technique that is 100% effective.
	- ==`Brute-force speeds depends heavily on the hashing algorithm and hardware that is used.`==

- ## Dictionary attack:
	
	-  A Dictionary attack also known as a wordlist attack.
	- It is also a most efficient techniques for cracking passwords, especially when operating under time-constrains.
	- Rather than attempting possible combination of character, a list containing statistically likely password is used.
	- Famous wordlist that is used the most are `rockyou.txt` and `SecList`.
		- ==`rockyou.txt is a list of over 14 million real passwords that were leaked.`==