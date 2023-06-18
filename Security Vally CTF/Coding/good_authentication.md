# Good Authentication

### Text: After your first strike, the development team has increased the power of there login function. Are you strong enough to break it again? 

&nbsp;

Score: 10

Link: https://github.com/SecurityValley/PublicCTFChallenges/tree/master/coding/good_authentication

---
 ## Writeup

 In this challenge you are given a javascript file similar to the one in **Easy Authentication**. The *Banner* function sends a post request to Security Vally with a password as data and claims it will send back the flag if the password correct.

 ```javascript
function banner(payload) {
    console.info("that was great !!!");
    console.info("run the following command to get the flag.")
    console.info(`curl -X POST http://ctf.securityvalley.org:7777/api/v1/validate -H 'Content-Type: application/json' -d '{"pass": "${payload}"}'`)
}
```

In the *validate* function the password is checked to have a length of 12 characters, and then sliced into 3 blocks of 4 characters.

 ```javascript
    if (password.length != 12) {
        throw new Error("pass violation. wrong password length");
    }


    const block1 = Array.from(password).slice(0, 4)
    const block2 = Array.from(password).slice(4, 8)
    const block3 = Array.from(password).slice(8, 12)

    const block = [
        block1,
        block2,
        block3
    ]
```

Each character in the block will then be converted to its ASCII numerical value and XORed with a specific integer depending on which block it is contained in.   

1. 7
2. 11
9. 9

Each resulting integer is converted back to an ASCII character and appended to the end of a resulting string. The entire process is shown below.

```javascript
    for (let i = 0; i < block.length; i++) {
        for (let a = 0; a < block[i].length; a++) {
            if (i == 0) {
                crafted += String.fromCharCode(String(block[i][a]).charCodeAt(0) ^ 7)
            } else if (i == 1) {
                crafted += String.fromCharCode(String(block[i][a]).charCodeAt(0) ^ 11)
            } else {
                crafted += String.fromCharCode(String(block[i][a]).charCodeAt(0) ^ 9)
            }
        }
    }
```

After the entire string is constructed, it is compared to the hardcoded string **sontTbxTjffe**. If the constructed strings matches *banner* will be called with the *original* password given. By reversing the hardcoded string using the process above we can find the password needed to pass this check. For example the first character will be converted like so,

s -> 73

73 ^ 7 = 74

74 -> t

Converting all the characters results in the string **this_is_cool**

Send this password as the value of the *pass* parameter in the post request and you will receive the flag as the response.
```
$ curl -X POST http://ctf.securityvalley.org:7777/api/v1/validate -H 'Content-Type: application/json' -d '{"pass": "this_is_cool"}'   
{"Value":"SecVal{9R34t_y0u_X0R3d}"}
```

## Flag: SecVal{9R34t_y0u_X0R3d}