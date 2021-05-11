### **Authentication and Authorization what is difference ?**

**Authentication** is the process of verifying who you are.
**Authorization** is what you are able to do; authorization attacks have to do with accessing information that the user does not have permission to access.

### **authentication factor representation**

An authentication factor represents something the user owns, knows, or is. We can distinguish the three different types of authentication factors:

**Ownership factors** some thing user **has** like security token, phone number, bank online token

**Knowledge factors** some thing user **know** like password, pin, secret question

**Inherence factors** some thing user **is** like fingerprint, signature, face

### **single-factor vs two factor**

**The single-factor authentication method** requires a user to provide one of these three authentication factors .

**The two-factor (or multi-factor authentication - MFA) authentication system** requires a user to provide two or more of the three authentication factors.



### **Common Vulnerabilities**

**1- Vulnerabilities in password-based login**

​	in this case we brute force on username and password using burpsuite *"intruder"*

**While attempting to brute-force a login page, you should pay particular attention to any differences in:**

- **Status codes:** During a brute-force attack, the returned HTTP status code is likely to be the same for the vast majority of guesses because most of them will be wrong. If a guess returns a different status code, this is a strong indication that the username was correct. It is best practice for websites to always return the same status code regardless of the outcome, but this practice is not always followed.

  example:

  on successful login response my be 302 *"redirecting"*

- **Error messages:** Sometimes the returned error message is different depending on whether both the username AND password are incorrect or only the password was incorrect. It is best practice for websites to use identical, generic messages in both cases, but small typing errors sometimes creep in. Just one character out of place makes the two messages distinct, even in cases where the character is not visible on the rendered page.

  example:

   *wrong username and password* when both are incorrect

  on correct username  *wrong password* 

- **Response times:** If most of the requests were handled with a similar response time, any that deviate from this suggest that something different was happening behind the scenes. This is another indication that the guessed username might be correct. For example, a website might only check whether the password is correct if the username is valid. This extra step might cause a slight increase in the response time. This may be subtle, but an attacker can make this delay more obvious by entering an excessively long password that the website takes noticeably longer to handle.

  example:

  use very long password and brute force username on username is correct server takes long time to validate password 

  

**HINT** : some website block ip temporary when run brute force attack we can bypass that using ` X-Forwarded-For : [nubmer]`  check [this](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For) for more



**Dealing with IP blocking while enter wrong  credentials**

It is highly likely that a brute-force attack will involve many failed guesses before the attacker successfully compromises an account. Logically, brute-force protection revolves around trying to make it as tricky as possible to automate the process and slow down the rate at which an attacker can attempt logins. The two most common ways of preventing brute-force attacks are:

Locking the account that the remote user is trying to access if they make too many failed login attempts
Blocking the remote user's IP address if they make too many login attempts in quick succession
Both approaches offer varying degrees of protection, but neither is invulnerable, especially if implemented using flawed logic.

so we can broke this protection by login successfully once after every brute force try hence word list be like

Your credentials: ayman : ayman_pass
Victim's username: ahmed

| username |  password  |
| :------: | :--------: |
|  ayman   | ayman_pass |
|  ahmed   |   123123   |
|  ayman   | ayman_pass |
|  ahmed   |  password  |
|  ayman   | ayman_pass |
|  ahmed   |   ShaDow   |
|  ayman   | ayman_pass |
|  ahmed   |  P@$$P@$$  |
|  ayman   | ayman_pass |
|   ...    |    ...     |



**Account locking**

in this case account locked after number of incorrect login ... so we can abuse login to catch a username 

- **how to perform :** 

  using cluster bomb attack set username payload normally 

  in password set some thing like that `anytext§§` 

  choose payloads as null repeated for number of tries before account locked ... start attack

  watch length column you will see length different from others this error message say that this account is locked *this is correct username* ... now you have username lets guess password

  in this step we will use sniper attack set payload in password field ... start attack

  grep error message ... you will notice that there is payload with out this error message ... this is password

  wait for awhile till website unlock login and login "now u IN"



**User rate limiting**

in this case IP blocked after number of incorrect login tries 

the IP can only be unblocked in one of the following ways:

- Automatically after a certain period of time has elapsed
- Manually by an administrator
- Manually by the user after successfully completing a CAPTCHA

As the limit is based on the rate of HTTP requests sent from the user's IP address, it is sometimes also possible to bypass this defense if you can work out how to guess multiple passwords with a single request.

so lets check the post request 

if parameters represented in json we can replace password value from

{"username":"ayman","password":"P@$$"}

To

{"username":"ayman","password":[list_of_passwords_to_brute_force_in_json_format]}

------

**2 - Vulnerabilities in multi-factor authentication**

**IF the user is effectively in a "logged in" state before they have entered the verification code**

If the user is first prompted to enter a password, and then prompted to enter a verification code on a separate page, the user is effectively in a "logged in" state before they have entered the verification code. In this case, it is worth testing to see if you can directly skip to "logged-in only" pages after completing the first authentication step. Occasionally, you will find that a website doesn't actually check whether or not you completed the second step before loading the page.

*so try to modify the URL to check if this vulnerability works*

------

**Flawed two-factor verification logic** 

Sometimes flawed logic in two-factor authentication means that after a user has completed the initial login step, the website doesn't adequately verify that the same user is completing the second step.

for example :

*first request*

on login app generate two-factor verification by trigger specific function in back-end depend on user cookie 

*second request*

lets say `virfy=ayman` this cookie with generate code for ayman when user enter it user login successfully   

*So we can trigger first request to generate code and brute force  second one like this*

replace ayman with another user in first request

brute force code in second request 

show response in browser >> logged in as another user

------

**Brute-forcing 2FA verification codes**

some apps sign you out after specific number of incorrect verification codes so we can use burp macro to automate login process 

In Burp, go to "Project options" > "Sessions". In the "Session Handling Rules" panel, click "Add". The "Session handling rule editor" dialog opens. In the dialog, go to the "Scope" tab. 

Under "URL Scope", select the option "Include all URLs". 

Go back to the "Details" tab and under "Rule Actions", click "Add" > "Run a macro". 

Under "Select macro" click "Add" to open the "Macro Recorder"

Click "Test macro" and check that the final response contains the page asking you to provide the 4-digit security code. This confirms that the macro is working correctly. 

then send verification code request to intruder and watch status code

It's 302 now you IN 



**3- Vulnerabilities in other authentication mechanisms**

**Keeping users logged in**

Some websites assume that if the cookie is encrypted in some way it will not be guessable even if it does use static values. While this may be true if done correctly, naively "encrypting" the cookie using a simple two-way encoding like Base64 offers no protection whatsoever. Even using proper encryption with a one-way hash function is not completely bulletproof. If the attacker is able to easily identify the hashing algorithm, and no salt is used, they can potentially brute-force the cookie by simply hashing their wordlists. This method can be used to bypass login attempt limits if a similar limit isn't applied to cookie guesses.

if you can identify how this cookie made you can brute force it

Even if the attacker is not able to create their own account, they may still be able to exploit this vulnerability. Using the usual techniques, such as XSS, an attacker could steal another user's "remember me" cookie and deduce how the cookie is constructed from that. If the website was built using an open-source framework, the key details of the cookie construction may even be publicly documented.

In some rare cases, it may be possible to obtain a user's actual password in cleartext from a cookie, even if it is hashed. Hashed versions of well-known password lists are available online, so if the user's password appears in one of these lists, decrypting the hash can occasionally be as trivial as just pasting the hash into a search engine. This demonstrates the importance of salt in effective encryption.

------

**Resetting user passwords**

we can abuse this function depend on type of reset

- **Sending passwords by email** this way not secure is data sent over unsecured channel Man In The Middle Attack "MIMA".

- **Resetting passwords using a URL** this method is more robust to reset password send a unique URL to users that takes them to a password reset page. Less secure implementations of this method use a URL with an easily guessable parameter to identify which account is being reset

  http://vulnerable-website.com/reset-password?user=victim-user

  In this example, an attacker could change the user parameter to refer to any username they have identified. They would then be taken straight to a page where they can potentially set a new password for this arbitrary user.

  A better implementation of this process is to generate a high-entropy, hard-to-guess token and create the reset URL based on that. In the best case scenario, this URL should provide no hints about which user's password is being reset.

  http://vulnerable-website.com/reset-password?token=a0ba0d1cb3b63d13822572fcff1a241895d893f659164d4cc550b421ebdd48a8

  When the user visits this URL, the system should check whether this token exists on the back-end and, if so, which user's password it is supposed to reset. This token should expire after a short period of time and be destroyed immediately after the password has been reset.

  However, *some websites fail to also validate the token again when the reset form is submitted.* In this case, an attacker could simply :
  
  visit the reset form from their own account
  
  delete the token
  
  and leverage this page to reset an arbitrary user's password.
  
  
  
  If the URL in the reset email is generated dynamically, this may also be vulnerable to password reset poisoning. In this case, an attacker can potentially steal another user's token and use it change their password.
  
  we well use `x-forwarded-host` to sent generated token to it ... review server log
  
  get token for target user
  
  replace token in original link to reset 
  
  ------
  
  **Changing user passwords**
  
  Typically, changing your password involves entering your current password and then the new password twice. These pages fundamentally rely on the same process for checking that usernames and current passwords match as a normal login page does. Therefore, these pages can be vulnerable to the same techniques.
  
  Password change functionality can be particularly dangerous if it allows an attacker to access it directly without being logged in as the victim user. For example, if the username is provided in a hidden field, an attacker might be able to edit this value in the request to target arbitrary users. This can potentially be exploited to enumerate usernames and brute-force passwords.