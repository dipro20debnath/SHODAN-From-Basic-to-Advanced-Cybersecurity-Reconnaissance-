## 🕵️‍♂️ SHODAN — From Basic to Advanced (Cybersecurity Reconnaissance)

### 📘 Overview

**Shodan** is the world’s first search engine for internet-connected devices.
While Google indexes *websites*, Shodan indexes *servers, IoT devices, routers, cameras, industrial control systems, and exposed services*.

> 🔐 **Use it ethically!**
> Shodan is powerful — always operate *within scope* (e.g., bug bounty, blue team asset discovery, research).
> Unauthorized scanning, exploitation, or accessing systems is **illegal**.

---

## 🧩 Table of Contents

1. [What is Shodan?](#-what-is-shodan)
2. [How Shodan Works](#-how-shodan-works)
3. [Getting Started](#-getting-started)
4. [Basic Usage & Queries](#-basic-usage--queries)
5. [Filters & Search Operators](#-filters--search-operators)
6. [Intermediate: Recon & Enumeration](#-intermediate-recon--enumeration)
7. [Advanced: Automation & API](#-advanced-automation--api)
8. [Defensive Usage](#-defensive-usage)
9. [Pro Tips](#-pro-tips)
10. [Resources & Labs](#-resources--labs)

---

## 🧠 What is Shodan?

> Shodan = “Search Engine for the Internet of Everything.”

It continuously scans the entire internet, recording information banners from open ports.
Each entry reveals:

* IP address
* Open ports
* Banner information
* Service version
* Geo-location
* Organization / ASN / ISP

---

## ⚙️ How Shodan Works

1. Shodan scanners send requests to every IP on common ports (80, 443, 22, 21, 25, 8080, etc.)
2. It stores the **banner** returned by each service.
3. You can query that data using search filters (like `port:22 country:BD`).

---

## 🚀 Getting Started

### 🪪 Create an Account

* Go to: [https://www.shodan.io](https://www.shodan.io)
* Sign up → Get a free account.
* Optional: Upgrade to get API key, advanced filters, and more results.

### 🧰 Install Shodan CLI

```bash
pip install shodan
shodan init <YOUR_API_KEY>
```

Check version:

```bash
shodan version
```

---

## 🔍 Basic Usage & Queries

### Example Queries

| Purpose             | Query Example          |
| ------------------- | ---------------------- |
| Find HTTP servers   | `port:80 http`         |
| Find HTTPS servers  | `port:443 ssl`         |
| Find FTP servers    | `port:21 ftp`          |
| Find open RDP ports | `port:3389`            |
| Search by country   | `country:BD port:22`   |
| Find a specific org | `org:"Google"`         |
| Find Apache servers | `product:"Apache"`     |
| Search by domain    | `hostname:example.com` |

### CLI Examples

```bash
shodan search apache
shodan search "nginx country:US"
shodan stats nginx country:BD
```

---

## 🧭 Filters & Search Operators

| Filter          | Description       | Example                             |
| --------------- | ----------------- | ----------------------------------- |
| `port:`         | Specific port     | `port:22`                           |
| `country:`      | Country code      | `country:IN`                        |
| `org:`          | Organization      | `org:"Amazon"`                      |
| `asn:`          | Autonomous System | `asn:AS15169`                       |
| `product:`      | Software/service  | `product:"nginx"`                   |
| `version:`      | Software version  | `product:"Apache" version:"2.4.49"` |
| `before/after:` | Date filter       | `after:2023-01-01`                  |
| `os:`           | Operating System  | `os:"Windows 10"`                   |
| `city:`         | City filter       | `city:"Dhaka"`                      |

---

## 🕵️ Intermediate: Recon & Enumeration

### 1️⃣ Find exposed dev servers

```bash
product:"nginx" "X-Powered-By: Express"
```

### 2️⃣ Find vulnerable software

```bash
vuln:CVE-2021-44228  # Log4Shell
vuln:CVE-2020-0601   # CurveBall
```

### 3️⃣ Find webcams / IoT devices

```bash
port:554 has_screenshot:true
```

### 4️⃣ Search by ASN (Company’s network)

```bash
asn:AS15169
```

### 5️⃣ Identify CDN / Cloud assets

```bash
org:"Cloudflare" hostname:"example.com"
```

---

## 🧑‍💻 Advanced: Automation & API

### Python Example — Automate Recon

```python
import shodan

API_KEY = "YOUR_API_KEY"
api = shodan.Shodan(API_KEY)

query = 'apache country:BD'
result = api.search(query)

print(f"Results found: {result['total']}")
for service in result['matches']:
    print(f"IP: {service['ip_str']} | Port: {service['port']} | Org: {service.get('org')}")
```

### Export to CSV

```bash
shodan download apache_servers "product:Apache country:US"
shodan convert apache_servers.json.gz csv apache_servers.csv
```

### Integrate with Recon Tools

Combine Shodan + Amass + Nmap:

```bash
subfinder -d example.com -o subs.txt
httpx -l subs.txt -o live.txt
for i in $(cat live.txt); do
    ip=$(dig +short $i)
    shodan host $ip
done
```

---

## 🛡️ Defensive Usage

Cybersecurity experts can use Shodan to:

* Track exposed internal assets
* Detect open admin panels
* Identify outdated software versions
* Receive alerts for newly exposed hosts

Set up **Shodan Monitor**:

```bash
https://monitor.shodan.io
```

Use it to automatically watch your organization’s IP space.

---

## 💣 Pro Tips

⚙️ **Pro Tip #1:** Combine filters smartly

```bash
product:"nginx" port:80 country:BD org:"ISPName"
```

⚙️ **Pro Tip #2:** Use `ssl:` filter to find SSL cert leaks

```bash
ssl:"example.com"
```

⚙️ **Pro Tip #3:** Track CVEs directly

```bash
vuln:CVE-2022-1388
```

⚙️ **Pro Tip #4:** Don’t use Shodan for active exploitation — Use it for mapping only.

⚙️ **Pro Tip #5:** Combine with `Censys`, `ZoomEye`, or `BinaryEdge` for more global coverage.

⚙️ **Pro Tip #6:** Always log your findings with timestamp & scope.

---

## 🎯 Pro Commands Cheat Sheet

```bash
# Show IP info
shodan host <IP>

# Search keyword
shodan search "nginx port:80"

# Save results
shodan download nginx_servers "nginx"

# Convert output
shodan convert nginx_servers.json.gz csv nginx_servers.csv

# Count stats
shodan stats nginx country:US

# Get all my account info
shodan info
```

---

## 🧠 Resources & Labs

### 🔹 Official

* [Shodan.io](https://www.shodan.io)
* [Shodan API Docs](https://developer.shodan.io/)
* [Shodan Monitor](https://monitor.shodan.io)

### 🔹 Practice Labs

* [TryHackMe — Shodan Room](https://tryhackme.com/room/shodan)
* [TryHackMe — Passive Recon](https://tryhackme.com/room/passiverecon)
* [HackTheBox Academy — Recon Modules](https://academy.hackthebox.com/)
* [CISA Shodan Guide (Defensive Use)](https://www.cisa.gov/)

---

## 🧩 License

This repository is for **educational and ethical cybersecurity research only.**
Unauthorized scanning or exploitation of systems is **strictly prohibited**.

---

## 🧢 Credits

Created by [**Gobindo Debnath Dipro**](https://github.com/dipro20debnath)
For ethical hackers, bug bounty hunters & security professionals 🕶️

