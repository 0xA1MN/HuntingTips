### Session Security

Most web applications offer a login page and need to keep track of the user session. The main goal is to avoid users from logging in each time they visit the web application.

Since HTTP is a stateless protocol, web applications use libraries, cookies, etc. to keep track of the user session.

The session identifier is the unique key that identifies a user session within a database of sessions. 

---

### Session Hijacking

Session Hijacking refers to the exploitation of a valid session assigned to a user. The attacker can get the victim’s session identifier using a few different methods, though typically an XSS is used. 

 if the session identifier is weakly generated the attacker might be able to bruteforce the session ID.

**Session Hijack attack can be happen by**

- XSS
- direct access to server filesystem
- packet sniffing
- Finding session IDs in logs or browser history (sessions carried through the URL)

**you can perform this attack when**

- website is vulnerable with XSS 

- cookie are readable by JS (doesn't contains HTTPONLY flag) : 

  craft payload stealing cookie if cookies accessible

- session id sent through cookies in each http request :

  This type of attack requires the attacker to be able to sniff the HTTP traffic of the victim; this is unlikely to happen for a remote attacker, but it is feasible on a local network if both the attacker and victim are present.

---

### Session Fixation

Session Fixation is an attack that permits an attacker to hijack a valid user session. The attack explores a limitation in the way the web application manages the session ID, more specifically the vulnerable web application. When authenticating a user, it doesn’t assign a new session ID, making it possible to use an existent session ID. The attack consists of obtaining a valid session ID (e.g. by connecting to the application), inducing a user to authenticate himself with that session ID, and then hijacking the user-validated session by the knowledge of the used session ID. The attacker has to provide a legitimate Web application session ID and try to make the victim’s browser use it.

