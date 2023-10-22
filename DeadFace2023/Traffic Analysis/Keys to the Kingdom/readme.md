# Keys to the Kingdom

### Text: An image was sent across De Monne Financial’s network. They’re still paranoid after being the primary target of DEADFACE last year, and they suspect this is DEADFACE activity. We’re not sure what to make of the PCAP they sent us, but we found a file on the anomalous De Monne machine named keytothekingdom.txt. We suspect that contents of the file have undergone some form of encoding or encryption. See if you can piece these two things together to find the flag in the pcap.

Submit the answer as flag{here-is-the-answer}.

Key ciphertext: 
ÓÄÖëÄøõàñããàøâñãõùãªÞåýåøåûåýñûùñûùñùñüåþñýÿâí

### Score: 100

Included Files: [PCAP02.pcpng](PCAP02.pcpng)

## Writeup
This packet capture is kinda weird. You can see that it contains only TCP SYN packets and no response. One interesting note is that although the packets are SYN packets, they also contain a payload. Looking at the payload of the first packet we can see that the first wo bytes are 

```
ff d8
```

This is the start of a JPEG image header. Maybe this packet contains an image. Lets display these bytes as an image.

![firstpacket.jpeg](first_packet_image.jpeg)

As you can see this does contain an image, but one thing you might have noticed from the large gap above this is only part of the image. Lets capture all of the packets are display it as an image. One useful command to capture all of the TCP payloads is by using this tshark command.

```
tshark -i 1 -r Thekeytothekingdom.pcap -T fields -e tcp.payload
```

Part of the output
```
ffd8ffe000104a46494600010100000100010000ffdb0043000505050505050506060508080708080b0a09090a0b110c0d0c0d0c111a1013101013101a171b1615161b1729201c1c20292f2725272f393333394744475d5d7dffdb0043010505050505050506060508080708080b0a09090a0b110c0d0c0d0c111a1013101013101a171b1615161b1729201c1c20292f2725272f393333394744475d5d7dffc000110803ab02bc03012100021101031101ffc4001f0000010501010101010100000000000000000102030405060708090a0bffc400b5100002010303020403050504040000017d01020300041105122131410613516107227114328191a1082342b1c11552d1f02433627282090a161718191a25262728292a...
```

This prints out all of the captured TCP payloads as hex. I then concatinated all of the payloads into a single output and displayed it as an image.

![full_image](image.jpeg)

As you can see we have now retreived the entire image. But what do we do with this image? In the challenge description we can see that although this is a traffic analysis question it is also a steg problem. One common way to hide information in images is to embed text in the image using programs like openstego and steghide. We were also given a ciphertext key. Looking at the key all of the characters are closely related extended ASCII codes. This means that the code may be ASCII characters that have been modified with adding or xoring by a certain value.

I decided to build a python program to see if this is the case.

Python script
```python
ct = 'ÓÄÖëÄøõàñããàøâñãõùãªÞåýåøåûåýñûùñûùñùñüåþñýÿâí'

for i in range(1, 0xff):
    pth = ""
    pta = ""
    for c in ct:
        pth += chr(ord(c) ^ i)
        try:
            pta += chr(ord(c) - i)
        except Exception as e:
          pass

    print(str(hex(i)) + ": " + pth)
    print(str(hex(i)) + ": " + pta)
```

Part of output
```
0x8f: \KYdKwzo~llowm~lzvl%Qjrjwjtjr~tv~tv~v~sjq~rpmb
0x8f: D5G\5ifQbTTQiSbTfjTVnViVlVnbljbljbjbmVobnpS^
0x90: CTF{Thepassphraseis:Numuhukumakiakiaialunamor}
0x90: C4F[4hePaSSPhRaSeiSNUmUhUkUmakiakiaialUnamoR]
0x91: BUGzUidq`rrqis`rdhr;Otltitjtl`jh`jh`h`mto`lns|
0x91: B3EZ3gdO`RROgQ`RdhRMTlTgTjTl`jh`jh`h`kTm`lnQ\
```
Passphrase

```
0x90: CTF{Thepassphraseis:Numuhukumakiakiaialunamor}
```

Now that we have a passphrase we can try to decode a message hidden in the image. I was able to achieve this using steghide.

```
┌──(wsilberstein㉿wsilber)-[/mnt/c/Users/silbe/Documents/ctfs/deadface/keys]
└─$ steghide --extract -sf image.jpeg -p Numuhukumakiakiaialunamor
wrote extracted data to "flag.txt".
```

We can see that data was extracted from the image file and placed in a file flag.txt. This file contains the flag.

## Flag: flag{Error404FlagNotFound}