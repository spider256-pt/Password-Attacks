- The tendency for user to create weak passwords occurs even when password policies are placed.
- Most individual follow predictable patterns when creating password.
- Basic OSINT techniques can be highly effective in uncovering such personal information and may assist in guessing the password.
- To know more about the OSINT techniques use: 
	- [OSINT: Corporate Recon module](https://academy.hackthebox.com/course/preview/osint-corporate-recon)
- According to WordPress Engine most password are not longer than ten characters.
- Approach is to select familiar terms that are at least five characters long- such as:
	- pet names
	- personal preferences
	- or other common interests

## Custom Wordlist:

- ### Using Hashcat:

	- Hashcat can be used to combine lists of potential names and labels with specific mutation rules to create custom wordlist.
	- Hashcat uses a specific syntax to define:
		- character
		- words
		- and their transformation
 

| Function | Description                                      |
| -------- | ------------------------------------------------ |
| `:`      | Do nothing                                       |
| `l`      | lowercase all letters                            |
| `u`      | Uppercase all letters                            |
| `c`      | Capitalize the first letter and lowercase others |
| `sXY`    | replace all instances X with Y                   |
| `$!`     | Add the exclamation character at the end         |

- Each rule is written on a new line and determine how a given word should be transformed.
	- `vim custom.rule`
	- ```
	  :
	  c
	  so0
	  c so0
	  sa@
	  c sa@
	  c sa@ so0
	  $!
	  $! c 
	  $! so0
	  $! sa@
	  $! c so0
	  $! c sa@
	  $! so0 sa@
	  $! c so0 sa@
	  ```

	- can use the command to apply rules in the `custome.rule` to  each word in `<file_name>.list` and store the mutated results in `<new_file_name>.list`
	
		- `hashcat --force <file_name>.list -r custom.rule --stdout | sort -u > <new_file_name>.list`

- ### Using CeWL:
	
	- `CeWL` is a tool to scan a potential words from a company's website and save them in a separate list.
	- The user can combine the user list with the desired rules to create customized password list.
	- Can specify parameters:
		- `-d` can be used to apply the depth 
		- `-m` it defines the minimum length of password
		- `--lowercase` the storage of the found words in lowercase.
		- command:
			- `cewl <webite_name> -d <specifed_num> -m <specified_num> --lowercase -w <file_name>.wordlist`
			- `wc -l <file_name>.wordlist`
