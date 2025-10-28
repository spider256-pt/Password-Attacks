- Most applications wisely use `TLS` to encryption to encrypt sensitive data in transit. Not all environments are fully secured.
	- Legacy systems
	- Misconfigured services
	- test applications launched without HTTPS can still results in the use of encrypted protocols such as HHTP or SNMP.
- There are so many tool that can be used to hunt credentials like:
	- Wireshark
	- Pcredz
	- Bettercap, etc.

## Wireshark:

- Wireshark is a well-known packets analyzer that is used in all penetration testing Linux distributions. 
- It features a powerful `filter engine` that allows for efficient searching through both live and captured network traffic.
- Some basic but useful filters include: 


| `Wireshrek filter`                                       | Description                                                                                          |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| ip.addr == <ip_address>                                  | filters packets with a specific ip address                                                           |
| tcp.port==80                                             | Filters packets by port(HTTP in this case )                                                          |
| https                                                    | Filters HTTPS traffic                                                                                |
| dns                                                      | Filters DNS traffic                                                                                  |
| tcp.flags.syn == 1 && tcp.flags.ack == 0                 | Filters SYN packets(used in TCP handshakes)                                                          |
| icmp                                                     | filters ICMP                                                                                         |
| http.request.method == "POST"                            | Filters for HTTP POST requests                                                                       |
| tcp.stream eq 53                                         | filters for a specific TCP stream                                                                    |
| eth.addr == <eth0_address>                               | Filters for a specific MAC address                                                                   |
| ip.src == <source_ip_add> && ip.dst == <destined_ip_add> | Filters traffic between two specific IP addresses. Helps track communication between specific hosts. |

## Pcredz:

- ==`Pcredz`== is a tool that can be used to extract credential from the live traffic or network packet captures.
- It supports extracting the following information:
	- Credit card numbers
	- POP credentials 
	- IMAP credentials 
	- SNMP community strings 
	- FTP credentials 
	- Credentials from HTTP NTLM/Basic headers, as well as HTTP Forms
	- NTLMv1/v2 hashes from various traffic including DCE-RPC, SMBv1/2, LDAP, MSSQL, and HTTP
	- Kerberos (AS-REQ Pre-Auth etype 23) hashes
- In order to run ==`Pcredz`==:
	- `./Pcredz -f demo.pcapng -t -v`


[[Introduction to Password Attacks]]