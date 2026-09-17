# Penetration Testing Report: Footprinting & Network Scanning

![Cybersecurity](https://img.shields.io/badge/Focus-Cybersecurity-blue)
![Program](https://img.shields.io/badge/Program-Networkwalks-green)
![Batch](https://img.shields.io/badge/Batch-B083F-orange)
![Status](https://img.shields.io/badge/Status-Phase%201%20%26%202%20Complete-success)

## 📌 Project Information

| Field | Details |
|---|---|
| **Pentester** | Collins L Simukoko |
| **Role** | Cybersecurity Student / Intern |
| **Program** | Cybersecurity at Networkwalks |
| **Batch / Cohort** | B083F |
| **Week** | 02 |
| **Modules** | W2-PM1 & W2-PM5 |
| **Date** | 15–16 September 2026 |
| **Status** | Phase 1 & 2 Complete; Phases 3–5 In Progress |
| **Client / Target** | Networkwalks |
| **Authorization** | Written permission secured |

> **Note:** The practical activities documented in this repository were performed within an authorized educational environment and on systems/networks for which permission was obtained.

---

## ⚖️ Liability Disclaimer

All activities described in this project were carried out only on systems and devices where I had permission to perform security testing, or within my own controlled laboratory environment.

The purpose of this work is strictly **educational and research-oriented**. The techniques and tools demonstrated here must not be used to access, scan, or interfere with systems without appropriate authorization.

Unauthorized access or security testing may violate applicable laws, organizational policies, or academic rules. Security testing should always be performed within an approved scope and with explicit authorization.

---

## 📝 Introduction

This project documents two practical cybersecurity modules completed during Week 2 of my Networkwalks programme:

- **W2-PM1 — Footprinting & Reconnaissance using Kali Linux**
- **W2-PM5 — Network Scanning using Zenmap**

The exercises introduced the first stages of a penetration testing workflow.

The footprinting activity focused on gathering publicly available information about the `networkwalks.com` domain. Different reconnaissance tools were used to identify domain registration information, DNS records, web technologies, HTTP response information, and the presence of a Web Application Firewall.

The network scanning activity focused on discovering active hosts within a controlled `/24` network using Zenmap. The results were then visualized using Zenmap's **Topology** feature.

These exercises helped me understand how penetration testers collect and organize information before moving into more detailed security assessment phases.

---

# 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Kali Linux** | Operating system used for reconnaissance activities |
| **WHOIS** | Retrieves publicly available domain registration and name-server information |
| **WhatWeb** | Identifies web technologies, CMS platforms, plugins, and related information |
| **Nslookup** | Resolves domain names to IP addresses through DNS |
| **curl** | Inspects HTTP response headers |
| **wafw00f** | Detects Web Application Firewalls |
| **DNSRecon** | Enumerates DNS records |
| **Zenmap / Nmap** | Performs network host discovery and visualizes network topology |
| **Windows CMD** | Used to identify local network configuration |

---

# 🔎 W2-PM1 — Footprinting & Reconnaissance

## 1. Updating Kali Linux

Before beginning the practical work, I updated the Kali Linux package lists.

```bash
sudo apt update
```

The purpose was to make sure the package information was current before using the reconnaissance tools.

---

## 2. WHOIS — Domain Registration Discovery

### Command

```bash
whois networkwalks.com
```

### Purpose

WHOIS was used to retrieve publicly available registration information associated with the target domain.

### Key observations

The results showed:

- Domain creation date: **6 November 2019**
- Registry expiry date: **6 November 2027**
- Registrar: **GoDaddy.com, LLC**
- Name servers included:
  - `NS6135.HOSTGATOR.COM`
  - `NS6136.HOSTGATOR.COM`
- Registrant information was privacy-protected.
- DNSSEC was reported as **unsigned**.
- Several domain status protections were enabled.

### Security relevance

WHOIS information can provide an initial understanding of a domain's registration, registrar, and DNS infrastructure. The DNSSEC status was also recorded as part of the assessment.

---

## 3. WhatWeb — Web Technology Fingerprinting

### Command

```bash
whatweb networkwalks.com
```

### Purpose

WhatWeb was used to identify technologies exposed by the website.

### Key observations

The scan identified information including:

- Apache web server
- WordPress 7.1
- WP Download Manager 3.3.58
- Bootstrap 7.1
- jQuery 3.7.1
- Google Tag Manager
- HTML5
- Open Graph Protocol
- HTTPS
- Server IP: `192.232.216.135`
- Contact email exposed in page metadata

### Security relevance

Technology and version information can help a security tester determine which software components require further security review. Version disclosure does **not**, by itself, prove that a vulnerability exists.

---

## 4. Nslookup — DNS Resolution

### Command

```bash
nslookup networkwalks.com
```

### Key observation

The domain resolved to:

```text
192.232.216.135
```

The lookup returned a non-authoritative DNS response.

### Security relevance

DNS resolution provides information about the IP address associated with a domain and can contribute to understanding the target's publicly visible infrastructure.

---

## 5. curl — HTTP Header Inspection

### Command

```bash
curl -I https://networkwalks.com
```

### Key observations

The response returned:

```text
HTTP/2 200
```

The response also exposed information including:

- Apache server information
- WordPress REST API links
- `/wp-json/`
- `/wp-json/wp/v2/pages/53`
- `__wpdm_client` cookie
- `Secure` and `HttpOnly` cookie attributes
- WordPress cache-related headers
- Permissions-policy information
- Referrer-policy information

### Security relevance

HTTP response headers can reveal technical information about the web server, application, caching configuration, and available endpoints. Such information may assist further authorized enumeration.

---

## 6. wafw00f — WAF Detection

### Command

```bash
wafw00f networkwalks.com
```

### Result

The tool identified:

```text
ModSecurity (SpiderLabs)
```

as the Web Application Firewall protecting the website.

### Security relevance

Identifying a WAF provides information about the security controls deployed in front of a web application. The presence of a WAF should not be interpreted as proof that the application is secure.

---

## 7. DNSRecon — DNS Enumeration

### Command

```bash
dnsrecon -d networkwalks.com
```

### Key observations

The DNS enumeration identified information including:

- SOA record
- NS records
- A record
- MX record
- TXT / SPF records
- Google site verification information
- SRV records
- cPanel-related service information

The A record resolved to:

```text
192.232.216.135
```

The MX record showed:

```text
mail.networkwalks.com
```

resolving to the same IP address.

### Important observation

The DNSRecon run also produced timeout/error messages during the exercise. Therefore, the results should be interpreted as observations from the available response rather than a guarantee that every possible DNS record was successfully enumerated.

---

# 🌐 W2-PM5 — Network Scanning with Zenmap

The second practical activity involved network discovery within a controlled VirtualBox NAT network.

## 1. Network Discovery

The target subnet used for the exercise was:

```text
10.0.0.0/24
```

The Zenmap **Ping Scan** profile was selected.

Equivalent Nmap command:

```bash
nmap -sn 10.0.0.0/24
```

### Purpose

The scan was used to discover active hosts without performing a full port and service scan.

The `/24` network contains **256 possible IPv4 addresses**.

---

## 2. Live Hosts Discovered

The scan identified two live hosts:

| IP Address | Observation |
|---|---|
| `10.0.0.1` | Virtual gateway/router |
| `10.0.0.2` | Kali Linux VM / scanning host |

### Gateway information

The gateway was identified with the MAC address:

```text
52:54:00:12:35:00
```

The result identified this as a QEMU virtual NIC.

---

## 3. Zenmap Topology

After completing the host discovery scan, I opened the **Topology** tab in Zenmap.

The topology view displayed the discovered hosts and their relationship within the `10.0.0.0/24` network.

This feature made it easier to understand the network visually and provided a graphical representation of the discovered environment.

---

# 📊 Risk Analysis

The findings below are **observations from the footprinting and network scanning exercises**. They are not confirmed vulnerabilities.

No exploitation or vulnerability validation was performed during these phases.

| # | Finding | Evidence | Potential Impact | Risk |
|---|---|---|---|---|
| 1 | CMS and plugin versions publicly exposed | WhatWeb identified WordPress 7.1 and WP Download Manager 3.3.58 | Version information can be compared with public security advisories | Medium |
| 2 | WordPress REST API endpoint exposed | `curl` identified `/wp-json/` | May provide information useful for further authorized enumeration | Medium |
| 3 | DNSSEC reported as unsigned | WHOIS / DNSRecon results | DNS integrity protections may be reduced compared with a DNSSEC-enabled configuration | Medium |
| 4 | Mail and web services share an IP | DNSRecon showed the same IP for web and mail services | A compromise affecting the host could potentially affect multiple services | Medium |
| 5 | DNS infrastructure information exposed | DNSRecon identified DNS and hosting-related records | Can contribute to a broader infrastructure profile | Medium |
| 6 | Live hosts identifiable | Zenmap discovered `10.0.0.1` and `10.0.0.2` | Host discovery provides information for subsequent authorized testing | Low |
| 7 | Virtual gateway MAC address visible | Zenmap identified the gateway MAC address | MAC information may provide additional network-level information | Low |
| 8 | Server IP and WAF technology identifiable | Nslookup, WhatWeb, and wafw00f results | Provides information about hosting and security architecture | Low |
| 9 | HTTP technical information exposed | `curl -I` returned server and cache-related headers | May assist later fingerprinting and enumeration | Low |

> **Important:** A disclosed IP address, software version, DNS record, HTTP header, or live host does not automatically mean that the system is vulnerable. Additional authorized testing is required to confirm actual security weaknesses.

---

# 🔐 Recommendations

Based on the observations made during the exercises, the following security improvements are recommended:

### 1. Review publicly exposed technology information

Review CMS, plugin, server, and framework information exposed by the website and minimize unnecessary technical disclosure where practical.

### 2. Keep software updated

Regularly update WordPress, plugins, themes, web servers, and other software components. Security advisories should be monitored for relevant vulnerabilities.

### 3. Review HTTP response headers

Review HTTP response headers and reduce unnecessary information disclosure while maintaining required application functionality.

### 4. Review DNS configuration

Periodically review DNS records and remove obsolete or unnecessary records.

### 5. Review DNSSEC configuration

Consider enabling and properly maintaining DNSSEC where appropriate to provide cryptographic validation of DNS responses.

### 6. Protect WordPress API exposure

Review publicly accessible WordPress REST API endpoints and restrict information that does not need to be available to unauthenticated users.

### 7. Maintain the WAF

Keep ModSecurity rules updated and monitor the WAF for suspicious activity. WAF configuration should be reviewed as the application changes.

### 8. Review internal network devices

Perform authorized network discovery regularly to maintain an accurate inventory of devices connected to internal networks.

### 9. Investigate unexpected devices

Any unknown or unauthorized device discovered during network monitoring should be investigated.

### 10. Maintain network documentation

Keep IP addressing, device information, network diagrams, and configuration documentation current.

---

# 📸 Evidence

Evidence collected during the practical sessions includes screenshots and outputs for:

- [x] Kali Linux environment
- [x] Kali Linux package update
- [x] WHOIS results
- [x] WhatWeb results
- [x] Nslookup results
- [x] Curl HTTP header results
- [x] Wafw00f results
- [x] DNSRecon results
- [x] Zenmap Ping Scan
- [x] Zenmap Topology View

> Screenshots can be stored in an `evidence/` directory in this repository and referenced here.

Example:

```text
evidence/
├── 01-kali-update.png
├── 02-whois.png
├── 03-whatweb.png
├── 04-nslookup.png
├── 05-curl-headers.png
├── 06-wafw00f.png
├── 07-dnsrecon.png
├── 08-zenmap-scan.png
└── 09-zenmap-topology.png
```

---

# 📚 What I Learned

Through these practical exercises, I gained hands-on experience with:

- Domain reconnaissance
- WHOIS enumeration
- DNS enumeration
- Web technology fingerprinting
- HTTP header analysis
- WAF detection
- Network host discovery
- Zenmap/Nmap
- Network topology visualization
- Security finding documentation
- Basic risk assessment
- Security recommendations

The exercises also helped me understand that penetration testing begins with **careful information gathering** before moving into deeper technical testing.

---

# 🎯 Conclusion

The W2-PM1 and W2-PM5 practical modules provided practical experience with the reconnaissance and network discovery stages of penetration testing.

The footprinting exercise demonstrated how tools such as **WHOIS, WhatWeb, Nslookup, curl, wafw00f, and DNSRecon** can be combined to understand publicly visible domain, DNS, hosting, web technology, and security-control information.

The Zenmap exercise demonstrated how a ping scan can identify live hosts within a controlled network and how the Topology feature can help visualize the discovered environment.

Overall, the practical work strengthened my understanding of reconnaissance, network discovery, documentation, risk identification, and defensive recommendations. All activities were conducted within the authorized scope of the Networkwalks educational programme.

---

## 👤 Author

**Collins Simukoko**  
Cybersecurity Student / Intern  
**Networkwalks — Cohort B083F**

### Project

**Week 02 | W2-PM1 & W2-PM5**

---

## ⚠️ Responsible Use

This repository is intended for **authorized cybersecurity education, research, and controlled laboratory practice**.

Always obtain explicit permission before scanning or testing systems and networks that you do not own.
