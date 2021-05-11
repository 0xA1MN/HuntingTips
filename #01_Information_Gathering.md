### Collect General Information about Target

`whois [target_name]`	general information about target

`nslookup`	translate host names to IP addresses and reverse

`whatweb` tool on github

### There are lots of ways to enumerate subdomains :

Netcraft
Google > site:.domain.com
Crawling / Brute force > (subbrute, dnsrecon and theHarvester
Tools
Zone transfers



### Our first step in this case will be to consider the overall scope of the application:
 What is it for?
 Does it allow user registration?
 Does it have an administration panel?
 Does it take input from the user?
 What kind of input?
 Does it accept file uploads?
 Does it use JavaScript or Ajax or Flash? And so on.



### Create functional graph

following questions should help guide you:
What is the purpose of the website/web application?
	 Sell online?
 	Corporate online presence?
 	Blogging?
What seems to be the core of the website?
	 Selling products?
 	Do they sell memberships? digital contents?
Does it require login to perform certain actions?
What are the main areas of the website?
 	Blogs?
 	eCommerce area?



### mapping the attack surface

**Client Side Validation** : Recognizing where the validation occurs will allow us to manipulate the input in order to mount our input validation attacks like SQL injection, Cross site scripting or general logical flaws.
**Database Interaction** : Detecting database interaction will enable us to look for SQL injection vulnerabilities in the testing phase.
**File Uploading And Downloading** : It is not uncommon to encounter web pages providing dynamic downloads according to a parameter provided by the user.
For example:`www.example.com/download.php?file=document.pdf`

This kind of behavior, if not handled correctly, can lead to a number of nasty attacks including Remote and Local File Inclusion vulnerabilities that will be explained in the next modules.

**Display Of User Supplied Data** :This is one of the most common features in a dynamic website.
Finding displayed user supplied data will bring us to the top web application vulnerability: Cross site scripting.
**Redirections** : Finding redirects is an important part of our attacking surface as HTTP response splitting and other Header manipulation attacks can be performed when redirects are not handled properly.
**Access Controls And Login Protected Pages** : Login pages will reveal the presence of restricted access areas of the site.
We will employ authentication bypass techniques as well as password brute forcing to test the security of the authentication routines in place.
**Error Messages** : While at this stage we will not intentionally cause the application to generate errors (we will see later to be a great source of information), we will collect all the errors in the application that we
may encounter while browsing it.
