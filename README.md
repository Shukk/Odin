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

Compromising the ```krbtgt``` user hash to create Kerberos tickets as if you were Active Directory.

1) 

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/how-golden-ticket-attack-works
