# Print-Spooler-NTLM-Relaying
What it is: The Print Spooler service has a bug (PrinterBug) where any domain user can force a remote machine to authenticate back to any machine they control. That reverse connection carries authentication credentials — specifically a TGT



Why it matters:
Microsoft said it won't be fixed because it's "by design." It's been around since 2018 and is still present on default Windows installations.


The attack in this lab:

1.Start NTLMRelayx on Kali listening for incoming connections and configured to forward them to DC2 for DCSync

<img width="1067" height="690" alt="image" src="https://github.com/user-attachments/assets/62013b21-2d0b-479c-ae46-d25baa8734e5" />

2.Use Dementor to trigger DC1's Print Spooler to connect back to the Kali machine

<img width="1012" height="184" alt="image" src="https://github.com/user-attachments/assets/05dc4958-e23c-4e4f-ab67-c00e2ecec101" />


3.NTLMRelayx intercepts that connection and relays it to DC2 impersonating DC1

4.DC2 thinks DC1 is requesting replication and hands over all the password hashes

<img width="1115" height="511" alt="image" src="https://github.com/user-attachments/assets/c2b9c3c3-7a6c-40a5-9737-074ed74edbcc" />

After the attack HTB prompts you to take care of preventatiive methods by changing the registry keys and re trying the attack to see the response

<img width="1342" height="312" alt="image" src="https://github.com/user-attachments/assets/de3d8460-75c1-4fc6-a170-194723cfa1a1" />


Prevention:
Disable Print Spooler on all non-print servers, especially Domain Controllers. Alternatively disable the RegisterSpoolerRemoteRpcEndPoint registry key to block remote connections while keeping the service running locally.

<img width="1074" height="241" alt="image" src="https://github.com/user-attachments/assets/3c69331e-b42b-4b86-8356-5ea73ceea4df" />


After disabling we then try the attack again to see the error 




Detection:
Event ID 4624 (successful logon) for DC1$ but originating from the Kali machine's IP instead of DC1's actual IP — that mismatch is the red flag. No Event ID 4662 is generated unlike a standard DCSync.

