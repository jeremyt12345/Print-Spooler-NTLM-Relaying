# Print-Spooler-NTLM-Relaying
What it is: The Print Spooler service has a bug (PrinterBug) where any domain user can force a remote machine to authenticate back to any machine they control. That reverse connection carries authentication credentials — specifically a TGT



Why it matters:
Microsoft said it won't be fixed because it's "by design." It's been around since 2018 and is still present on default Windows installations.


The attack in this lab:

1.Start NTLMRelayx on Kali listening for incoming connections and configured to forward them to DC2 for DCSync

2.Use Dementor to trigger DC1's Print Spooler to connect back to the Kali machine

3.NTLMRelayx intercepts that connection and relays it to DC2 impersonating DC1

4.DC2 thinks DC1 is requesting replication and hands over all the password hashes


Detection:
Event ID 4624 (successful logon) for DC1$ but originating from the Kali machine's IP instead of DC1's actual IP — that mismatch is the red flag. No Event ID 4662 is generated unlike a standard DCSync.


Prevention:
Disable Print Spooler on all non-print servers, especially Domain Controllers. Alternatively disable the RegisterSpoolerRemoteRpcEndPoint registry key to block remote connections while keeping the service running locally.
