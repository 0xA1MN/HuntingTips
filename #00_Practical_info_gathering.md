## Information Gathering for Website Hacking

### #01 Whois Lookup

Command Line Tool format like `whois <target_name Or target_IP>`

this command result 

- The domain information ... general details about the domain 
  - **Domain :** This field will give you the domain name which we are querying the WHOIS details.
  - **Registrar :** This is the details of the registrar with whom the domain name is registered.
  - **Registration Date :** This is the date when the domain name was first registered.
  - **Expiration Date : **This is the date when the domain will expire.
  - **Updated Date :**  This is the date when the WHOIS details last updated.
  - **Status :** This is the registrar status of the domain. This will be “OK” if there is no restriction and the domain is free to transfer from one registrar to another.
  - **Name Servers :** This field will provide the details of the nameservers used by the domain.
- Registrant Contact
- Administrative Contact
- Technical Contact



### #02 Netcraft

gathering in-depth information about target

- **Background :** This includes basic domain information.
- **Network :** This includes information from IP Address to Domain names to nameservers.
- **SSL/TLS :** This gives the ssl/tls status of the target
- **Hosting History :** This gives the information on the hosting history of the target
- **Sender Policy Framework (SPF) :** This describes who can send mail on the domains behalf
- **DMARC :** This is a mechanism for domain owners to indicate how mail purporting to originate from their domain should be authenticated
- **Web Trackers :** This trackers can be used to monitor individual user behavior across the web
- **Site Technology :** This section includes details on :



**Cloud & PaaS**: Cloud computing is the use of computing resources (hardware and software) that are delivered as a service over a network (typically the Internet). Platform as a service (PaaS) is a category of cloud computing services that provide a computing platform and a solution stack as a service.

**Server-Side**: Includes all the main technologies that Netcraft detects as running on the server such as PHP.

**Client-Side** Includes all the main technologies that run on the browser (such as JavaScript and Adobe Flash).

**Content Delivery Network**: A content delivery network or content distribution network (CDN) is a large distributed system of servers deployed in multiple data centers on the Internet. The goal of a CDN is to serve content to end-users with high availability and high performance.

**Content Management System**: A content management system (CMS) is a computer program that allows publishing, editing and modifying content as well as maintenance from a central interface.

**Mobile Technologies:** Mobile technology is the technology used for hand held mobile devices.

**Web Stats**: Web analytics is the measurement, collection, analysis and reporting of internet data for purposes of understanding and optimizing web usage.

**Character Encoding**: A character encoding system consists of a code that pairs each character from a given repertoire with something else such as a bit pattern, sequence of natural numbers, octets, or electrical pulses in order to facilitate the transmission of data (generally numbers or text) through telecommunication networks or for data storage.

**Web Browser Targeting**: Web browser targeting enables software applications to make use of specific functions of the browser as well as optimizing the application for specific browser versions.



### #03 Robotext

This will help gather comprehensive Domain Name Server (DNS) information on the target victim.

### #04 dirb 

Dirb is a web content scanner. This will help you discover sensitive and hidden files and directories of a website or web application.



**Robots.txt : This is a file that contains the files that we don’t want the website or Google to read.**