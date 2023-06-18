# Easy Authentication

### Text: Let's start simple in this game. We have collected a piece of javascript. There is a validate function but we don't know the password... can you hack it? 

&nbsp;

Score: 5

Link: https://github.com/SecurityValley/PublicCTFChallenges/blob/master/coding/easy_authentication

---
 ## Writeup

 In this challenge you are just given a javascript file. The first function that I looked at is the banner function

 ```javascript
 function banner(payload) {
    console.info("that was great !!!");
    console.info("run the following command to get the flag.")
    console.info(`curl -X POST http://ctf.securityvalley.org:7777/api/v1/validate -H 'Content-Type: application/json' -d '{"pass": "${payload}"}'`)
}
```

this function sends a post request with a password to Security Valley and claims it will send back the flag if the password is correct. Looking at the validate function which calls the banner function, we are given a hardcoded password array.

 ```javascript
function validate(password) {
    const pass = [106,117,115,116,95,119,97,114,109,105,110,103,95,117,112];
    const pa = Array.from(password);

    for(let i = 0; i < pa.length; i++) {
        if(pa[i].charCodeAt(0) !== pass[i]) {
            throw new Error("pass violation. wrong credentials");
        }
    }

    banner(password);
}
```

The function converts the password given to an array of characters, and compares their ASCII values to the numbers contained in the pass array. By converting the pass array back to ASCII characters you can construct the password. **just_warming_up**. Send this password as the value of the *pass* parameter in the post request and you will receive the flag as the response.
```
$ curl -X POST http://ctf.securityvalley.org:7777/api/v1/validate -H 'Content-Type: application/json' -d '{"pass": "just_warming_up"}' 
{"Value":"SecVal{J4v45Cr1P7_15_57r4444N93}"}
```

## Flag: SecVal{J4v45Cr1P7_15_57r4444N93}