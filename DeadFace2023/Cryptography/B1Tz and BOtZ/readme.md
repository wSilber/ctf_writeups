# B1Tz and BOtZ

### Text: Yet another message was left at the scene. Perhaps they think they are giving us a lesson…either way report back to us what this says but dont give us guesses! Make sure you check your work!

Submit Flag as flag{hiddenmessage}

### Score: 25

Included Files: [Download File](bits.txt)

## Writeup
In this challenge we are given another encoded message. This time the message appears to be in binary. After converting from binary to ascii we are left with this message.

```
dont forget the basics! but you diddnt think it would be that easy did you? HAHAHAHAHA Silly Turbos! More Like Turbo TACKY!!!! Go ahead and ROT 
73 79 6E 74 7B 73 79 76 63 76 67 6E 61 71 65 72 69 72 65 66 72 76 67 7D
```

The characters on the bottom appear to be hex encoded characters so lets decode them.

```
synt{syvcvgnaqerirefrvg}
```

The resulting text is definitely a caesar cipher. It follows the same format as the flag. After rotating the characters by 13 we are left with the resulting message.

```
flag{flipitandreverseit}
```

## Flag: flag{flipitandreverseit}