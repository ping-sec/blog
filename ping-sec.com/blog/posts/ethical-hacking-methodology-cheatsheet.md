# Ethical Hacking Methodology — Beginner's Cheatsheet

A phase-by-phase reference for how a penetration test actually flows. The order matters: each phase feeds the next. Rushing to "exploitation" is the most common beginner mistake, most of the real work lives in recon and enumeration.

> **Rule zero:** Everything below assumes you have **written authorization** and a defined **scope**. No signed scope = not ethical hacking, just crime. Stay in scope, keep your rules of engagement handy, and stop if you find something outside your authorization.

---

## The Phases at a Glance

| # | Phase | Goal | One-line summary |
|---|-------|------|------------------|
| 0 | Pre-Engagement | Get permission & define scope | Paperwork before packets |
| 1 | Reconnaissance | Gather info | Learn everything, touch nothing (passive) → light touch (active) |
| 2 | Scanning & Enumeration | Map the attack surface | Find ports, services, versions, users, shares |
| 3 | Vulnerability Analysis | Identify weaknesses | Match what you found to known flaws |
| 4 | Exploitation | Gain access | Prove the vuln is real |
| 5 | Post-Exploitation | Escalate & expand | Priv-esc, loot, pivot, persist |
| 6 | Reporting | Communicate findings | The deliverable that actually matters |

Common frameworks that formalize this: **PTES** (Penetration Testing Execution Standard), **OSSTMM**, **NIST SP 800-115**, and the **Cyber Kill Chain**. They differ in labels, not spirit.

---

## Phase 0: Pre-Engagement

The unglamorous phase that keeps you employed and out of jail.

- **Scope**: exactly which IPs, domains, apps, and accounts are in bounds. Everything else is off-limits.
- **Rules of Engagement (RoE)**: allowed hours, allowed techniques (is social engineering ok? DoS? password spraying against prod?), and who to contact.
- **Authorization**: signed, dated, from someone with authority to grant it. Keep a copy reachable during the test.
- **Emergency contacts**: who to call if you knock something over or find evidence of a prior breach.
- **Goals**: black box / grey box / white box? Assumed-breach? Compliance-driven?

---

## Phase 1: Reconnaissance (Information Gathering)

**Passive** (no packets to the target) → **Active** (light interaction).

**Passive / OSINT**
- `whois example.com` - registration, name servers
- Certificate transparency: `crt.sh` for subdomains
- Google dorking: `site:example.com filetype:pdf`, `intitle:index.of`
- DNS: `dig`, `nslookup`, `dnsrecon`, `subfinder`, `amass`
- People/tech: LinkedIn, job postings, GitHub leaks, `theHarvester`
- Breach data awareness (know what's already public)

**Active recon**
- DNS zone transfer attempt: `dig axfr @ns1.example.com example.com`
- Subdomain brute force: `ffuf`, `gobuster dns`
- Banner/tech fingerprinting: `whatweb`, Wappalyzer

**Output:** a target map: domains, subdomains, IP ranges, tech stack, people, exposed assets.

---

## Phase 2: Scanning & Enumeration

Enumeration is where tests are won. Be thorough, take notes per host.

**Host discovery & port scanning — `nmap`**
```bash
# Quick full-port sweep
nmap -p- --min-rate 1000 -T4 <target> -oA scans/allports

# Service + version + default scripts on found ports
nmap -sC -sV -p <ports> <target> -oA scans/detailed
```

**Per-service enumeration**
- **Web (80/443/8080):** `gobuster`/`ffuf`/`feroxbuster` for dirs & files, `nikto`, Burp Suite for manual testing, check `robots.txt`, source, headers
- **SMB (139/445):** `smbclient -L`, `enum4linux-ng`, `nxc smb` (NetExec) for shares/users/null sessions
- **DNS (53):** `dnsrecon`
- **FTP (21):** anonymous login check
- **SNMP (161):** `snmpwalk`, `onesixtyone`
- **LDAP/AD (389/88):** `nxc ldap`, `bloodhound-python` for the AD graph

**Output:** open ports, running services with versions, usernames, shares, endpoints, and every juicy detail worth chasing.

---

## Phase 3: Vulnerability Analysis

Turn enumeration data into a prioritized list of "here's what might break."

- Map service versions to known CVEs (`searchsploit <service>`, vendor advisories)
- Run scanners where appropriate (Nessus, OpenVAS) — but **verify manually**; scanners produce false positives
- Look for misconfigurations, not just CVEs: default creds, weak permissions, exposed admin panels, verbose errors
- Web: think OWASP Top 10: injection, broken access control (IDOR), auth flaws, SSRF, XSS
- Prioritize by **exploitability × impact**, not just CVSS score

**Output:** a ranked shortlist of candidate weaknesses to test.

---

## Phase 4: Exploitation

Prove the vulnerability is real. Aim for the lowest-noise path that demonstrates impact.

- Manual first, tooling second, understand *why* it works
- Public exploits: `searchsploit`, Exploit-DB, GitHub PoCs — **read the code before running it**
- Frameworks: Metasploit (`msfconsole`) for known modules
- Web: exploit findings by hand via Burp Repeater; validate injection, access-control, upload flaws
- Password attacks: `hydra`, `john`, `hashcat` (spraying > brute force to avoid lockouts)

**Discipline:**
- Take a screenshot / capture proof for each successful step
- Note exact request/command used (you'll need it for the report and remediation retest)
- Don't destroy data. Don't exfiltrate real customer data — prove access, don't loot indiscriminately

**Output:** confirmed foothold(s) with evidence.

---

## Phase 5: Post-Exploitation

Access is the start, not the finish. Show the *business impact*.

- **Situational awareness:** `whoami`, `id`, `hostname`, network config, running processes
- **Privilege escalation:**
  - Linux: `linpeas.sh`, sudo rights, SUID binaries, cron jobs, kernel version
  - Windows: `winpeas`, unquoted service paths, token privileges, stored creds
- **Credential harvesting:** dump/collect creds where in scope (e.g., `mimikatz`, SAM, config files)
- **Lateral movement / pivoting:** reach new hosts through the compromised one
- **Active Directory chains:** Kerberoasting, AS-REP roasting, DCSync, ACL abuse — map with BloodHound first
- **Persistence:** only if the RoE allows it, and always documented so it can be cleaned up

**Cleanup:** track every change you make (accounts, files, shells) so nothing is left behind.

**Output:** demonstrated depth: how far an attacker could actually get.

---

## Phase 6: Reporting

The report is the product. A brilliant exploit chain nobody can remediate is worthless.

**Structure**
- **Executive summary** — plain-language risk for leadership, no jargon
- **Methodology** — scope, timeline, approach
- **Findings** — each with:
  - Title & severity (CVSS + business context)
  - Description & affected assets
  - **Steps to reproduce** (clear enough for the client to replay)
  - Evidence (screenshots, requests)
  - **Impact** and **remediation** guidance
- **Appendices** — raw data, tool output

**Good-finding checklist:** Can the client reproduce it? Do they understand the risk? Do they know how to fix it? Can you retest the fix later?

---

## The Beginner Mindset

- **80% of the work is recon + enumeration.** If you're stuck, you haven't enumerated enough.
- **Take notes constantly** (Obsidian, CherryTree, whatever) — per host, timestamped.
- **Understand tools, don't just run them.** Know what a command sends on the wire.
- **Stay in scope. Always.**
- **Practice legally:** TryHackMe, Hack The Box, PortSwigger Web Security Academy, VulnHub, and your own lab.

---

## Quick Tool Reference

| Category | Tools |
|----------|-------|
| Recon/OSINT | `whois`, `dig`, `theHarvester`, `amass`, `subfinder`, `crt.sh` |
| Scanning | `nmap`, `masscan`, `rustscan` |
| Web enum | `gobuster`, `ffuf`, `feroxbuster`, `nikto`, `whatweb`, Burp Suite |
| SMB/AD | `enum4linux-ng`, NetExec (`nxc`), BloodHound, Impacket |
| Vuln analysis | `searchsploit`, Nessus, OpenVAS |
| Exploitation | Metasploit, `hydra`, `hashcat`, `john` |
| Priv-esc | `linpeas`, `winpeas`, `mimikatz` |

---

*Use only against systems you own or are explicitly authorized to test.*
