Introduction

One of the quieter truths about Active Directory: You don't need to be an admin to start collecting crackable credentials. You just have to be _anyone_ on the domain. Kerberoasting is the attack that actually proves this. With just a valid domain account, an attacked can pull encrypted material tied to privileged service accounts and grind it out offline, out of sight of the Domain Controller. It's cheap, reliable, and it's been a red-team staple for _years_. This is because the underlying design works exactly as _intended_, the weakness lives in how organizations configure their service accounts, not in Kerberos itself.

In this post, I break down what Kerberoasting actually is, the specific misconfigurations that make it possible, how to defend against it, and what to watch for on the detection side.

The Mechanics
Kerberoasting abuses the normal way Kerberos issues service tickets in Active Directory.

Every service account meant to be reached over Kerberos has a Service Principal Name (SPN) registered to it. When any authenticated user wants to access that service, they ask the Key Distribution Center (KDC — the domain controller) for a service ticket (TGS) for that SPN. Here's the catch: part of that ticket is encrypted with a key derived from the service account's password. And the KDC doesn't check whether the requester has any business accessing the service — if you're authenticated and you ask, you get the ticket.

That's the entire opening. The attack flow looks like this:

Enumerate SPNs. An authenticated user queries the domain for accounts that have SPNs set — these are the roastable targets.
Request TGS tickets. For each target SPN, request a service ticket from the KDC. This is a completely legitimate Kerberos operation.
Extract the encrypted blob. The portion of the ticket encrypted with the service account's password-derived key is pulled out.
Crack offline. Take that blob to a separate machine and run a dictionary or brute-force attack against it with Hashcat or John. Because the cracking happens offline, there are no failed-logon events and no lockouts on the domain controller.
Use the password. Once cracked, the plaintext password is used to authenticate as the service account — and if that account is over-privileged, the domain may fall with it.

The reason this is so dangerous is the asymmetry: requesting the ticket is silent and looks normal, and all the noisy work happens somewhere the defenders can't see.

A minimal engagement toolkit looks like this:

bash
# Enumerate SPNs (Windows, no special tooling)
setspn -T domain.com -Q */*

# Request + extract roastable hashes with Impacket
GetUserSPNs.py domain.com/user:password -dc-ip 10.10.10.10 -request

# Or from Windows with Rubeus
Rubeus.exe kerberoast
bash
# Crack offline — mode 13100 is TGS-REP (etype 23 / RC4)
hashcat -m 13100 hashes.txt rockyou.txt
The Misconfigurations It Exploits

Kerberoasting is only viable because of choices made when service accounts are set up. The vulnerability is configuration, not protocol.

Weak, human-set passwords on SPN accounts. The entire attack hinges on the password being crackable offline. A short or dictionary-based password falls in minutes; a 25+ character random one makes cracking infeasible. This is the root cause — everything else just changes how fast the crack lands.
Legacy RC4 (etype 23) encryption. RC4-encrypted tickets are derived directly from the NTLM hash and crack dramatically faster than AES. Roughly speaking, AES128 is ~350× slower to crack than RC4 and AES256 ~700× slower — an RC4 ticket that falls in a day becomes closer to a year (AES128) or two (AES256). That's a real speed bump, but note it's only a speed bump: a weak password still falls, just later. Environments that still permit RC4 for Kerberos are handing attackers the fast lane.
Over-privileged service accounts. A cracked account is only as valuable as its permissions. Service accounts dropped into Domain Admins or other high-privilege groups turn one cracked password into full domain compromise.
Unnecessary or stale SPNs. Accounts carrying SPNs they don't need — or decommissioned services never cleaned up — expand the attack surface for zero operational benefit. Watch too for targeted Kerberoasting: an attacker holding GenericAll/GenericWrite over a user can temporarily add an SPN, roast it, and strip the SPN back off. Adding an SPN is far quieter than resetting a password, so this passes the scream test.

A note on Server 2025. Microsoft is phasing RC4 out — WS2025 DCs no longer issue RC4 TGTs by default, new domains stood up from Q1 2026 have RC4 disabled, and the plan is to drop RC4 as an assumed supported etype by end of Q2 2026. But RC4 is still enabled by default in most existing deployments, so downgrade requests remain in play today. And none of this touches the core issue: Kerberoasting is fundamentally a weak-password problem, and AES-only does not make you safe.

Defenses
Use group Managed Service Accounts (gMSAs). This is the real kill switch. gMSA passwords are 120 characters, randomly generated, and rotated automatically — there's no human-set password left to crack, so even an AES ticket is effectively uncrackable. Migrating SPN-bearing accounts to gMSAs removes the vulnerability rather than slowing it down.
Enforce AES, disable RC4. Where a gMSA isn't yet possible, force AES encryption for Kerberos and disable RC4 so attackers can't downgrade to the fast-cracking path.
Keep service accounts out of privileged groups. Apply least privilege ruthlessly. A cracked account that has no path to anything valuable is a dead end.
Enforce long, random passwords on any account that must stay human-managed. 25+ characters, randomly generated, rotated. If it can't be a gMSA, make it look like one.
Audit and remove unused SPNs. Regularly review SPN assignments and decommission stale service accounts so the roastable surface stays as small as possible.
Detection

Kerberoasting hides inside normal Kerberos traffic, so detection is about spotting anomalies rather than a single smoking-gun event.

Event ID 4769 (Kerberos service ticket requested). This fires thousands of times a day legitimately, so raw volume is useless — filter on the signal. Watch for a single account requesting an unusual volume of distinct SPNs in a short window, and pay special attention to requests with ticket encryption type 0x17 (RC4) in an environment that should be AES-only. RC4 requests where you'd expect AES are a strong roasting indicator.
Honeypot / decoy service accounts. Create a service account with an SPN that no legitimate system ever requests, give it no real privileges, and alert on any 4769 for it. Because nothing should ever ask for that ticket, a request is high-fidelity evidence of enumeration.
Managed detections. If you run Microsoft Defender for Identity / XDR, the built-in Kerberoasting alert (alert ID 2410, "Suspected Kerberos SPN exposure") flags anomalous SPN-request patterns for you and is worth tuning in.

The single best detection posture, though, is to shrink the attack surface first: once your privileged service accounts are gMSAs and RC4 is off, the remaining 4769 noise gets a lot easier to reason about — and a lone RC4 request against a decoy account stands out immediately.