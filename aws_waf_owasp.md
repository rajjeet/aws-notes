# Use AWS WAF to Mitigate OWASP's Top 10 Web Application Vulnerabilities
* web app firewall protects websites and web apps against attack vectors at HTTP protocol level
* OWASP = Open Web Application Security Project

## Introduction
* 10 most critical web app security flaws
* AWS WAF define custom web security rules to allow or block requests to web apps
* WAF integrates with CloudFront and ELB

## Web Application Vulnerability Mitigation
1. Injection
	* App sends untrusted data to interpreter
	* Untrusted data alters the intent of the request and causes side effects
	* I.e. SQL injection
	* Apply URL_DECODE and HTML_ENTITY_DECODE transformation in AWS WAF
	* Most common in _query_string_ and body of request
	* _string match condition_ against reserved words 
2. Broken Authentication and Session Management
	* attackers impersonate legitimate users 
	* i.e. attacks obtain JWT tokens to call RESTful API
	* detect using own securiy controls and blacklist the stolen token
	* _string match condition_
	* keep track of client devices and location where users commenly accesses app from
	* rate-based rules 
3. Cross-Site Scripting (XSS)
	* user provided data included in webpages without proper sanitization
	* attackers embed scripts, inline frames into rendered page for malicous purposes
	* easy to mitigate because they require detectable HTML tag names
	* use AWS WAF's _cross-site scripting match condition_
	* Most commmon in body, and sometimes in query string and header cookie
	* detection is difficult if workload allows users to submit HTML
		* need to detect specific patterns
4. Broken Access Control 
	* improper enforcement of restrictions on authenticated users
	* app needs to properly check and restrict access
	* use of WAF is difficult because the detection involves knowing the context of the user session and priviledges
	* can check for apth traversal attempts(../) and remote file inclusion (//)
		* can't detect if this is part of the workload
	* if admin role is limited to few users, whitelist them using IP
5. Security Misconfiguration
	* can happen at any level of stack
	* i.e. listing version numbers and dependencies - attackers can learn about vulnerabiltiies
	* i.e. leaving default directory listening enabled - attackers browse files
	* i.e. app servers that require stack traces - attackers reverse engineer code to find flaw
	* WAF - allow admin access via whitelist
	* WAF - protect against known attack patterns for given platform
	* use Amazon Inspector to detect problems
	* use Config and Systems manager to track and manage configuration changes over time
6. Sensitive Data Exposure
	* encrpytion process that is deficiently implemented
	* lack of encryption on sensitive data
	* vulnerable legacy ciphers
	* malcious parties can intercept and decode data
	* For WAF, this cannot be detected in the HTTP request unless the pattern is detectable
	* Load Balancers can select ciphers 
	* CloudFront can configure minimum SSL protocol version
7. Insufficent Attack Protection
	* WAF - use _Size constraint conditions_ to ensure that HTTP requests fall under specific ranges
	* WAF - use rate-based rules to reject threshold reaching requests
	* programmatically configure WAF to protect against ongoing attacks
	* _Scanner and probe mitigration_ - source sending series of request that result in 4xx error codes
	* _Known attacker origin mitigation_ - block known ip addresses of attackers, spammers, botnets, and malware distributors
	* _bots and scraper mitigation_ - honeyput help identify bad bots and scrapers - restricted parts of website 
8. Cross-Site Request Forgery (CSRF)
	* state changing functions in web app
	* depending soley on client-side attributes isnt effective - malcious actors can replicate it
	* attacks take advantage that all facts of an action are predictable
	* WAF
		* include unpredictable tokens in HTTP requests (trasparent to users)
		* prompt users to authenticate
		* include CAPTCHA challenges		
9. Using Components with Know Vulnerabilities
	* developers have to implment, test, and deploy new versions of components regularly
	* need process to address lifecylce of components
	* monitoring process to track security of these components
	* _string match condition_ to block requests to unused components 
	* penetration testing can be effective to discover vulnerabilities
10. Underprotected APIs
	* hardening the protocol parse itself 
	* WAF's _string match condition_ and _size restrictions_ to block exploit flaws of parser

