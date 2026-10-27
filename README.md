
# Reconnaissance Notes

A quick-reference cheat sheet for learning reconnaissance during authorized cybersecurity labs and security assessments.

## Table of Contents

1. [Subdomain Enumeration](#1-subdomain-enumeration)
2. [DNS Reconnaissance](#2-dns-reconnaissance)
3. [TCP and Port Enumeration](#3-tcp-and-port-enumeration)
4. [Directory Enumeration](#4-directory-enumeration)
5. [Employee and Email Enumeration](#5-employee-and-email-enumeration)
6. [Contact and Phone Number Research](#6-contact-and-phone-number-research)
7. [Mail Server Enumeration](#7-mail-server-enumeration)
8. [SNMP Enumeration](#8-snmp-enumeration)
9. [SMB Enumeration](#9-smb-enumeration)
10. [Quick Revision](#10-quick-revision)
11. [Important Notes](#important-notes)
12. [Further Learning](#further-learning)

---

## 1. Subdomain Enumeration

**Purpose:** Discover subdomains associated with a domain.

### Subfinder

A tool for discovering subdomains using multiple sources.

```bash
subfinder -d example.com
```

- `-d`: Specifies the target domain.
- `example.com`: Replace with the authorized target domain.

### Sublist3r

A tool for subdomain enumeration using search engines and other sources.

- Project: [Sublist3r on GitHub](https://github.com/aboul3la/Sublist3r)

**Remember:** Subdomains may reveal additional websites, applications, and exposed services.

---

## 2. DNS Reconnaissance

**Purpose:** Identify DNS records, name servers, mail servers, and other information about a domain.

### `host`

Perform DNS lookups.

```bash
host example.com
```

Look up mail exchange (MX) records:

```bash
host -t mx example.com
```

- `-t`: Specifies the DNS record type.
- `mx`: Requests mail exchange records.

Other useful record types:

| Record | Purpose |
|---|---|
| `A` | IPv4 address |
| `AAAA` | IPv6 address |
| `NS` | Name servers |
| `MX` | Mail servers |
| `TXT` | Text records, often used for domain verification and email security |
| `CNAME` | Canonical name (alias) |

Examples:

```bash
host -t ns example.com
host -t txt example.com
```

### `dnsrecon`

A DNS reconnaissance tool that can query and enumerate DNS information.

```bash
dnsrecon -d example.com
```

- `-d`: Specifies the target domain.

### `nslookup`

A DNS lookup utility available on Windows and other operating systems.

```bash
nslookup example.com
```

Query a specific record type:

```text
nslookup
set type=MX
example.com
```

### `dig`

A DNS lookup tool that displays detailed query results, including DNS headers, flags, answers, and other sections when available.

```bash
dig example.com
```

Query specific record types:

```bash
dig example.com A
dig example.com MX
dig example.com NS
dig example.com TXT
```

### `dnsenum`

A DNS enumeration tool that gathers DNS information and can perform additional enumeration, such as subdomain discovery and DNS zone transfer checks.

```bash
dnsenum example.com
```

### `tracert` / `traceroute`

These commands help identify the network path packets take toward a destination. They are network path discovery tools, not DNS enumeration tools.

**Windows:**

```cmd
tracert example.com
```

**Linux:**

```bash
traceroute example.com
```

**Remember:** DNS enumeration focuses on domain records; traceroute focuses on the network path.

---

## 3. TCP and Port Enumeration

**Purpose:** Discover accessible TCP services and identify which ports may be open.

### Netcat (`nc`)

A networking utility for connecting to TCP/UDP services and testing connectivity.

Check a TCP port:

```bash
nc -vz TARGET_IP 80
```

Example for HTTPS:

```bash
nc -vz TARGET_IP 443
```

- `-v`: Verbose output.
- `-z`: Checks connectivity without sending application data.
- `TARGET_IP`: Replace with the authorized target IP.

### Nmap

A network scanning and service enumeration tool.

Scan a target:

```bash
nmap TARGET_IP
```

Scan selected ports:

```bash
nmap -p 22,25,80,443 TARGET_IP
```

Detect service versions:

```bash
nmap -sV TARGET_IP
```

- `-p`: Specifies ports.
- `-sV`: Attempts to identify service versions.

**Remember:** An open port indicates a reachable service, not necessarily a vulnerability.

---

## 4. Directory Enumeration

**Purpose:** Discover directories and files exposed by a web server.

### Dirb

A web content scanner that tests wordlists against a web server to find potentially accessible paths.

```bash
dirb https://example.com/
```

Dirb uses a wordlist to test paths such as `/admin`, `/uploads`, or `/backup`.

**Remember:** Only scan websites for which you have authorization.

---

## 5. Employee and Email Enumeration

**Purpose:** Find publicly available information about an organization's employees, email addresses, and online presence.

### theHarvester

A tool that collects publicly available information from supported search engines and data sources.

```bash
theHarvester -d example.com -b crtsh
```

- `-d`: Specifies the target domain.
- `-b`: Specifies a data source. Supported sources depend on the installed version.

Check available options:

```bash
theHarvester -h
```

Potential findings:

- Public email addresses
- Employee names
- Subdomains and hostnames
- Publicly indexed information

Use collected information for authorized security assessments and avoid targeting individuals.

---

## 6. Contact and Phone Number Research

### SignalHire

A people and professional contact information service.

- Website: [SignalHire](https://www.signalhire.com/)

**Purpose:** Research professional profiles and contact information, subject to the service's access rules and applicable privacy laws.

**Remember:** Treat personal contact information as sensitive. Collect only what is necessary and authorized.

---

## 7. Mail Server Enumeration

**Purpose:** Identify mail services and, where permitted, test which SMTP features are exposed.

### Nmap SMTP User Enumeration

```bash
nmap -p 25 --script smtp-enum-users TARGET_IP
```

- `-p 25`: Scans TCP port 25, commonly used for SMTP.
- `--script smtp-enum-users`: Requests Nmap's SMTP user enumeration script.
- `TARGET_IP`: Replace with the authorized target IP.

**Important:** Script availability and server behavior vary. SMTP user enumeration may be disabled, unsupported, or blocked by the server.

### Netcat SMTP Connection

```bash
nc -nv TARGET_IP 25
```

This attempts a TCP connection to the SMTP service. If the server permits the connection, it may display an SMTP greeting.

- `-n`: Disables hostname resolution.
- `-v`: Enables verbose output.

A successful connection confirms reachability, not the existence of valid user accounts.

---

## 8. SNMP Enumeration

**Purpose:** Inspect information exposed by the Simple Network Management Protocol (SNMP), commonly used for network monitoring and device management.

SNMP may reveal device information, interface details, system descriptions, and other management data depending on configuration and access permissions.

### `snmpwalk`

Queries SNMP objects and walks a portion of the Management Information Base (MIB).

General syntax:

```bash
snmpwalk -v2c -c COMMUNITY TARGET_IP
```

- `-v2c`: Uses SNMP version 2c.
- `-c`: Specifies the community string.
- `TARGET_IP`: The authorized target device.

Use only credentials and community strings explicitly provided for your lab or assessment.

### `snmp-check`

A tool for collecting information from SNMP-enabled devices.

Check the installed tool's supported options:

```bash
snmp-check -h
```

**Remember:** SNMP exposure can reveal sensitive infrastructure information when access controls are weak.

---

## 9. SMB Enumeration

**Purpose:** Discover information about Windows file sharing, SMB services, shares, and domain environments.

SMB commonly uses TCP port 445. Some environments also use NetBIOS-related ports.

### `smbclient`

A command-line SMB client used to list and access network shares when permitted.

List shares:

```bash
smbclient -L //TARGET_IP -U USERNAME
```

Connect to a specific share:

```bash
smbclient //TARGET_IP/SHARE_NAME -U USERNAME
```

### `enum4linux`

A tool that gathers information from Windows and Samba systems through supported SMB and related enumeration methods.

```bash
enum4linux -h
```

Use the help output to identify the syntax supported by your installed version.

### CrackMapExec / NetExec

CrackMapExec is an SMB and Windows network assessment framework. Its successor, NetExec, is used in many modern environments.

NetExec's general SMB syntax:

```bash
nxc smb TARGET_IP
```

For environments that still use CrackMapExec, consult the installed version's help because commands and options may differ.

- NetExec project: [GitHub](https://github.com/Pennyw0rth/NetExec)

### `smbmap`

A tool for inspecting SMB shares and assessing accessible permissions.

```bash
smbmap -H TARGET_IP
```

- `-H`: Specifies the target host.

**Remember:** SMB enumeration helps identify exposed shares and permissions. Accessing files or testing credentials should be limited to explicitly authorized systems.

---

## 10. Quick Revision

| Topic | Tool | Main Purpose |
|---|---|---|
| Subdomains | Subfinder | Discover subdomains |
| Subdomains | Sublist3r | Search for subdomains |
| DNS | `host` | Query DNS records |
| DNS | `dnsrecon` | DNS reconnaissance |
| DNS | `nslookup` | DNS lookups |
| DNS | `dig` | Detailed DNS queries |
| DNS | `dnsenum` | DNS enumeration |
| Network path | `tracert` / `traceroute` | Trace network routes |
| TCP connectivity | Netcat (`nc`) | Test service connectivity |
| Port scanning | Nmap | Discover ports and services |
| Web content | Dirb | Discover web paths |
| Public information | theHarvester | Collect public domain-related information |
| Professional contacts | SignalHire | Research professional contact information |
| Mail services | Nmap / Netcat | Inspect SMTP exposure |
| SNMP | `snmpwalk` | Query SNMP objects |
| SNMP | `snmp-check` | Gather SNMP information |
| SMB | `smbclient` | Interact with SMB shares |
| SMB | `enum4linux` | Enumerate Windows/Samba information |
| SMB | CrackMapExec / NetExec | Assess SMB and Windows services |
| SMB | `smbmap` | Inspect SMB shares and permissions |

---

## Important Notes

- Replace example domains, IP addresses, usernames, and share names with values from your authorized lab.
- Tool options and data-source integrations can change between versions.
- A discovered hostname, open port, or exposed service is a finding to investigate, not proof of a vulnerability.
- Prefer passive collection where appropriate; active scanning can generate traffic and trigger security monitoring.
- Do not publish private assessment results, credentials, tokens, or personal information in a public repository.

---

## Further Learning

- [ProjectDiscovery Subfinder](https://github.com/projectdiscovery/subfinder)
- [Nmap Documentation](https://nmap.org/book/man.html)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [theHarvester](https://github.com/laramies/theHarvester)
- [Sublist3r](https://github.com/aboul3la/Sublist3r)
