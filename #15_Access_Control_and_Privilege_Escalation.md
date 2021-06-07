### Access control vulnerabilities and privilege escalation

Access control (or authorization) is the application of constraints on who (or what) can perform attempted actions or access resources that they have requested.

#### Access control is dependent on authentication and session management :

- **Authentication** identifies the user and confirms that they are who they say they are.
- **Session management** identifies which subsequent HTTP requests are being made by that same user.
- **Access control** determines whether the user is allowed to carry out the action that they are attempting to perform.



#### Access controls can be divided into the following categories :

- **Vertical access controls**  *act like u are another user with SAME privileges*

- **Horizontal access controls**  *act like u are another user with HIGHER privileges*

- **Context-dependent access controls** *performing actions in the WRONG ORDER*

   Context-dependent access controls restrict access to functionality and resources based upon the state of the application or the user's interaction with it.

  Context-dependent access controls prevent a user performing actions in the wrong order. For example, a retail website might prevent users from modifying the contents of their shopping cart after they have made payment.



#### Examples of broken access controls

##### -	Vertical privilege escalation

If a user can gain access to functionality that they are not permitted to access then this is vertical privilege escalation. For example, if a non-administrative user can in fact gain access to an admin page where they can delete user accounts, then this is vertical privilege escalation.

- **Unprotected functionality ** *anyone can access admin functionalities*

  In some cases, the administrative URL might be disclosed in other locations, such as the `robots.txt` file: `https://insecure-website.com/robots.txt`

  or brute force admin directory

  **in some cases :**

  sensitive functionality is not robustly protected but is concealed by giving it a less predictable URL

  in this case check JS files may contain URL of admin functions

- **Parameter-based access control methods**

  Some applications determine the user's access rights or role at login, and then store this information in a user-controllable location, such as a hidden field, cookie, or preset query string parameter

  ex : 

  `https://insecure-website.com/login/home.jsp?admin=true
  https://insecure-website.com/login/home.jsp?role=1`

  in these case try to modify admin cookie if exist or abuse update function if exist `"roleid":2`

- **Broken access control resulting from platform misconfiguration**

  Some applications enforce access controls at the platform layer by restricting access to specific URLs and HTTP methods based on the user's role. For example an application might configure rules like the following:

  DENY: POST, /admin/deleteUser, managers

  Some application frameworks support various non-standard HTTP headers that can be used to override the URL in the original request, such as X-Original-URL and X-Rewrite-URL. If a web site uses rigorous front-end controls to restrict access based on URL, but the application allows the URL to be overridden via a request header, then it might be possible to bypass the access controls using a request like the following:

  ```http
  POST / HTTP/1.1
  X-Original-URL: /admin/deleteUser
  ...
  ```

  An alternative attack can arise in relation to the HTTP method used in the request. The front-end controls above restrict access based on the URL and HTTP method. Some web sites are tolerant of alternate HTTP request methods when performing an action. If an attacker can use the `GET` (or another) method to perform actions on a restricted URL, then they can circumvent the access control that is implemented at the platform layer.

  steal admin session 

  change request method 
  
  

##### -	Horizontal privilege escalation

Horizontal privilege escalation arises when a user is able to gain access to resources belonging to another user, instead of their own resources of that type. For example, if an employee should only be able to access their own employment and payroll records, but can in fact also access the records of other employees, then this is horizontal privilege escalation.

- **modifies the id parameter value **

  `https://insecure-website.com/myaccount?id=123`

- **find GUID for target user**

  In some applications, the exploitable parameter does not have a predictable value. For example, instead of an incrementing number, an application might use globally unique identifiers (GUIDs) to identify users. Here, an attacker might be unable to guess or predict the identifier for another user. However, the GUIDs belonging to other users might be disclosed elsewhere in the application where users are referenced, such as user messages or reviews.

- **no user check**

  In some cases, an application does detect when the user is not permitted to access the resource, and returns a redirect to the login page. However, the response containing the redirect might still include some sensitive data belonging to the targeted user, so the attack is still successful.



##### -	Horizontal to Vertical privilege escalation

Often, a horizontal privilege escalation attack can be turned into a vertical privilege escalation, by compromising a more privileged user. For example, a horizontal escalation might allow an attacker to reset or capture the password belonging to another user. If the attacker targets an administrative user and compromises their account, then they can gain administrative access and so perform vertical privilege escalation.

for example : `Change the "id" parameter in the URL to "administrator"`



##### - Access control vulnerabilities in multi-step processes

Many web sites implement important functions over a series of steps. This is often done when a variety of inputs or options need to be captured, or when the user needs to review and confirm details before the action is performed. For example, administrative function to update user details might involve the following steps:

- Load form containing details for a specific user.
- Submit changes.
- Review the changes and confirm.

Sometimes, a web site will implement rigorous access controls over some of these steps, but ignore others. For example, suppose access controls are correctly applied to the first and second steps, but not to the third step. Effectively, the web site assumes that a user will only reach step 3 if they have already completed the first steps, which are properly controlled. Here, an attacker can gain unauthorized access to the function by skipping the first two steps and directly submitting the request for the third step with the required parameters.



##### - Referer-based access control

Some websites base access controls on the Referer header submitted in the HTTP request. The Referer header is generally added to requests by browsers to indicate the page from which a request was initiated.

For example, suppose an application robustly enforces access control over the main administrative page at /admin, but for sub-pages such as /admin/deleteUser only inspects the Referer header. If the Referer header contains the main /admin URL, then the request is allowed.

In this situation, since the Referer header can be fully controlled by an attacker, they can forge direct requests to sensitive sub-pages, supplying the required Referer header, and so gain unauthorized access.



##### - Location-based access control

Some web sites enforce access controls over resources based on the user's geographical location. This can apply, for example, to banking applications or media services where state legislation or business restrictions apply. These access controls can often be circumvented by the use of web proxies, VPNs, or manipulation of client-side geolocation mechanisms.























​	