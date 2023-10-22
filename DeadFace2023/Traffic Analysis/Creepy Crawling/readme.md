# Creepy Crawling

### Text: One of our clients, TGRI, had an SSH server compromised at one of their smaller remote locations. Their only security analyst was fired and “accidentally” deleted information specific to the attack. Thankfully, TGRI still has the PCAP that captured the SSH brute force attack. What SSH protocol did TGRI run that was eventually compromised by DEADFACE?

Submit the SSH protocol as the flag: flag{SSH-1.1.1: Simple SSH Server}

### Score: 75

Included Files: [PCAP02.pcpng](PCAP02.pcpng)

## Writeup
We are given another pcap file with information about the attack that occured to TGRI. We know that we are looking for an attack that exploited the SSH protocol so lets filter the packets by the SSH protocol. We are looking for the specific SSH protocol that was exploited. Looking at the first SSH packet on line 5050 we can see the specific type of protocol used on the server.

```
SSH-2.0-9.29 FlowSsh: Bitvise SSH Server (WinSSHD) 9.29
```

## Flag: flag{SSH-2.0-9.29: Bitvise SSH Server}