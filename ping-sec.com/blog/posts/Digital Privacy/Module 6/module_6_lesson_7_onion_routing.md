---
title: "Module 6 Lesson 7 - Onion Routing: Why Tor Is Not a Tunnel With Extra Hops"
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.7
format: article
reading_time: 35 minutes
tags:
  - digital-privacy-course
  - privacy
  - tor
  - onion-routing
  - anonymity
  - network-anonymity
---

# Onion Routing: Why Tor Is Not a Tunnel With Extra Hops

## About This Lesson

Module 6 has spent six lessons on tunnels. Lesson 6.1 established that a tunnel moves an observation rather than removing it. Lesson 6.3 established that the central claim a provider makes is the one you cannot verify. Lesson 6.4 built chains of tunnels and found that the separation they achieve depends on companies staying separate, which is a commercial fact rather than a technical one. Lesson 6.6 rented a server and found that self-hosting replaces the party you trust rather than removing it.

Every one of those lessons ends in the same place: **you are choosing which company to trust, and you cannot check whether the trust is warranted.**

This lesson is about a system built to make that question unnecessary.

The usual introduction says Tor routes your traffic through three relays instead of one, which makes it sound like a VPN with more hops. **That framing gets the point exactly backwards.** A chain of tunnels is an attempt to split trust between parties you selected. A Tor circuit is constructed so that no party is in a position to be trusted in the first place, and it does that without you knowing or choosing who any of them are.

The difference is structural, and this lesson is about the structure.

### A Note on This Lesson

**This lesson is new, not a conversion.** Everything so far in this series has taken an existing course script and corrected it. There is no script for this one.

Tor does appear elsewhere in the course. Module 2 Lesson 3 covers Tor Browser as a browser choice, and it is a decent treatment: the three hops, verified downloads, the security slider, and the behavioural rules. Module 3 Lesson 4 covers timing correlation. Module 6 Lesson 4 covers combining a tunnel with Tor. Module 8 and Module 19 reference it.

**What no lesson does is teach the network.** That gap matters because every threat model in this course resolves its highest tier to "use Tor," and a student meeting that advice has been taught Tor as a Firefox variant with a slider. These four lessons supply what the advice assumes you already know.

Lesson 6.4 already forward-references this lesson twice. Those references were written before it existed.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Explain what each relay in a circuit knows, and what it cannot know.
- State the property a circuit provides, and say why it is structural rather than a promise.
- Explain why the middle relay exists, which is not obvious and is not decoration.
- Explain why three hops rather than two or ten.
- Explain why the first hop deliberately does not rotate, and why rotating it would be worse.
- Describe how a client learns what the network is, and identify the part of the design that is centralised.
- State where the trust argument gets weaker in practice, using published numbers.

### Prerequisites

- Module 6 Lessons 6.1 through 6.6, particularly 6.4 on chains.
- Module 2 Lesson 3 if you have never opened Tor Browser. This lesson explains the network, not the application.

---

## 1. What Each Relay Knows

![[module_6_lesson_7_who_knows_what.svg]]

*Figure 1. The circuit, and the comparison that makes it meaningful.*

Your client picks three relays and builds a circuit through them. Each relay is handed only the address of the next one, and the traffic is wrapped in layers of encryption so that no relay can read past its own.

**The guard**, your first hop, sees your real address. It does not see where you are going, because everything past it is encrypted to keys it does not have.

**The middle relay** sees two relay addresses and nothing else. It does not know who you are and it does not know where you are going.

**The exit relay** sees where you are going, and carries your traffic out onto the ordinary internet. It does not see your address. Anything not separately encrypted is readable here, which is why transport encryption matters more on Tor than off it, and Lesson 6.9 returns to that.

**The destination** sees the exit relay's address.

### The property, stated exactly

**No single party holds both your address and your destination.**

That is not a promise anybody made and it is not a policy that could be revised next quarter. It follows from how the circuit is constructed.

The requirement it places on you is worth stating precisely, because it is much weaker than the requirement a tunnel places on you. **You do not have to trust any relay.** You have to believe the three of them are not all the same party. Section 4 examines how well that holds.

### Why this is different from everything else in the module

Put the three arrangements side by side:

| Arrangement | Who holds both halves | What stops them using it |
|---|---|---|
| One tunnel provider (6.1) | The provider, continuously | Their policy, which 6.3 established you cannot verify |
| Two providers chained (6.4) | Neither, by design | Corporate separation, a commercial fact rather than a technical one |
| A Tor circuit | Nobody | The construction, plus relays that did not choose each other |

The third row differs from the second in a way that is easy to miss. In a chain **you** selected both providers, they are companies, and companies merge. In a circuit your client selected three strangers from a pool of thousands, they have no relationship with each other, and the selection changes.

### Why the middle relay exists

It looks like a spare part. It is not.

Without it, the guard and the exit would connect directly to each other, and each would learn the other's address for your circuit. The middle relay exists **so that the two relays which each hold half of the answer never speak to each other.**

### Why three and not more

Longer paths cost latency and network capacity, and buy essentially nothing against the adversary who matters (Reference 8). An observer positioned to watch both ends of your connection wins regardless of how many relays sit in the middle, and that is the attack Lesson 6.9 is about.

There is a second reason, and it is the more interesting one. **A non-standard path length makes you rarer.** If a handful of people run four hops, that handful is distinguishable from everybody else. Being rare is the opposite of what you came here for, and this idea returns repeatedly in Lesson 6.8.

---

## 2. Why the First Hop Does Not Rotate

![[module_6_lesson_7_entry_guards.svg]]

*Figure 2. The guard argument.*

Here is the part of the design that is genuinely counter-intuitive, and the single most useful idea in this lesson.

The middle and exit relays change for every circuit. **The first one does not.** Your client picks a small set of first relays, called guards, and keeps using them for months.

That sounds wrong. Surely changing your entry point constantly is safer?

### It is not, and the reason generalises

Suppose you picked a fresh first relay for every circuit. Some fraction of relays are operated by somebody who wants to observe you. Every new circuit is a fresh draw from that pool.

**Draw enough times and the probability of picking a hostile relay at least once approaches certainty.** And once is enough, because on that occasion the relay sees your real address.

So constant rotation does not spread the risk. **It accumulates it.**

Now the actual design. Your client picks a small stable set and keeps it. The outcome is decided once rather than sampled repeatedly. Either your guards are hostile, in which case you are exposed for the rotation period, or they are not, in which case you are safe for the whole of it.

**The trade, stated honestly:** a small chance of sustained exposure, instead of a near-certainty of brief exposure. Most people are better off under that trade. A few are worse off, and the design does not pretend otherwise.

### The current settings, which have changed three times

Clients use **two primary guards**, rotating on roughly a **three and a half month** schedule (References 1, 2).

The number is carried in the signed network consensus rather than compiled into the software, so it can be changed for the whole network at once.

The history is worth knowing, because it tells you what kind of thing this is. Three guards originally. Reduced to one in 2014, to narrow the points at which a client can be observed entering the network. Raised back to two in 2022 (Reference 2). **A parameter revised twice in a decade is one to check rather than memorise.**

### What this means for putting a tunnel in front of Tor

Lesson 6.4 reported that the Tor Project does not recommend adding a VPN, and gave the guard argument in outline. This is the argument in full.

A tunnel in front of Tor replaces a considered guard design with **a single permanent first hop that you chose, that sees every session you ever run, and that knows exactly who you are because you pay it every month.**

That is a change to the threat model rather than a layer added on top of it. It may still be the right change: hiding Tor usage from your network operator is a serious requirement in some places. It is not a free one, and now you can weigh it rather than defer to an authority.

### The transferable idea

**A defence that reduces each individual exposure can still increase your total exposure, if you sample often enough.**

"Rotate constantly" is good advice in some contexts and actively harmful in this one. The question that separates them is always: what accumulates?

---

## 3. How a Client Knows the Network Exists

Before any of this can happen, your client needs to know which relays exist, which are running, and which are suitable for which position. That information has to come from somewhere, and where it comes from is the part of Tor most introductions skip.

**Nine directory authorities**, hard-coded into the client. Each publishes an hourly vote describing its own view of the network. The votes are combined into a consensus document that a majority of the authorities must sign, and your client checks those signatures (References 3, 4).

Three things follow.

**This is the centralised part, and it should be named as such.** Nine servers deciding what the network is, is centralisation. Section 4 is about being honest rather than promotional, and this belongs there.

**It is also published, signed, and checkable.** Anybody can fetch the consensus, verify the signatures, and read what the network looked like at any hour. No commercial provider offers anything comparable about its own infrastructure, which is the point Lesson 6.3 spent its length on.

**Network-wide behaviour can change without new software.** The guard count in Section 2 lives here. That is operationally useful and it means the network's parameters are a live thing rather than a fixed property.

### Circuits and identities

Two details that cause confusion in practice.

**A circuit is reused for about ten minutes by default**, then new connections get a fresh one. Existing connections finish on the old circuit (Reference 5). So "a new circuit" is not an instantaneous switch.

**A new circuit is not a new identity.** Tor Browser separates the two deliberately. Requesting a new circuit for a site changes the relays, which helps when an exit is blocked or slow. **Requesting a new identity closes your tabs, clears cookies and history, and builds fresh circuits** (Reference 6). Only the second breaks the link between what you were doing and what you do next, because the relays were never the thing linking you. The cookies were.

That distinction is Lesson 6.8's territory, and it is the single most common way people believe they have reset and have not.

---

## 4. Who Actually Runs This

![[module_6_lesson_7_who_runs_it.svg]]

*Figure 3. The network's real shape, including the parts that weaken the argument.*

Figure 1's property depends on the three relays not being the same party. That is worth checking rather than assuming.

**Roughly eight thousand relays**, of which **about two thousand permit exit traffic** (Reference 7). Exits are scarce for a straightforward reason: the exit address is the one that appears in everybody else's logs, so the operator receives the complaints and the legal attention. Lesson 6.10 covers what that involves.

**Relays sit in over a thousand separate networks**, which sounds well distributed. **Roughly a third of capacity sits in three of them** (Reference 7).

That last number is the one to sit with. Concentration makes it more likely that two of your three relays sit somewhere a single observer can see both.

### So what survives

**The property holds for the ordinary case.** The client avoids selecting two relays operated by the same person or sitting in the same network, so an individual hostile operator does not get both ends.

**It weakens against an adversary who can observe large parts of the internet's core**, because concentration puts more circuits within reach of fewer vantage points. That adversary is Lesson 6.9's subject, and **Tor does not claim to defeat it.** Saying so is not a weakness in the argument. It is what makes the rest of the argument credible.

### What to take from this

**Every one of these numbers is public.** The project publishes relay counts, capacity, and distribution, and you can check them today. That is the difference that matters against a commercial provider, whose equivalent numbers do not exist in any form you can inspect.

**The design handles some of this and not all of it**, and says which is which.

**Running a relay is the one action in this entire module that improves the system for everybody** rather than only for you. Lesson 6.10 covers what it involves.

---

## 5. Where This Leaves Module 6

Six lessons asked who you have to trust and whether you can check. The answers were: one company and no; two companies and no; a hosting company and partly.

Here the answer is **fewer parties than any of those, and yes, you can check.**

That is the strongest claim available anywhere in this module. It is still a bounded claim rather than a guarantee, and the next three lessons are about the bounds: what you do that undoes it, what it does not defend against, and when it is the wrong tool entirely.

---

## Key Takeaways

- **No single relay holds both your address and your destination.** This follows from the construction rather than from a policy, which is why it is a different kind of claim from anything earlier in the module.
- **You do not have to trust any relay.** You have to believe they are not all the same party, which is a much weaker requirement than trusting a provider.
- **The middle relay exists so the two relays holding half the answer each never speak to one another.**
- **Three hops, because more costs latency and buys nothing** against an observer watching both ends, and a non-standard path length makes you rare.
- **The first hop deliberately does not rotate.** Rotating it accumulates risk towards certainty; keeping it decides the outcome once. Two guards, roughly three and a half months, and the value has changed twice.
- **Nine directory authorities publish an hourly signed consensus.** That is the centralised part, and it is also the auditable part.
- **A new circuit is not a new identity.** The relays were never what linked you.
- **About eight thousand relays, about two thousand exits, and roughly a third of capacity in three networks.** The last number is the honest limit on the argument.

---

## Knowledge Check

**Question 1.** What does the guard relay know?

A. Your address, but not your destination
B. Your destination, but not your address
C. Both your address and your destination
D. Neither, because everything is encrypted end to end

**Answer: A**

It is your first hop, so it sees where you connect from. Everything past it is encrypted to keys it does not hold, so it cannot see where the circuit ends.

---

**Question 2.** Why is the property "no single party holds both halves" different in kind from a provider's no-logging claim?

A. Because relay operators are legally prohibited from keeping logs
B. Because it follows from how the circuit is constructed rather than from a policy somebody could revise
C. Because the relays are audited annually by an independent firm
D. Because relay operators do not have the technical capability to record anything

**Answer: B**

Relay operators can log whatever passes through them. The point is that what passes through any one of them is only half the picture, and that is a structural fact rather than a promise.

---

**Question 3.** Why does the middle relay exist?

A. To add latency that frustrates timing analysis
B. To provide a spare path if the exit relay fails
C. So the guard and the exit never connect directly and learn each other's address for your circuit
D. To perform an additional layer of encryption that the other two cannot

**Answer: C**

Without it the two relays that each hold half the answer would be talking to each other. It looks like a spare part and it is doing the load-bearing work.

---

**Question 4.** Why does Tor use three hops rather than five or ten?

A. Because the protocol cannot address more than three relays in one circuit
B. Because directory authorities only publish three relay categories
C. Because longer paths reduce anonymity by encrypting the traffic too many times
D. Because more hops cost latency and capacity, buy nothing against an observer watching both ends, and a non-standard path length makes you rarer

**Answer: D**

The adversary that defeats Tor sees both ends and is unaffected by what happens between them. Being distinguishable from other users is a cost, not a neutral outcome.

---

**Question 5.** Why would picking a fresh first relay for every circuit be worse than keeping a stable set?

A. Because building new circuits is computationally expensive for the client
B. Because the probability of eventually picking a hostile relay at least once approaches certainty, and once is enough
C. Because the directory authorities rate-limit how often a client may change relays
D. Because a rotating first hop would prevent the middle relay from being selected correctly

**Answer: B**

Constant rotation does not spread risk, it accumulates it. A stable set decides the outcome once: either your guards are hostile or they are not.

---

**Question 6.** What are the current guard settings, and what does their history tell you?

A. Two guards on a roughly three and a half month rotation, and the value has changed twice in a decade, so check it rather than memorise it
B. One guard, permanently, and it has never changed since the design was published
C. Three guards rotating weekly, fixed in the client software
D. A new guard for every session, chosen from the consensus

**Answer: A**

Three originally, reduced to one in 2014, raised to two in 2022. The number is carried in the signed consensus, so it can change network-wide without new software.

---

**Question 7.** How does a client learn which relays exist, and what does that imply?

A. It queries the relays directly and builds its own list from their responses
B. The list is compiled into each release of the software and updated with it
C. Nine hard-coded directory authorities publish an hourly consensus that a majority must sign, which is the centralised part of the design and also the auditable part
D. Relay operators register with a distributed ledger that clients read

**Answer: C**

Naming it as centralisation is the honest description. It is also fully published and signature-checked, which no commercial provider offers about its own infrastructure.

---

**Question 8.** What is the difference between requesting a new circuit for a site and requesting a new identity?

A. There is none; both terms describe the same operation
B. A new circuit clears cookies, while a new identity only changes the exit relay
C. A new identity applies to one tab, while a new circuit applies to the whole browser
D. A new circuit changes the relays, useful when an exit is blocked; a new identity also closes tabs and clears cookies and history

**Answer: D**

Only the second breaks the link between what you were doing and what you do next, because the relays were never what linked you. The cookies were.

---

**Question 9.** Roughly a third of network capacity sits in three hosting networks. Why does that matter for Figure 1's argument?

A. It means the directory authorities cannot produce a valid consensus
B. It makes exit relays disproportionately slow compared to guards
C. It makes it more likely that two of your three relays sit where a single observer can see both
D. It means the guard rotation period is effectively shorter than advertised

**Answer: C**

The client avoids two relays from the same operator or network, which handles the ordinary case. It does not handle an adversary with visibility into the internet's core, and the project does not claim it does.

---

**Question 10.** What does a Tor circuit require you to trust, compared with a VPN provider?

A. That every relay operator has been vetted by the Tor Project
B. That the three relays are not all the same party, which is weaker than trusting any single one of them
C. That the exit relay does not read your traffic, which it cannot do
D. That the directory authorities never disagree with one another

**Answer: B**

You do not have to trust any individual relay, because no individual relay holds enough to harm you. Option C is wrong: an exit relay can read anything not separately encrypted, which Lesson 6.9 covers.

---

## References

1. Tor Project, guard specification, on primary guards and the selection algorithm. https://spec.torproject.org/guard-spec/
2. Tor Project, proposal 291, "The move to two guard nodes," and the consensus parameter that deployed it in 2022. https://spec.torproject.org/proposals/291-two-guard-nodes.html
3. Tor Project, directory protocol version 3 specification. https://spec.torproject.org/dir-spec/
4. Höller, Roland and Mayrhofer, "Analyzing inconsistencies in the Tor consensus," on the nine authorities and the hourly consensus process. https://dl.acm.org/doi/fullHtml/10.1145/3487664.3487793
5. Tor Project, path specification, stream isolation and circuit sharing, including the default circuit lifetime. https://spec.torproject.org/path-spec/stream-isolation.html
6. Tor Browser manual, "Managing identities," on new circuit versus new identity. https://tb-manual.torproject.org/managing-identities/
7. Tor Metrics, relay counts, advertised bandwidth and network distribution. https://metrics.torproject.org/
8. Tor Project support, "Changing the number of Tor hops," on why longer paths are discouraged. https://support.torproject.org/about-tor/using-and-sharing/circuit-length/

---

## Where This Goes Next

**Lesson 6.8** covers using Tor without undoing it: what the security slider actually changes, the behaviours that identify you regardless of routing, onion services, and bridges and pluggable transports for networks that block Tor outright. Section 1's point about being rare returns there repeatedly.

**Lesson 6.9** covers the limits, stated the way the Tor Project states them: correlation by an observer who sees both ends, malicious exits, and what the anonymity-set argument means in practice.

**Lesson 6.10** covers Tor for security work, when it is the wrong tool, and what running a relay commits you to.
