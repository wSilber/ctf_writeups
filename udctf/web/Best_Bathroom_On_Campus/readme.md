# Best Bathroom on Campus

### Text: https://best-bathroom-default-rtdb.firebaseio.com/flag/UDCTF.json

Author: Dr.Doctah

(Tried 'em all)

![bestbathroom.png](bestbathroom.png)

&nbsp;

Score: 100

## Writeup

In this challenge you are given a link to a public endpoint in a database. When you go to the endpoint you are given a json response of true. Looking at the image provided in the challenge description you can see that each character in the flag is an endpoint in the database with the value of true. All that needs to be done is to find the characters. I used a bruteforce solution that find the next character in the flag that returns a true response.


```python
import requests

base_url = 'https://best-bathroom-default-rtdb.firebaseio.com/flag/'
flag = ""

start = 0x20
end   = 0x7E

for j in range(0, 100):

    lenf = len(flag)

    for i in range(start, end):

        if i == 0x2F:
            continue

        c = chr(i)

        url = base_url + flag + c + '.json'

        try:
            resp = requests.get(url)
            resp = resp.json()

            if resp == True:
                flag += c
                print(flag)
                break

        except Exception:
            pass

    if len(flag) == lenf:
        print("ERROR: Did not find character")
        print(flag)
        break
```

Output
```
U
UD
UDC
UDCT
UDCTF
UDCTF{
UDCTF{1
UDCTF{1c
UDCTF{1ce
UDCTF{1ce_
UDCTF{1ce_L

...

UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4t
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4th
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr0
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr00
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr00m
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr00m}
ERROR: Did not find character
UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr00m}
```

## Flag: UDCTF{1ce_L4br4t0ry_s3C0nd_Fl0or_b0y's_b4thr00m}