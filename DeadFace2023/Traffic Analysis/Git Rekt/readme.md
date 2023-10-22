# Git Rekt

### Text: One of our teammates at Turbo Tactical ran a phishing campaign on spookyboi and thinks spookyboi may have submitted credentials. We need you to take a look at the PCAP and see if you can find the credentials.

Submit the password as the flag: flag{password}.

### Score: 50

Included Files: [gitrekt.zip](gitrekt.zip)

## Writeup
We are given another pcap file in this challenge. Since the name of this challenge contains Git, I know that we are most likely looking for the protocols ssh or http, since those are the two protocols that can be used to clone repos. I started with analyzing the http traffic. After filtering by http traffic I noticed that a form was sent on packet 3240. This form contains a logon username of spookyboi's email as well as a password *SpectralSecrets#2023*

## Flag: flag{SpectralSecrets#2023}