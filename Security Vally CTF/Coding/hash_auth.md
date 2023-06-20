# Hash Auth

### Text: Ok ok ok. They have learned there lesson. Instead of using confusing authentication functions there have switched to crypto functions. At least they think they have switched to crypto functions. Anyway - please help us again and hack this piece of code! 

&nbsp;

Score: 15

Link: https://github.com/SecurityValley/PublicCTFChallenges/tree/master/coding/hash_auth

---
 ## Writeup

 This challenge builds upon the previous challenges **Easy Authentication** and **Good Authentication** by using action encryption methods. In this case, the python script uses the *validate_password* function to encrypt the password before sending the post request to Security Vally.

 ```python
def validate_password(password):
    # be creative. it has something to do with SecurityValley ;-)
    if sha256(password.encode("utf-8")).hexdigest() == "f51f333ed26c41bedd99e1e483c0a15d2caeed7dc5a9ae02159f196799a74893":
        return True 

    return False
 ```

Looking at this validate function, it seems that we must imput a password that matches the hardcoded sha256 hash. Since this is the only part in the *validate_password* function, it is clear that we must somehow crack this password. This can be done using *Hashcat* or *John*. 

I first attempted to crack this password using *Hashcat*, but quickly found that none of the worklists I found contained the password. I then went on to try words that had something to do with Security Valley itself such as *SecurityValley* or *CTF*, using the hint given as a clue. Knowing that pervious challenges (as well as many other CTFs) like to replace characters with similar looking numbers, I attempted to create my own wordlist using numbers as substitutions. I created the following script to create my wordlist.

 ```python
def validate_password(password):
words = ["securityvalley", "ctf", "security", "cybersecurity", "1337", "capturetheflag", "hashauth"]

subs = {"a": "@", "b": "8", "e": "3", "f": "7", "i": "1", "l": "1", "o": '0', "p": '9', "t": "7"}

wordlist = []

def replace_char(word, index):
    if index == len(word):
        wordlist.append(word)
        return

    replace_char(word, index + 1)
    replace_char(word.replace(word[index], word[index].upper()), index + 1)

    if word[index] in subs.keys():
        print(word[index])
        word = word.replace(word[index], subs[word[index]])
        replace_char(word, index + 1)

for word in words:

    replace_char(word, 0)

for word in wordlist:
    print(word)

 ```

 Using this wordlist, I was able to crack the hash using *Hashcat*.
 ```
$ python combination.py > wordlist.txt
$ hashcat -a 0 -m 1400 f51f333ed26c41bedd99e1e483c0a15d2caeed7dc5a9ae02159f196799a74893 wordlist.txt  
$ hashcat -a 0 -m 1400 f51f333ed26c41bedd99e1e483c0a15d2caeed7dc5a9ae02159f196799a74893 wordlist.txt --show
f51f333ed26c41bedd99e1e483c0a15d2caeed7dc5a9ae02159f196799a74893:Secur1tyValley
 ```

After getting the password, Sending this password as the value of the *pass* parameter in the post request and you will receive the flag as the response.
 ```
$ curl -X POST http://ctf.securityvalley.org:7777/api/v1/validate -H 'Content-Type: application/json' -d '{"pass": "Secur1tyValley"}' 
{"Value":"SecVal{To_51MPl3_foR_You}"}
 ```

 ## Flag: SecVal{To_51MPl3_foR_You}