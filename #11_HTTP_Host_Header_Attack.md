### HTTP Host header attacks

**How to test for vulnerabilities using the HTTP Host header**

- Supply an arbitrary Host header

- Check for flawed validation

  ```http
  GET /example HTTP/1.1
  Host: vulnerable-website.com:bad-stuff-here
  ```

  ```http
  GET /example HTTP/1.1
  Host: notvulnerable-website.com
  ```

  ```http
  GET /example HTTP/1.1
  Host: hacked-subdomain.vulnerable-website.com
  ```

- Send ambiguous requests

  - Inject duplicate Host headers

    ```http
    GET /example HTTP/1.1
    Host: vulnerable-website.com
    Host: bad-stuff-here
    ```

  - Supply an absolute URL

    ```http
    GET https://vulnerable-website.com/ HTTP/1.1
    Host: bad-stuff-here
    ```

  - Add line wrapping

    ```HTTP
    GET /example HTTP/1.1
     Host: bad-stuff-here
    Host: vulnerable-website.com
    ```

- Inject host override headers

  You can sometimes use X-Forwarded-Host to inject your malicious input while circumventing any validation on the Host header itself.

  ```http
  GET /example HTTP/1.1
  Host: vulnerable-website.com
  X-Forwarded-Host: bad-stuff-here
  ```

  Although X-Forwarded-Host is the de facto standard for this behavior, you may come across other headers that serve a similar purpose, including:

  ```http
  X-Host
  X-Forwarded-Server
  X-HTTP-Host-Override
  Forwarded
  ```



-----------------------

### How to exploit the HTTP Host header

- **Web cache poisoning via the Host header**

  in this case try to second host header if input reflected in response try to craft xss payload

- **Exploiting classic server-side vulnerabilities**

  Every HTTP header is a potential vector for exploiting classic server-side vulnerabilities, and the Host header is no exception. "try SQLI"

- **Accessing restricted functionality**

  `robots.txt` may display end points that can't be accessed by normal user 

  may be only local user can access it try to alter host to `localhost` and request this page

- **Accessing internal websites with virtual host brute-forcing**

  www.example.com: 12.34.56.78
  intranet.example.com: 10.0.0.132

  alter host value to :

  localhost

  dev

  stage

  test

- **Routing-based SSRF**

  - change host to site u have *Burp Collaborator* if the site trigger your logs This confirms that you are able to make the website's middleware issue requests to an arbitrary server
  - brute force the internal infrastructure with burp intruder `Host: 192.168.0.§0§`

- **SSRF via a malformed request line**

  `GET @private-intranet/example HTTP/1.1`

  The resulting upstream URL will be http://backend-server@private-intranet/example, which most HTTP libraries interpret as a request to access private-intranet with the username backend-server.

  

  





























alter host value to :

localhost

dev

stage

test

---

pass reset

alter host to your site and check if rest message contaning your site

---

