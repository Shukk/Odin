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


## Golden Ticket

Compromising the ```krbtgt``` user hash to create Kerberos tickets as if you were Active Directory: issuing tickets for users that don’t exist, adding users to groups in which they don’t belong, or issuing tickets with lifetimes far beyond the configured maximum.

The Golden Ticket recipe contains 3 elements:
- Domain name
- Domain SID
- ```krbtgt``` user password hash

1) Compromise the ```krbtgt``` Password Hash (by using ```mimikatz.exe "lsadump::dcsync /user:DOMAIN\KRBTGT"``` for example)
2) Forge Kerberos tickets like TGT using the Golden Ticket (```krbtgt``` Password Hash) with tools like ```mimikatz``` or ```impacket``` (example: ```mimikatz.exe "kerberos::golden /domain:[DOMAIN] /sid:[SID] /aes256:[KRBTGT hash] /user:NonExistentUser /groups:513,2668 /ptt"```)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/how-golden-ticket-attack-works
