Advisories
==========

Security advisories I've published in the latest years.


### VMTurbo Operations Remote Command Execution

VMTurbo Operations Manager appliance can be exploited by an unauthenticated attacker to execute unauthenticated arbitrary remote commands.

25-07-2014 | [CVE-2014-5073](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-5073) | [Original advisory](https://github.com/epinna/advisories/blob/master/CVE-2014-5073/secunia_advisory.txt) | [Advisory details](http://disse.cting.org/2014/07/30/vmturbo-operation-manager-remote-command-execution/) | [Metasploit Module](https://github.com/epinna/advisories/blob/master/CVE-2014-5073/vmturbo_vmtadmin_exec_noauth.rb) | Status: Fixed in 4.6-28657

### Moodle PHP Object Injection Leads to XSS and File Deletion

Moodle CMS can be exploited passing unsanitized user-supplied input to the PHP unserialize() function and can be exploited to delete arbitrary files and to conduct reflected XSS attacks.

16-09-2013 | [CVE-2013-5674](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-5674) | [Advisory](https://github.com/epinna/advisories/blob/master/CVE-2013-5674/2013-09-16-moodle-2_5_0_1-badges-external-object-injection.markdown) | Status: Fixed in 2.5.2

### Joomla Core Reflected XSS Vulnerability

Joomla core suffers from reflected XSS vulnerability that can be exploited to steal cookies, session tokens, and other sensitive information in the context of the affected website.

04-09-2013 | [CVE-2013-5583](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-5583) | [Advisory](https://github.com/epinna/advisories/blob/master/CVE-2013-5583/2013-08-05-joomla-core-3_1_5_reflected-xss-vulnerability.markdown) | Status: Fixed after version 3.1.5

### Alice Gate CSRF Reconfiguration Security Bypass

The ADSL routers Telecom ADSL Alice Gate VoIP 2 Plus Wi-Fi and ADSL2+ Wi-Fi N suffer from a CSRF attack that can be exploited to manipulate internal configuration and e.g. replace DNS addresses, open the telnet service to the WAN side, change the traffic routing, reconfigure the VoIP, etc. leading to a complete takeover of the system and the LAN. Can be also exploited to enable hidden administrative features.

02-09-2012 | [Main advisory (ITA)](https://github.com/epinna/advisories/blob/master/alice_gate_CSRF/alice_gate_csrf_ITA.markdown) | [Technical advisory (ITA)](https://github.com/epinna/advisories/blob/master/alice_gate_CSRF/alice_gate_csrf_details_ITA.markdown) | Status: Mitigated in next versions.


### KusabaX Reflected XSS and CSRF Vulnerabilites 

KusabaX suffers from reflected XSS vulnerability that can be exploited to steal cookies, session tokens, and other sensitive information in the context of the website embedding the vulnerable editor. This also suffers from CSRF vulnerability that can be exploited to execute arbitray SQL statements. 

27-04-2011 | [Advisory](https://github.com/epinna/advisories/blob/master/kusaba_x_XSS_CSRF/kusaba-x-advisory.txt) | Status: Fixed in 0.9.2

### Fastweb XSS and Myfastpage Authentication Security Bypass

Fastweb website suffers from an XSS vulnerability that can be exploited to steal the authentication token. This can be exploited to access to the Fastweb account control panels bypassing the proper authentication and IP checks.

03-06-2010 | [Advisory (ITA)](https://github.com/epinna/advisories/blob/master/fastweb_myfastpage_XSS_security_bypass/fastweb_myfastpage_advisory.pdf) | Status: Fixed
