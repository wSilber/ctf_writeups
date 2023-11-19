# SQL 4 Dummies

### Text: Break into ole Professor James' school account. I think his username is rickjames.

https://bluehens-sql-for-dummies-service.chals.io/

-Dave the Daemonslayer

&nbsp;

Score: 150

## Writeup

In this challenge you are given a login page. All you have to do is input an SQL injection on the username. I was able to achieve this by escaping the username and providing a union with 1='1. I also escaped the password and ended the query. From the hint provided the username is most likely rickjames.

Injection used
```
username=rickjames' OR 1='1&password=test' --
```

The flag is given when you successfully log in

## Flag: UDCTF{wh4ts_my_n4m3_4g4in?}