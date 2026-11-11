# 🛡️ WEEK 2 SECURITY ASSESSMENT REPORT
## 🔍 Reconnaissance and Network Discovery
**W2-PM-FINAL | NETWORKWALKS CYBERSECURITY & ETHICAL HACKING INTERNSHIP**

| Detail | Information |
| :--- | :--- |
| 👤 **Prepared by** | Bhavanasi Sreevathsa |
| 🆔 **Intern ID** | NW-084-094 |
| 🎓 **Internship batch** | B084-Networkwalks |
| 📅 **Report date** | 11 November 2026 |
| ✅ **Modules done** | W2-PM1 (footprinting with Kali tools) , W2-PM5 (Zenmap network scan) |
| 🎯 **Targets** | 1. networkwalks.com (lab assigned)
| 🔐 **Authorisation** | Lab-assigned targets & self-owned network. theHarvester used public sources without direct contact. |
| 🧭 **Stage reached** | Stage 1: Information Gathering | Stage 2: Scanning & Host Discovery |

---

## ⚖️ 1. Liability Disclaimer
Everything in this report was done inside a supervised learning lab. I only worked on targets that the lab assigned to me, plus a network that belongs to me. The commands and findings are shared for study purposes. They should never be used against a system you have no permission to test. In most countries, entering someone else's system without consent is a crime whether or not any damage is done, and the consequences can be serious. I take responsibility for my own actions.

## 📖 2. Introduction
This is my Week 2 write-up for the Networkwalks internship. I worked through three practical modules:
- **W2-PM1:** Learning what the internet can reveal about `networkwalks.com` using six Kali Linux too
- **W2-PM5:** Finding live devices on my own Wi-Fi network with Zenmap.

The first two modules are about what an outsider can learn without touching a target, while the third is about discovering what is active on a network I control. Together they follow the path a real assessment takes, from public information to a map of reachable hosts. All footprinting commands were run from Kali Linux inside VirtualBox, and network scans from Windows.

## 🧰 3. Tools Used
| Tool / Environment | Purpose |
| :--- | :--- |
| 🐧 **Kali Linux (VirtualBox)** | Main environment for footprinting and OSINT commands. |
| 🌐 **WHOIS** | Retrieves domain registration records, creation dates, and name server details. |
| 🔎 **whatweb** | Fingerprints web server software, CMS, version numbers, and front-end frameworks. |
| 📡 **nslookup** | Resolves domain names to IP addresses via designated DNS servers. |
| 📨 **curl -I** | Fetches and inspects HTTP response headers, cookies, and security policies. |
| 🧱 **wafw00f** | Probes websites to detect Web Application Firewalls (WAF). |
| 🗂️ **dnsrecon** | Pulls comprehensive DNS records (NS, MX, SPF, TXT, SRV). |
| 🌾 **theHarvester** | Gathers emails, sub-domains, and hosts from public search sources. |
| 🗺️ **Zenmap (Nmap GUI)** | Graphical frontend for Nmap, used to perform ping sweeps on local subnets. |
| 💻 **Windows CMD** | Identifies local IP address, subnet mask, gateway, and MAC addresses. |

---

## 🧪 4. Activities Performed

### 🔍 4.1 W2-PM1: Footprinting & Reconnaissance (networkwalks.com)
I performed reconnaissance against `networkwalks.com` using six Kali Linux tools:
* **WHOIS (`whois networkwalks.com`):** Registered via GoDaddy (created 2019-11-06, expires 2027-11-06). Uses name servers `NS6135.HOSTGATOR.COM` and `NS6136.HOSTGATOR.COM` (HostGator hosted). DNSSEC is unsigned, with standard domain status locks enabled.
* **WhatWeb (`whatweb networkwalks.com`):** Identified Apache web server, WordPress 7.1.4, WordPress Download Manager 3.3.76, jQuery 3.7.1, Bootstrap, and Google Tag Manager running on IP `192.232.216.135` (United States).
* **Nslookup (`nslookup networkwalks.com`):** Resolved `networkwalks.com` via Google DNS (`8.8.8.8`) to IP `192.232.216.135`.
* **Curl (`curl -I https://networkwalks.com`):** Returned HTTP/2 200 OK, Apache server header, caching directives (`x-nginx-cache: WordPress`), secure `__wpdm_client` cookie, and exposed REST API endpoint at `/wp-json/`. Noted absence of HSTS, X-Frame-Options, X-Content-Type-Options, and CSP headers.
* **Wafw00f (`wafw00f https://networkwalks.com`):** Detected that the site is protected behind a **ModSecurity (SpiderLabs) WAF**.
* **DNSRecon (`dnsrecon -d networkwalks.com`):** Enumerated SOA/NS records (BIND version 9.16.23-RH), A record (`192.232.216.135`), MX record, SPF TXT record (`~all` softfail), Google site-verification TXT, and cPanel SRV autodiscover records.

### 🌾 4.2 W2-PM4: theHarvester (microsoft.com)
Using theHarvester v4.10.1 for public-source OSINT without direct contact:
* **Task 1 (Baidu source, limit 1000):** Completed successfully with no direct IPs or emails extracted from this specific engine run.
* **Task 2 (All public sources, limit 50):** Harvested a rich list of `microsoft.com` sub-domains and IP addresses (e.g., `131.107.86.5`, `131.107.86.17`), identifying staging, QA, and pre-production environments (e.g., `wwwqa`, `wwwbeta`). Saved output contained 184 lines.

### 🗺️ 4.3 W2-PM5: Network Scanning with Zenmap (Home Wi-Fi)
Conducted local network discovery following all practical tasks:
1. Installed Nmap 7.993 / Zenmap.
2. Identified local interface: IP `192.168.61.164`, mask `255.255.255.0`, gateway `192.168.61.230` on subnet `192.168.61.0/24`.
3. Executed Ping Scan (`nmap -sn`) discovering 2 live hosts (`192.232.61.230` gateway and `192.232.61.164` PC) in 10.12 seconds.
4. Extracted MAC addresses and generated the topology map saved as `zenmap_topology.pdf`.

---

## ⚠️ 5. Risk Analysis
| # | Risk / Finding | Evidence / Observation | Potential Impact | Level |
|:---:|:---|:---|:---|:---:|
| 1 | **CMS & plugin versions visible** | WordPress 7.1.4 & Download Manager 3.3.76 identified | Known vulnerabilities can be targeted quickly. | 🟡 Medium |
| 2 | **Key security headers absent** | No HSTS, X-Frame-Options, or CSP headers | Reduced browser-side protection against attacks. | 🟡 Medium |
| 3 | **SPF set to softfail** | Policy ends in `~all` | Forged emails may be accepted by mail servers. | 🟡 Medium |
| 4 | **Many sub-domains public** | Staging/QA sub-domains exposed via theHarvester | Expands attack surface and reveals naming conventions. | 🟡 Medium |
| 5 | **WordPress REST API advertised** | Link headers point to `/wp-json/` | Aids in content enumeration. | 🟢 Low |
| 6 | **DNSSEC not enabled** | Domain is unsigned | DNS responses lack cryptographic integrity. | 🟢 Low |
| 7 | **DNS version disclosed** | BIND 9.16.23-RH shown on name servers | Aids in targeting specific software flaws. | 🟢 Low |
| 8 | **Hosting setup identifiable** | HostGator and cPanel details exposed | Reveals underlying hosting provider infrastructure. | 🟢 Low |
| 9 | **WAF product identifiable** | ModSecurity WAF detected | Informs attackers of active defensive controls. | 🟢 Low |
| 10 | **Local devices discoverable** | Zenmap ping scan identified live hosts & MACs | Maps local network layout. | 🟢 Low |

*(Note: These are observations, not proven vulnerabilities. No exploitation was performed.)*

---

## ✅ 6. Recommendations
1. **Review Public Technology Info:** Audit publicly visible server banners, CMS versions, and plugin signatures.
2. **Maintain Strict Patching:** Keep WordPress, themes, and plugins updated against current security advisories.
3. **Harden HTTP Headers:** Enable HSTS, X-Frame-Options, X-Content-Type-Options, and CSP.
4. **Strengthen SPF Policy:** Transition SPF records from softfail (`~all`) to hard fail (`-all`) and implement DKIM/DMARC.
5. **Enable DNSSEC:** Sign domain zone records to ensure cryptographic verification.
6. **Conceal Version Banners:** Restrict BIND and Apache version disclosures on name servers and web servers.
7. **Monitor WAF Rules:** Keep ModSecurity active and properly tuned.
8. **Control Sub-Domain Exposure:** Restrict public visibility of staging and pre-production servers.
9. **Perform Regular Internal Audits:** Periodically scan local subnets to maintain asset visibility.
10. **Enforce Authorization:** Ensure all testing and scanning activities remain strictly within authorized scopes.

---

## 🏁 7. Conclusion
Week 2 demonstrated how much intelligence can be gathered about a target prior to active exploitation. Using basic Kali utilities and public OSINT frameworks, I built a comprehensive profile of `networkwalks.com` Zenmap provided practical insight into local host discovery on my home network.

This module reinforced that reconnaissance is fundamental to understanding attack surfaces and hardening network boundaries. All exercises were completed strictly within ethical guidelines and supervised learning parameters.
