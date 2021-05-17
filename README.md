# Odin Project - Cyberattacks in a nutshell

## Pass the Hash

Abusing the challenge-and-response nature of NTLM to authenticate as a user with only the NTLM hash of the user’s password.

1) Steal password hashes (most common way to do that is extracting hashes from lsass.exe but you need to have administrative privileges on the computer)
2) Use the pass-the-hash technique to authenticate as the compromised user (by using ```mimikatz sekurlsa::pth``` for example)

More information, demonstration and ways to detect, mitigate and respond: https://attack.stealthbits.com/pass-the-hash-attack-explained
