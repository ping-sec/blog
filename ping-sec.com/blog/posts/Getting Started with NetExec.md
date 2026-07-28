---
title: "Getting Started with NetExec (nxc)"
description: "A beginner-friendly walkthrough of NetExec, the Swiss Army knife for network and Active Directory assessments."
tags:
  - active-directory
  - pentesting
  - tools
  - netexec
draft: false
date: 2026-07-28
---

If you've spent any time reading Active Directory pentest writeups, watching HTB videos, or working through a course, you've almost certainly seen a tool called **NetExec** (or `nxc`) show up over and over. It's one of those tools that quietly does about a dozen jobs, and once it clicks, it becomes the first thing you reach for on nearly every internal engagement.

This post is a getting-started guide. No prior experience with the tool assumed — just a working Kali box (or any Linux host) and a lab to point it at. By the end you'll understand what NetExec is, how to install it, how to read its output, and how to run your first handful of commands without feeling like you're copy-pasting magic incantations.

## What NetExec actually is

NetExec is a network service exploitation tool built to automate security assessments of large networks. In plain terms: you feed it a target (or a whole subnet), some credentials, and a protocol, and it tells you what you can see and do across every host at once.

Here's the piece of history worth knowing. NetExec is the direct continuation of **CrackMapExec** (CME), a legendary tool maintained for years by mpgn. When mpgn stepped back and CME development wound down, the community picked up the torch and forked it into NetExec under active new maintainership. So if you find an old blog post or cheatsheet that references `crackmapexec` or `cme`, mentally swap in `nxc` — the concepts carry over almost one-to-one, and the new project has moved well past where CME left off.

A couple of things make it worth learning:

- **It's protocol-oriented.** NetExec speaks SMB, LDAP, WinRM, MSSQL, SSH, FTP, RDP, WMI, NFS, and VNC. Each protocol is its own mode with its own relevant flags.
- **It scales.** Point it at a `/24` and it'll sweep every live host, checking credentials, enumerating shares, and flagging admin access in one pass.
- **It's modular.** Beyond the built-in flags, a large library of modules handles specialized tasks — dumping LSASS, extracting gMSA passwords, checking for specific CVEs, and much more.
- **It's actively developed.** The project ships regular releases with new modules and fixes. As of this writing the current version is in the 1.5.x line, and updates land frequently.

If CME was the original "spray creds across a network and see what sticks" tool, NetExec is that idea grown up, cleaned up, and considerably more capable.

## Installing NetExec

There are two paths most people take, and I'd nudge you toward the first.

### Option 1: pipx from source (recommended)

The maintainers' recommended install is straight from the GitHub repo using `pipx`, which keeps the tool and its dependencies isolated in their own environment. This is also the way to stay on the bleeding edge, which matters because new modules land often.

```bash
sudo apt install pipx git
pipx ensurepath
pipx install git+https://github.com/Pennyw0rth/NetExec
```

That's it. After `pipx ensurepath`, you may need to restart your shell so the `nxc` binary is on your `PATH`.

To update later:

```bash
pipx upgrade netexec
```

### Option 2: apt on Kali

If you're on Kali and just want it available quickly, it's in the repos:

```bash
sudo apt install netexec
```

The tradeoff is that the packaged version can lag behind the GitHub source, so you'll miss the newest modules and fixes. For a lab or for learning, that's totally fine. For real work where you want the latest capabilities, the pipx-from-source route wins.

> **A quick note on tab completion.** NetExec supports shell autocompletion when installed via pipx. It's worth setting up early — the tool has a *lot* of flags, and tab-completing them saves real time. The wiki has a short page on enabling it.

Once installed, confirm it's working:

```bash
nxc --version
nxc -h
```

You'll also notice a second binary, `nxcdb` — that's the database navigator, which we'll touch on near the end.

## The command structure

Every NetExec command follows the same basic shape:

```
nxc <protocol> <target> <options>
```

So the very first decision is always *which protocol*. For AD work you'll live mostly in `smb` and `ldap`, with `winrm` and `mssql` close behind. A minimal command looks like this:

```bash
nxc smb 192.168.1.10
```

Run against a Windows host with no credentials, that single command already does useful work: it fingerprints the target, pulling the hostname, domain, OS version, and SMB signing status. That last detail — whether SMB signing is required — is exactly the kind of thing you note early, because hosts *without* it are candidates for relay attacks later.

### Targets can be flexible

The target field isn't limited to a single IP. NetExec accepts:

- A single IP: `192.168.1.10`
- A CIDR range: `192.168.1.0/24`
- A hostname or FQDN: `dc01.corp.local`
- A file of targets: `targets.txt`

That range support is the whole point of the tool. Sweeping an entire subnet with one command is where NetExec earns its keep.

## Reading the output

This trips up newcomers, so let's slow down here. NetExec's output is color-coded and shorthand-heavy, and once you learn to read it, you can scan a hundred lines and instantly spot what matters.

The two markers you'll see constantly:

- **`[*]`** — informational. Host details, banners, protocol info.
- **`[+]`** — success, shown in green. A credential worked, an action succeeded.
- **`[-]`** — failure, shown in red. Login rejected, access denied.

And the one everybody's chasing:

- **`(Pwn3d!)`** — this appears when the credentials you supplied have **administrative** access on that host. When you spray creds across a subnet and see `(Pwn3d!)` light up next to a machine, that's your foothold for command execution, credential dumping, and lateral movement.

So a line like this:

```
SMB  192.168.1.10  445  DC01  [+] corp.local\jsmith:Summer2026! (Pwn3d!)
```

...is telling you, at a glance: over SMB, on host DC01, the domain user `jsmith` authenticated successfully **and** has admin rights on that box. That single line is often the difference between "still enumerating" and "we're in."

## Your first real commands

Let's walk through the workflow you'll actually use, building from unauthenticated enumeration up to credentialed access. Assume a lab domain `corp.local` with a DC at `192.168.1.10`.

### 1. Fingerprint the target

```bash
nxc smb 192.168.1.10
```

Grab the hostname, domain, OS, and signing status. Do this first, always.

### 2. Try a null / guest session

Sometimes you get lucky and a host allows anonymous access:

```bash
nxc smb 192.168.1.10 -u '' -p ''
```

If null sessions are permitted, you may be able to enumerate users, shares, or the password policy without any real credentials — a surprisingly common finding.

### 3. Enumerate shares

Once you have *any* working credentials (or a permissive null session), see what file shares exist and what you can reach:

```bash
nxc smb 192.168.1.10 -u jsmith -p 'Summer2026!' --shares
```

NetExec lists each share alongside your READ/WRITE permissions. Readable shares are a classic hiding spot for scripts, config files, and — more often than they should be — passwords.

### 4. Enumerate domain users

Point it at the DC to pull the domain user list:

```bash
nxc smb 192.168.1.10 -u jsmith -p 'Summer2026!' --users
```

This gives you a roster of accounts, which feeds directly into password spraying and Kerberos attacks.

### 5. Spray credentials across the network

Here's the flagship move. Take one credential and test it against every host in a range:

```bash
nxc smb 192.168.1.0/24 -u jsmith -p 'Summer2026!'
```

Watch for those `(Pwn3d!)` markers. You can just as easily supply files for both users and passwords:

```bash
nxc smb 192.168.1.0/24 -u users.txt -p passwords.txt
```

> **Slow down and think before you spray.** Testing many passwords against many accounts can trip account lockout policies. Know the domain's lockout threshold first (`--pass-pol` will tell you), and space your attempts accordingly. This is the difference between a clean assessment and locking out half the company's accounts.

### 6. Pass-the-hash

NetExec doesn't require a plaintext password. If you've recovered an NTLM hash, hand it over with `-H`:

```bash
nxc smb 192.168.1.10 -u administrator -H <NTLM_hash>
```

The same is true across protocols — this is central to lateral movement once you start dumping credentials.

## Stepping up to LDAP

Once you've got a foothold in SMB, the `ldap` protocol opens up the Active Directory enumeration side of the house. Same command shape, different intel:

```bash
nxc ldap 192.168.1.10 -u jsmith -p 'Summer2026!' --users
```

From here you can pull user descriptions (occasionally stuffed with passwords), enumerate groups and trusts, and run higher-value attacks. Two you'll meet early:

- **ASREPRoasting** — find accounts that don't require Kerberos pre-authentication and grab crackable hashes.
- **Kerberoasting** — request service tickets for accounts with an SPN and crack them offline.

```bash
nxc ldap 192.168.1.10 -u jsmith -p 'Summer2026!' --asreproast output.txt
nxc ldap 192.168.1.10 -u jsmith -p 'Summer2026!' --kerberoast output.txt
```

Both of these deserve their own posts (and one of them already has one on this blog), but it's worth seeing that NetExec puts them one flag away.

## Modules: where it gets deep

Beyond built-in flags, NetExec has an extensive module system for specialized tasks. List everything available for a protocol with `-L`:

```bash
nxc smb -L
```

You'll see modules for dumping credentials from all sorts of places, checking specific vulnerabilities, extracting secrets from installed software, and more. Run one with `-M`:

```bash
nxc smb 192.168.1.10 -u administrator -p 'Password123!' -M lsassy
```

When a module needs extra input, you pass options with `-o KEY=value`. Get into the habit of running `nxc <protocol> -L` whenever you're on a new engagement — the module list grows with nearly every release, and there's a decent chance someone has already automated exactly the thing you're about to do by hand.

## The database: nxcdb

Everything NetExec discovers — hosts, credentials, shares — gets stored automatically in a local database, organized into **workspaces**. This is genuinely useful on a real engagement, because you accumulate a queryable record of everything that worked as you go.

Create a workspace for your engagement:

```bash
nxc -nw create-workspace clientname
```

Then explore what you've collected with the database navigator:

```bash
nxcdb
```

Inside, you can list hosts, pull up cracked credentials, and see which accounts have admin where. When you're three days into an assessment and can't remember which of forty machines you owned, this is the tool that saves you.

## Where to go from here

You now have the mental model: pick a protocol, aim at a target, read the output, and escalate from unauthenticated enumeration to credentialed access to admin. That loop covers a huge fraction of what internal assessments actually involve.

A few suggestions for building real fluency:

- **Practice in a lab, not in production.** The official [NetExec Lab](https://github.com/Pennyw0rth/NetExec-Lab) is purpose-built for this, and it's a fantastic sandbox to break things safely.
- **Read the [official wiki](https://www.netexec.wiki/).** It's organized by protocol and genuinely well written — bookmark it.
- **Keep it updated.** Because new modules ship constantly, `pipx upgrade netexec` should be a regular habit.
- **Learn the theory alongside the tool.** NetExec makes Kerberoasting a one-liner, but you'll get far more out of it if you understand *why* the attack works. The tool is the easy part; the concepts are what make you good.

NetExec rewards the time you put into it. Start with `smb` fingerprinting and credential spraying, get comfortable reading `(Pwn3d!)`, then branch out into LDAP and the module library. Before long it'll be the first tab you open on every internal.

---

*Everything here is meant for authorized testing in environments you own or have explicit written permission to assess. Point it at your own lab, keep learning, and stay curious.*
