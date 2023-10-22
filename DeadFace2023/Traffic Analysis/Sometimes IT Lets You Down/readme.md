# Sometimes IT Lets You Down

### Text: Lytton Labs has been having a slew of weird activity in the network lately. This recent PCAP capture we know contains a user account who compromised our domain controller. Can you figure out what user account was compromised?

Submit the flag as: flag{username}.

### Score: 25

Included Files: [ITletsyoudown.zip](ITletsyoudown.zip)

## Writeup
In this challenge we are given a pcap file that contains network logs for Lytton Labs. Like most pcap files most of the information is not useful and a filter should be used to analyze the network traffic. Since we are looking for a user who compromized the domain controller the best protocol that this file contains which can edit remote paths is the SMB protocol. I started by filtering for the SMB protocol in Wireshark. In the beginning there is a request to spook.domain without a user which resulted in a logon failure message. In packet number 8376 we can see that there is a successful logon to user *mmeyers* on the spooky.domain.

## Flag: flag{mmeyers}