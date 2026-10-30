---
title: "Module 6 Lesson 2 - VPN Protocols: The Speed Row Is Wrong and Port 443 Is Not a Disguise"
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.2
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - vpn
  - wireguard
  - openvpn
  - censorship-resistance
  - post-quantum
---

# VPN Protocols: The Speed Row Is Wrong and Port 443 Is Not a Disguise

## About This Lesson

Protocol comparisons age badly, and this one has aged in three specific places.

The performance figures everybody quotes stopped being true in 2023. The advice for restrictive networks describes how inspection equipment worked about a decade ago. And the one claim about quantum resistance is backwards, in a lesson that otherwise handles cryptography carefully.

Underneath those three corrections the material is sound, so this lesson keeps the structure and fixes what needs fixing. It also adds two protocol-level features that no comparison chart lists, one of which has been on by default at a major provider since January 2025.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Compare the three current protocols on the properties that still differ between them.
- Explain what data channel offload changed, and why quoted throughput figures are stale.
- Say why running a tunnel on the standard web port does not conceal it, and name four things inspection equipment actually looks at.
- Distinguish obfuscation from protocol choice, and check for it by name.
- State accurately what is and is not quantum resistant, and what provides the hardening.
- Describe what traffic analysis defence does at the protocol layer, and what it costs.
- Recognise which protocols are genuinely retired.

### Prerequisites

- Lesson 6.1, particularly Figure 1 on what a VPN moves.
- Lesson 3.4 on metadata and timing, which Section 6 returns to.
- Basic familiarity with the difference between the two transport protocols. No cryptography background needed.

---

## 1. The Three Protocols, Briefly

**WireGuard.** Around four thousand lines against OpenVPN's hundred thousand or so, in the Linux kernel since version 5.6. Fixed modern cryptography with nothing to choose and therefore nothing to choose wrongly. Holds no session, which is why it recovers instantly when the network changes.

**OpenVPN.** Twenty-five years old, extensively deployed, extensively audited. Runs over either transport protocol on any port. Its flexibility is the source of both its compatibility and its main weakness, which is the number of ways it can be configured badly.

**IKEv2 with IPsec.** Native on Apple platforms with no application required, and built around mobility from the start, so it survives a change of network by design rather than by accident.

**One thing worth noting about the line count comparison**, which is used to argue that WireGuard is easier to audit. It is a fair point and slightly generous, because WireGuard delegates its cryptography to kernel implementations that are not in that four thousand lines. The argument still holds. It is smaller than the headline number suggests.

---

## 2. What Actually Differs Now

![[module_6_lesson_2_what_differs.svg]]

*Figure 1. Six properties, with the row everybody quotes corrected.*

### The Speed Row, Corrected

You will read that OpenVPN manages 100 to 200 megabits and WireGuard four to six hundred. **That gap was real, and it was a consequence of OpenVPN moving every packet through user space** rather than anything about its cryptography.

**Data channel offload changed that.** It moves the OpenVPN data path into the kernel while leaving the control channel outside, it has been enabled by default since OpenVPN 2.6 in 2023, and the kernel module was merged upstream in 2026. (Reference 1)

Recent benchmarks put the two within a few percent of each other and split in both directions depending on the hardware: one router measured OpenVPN with offload at 700 megabits against WireGuard's 600, and another had them level. (Reference 2)

**Processor load still favours WireGuard**, measured at roughly 12 percent against 20 percent at the same throughput, which is why the battery row is unchanged and why the mobile recommendation stands. (Reference 2)

**So throughput is no longer a reason to choose between them.** If your provider gives you either, pick on the other rows.

### The Rows That Do Still Differ

**How much you can get wrong.** This is the genuine security difference and it is often buried. WireGuard has no cipher negotiation, so a misconfigured WireGuard tunnel is mostly not a thing. OpenVPN has a great deal of surface, weak options still exist in it for compatibility, and a provider or an administrator can produce a technically working tunnel with poor cryptography. IKEv2 sits between the two.

**When the network changes.** WireGuard and IKEv2 both survive a switch from wireless to cellular, for different reasons: WireGuard because it holds no session to lose, IKEv2 because mobility was designed into it. OpenVPN reconnects, and you notice.

**What is built in.** IKEv2 is native on Apple platforms. WireGuard is in the Linux kernel and an application everywhere else. OpenVPN is an application everywhere.

**And the last row, where all three answer the same way**, which Section 4 is about.

---

## 3. Why This Matters More For You

You will configure these for clients and write about them for readers, and both audiences will have read the stale figures.

**Being right about the performance row is a small credibility test.** Somebody who knows about data channel offload will notice if you quote 2021 benchmarks, and it takes one sentence to be current instead.

**Being right about port 443 is a larger one**, because that advice gets given to people travelling to censored networks, where being wrong has consequences beyond credibility.

---

## 4. Port 443 Is a Destination, Not a Disguise

This is the correction with the most practical weight.

![[module_6_lesson_2_port_443.svg]]

*Figure 2. Four things inspection equipment looks at, none of which is the port.*

**The claim**, which appears in nearly every protocol guide including the source for this lesson, is that running OpenVPN over TCP on port 443 makes it indistinguishable from ordinary web traffic, because a firewall cannot block that port without blocking the web.

**The first half is true and the second does not follow.** Inspection equipment has not worked by port for some years.

### What It Actually Looks At

**The opening bytes.** Every protocol begins with a handshake, and the byte patterns in those first packets are distinctive. Research published at a security conference in 2022 demonstrated identifying OpenVPN flows this way at scale, with high accuracy and low false positives, regardless of port. (Reference 3)

**Your client's own fingerprint.** Where a connection does use a real encrypted handshake, the cipher list, extension set and version ordering form a recognisable signature. A VPN client's signature does not match any browser, and that mismatch is itself the finding.

**Shape and rhythm.** A page load is a burst followed by a pause. A tunnel is a long steady flow of similarly sized packets in both directions. The two do not resemble each other and no port setting changes that.

**Being probed.** A censor that suspects a server can connect to it and see how it answers. A web server serves a page; a VPN endpoint does not. Active probing settles what passive inspection only suspected.

### Where the Real Answer Lives

**Obfuscation is a separate layer, not a port number.** It wraps the tunnel and removes exactly those signals: randomising the handshake so there are no fixed opening bytes, making the endpoint refuse to identify itself when probed, and in the better designs reshaping packet sizes and timing.

- **Providers ship this under their own names.** Look for a mode described as obfuscated, stealth, or bridge. Do not assume a protocol choice covers it.
- **It is a different question from which protocol you use**, and a provider may offer excellent protocols and no obfuscation at all.

**And the caveat the research itself insists on.** Simple obfuscation, the kind that scrambles the payload without changing the behaviour, was defeated in the same study. Obfuscation makes traffic unremarkable at scale. It does not make it unobservable to somebody looking specifically at you, and anybody whose safety depends on that distinction needs to understand it.

**So the honest advice is:** pick the protocol on the other five rows of Figure 1, and treat censorship resistance as a separate feature you check for by name. Port 443 remains useful against a firewall that filters only by port, which is a real but much smaller claim than the one usually made for it.

---

## 5. What Is and Is Not Quantum Resistant

The source material says the elliptic curve exchange WireGuard uses is quantum resistant with modifications. **The first part is wrong and it matters, because it is the reverse of the situation.**

![[module_6_lesson_2_two_additions.svg]]

*Figure 3. Two protocol level additions, and a correction.*

**Curve25519 is not quantum resistant.** It is among the things a sufficiently large quantum computer would break, and that is the entire reason this work exists.

**The threat is not the future computer.** It is that traffic captured today can be stored and decrypted later. Nothing has to exist yet for the recording to be worth making, so anything you send now with a long secrecy requirement is already exposed to it. This is worth stating plainly because it is the part that makes the timeline urgent for some people and irrelevant for most.

**What actually provides the hardening** is a feature the protocol already had for other reasons: an optional pre-shared key field. A key derived from post-quantum algorithms is placed into it and combined with the ordinary exchange, so an attacker must break both.

**One provider has run this by default on desktop since January 2025**, pairing the lattice-based algorithm standardised by NIST with an older code-based one, on the reasoning that two families are safer than one if either turns out to have a flaw. (Reference 4) Others have implemented the standardised algorithm as well.

**Do you need it?** Almost certainly not today, and it costs you nothing, which is a good combination. The people for whom it matters now are those whose traffic has a secrecy requirement measured in decades rather than years.

---

## 6. The Attack a Perfect Tunnel Does Not Stop

Lesson 3.4 established that encryption hides contents and not patterns. That applies inside a VPN tunnel exactly as it applies to a messenger.

**How many packets there were, how large they were, and when they arrived** are all still visible to whoever is watching the encrypted link, and those three together identify which page was loaded with uncomfortable accuracy. The tunnel is doing its job perfectly and the information leaks anyway.

**A defence exists at the protocol layer.** It sends cover traffic in both directions at unpredictable times and regularises packet sizes, so the shape of a page load stops being a shape anybody recognises. It was introduced in 2024, built on an open framework developed with a university, and published and peer reviewed rather than merely announced. A second version in 2025 moved from fixed client-side rules to defences negotiated with the server. (Reference 5)

**The costs, stated plainly.** Cover traffic is real traffic: it uses bandwidth you are not using for anything, and it is offered on a limited set of servers rather than across a whole network.

**Why it is in a protocol lesson.** Because it is a protocol-layer answer to a Module 3 problem, and because it is invisible in every comparison chart. It is not a reason to change provider today. It is a reason to know the category exists when somebody asks whether a VPN stops traffic analysis, because the correct answer is "not on its own, and here is what does."

---

## 7. Corrections and Currency

**"Lightway is proprietary but audited."** It was rewritten in Rust, open sourced, and audited by two independent firms in late 2024, with only low-risk findings, since addressed. (Reference 6) The proprietary half of that description is out of date.

**"Modern providers have solved the static address issue."** WireGuard assigns a fixed internal address and uses a long-lived key, which in principle allows a provider to correlate sessions. Providers mitigate this in different ways, including rotating keys and using address translation. **Mitigated is the right word, and solved is too strong**, and the specific mechanisms differ enough that it is worth reading what your provider actually does rather than accepting a general reassurance. The source material also attributes one provider's approach to a port forwarding protocol, which does not do key rotation and is a different thing entirely.

**On the retired protocols**, the source material is right and the reasoning can be sharper. The authentication scheme underpinning PPTP was broken publicly in 2012, reduced to the difficulty of a single obsolete cipher key, which is to say hours on rented hardware. It is not weak. It is finished. If a provider offers only that, the protocol list is telling you about the rest of the operation.

**On L2TP with IPsec**, dated is fair. The specific concern worth knowing is that many deployments use a shared secret common to all users, which the provider publishes, and that changes the security properties considerably.

**On IKEv2's origins.** The source notes it was developed largely by Microsoft and Cisco and that some people find that a reason for caution. The concrete version is more useful: documents published in 2013 indicated sustained intelligence interest in IPsec key exchange, and separately, weak Diffie-Hellman groups in older configurations were shown to be attackable by a well-resourced adversary. Current configurations with modern groups are not in that position, and the history is a reason to check the configuration rather than to avoid the protocol.

---

## 8. Choosing, Compressed

**If your provider offers WireGuard, use it.** Best processor efficiency, least configuration surface, instant recovery when the network changes. Throughput is no longer part of this argument.

**On Apple platforms where you want no application**, IKEv2 is native and good.

**If you need to get through a network that inspects traffic**, the protocol is the wrong lever. Find the provider's obfuscation mode and use that, whatever it wraps.

**If you need to get through a network that filters by port only**, OpenVPN over TCP on 443 still does that job.

**Configure more than one.** The multi-protocol advice in the source material is sound: have a second option ready before you are somewhere that needs it, because the moment you need it is a bad time to be reading setup documentation.

---

## 9. What to Take From This

**The performance gap closed in 2023** and the old figures are still quoted everywhere, including in material written this year.

**Port 443 is a destination, not a disguise.** Inspection equipment fingerprints the protocol, the client, the traffic shape, and the server's response to a probe. None of that is the port.

**Obfuscation is a separate feature** with its own name at each provider, and it is not implied by any protocol choice.

**Curve25519 is not quantum resistant.** The pre-shared key field is what carries the hardening, and one provider has had it on by default for over a year.

**And a perfect tunnel still leaks its shape**, which is a Module 3 problem with a protocol-layer answer that no comparison chart mentions.

**Next lesson:** Tor, where the anonymity properties a VPN does not have are the entire subject.

---

## Knowledge Check

**Question 1.** What changed the OpenVPN performance picture in 2023?

- A) Data channel offload, which moved the data path into the kernel and is on by default since version 2.6
- B) A new cipher suite with lower overhead
- C) A switch from TCP to UDP as the default transport
- D) Nothing changed, the old figures remain accurate

**Answer: A.** Recent benchmarks put OpenVPN and WireGuard within a few percent of each other, split in both directions by hardware.

**Question 2.** Which property still clearly favours WireGuard?

- A) Raw throughput
- B) Native operating system support
- C) Processor efficiency, which is why the battery and mobile recommendations are unchanged
- D) Resistance to identification by inspection equipment

**Answer: C.** Roughly 12 percent processor load against 20 percent at the same throughput.

**Question 3.** Why does running OpenVPN over TCP on port 443 not make it look like ordinary web traffic?

- A) Because port 443 is commonly blocked
- B) Because the connection is not really encrypted
- C) Because TCP is slower and therefore distinguishable by speed
- D) Because inspection equipment examines handshake bytes, client fingerprints, traffic shape and the server's response to probing, none of which is the port

**Answer: D.** Research published in 2022 demonstrated identifying OpenVPN flows this way at scale, regardless of port.

**Question 4.** What is obfuscation, in this context?

- A) Choosing a non-standard port for the tunnel
- B) A separate wrapper around the tunnel that randomises the handshake, refuses to identify the endpoint when probed, and may reshape traffic
- C) Another name for encryption
- D) A feature of the WireGuard protocol specifically

**Answer: B.** Providers ship it under names such as obfuscated, stealth, or bridge, and it is not implied by any protocol choice.

**Question 5.** What does the research on protocol fingerprinting say about simple obfuscation?

- A) It defeats all known inspection methods
- B) It is only required in a handful of countries
- C) Payload scrambling that does not change behaviour was defeated in the same study, so obfuscation makes traffic unremarkable rather than unobservable
- D) It has no effect at all

**Answer: C.** Anybody whose safety depends on a tunnel being invisible needs to understand that distinction.

**Question 6.** Is the elliptic curve key exchange used by WireGuard quantum resistant?

- A) Yes, it was designed to be
- B) Yes, once a modification is applied to the curve
- C) It depends on the provider's configuration
- D) No. It is among the things a sufficiently large quantum computer would break, which is why the hardening work exists

**Answer: D.** The optional pre-shared key field is what carries a post-quantum derived key, combined with the ordinary exchange so both must be broken.

**Question 7.** Why does post-quantum hardening matter before quantum computers exist?

- A) It does not, it is entirely speculative
- B) Because traffic captured today can be stored and decrypted later, so long-lived secrets are already exposed
- C) Because it improves performance
- D) Because current encryption is already broken

**Answer: B.** It matters most for traffic with a secrecy requirement measured in decades, and costs nothing for everybody else.

**Question 8.** What does a traffic analysis defence do at the protocol layer?

- A) It encrypts the payload a second time
- B) It routes traffic through additional servers
- C) It sends cover traffic at unpredictable times and regularises packet sizes, so a page load stops having a recognisable shape
- D) It blocks tracking scripts

**Answer: C.** The costs are real bandwidth consumed by cover traffic, and availability on a limited set of servers.

**Question 9.** What is the current status of ExpressVPN's Lightway protocol?

- A) It was rewritten in Rust, open sourced, and audited by two independent firms in late 2024
- B) It remains fully proprietary and unaudited
- C) It was discontinued in favour of WireGuard
- D) It was never released publicly

**Answer: A.** Descriptions of it as proprietary are out of date.

**Question 10.** How should the claim that providers have "solved" WireGuard's static address issue be treated?

- A) As accurate, the issue is fully resolved
- B) As irrelevant, since the addresses are internal
- C) As applying only to self-hosted setups
- D) As too strong. Providers mitigate it in different ways, and it is worth reading what yours actually does

**Answer: D.** The source material also attributes one provider's approach to a port forwarding protocol, which does not perform key rotation.

---

## Lab: Measure It, Fingerprint It, Check For It

**Time: 2 to 3 hours.**

Mostly hands-on. Parts 1, 2 and 4 need a VPN subscription; Part 3 and Part 5 do not.

---

### Part 1: Configure Two Protocols (35 minutes)

Set up both WireGuard and OpenVPN with the same provider and the same server location.

| | WireGuard | OpenVPN |
|---|---|---|
| Client used and version | | |
| Time taken to configure | | |
| Configuration file length in lines | | |
| Number of settings you could have got wrong | | |
| Connection verified how | | |

**Verify each connection properly**, not just that it says connected: check the exit address, the resolver being used, and whether any address leaks outside the tunnel.

**Deliverable.** The table, plus the verification results for both.

---

### Part 2: Measure, Rather Than Quote (30 minutes)

Test both protocols yourself. Three runs each, same server, same time of day, and record the median.

| | WireGuard | OpenVPN | Notes |
|---|---|---|---|
| Download throughput | | | |
| Upload throughput | | | |
| Latency to a fixed host | | | |
| Processor use during transfer | | | |
| Battery drain over 30 minutes, if mobile | | | |

Then answer: **does your measurement match the figures in the source material, or the figures in Section 2?** And: **can you tell whether your provider's OpenVPN deployment uses data channel offload?**

**Deliverable.** The table, both answers, and your method.

---

### Part 3: What Does Your Traffic Look Like? (30 minutes)

No subscription needed for the first half.

1. Capture a short sample of your own ordinary web browsing with a packet capture tool. Record the packet size distribution and the pattern over time.
2. Capture a sample of VPN traffic if you have one running.
3. Compare them. **Without decrypting anything, could you tell which was which?**

| | Ordinary browsing | Tunnelled |
|---|---|---|
| Typical packet sizes | | |
| Pattern over time | | |
| Distinguishable at a glance? | | |

**Deliverable.** The table and your answer to the third question. This is Figure 2 item three, established for yourself rather than taken on trust.

---

### Part 4: Find the Obfuscation (25 minutes)

For your provider and two others:

| Provider | Obfuscation offered? | What is it called | What does the documentation claim it defeats | Available on which protocols |
|---|---|---|---|---|

**The fourth column is the exercise.** Compare what is claimed against Figure 2's four signals, and note which of the four each provider's documentation actually addresses.

**Deliverable.** The table for three providers, and one sentence on which documentation was most honest.

---

### Part 5: The Currency Audit (25 minutes)

Find three published VPN protocol comparisons, from any source, and check them against this lesson.

| Source | Date | Speed figures current? | Port 443 claim present? | Post-quantum mentioned? | Obfuscation treated separately? |
|---|---|---|---|---|---|

**Deliverable.** The table, and one paragraph on what a protocol comparison would need to contain to still be right in three years.

---

### Part 6: The Recommendation (15 minutes)

Write the protocol advice you would give a client travelling to a country that inspects network traffic.

Apply the rule from Lesson 5.2: date it, give reasons, state what would make it stop being true. It must name obfuscation separately from protocol, and it must say what you do not know.

**Deliverable.** Half a page at most.

---

### Submission

1. **Configuration**, Part 1, with verification for both.
2. **Measurements**, Part 2, with your method and both answers.
3. **Traffic shapes**, Part 3, with your own captures.
4. **Obfuscation**, Part 4, three providers.
5. **Currency audit**, Part 5, three sources.
6. **Recommendation**, Part 6.
7. **Reflection**, half a page: which of the three corrections in this lesson had you believed, and where had you read it?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Configuration | 15 | Both protocols working and properly verified, not just connected |
| Measurements | 25 | Measured rather than quoted, method recorded, offload question addressed |
| Traffic shapes | 20 | Own captures compared, conclusion reached from the data |
| Obfuscation | 20 | Three providers, claims compared against the four signals |
| Currency audit | 10 | Three sources checked, and a view on what ages well |
| Recommendation | 5 | Dated, separates obfuscation from protocol, states its limits |
| Reflection | 5 | Honest about what was previously believed |
| **Total** | **100** | |

**Note the weighting.** Measuring and capturing carry nearly half, because this is the one lesson in the module where you can settle the disputed claims yourself in an afternoon.

---

## Additional Resources

### Primary Sources

- OpenVPN, on data channel offload and what it changed: https://blog.openvpn.net/openvpn-data-channel-offload/
- Xue et al., OpenVPN is Open to VPN Fingerprinting, USENIX Security 2022: https://www.usenix.org/system/files/sec22-xue-diwen.pdf
- Mullvad, on quantum-resistant tunnels becoming the desktop default: https://mullvad.net/en/blog/quantum-resistant-tunnels-are-now-the-default-on-desktop
- Mullvad, on defence against traffic analysis: https://mullvad.net/en/blog/daita-defense-against-ai-guided-traffic-analysis

### On Fingerprinting

Read the 2022 paper rather than summaries of it. The section on what defeats obfuscation is the part that changes how you write advice, and it is written by people who are careful about the limits of their own result.

### Discussion Questions

1. Part 2 asked you to measure rather than quote. How far off were the figures you had been repeating, and where had you got them?
2. Section 4 says the port 443 advice is given to people travelling to censored networks. What is the responsibility of somebody publishing that advice, and does it differ from ordinary technical writing?
3. Section 6 covers a defence most readers do not need. When is it right to teach something nobody currently requires, and when is it padding?

---

## References

1. [OpenVPN, Data Channel Offload released and what it does](https://blog.openvpn.net/openvpn-data-channel-offload/)
2. [Datazone, WireGuard against OpenVPN 2026 benchmark](https://datazone.de/en/aktuelles/wireguard-vs-openvpn-2026-benchmark/)
3. [Xue, Ramesh, Jain et al., OpenVPN is Open to VPN Fingerprinting, USENIX Security 2022](https://www.usenix.org/system/files/sec22-xue-diwen.pdf)
4. [Mullvad, quantum-resistant tunnels are now the default on desktop](https://mullvad.net/en/blog/quantum-resistant-tunnels-are-now-the-default-on-desktop)
5. [Mullvad, Defense Against AI-guided Traffic Analysis](https://mullvad.net/en/blog/daita-defense-against-ai-guided-traffic-analysis)
6. [ExpressVPN, security audits of the Lightway Rust rewrite by Cure53 and Praetorian](https://www.expressvpn.com/blog/lightway-audits-cure53-praetorian/)
