---
title: "Nmap for Beginners: Your First Real Recon Tool"
description: "A hands-on introduction to Nmap — what it actually does, how to read its output, and the handful of flags that cover 90% of real scanning work."
tags:
  - nmap
  - recon
  - networking
  - beginner
  - tools
draft: false
date: 2026-07-28
---

## Why Nmap Matters

Before you exploit anything, you have to know what's there. That's the whole game in the early phase of an engagement — turning "there's a network at 10.0.0.0/24" into "there's a Windows box at .15 running SMB and RDP, and a Linux box at .22 running an outdated Apache." Nmap is the tool that makes that translation.

It's been around since 1997, it's on every pentest distro, and it shows up in almost every certification exam and CTF you'll ever touch. Learning it well early pays off for years. The good news: you can get genuinely productive with about eight flags.

> [!warning] Scan only what you own or have written permission to test
> Port scanning a network you don't have authorization for can be illegal depending on where you are, and it's a fast way to get your ISP account terminated. Practice on your own lab, on intentionally vulnerable VMs, on HTB/TryHackMe, or on `scanme.nmap.org` — a host the Nmap project explicitly maintains for people to test against.

## Installing It

Kali, Parrot, and most security distros ship with it. Otherwise:

```bash
# Debian / Ubuntu
sudo apt install nmap

# Fedora / RHEL
sudo dnf install nmap

# macOS
brew install nmap
```

Windows users can grab the installer from [nmap.org](https://nmap.org/download.html), which includes Zenmap (the GUI). I'd skip the GUI — the command line is where you'll actually live.

Verify it works:

```bash
nmap --version
```

## The Mental Model

Nmap does two things, in order:

1. **Host discovery** — which IPs in this range are actually alive?
2. **Port scanning** — on the hosts that are alive, which ports are open, and what's listening on them?

Everything else (version detection, OS fingerprinting, scripts) is built on top of step 2.

The core trick behind most scanning is the **TCP three-way handshake**. Normally a client sends `SYN`, the server replies `SYN/ACK`, and the client finishes with `ACK`. Nmap abuses the first two steps to learn things:

- Server replies `SYN/ACK` → the port is **open**
- Server replies `RST` → the port is **closed**
- No reply at all → the port is **filtered**, usually by a firewall dropping packets silently

That third state trips up beginners constantly. "Filtered" doesn't mean nothing is there — it means something is deliberately not answering you. Understanding the difference between *closed* (host is reachable, nothing listening) and *filtered* (something is blocking you) is one of the more useful skills Nmap teaches.

## Your First Scan

```bash
nmap scanme.nmap.org
```

That's it. With no flags, Nmap does a default scan: it checks whether the host is up, then scans the 1,000 most common TCP ports. Output looks roughly like this:

```
Starting Nmap 7.94 ( https://nmap.org )
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.089s latency).
Not shown: 995 closed tcp ports (reset)

PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
9929/tcp  open     nping-echo
31337/tcp open     Elite

Nmap done: 1 IP address (1 host up) scanned in 4.82 seconds
```

Three columns worth reading carefully:

- **PORT** — port number and protocol
- **STATE** — open, closed, filtered, or a combination like `open|filtered` when Nmap can't tell
- **SERVICE** — Nmap's *guess* based on a lookup table of what usually runs on that port. It's a guess, not a fact. Port 80 having "http" next to it doesn't mean HTTP is actually running there.

That last point matters more than it sounds. Confirming what's really listening requires version detection, which we'll get to.

## Specifying Targets

Nmap is flexible about what you point it at:

```bash
nmap 192.168.1.10                  # single IP
nmap 192.168.1.10 192.168.1.20     # multiple IPs
nmap 192.168.1.0/24                # whole subnet (CIDR)
nmap 192.168.1.1-50                # a range
nmap 192.168.1.*                   # wildcard, same as /24
nmap -iL targets.txt               # read targets from a file
nmap 192.168.1.0/24 --exclude 192.168.1.1   # skip the gateway
```

The `-iL` flag is worth remembering — on real engagements you'll get a scope document with a list of IPs, and feeding that in directly beats typing them out.

## Choosing Which Ports to Scan

The default 1,000 ports are the most *common* ones, not all of them. There are 65,535.

```bash
nmap -p 80 target                  # one port
nmap -p 22,80,443 target           # specific ports
nmap -p 1-1000 target              # a range
nmap -p- target                    # ALL 65,535 ports
nmap -F target                     # fast: top 100 only
nmap --top-ports 20 target         # 20 most common
```

`-p-` is the one you want when something feels off. Plenty of services get parked on weird high ports specifically so casual scans miss them — an SSH daemon on 2222, a web admin panel on 8443. A default scan walks right past those. It's slower, but on an engagement you run it.

## Finding Live Hosts Without Port Scanning

Sometimes you just want a map of what's alive:

```bash
nmap -sn 192.168.1.0/24
```

`-sn` disables port scanning entirely — it's a ping sweep. Fast, quiet, and a good first move on an unfamiliar network. The inverse is `-Pn`, which skips host discovery and scans anyway:

```bash
nmap -Pn 192.168.1.15
```

Use `-Pn` when a host is blocking pings and Nmap wrongly concludes it's down. If a scan comes back with "Host seems down" but you're fairly sure something is there, `-Pn` is your next move. It's slower, since Nmap now scans hosts that may genuinely not exist, but it stops firewalls from hiding targets from you.

## Scan Types

The three you'll actually use:

```bash
sudo nmap -sS target      # SYN scan (default when root)
nmap -sT target           # TCP connect scan
sudo nmap -sU target      # UDP scan
```

**`-sS` (SYN scan)** sends a SYN, reads the response, then sends a RST to tear the connection down before it completes. It's fast and it's the default when you run Nmap with root privileges. It's sometimes called a "stealth scan," though that name is a holdover — any modern IDS flags it immediately.

**`-sT` (connect scan)** completes the full handshake using the OS's networking stack. It's what you get without root, and it's noisier and slower, but it works when you can't send raw packets.

**`-sU` (UDP scan)** is a different animal. UDP is connectionless, so there's no handshake to read. Nmap sends a packet and waits — no response usually means open *or* filtered, and it can't always tell which. UDP scans are painfully slow. Do not run `-sU -p-` and expect to be done before tomorrow. Scan a targeted set instead:

```bash
sudo nmap -sU --top-ports 20 target
```

Don't skip UDP entirely, though. DNS (53), SNMP (161), and TFTP (69) all live there, and SNMP with a default community string has handed over more networks than I can count.

## Actually Identifying Services

This is where scanning gets useful:

```bash
nmap -sV target
```

Version detection connects to each open port and interrogates whatever answers, matching responses against a database of thousands of service signatures. Instead of a guess, you get:

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```

Now you have something to work with. Specific versions map to specific known vulnerabilities, and "Ubuntu 4ubuntu0.5" tells you the distro too. If you take one thing from this post: **`-sV` is the flag that turns a port list into intelligence.**

Related flags:

```bash
sudo nmap -O target       # OS detection via TCP/IP fingerprinting
sudo nmap -A target       # aggressive: -sV, -O, scripts, traceroute
```

`-A` is convenient but loud. It's fine in a lab or a CTF. On an engagement where you care about detection, build your scans deliberately instead.

## Timing and Speed

```bash
nmap -T4 target
```

Templates run `-T0` (paranoid) through `-T5` (insane). `-T3` is the default. `-T4` is the practical sweet spot for most local networks — noticeably faster without dropping packets and giving you bad results.

The extremes have real uses. `-T0` waits five minutes between probes and exists purely for evading detection over a long engagement. `-T5` is aggressive enough that it can miss open ports on a congested or slow network, which is worse than being slow. When results look suspiciously empty, dialing the timing *down* is a legitimate troubleshooting step.

## The Scripting Engine

NSE (Nmap Scripting Engine) extends Nmap with hundreds of Lua scripts for enumeration, vuln checks, and more.

```bash
nmap -sC target                              # default script set
nmap --script=http-title target              # one specific script
nmap --script=smb-enum-shares -p 445 target  # SMB share enumeration
nmap --script=vuln target                    # everything in the vuln category
```

`-sC` runs the "default" category — safe, generally useful scripts. You'll commonly see it combined as `-sC -sV`, or bundled inside `-A`.

Categories worth knowing: `default`, `safe`, `discovery`, `vuln`, `auth`, `brute`. Be careful with `brute` and `vuln` — some of those scripts are genuinely intrusive and can knock over fragile services. Read what a script does before you fire it at something that matters:

```bash
nmap --script-help=smb-enum-shares
```

Scripts live in `/usr/share/nmap/scripts/` on most Linux installs. Browsing that directory is a good afternoon.

## Saving Your Output

Do this every single time. You will need it later.

```bash
nmap -sV -oN scan.txt target      # normal, human-readable
nmap -sV -oG scan.gnmap target    # greppable
nmap -sV -oX scan.xml target      # XML
nmap -sV -oA scan target          # all three at once
```

`-oA` is the habit to build. Reports need evidence, retests need a baseline to compare against, and "I think port 8080 was open on that host" is not something you want to be saying three weeks later. The XML output also imports cleanly into other tools, and the greppable format makes quick one-liners easy:

```bash
grep "open" scan.gnmap | cut -d' ' -f2
```

## A Realistic Workflow

Here's how these pieces fit together on an actual internal network:

```bash
# 1. What's alive?
nmap -sn 192.168.1.0/24 -oA discovery

# 2. Full TCP sweep on the hosts you found
sudo nmap -p- -T4 192.168.1.15 -oA allports

# 3. Deep dive on the ports that came back open
sudo nmap -sC -sV -p 22,80,445,3389 192.168.1.15 -oA detailed

# 4. Don't forget UDP
sudo nmap -sU --top-ports 20 192.168.1.15 -oA udp
```

Broad and fast first, then narrow and thorough. Running `-sC -sV` against all 65,535 ports from the start wastes enormous amounts of time on ports that turn out to be closed.

## Mistakes Beginners Make

**Trusting the SERVICE column.** It's a port-number lookup, not detection. Confirm with `-sV`.

**Only scanning the default 1,000 ports.** You'll miss the interesting stuff. Run `-p-` when time allows.

**Skipping UDP.** SNMP and DNS misconfigurations are common and impactful.

**Not saving output.** Rescanning because you lost your results is a self-inflicted wound.

**Forgetting sudo.** Without root, `-sS`, `-O`, and `-sU` silently degrade or fail. If your results look strange, check whether you needed elevated privileges.

**Reading "filtered" as "nothing here."** Filtered means something is actively blocking you — which is itself a finding worth noting.

## Cheat Sheet

| Flag | What it does |
|------|-------------|
| `-sn` | Host discovery only, no port scan |
| `-Pn` | Skip host discovery, assume host is up |
| `-sS` | SYN scan (needs root, default when root) |
| `-sT` | TCP connect scan (no root needed) |
| `-sU` | UDP scan |
| `-p-` | Scan all 65,535 ports |
| `-p 80,443` | Scan specific ports |
| `-F` | Fast scan, top 100 ports |
| `-sV` | Service and version detection |
| `-O` | OS detection |
| `-A` | Aggressive: -sV, -O, scripts, traceroute |
| `-sC` | Run default NSE scripts |
| `--script=X` | Run a specific script or category |
| `-T0` to `-T5` | Timing template (slow → fast) |
| `-iL file` | Read targets from a file |
| `-oA name` | Save output in all three formats |
| `-v` / `-vv` | Increase verbosity |

## Where to Practice

- **`scanme.nmap.org`** — maintained by the Nmap project for exactly this. Don't hammer it.
- **Your own lab** — spin up Metasploitable 2, DVWA, or a stock Ubuntu VM and scan it. Best option by far, since you can change the configuration and watch the output change.
- **TryHackMe / HackTheBox** — legal targets with structured guidance.
- **Your own home network** — you own it, so you can scan it. You'll probably be unpleasantly surprised by what your IoT devices are exposing.

## Where to Go Next

Once the basics feel automatic, the natural next steps are firewall evasion (`-f`, `--source-port`, decoys), writing your own NSE scripts, and learning how Nmap output feeds into the rest of a workflow — importing XML into Metasploit or Burp, or chaining into targeted enumeration tools.

But get comfortable here first. A pentester who really understands `-sV`, `-p-`, and how to read port states is more effective than one who memorized twenty evasion flags without understanding what a filtered port is telling them.

Now go scan something you're allowed to scan.
