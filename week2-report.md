# Week 2 Report - Footprinting and Network Scanning

Pentester: Muhammad Abdul Hanan
Program/Batch: B084M - Networkwalks
Date: 15 November 2026
Modules completed: W2-PM1 (Multiple Kali Tools), W2-PM5 (Zenmap Scanning)
Target 1: networkwalks.com (their own site, internship lab)
Target 2: my own lab network 192.168.64.0/24
Permission: Yes, internship lab + own network only.

## 1. Liability Disclaimer

I did these activities only on systems I own or where I had written permission. All material is for education and research only.

## 2. Introduction

This report covers footprinting networkwalks.com with six Kali tools (PM1) and scanning my own lab network with Zenmap / nmap ping scan (PM5).

## 3. Tools Used

whois, whatweb, nslookup, curl -I, wafw00f, dnsrecon, Zenmap (nmap).

## 4. Activities

### 4.1 PM1 - Footprinting networkwalks.com

All six tools run in Kali Linux on 15 November 2026. Screenshots in screenshots/ 1 to 6.

- whois: Registrar GoDaddy.com, LLC (IANA 146). Domain created 2019-11-06, expires 2027-11-06. Name servers NS6135.HOSTGATOR.COM and NS6136.HOSTGATOR.COM, so hosting is HostGator. Registrant is private (Domains By Proxy). DNSSEC unsigned. Abuse contact abuse@godaddy.com.
- whatweb: Server Apache. WordPress 7.1.4, WordPress Download Manager 3.3.76, Bootstrap 7.1.4, jQuery 3.7.1. IP 192.232.216.135. Email info@networkwalks.com exposed. Title "Networkwalks Academy". Note: the sample PDF listed WordPress 7.0.4 and Download Manager 3.3.58, the site has since been updated to 7.1.4 / 3.3.76.
- nslookup: Server 8.8.8.8. networkwalks.com resolves to 192.232.216.135.
- curl -I: HTTP/1.1 200 OK, Server Apache, Content-Type text/html; charset=UTF-8. Headers expose the WordPress REST API link /wp-json/ and X-nginx-cache: WordPress. Date of run: Sun, 15 Nov 2026 08:27:35 GMT.
- wafw00f v2.4.2: The site is behind ModSecurity (SpiderLabs) WAF. Number of requests: 2.
- dnsrecon: SOA ns6135.hostgator.com 50.87.144.87. NS ns6135.hostgator.com and ns6136.hostgator.com. Bind version 9.16.23-RH. MX mail.networkwalks.com 192.232.216.135. A record 192.232.216.135. TXT google-site-verification and SPF v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all. SRV _autodiscover._tcp cPanel records. 8 records found. One problem faced: the first dnsrecon run saved an empty file, because dnsrecon writes its log to stderr, not stdout. Fixed by running `dnsrecon -d networkwalks.com > 6-dnsrecon.txt 2>&1`, file then saved at 2.3K.

### 4.2 PM5 - Zenmap scan of 192.168.64.0/24

Ping scan run in Kali on 15 November 2026 with `sudo nmap -sn 192.168.64.0/24`, the command-line equivalent of a Zenmap Ping Scan. Screenshot 7-nmap-ping.png.

- My Kali IP: 192.168.64.5, gateway 192.168.64.1
- Live hosts count: 2, out of 256 addresses scanned in 2.91 seconds
- Live host IPs: 192.168.64.1 and 192.168.64.5
- Live host MACs: 192.168.64.1, MAC C6:35:D9:C7:3F:64 (Unknown); 192.168.64.5 is the Kali machine itself
- Topology PDF: topology.pdf saved (6.9K) from Zenmap, screenshot 8-zenmap-topology.png

The RedMind victim VM 192.168.64.6 was not running during the scan, so it does not appear. No other hosts were live. The scan was on the lab network only, never on the home WiFi 192.168.100.0/24.

Zenmap GUI was also run with the same Ping Scan profile. The Topology view shows localhost 192.168.64.5 connected to 192.168.64.1.

## 5. Risk Analysis

Findings are observations from footprinting and scanning, not confirmed vulnerabilities. No active exploitation was done.

- WordPress 7.1.4 and Download Manager 3.3.76 are current as of the run date, but CMS versions exposed by whatweb should be kept updated, as outdated plugins are a common entry point. Risk: Low.
- Email info@networkwalks.com and the WordPress REST API link /wp-json/ are publicly exposed in headers. Useful for attackers to plan phishing or enumeration. Risk: Low.
- DNS records expose Bind 9.16.23, the mail server, SPF details and cPanel SRV records. This helps an attacker map the infrastructure. Risk: Low.
- ModSecurity (SpiderLabs) WAF is active, which blocks common web attacks. Good control, keep it enabled. Risk: Low.
- Lab network scan found only 2 hosts, gateway and Kali. No unknown device was live at scan time. Keep scanning the lab network to spot new devices early. Risk: Low.

## 6. Recommendations

Keep software updated, review exposed headers and DNS records, keep WAF enabled, scan own network regularly, investigate unknown devices.

## 7. Conclusion

I footprinted networkwalks.com with six Kali tools and scanned my own lab network with Zenmap and nmap. I learned what information a public site exposes, how to read whois, DNS and header output, and how to find live hosts on my own network. The site has a WAF and current WordPress version, and my lab network had only the gateway and Kali live. I will keep my own lab scanned and my software updated.

## 8. Evidence

- 1-whois.png - whois output, GoDaddy registrar and HostGator name servers
- 2-whatweb.png - whatweb output, WordPress 7.1.4 and IP 192.232.216.135
- 3-nslookup.png - nslookup output, IP 192.232.216.135
- 4-curl.png - curl -I headers, Apache and WordPress REST API link
- 5-wafw00f.png - wafw00f output, ModSecurity (SpiderLabs) WAF
- 6-dnsrecon.png - dnsrecon output, NS, MX, SPF, TXT, SRV records
- 7-nmap-ping.png - PM5 Zenmap / nmap ping scan, 2 hosts up
- 8-zenmap-topology.png - PM5 Zenmap Topology view, 192.168.64.5 connected to 192.168.64.1
