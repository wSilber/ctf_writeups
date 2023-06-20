# The Data

### Text: There is pcapng file. Can you, again, reveal the flag ? 

&nbsp;

Score: 10

Link: https://github.com/SecurityValley/PublicCTFChallenges/tree/master/network/the_data

---
 ## Writeup

In this challenge you are given a pcap file that contains a single HTTP request. One IP address requests a file called **flag.png** while the other IP address reponds with a **200 OK** response with a PNG file. Using the *Object Extraction* tool in *Wireshark* I was able to extract the iamge from the HTTP request. After downloading the file and viewing it, the flag could clearly be seen in the image.

## Flag: SecVal{g1mp_g1mp_g1mp}