# Big JPG

### Text: There's more data to this image than what meets the eye.

-azroberts

&nbsp;

Score: 50

![big-image.jpg](big-image.jpg)

## Writeup

In this challenge you are given a login page. All you have to do is input an SQL injection on the username. I was able to achieve this by escaping the username and providing a union with 1='1. I also escaped the password and ended the query. From the hint provided the username is most likely rickjames.

Injection used
```
username=rickjames' OR 1='1&password=test' --
```

The flag is given when you successfully log in

## Flag: UDCTF{wh4ts_my_n4m3_4g4in?}