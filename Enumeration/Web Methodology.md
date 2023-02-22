- [ ] Fuzz Subdomains
- [ ] Fuzz Files (Interesting extensions like, js, txt, php, and anything that could lead to information about what is running in the web server)
- [ ] Fuzz Headers
- [ ] Fuzz Directories
- [ ] If login is present (check for simple logins like, admin:admin, guest:guest, admin:administrator, etc...)
- [ ] Webservice being used (you can enumerate this using different methods, source files, responses , 404 errors, extensions being used, etc.)
- [ ] SQL injenctions in Login/Register/Etc
- [ ] Check libraries for vulnerabilities and outdated versions for exploits.
- [ ] Check if CSRF is present, if not, try to exploit by abusing an user to perform undesired changes to the profile and such.
	- [ ] Check if CSRF being present affect the response of the page
	- [ ] Static CSRF present
	- [ ] Method check (instead of using POST , send a GET without CSRF token)
	- [ ] Check if using B account CSRF token works on A account CSRF token.
	- [ ] Check if CSRF token is duplicated on Cookie.
- [ ] If 2FA present check for different bypass on it
	- [ ] Direct bypass 
	- [ ] Reusing OTP Code
	- [ ] Sharing unused token
	- [ ] Leaked token in Response
	- [ ] Session management through 2FA disable. 
	- [ ] Check if session persist on another device if 2FA is enable in one browser and the other one doesn't 
	- [ ] Request manipulation by changing the JSON Response. Verify for 
- [ ] Verify if OAuth is present, and try to abuse it. There are a couple of vulnerabilities to abuse it.












## References 
### 2FA
[Simple checklist for bug bounty on this](https://thegrayarea.tech/the-ultimate-bug-bounty-checklist-for-2fa-f0ffa9e666cc)
[OAuth Abuse](https://portswigger.net/web-security/oauth)
