---
title: Module 6 Lesson 10 - Tor for Security Work, and What Module 6 Was Actually About
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.1
format: article
reading_time: 35 minutes
tags:
  - digital-privacy-course
  - privacy
  - tor
  - opsec
  - relay-operation
  - network-anonymity
---

# Tor for Security Work, and What Module 6 Was Actually About

## About This Lesson

Three lessons have described a network, how to use it without undoing it, and where it stops working. This one is about applying it to the work you actually do, and then closing the module.

Two things it says that the surrounding literature mostly does not.

**"Do your research over Tor" is right for one kind of research and wrong for two others**, and the case where it is wrong is not a technical mistake so much as a professional one. Sending a scan through Tor puts a volunteer's address on your scan and hands them the consequences.

**Running a relay is the only action in this entire module that improves the situation for anybody other than you.** Two of the three positions you could run are low risk and fine on a spare machine at home. The third is not, and the guidance about it is specific enough to be worth following exactly.

Then the module closes with a single table: which observer, which tool. That table is the thing to keep.

### A Note on This Lesson

Authored rather than converted, the last of four. It also replaces the module-closing summary in Lesson 6.6, which described a six-lesson module.

**It contains one correction to Lesson 7.1**, in Section 1.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Decide, per task, whether Tor is the right tool for a piece of reconnaissance.
- Explain why routing active scanning through Tor is a problem for somebody other than you.
- State what Tor changes about attribution and what it changes about authorisation, which are not the same.
- Compare the three relay positions on abuse exposure, legal exposure, and whether to run them at home.
- Follow the published guidance for an exit relay, or decide correctly not to run one.
- Use the module's decision table to choose a tool by naming the observer first.

### Prerequisites

- Lessons 6.7 through 6.9.
- Lesson 6.6 on self-hosting, which is where Section 1 sends you for active work.

---

## 1. Reconnaissance, Task by Task

![[module_6_lesson_10_recon.svg]]

*Figure 1. Four tasks, and whether Tor is the right tool for each.*

**Reading public sources about a target.** Registries, filings, archives, code repositories, anything published by a third party. Tor removes your address from that source's logs, so nobody can determine later who was researching what. Latency does not matter for reading.

**Use it. This is the case the standard advice means**, and it is a good case.

**Fetching pages from the target's own site.** The target sees a relay rather than you, which is the point. But relay addresses are published, so **they also know the request arrived through Tor**, and a small site may find that notable in itself.

You traded being identified for being conspicuous. Which of those is worse depends on the target, and it is a decision rather than a default.

**Active scanning and probing.** Port scans, enumeration, anything that sends traffic the target did not ask for.

The technical objections are real: it is slow enough to change your results, and many exits refuse the ports involved, so your coverage is uneven in ways you will not notice.

**The objection that should settle it is not technical.** Your scan now appears to come from a volunteer's relay. That volunteer receives the abuse complaint, explains themselves to their hosting provider, and carries the legal exposure Section 2 describes. **They did not agree to that and they cannot decline it after the fact.**

Rent your own infrastructure for active work. Lesson 6.6 covers it, it costs a few dollars a month, and it puts your own name on your own traffic, which is the correct arrangement for work you are authorised to do.

**Anything needing an account or a callback address.** Nothing helps. Lesson 6.8's first row: you supplied the identifier yourself.

### A correction to Lesson 7.1

That lesson recommended using a privacy-respecting resolver for reconnaissance lookups. I want to restate it more sharply than I did there.

**That is a secondary question at best.** Which resolver answered barely matters when the connection that follows the lookup carries your own address straight to the target. Fix the connection first. The resolver is the last thing on the list, not the first.

### And the distinction that matters most

**Tor changes who the target sees. It changes nothing whatsoever about whether you were authorised to be there.**

Worth stating because the two get blurred. Any tool that makes attribution harder also makes it harder for you to demonstrate afterwards that you acted within scope. That cuts both ways, and mostly against you.

---

## 2. Running a Relay

![[module_6_lesson_10_relay_positions.svg]]

*Figure 2. Three positions, and what each commits you to.*

Lesson 6.7 noted that this is the one action in the module that helps everybody. Here is what it involves, and the three positions are not remotely equivalent.

**A bridge.** An unlisted entry point that lets somebody on a censored network reach the network at all. Essentially no abuse complaints, because your address is never the source of anything. Low legal exposure in most countries. **Fine at home**, and because it is unlisted, services do not block it either (Reference 1).

**A guard or middle relay.** Carries traffic between other relays. Adds capacity, and more usefully adds another separate operator, which is the assumption Lesson 6.7's entire argument rests on. Not usually any abuse complaints, because your address never appears as the source of traffic to anybody's site. **Also fine at home**, given bandwidth you can spare (Reference 1).

**An exit relay.** The address that appears in everybody else's logs. Roughly two thousand of eight thousand relays, and the position the network is shortest of.

Abuse complaints come **directly to you, continuously**, including copyright notices for traffic you never saw. Legal exposure is real: traffic may be attributed to you before anybody gives you the chance to explain (References 2, 3).

### If you want to run an exit, the guidance is specific

**Not from your home connection, and not from your home address.** If law enforcement takes an interest, the traffic looks like it came from your house, and the response to that is a search rather than a phone call (Reference 2).

**Use a hosting provider that knows and agrees.** Tell them before you start. A provider that has not been told terminates you at the first complaint.

**Use a reduced exit policy, publish an abuse contact, and have a response letter ready.** The organisations supporting this work publish templates for exactly this (References 2, 3, 4).

### And if that is more than you want

**Run a bridge or a middle relay instead.** Both are genuinely useful, both are low risk, and both run happily on a spare machine.

This is also the only real answer to Lesson 6.9's concentration problem. More independent operators in more independent places is the thing that fixes it, and it cannot be bought. It is volunteer work.

---

## 3. When Tor Is the Wrong Tool

Worth stating directly, because a course that spends four lessons on something tends to imply you should use it.

**When you need to appear to be in a particular country.** Exit selection is not a country picker. Rent a server or use a commercial provider.

**When you need a fixed address that is yours.** Allow lists at work, services that reject shared exits. Lesson 6.6, and there is no substitute anywhere.

**When latency changes your results.** Anything timed, anything interactive, anything where a slow response is a finding.

**When the destination rejects Tor.** Blocking is widespread and challenges are common. Some services offer onion addresses instead, which is worth checking before assuming you are stuck.

**When you are doing authorised work in your own name.** If the client knows you are testing and expects traffic from you, hiding it serves nobody and complicates the report.

**And when the account is the identifier.** Which is most of what most people do online.

---

## 4. Module 6, in One Table

![[module_6_lesson_10_decision.svg]]

*Figure 3. Which observer, which tool.*

Ten lessons and six tools. The question that decides between them is never which is strongest.

| The observer you want to remove | What removes it | What that costs |
|---|---|---|
| The network you just joined | Any tunnel, including one at home | Almost nothing |
| Your own internet provider | Any tunnel, or Tor | The observation moves to whoever you chose |
| A destination profiling you across visits | Tor. A tunnel gives you one stable address | Speed, and Lesson 6.8's list |
| Appearing to be in a particular country | A rented server, or a commercial provider | Tor cannot do this reliably |
| One fixed address that is yours | A rented server. Nothing else supplies it | An anonymity set of one |
| Anybody holding both halves | Tor, structurally | Speed, and Lesson 6.9's caveats |
| An adversary watching both ends | Nothing in this module | The project says so plainly |
| Anything you did while signed in | Nothing, anywhere | Lesson 6.8, first row |

**Read the middle column.** No tool appears in every row, and two rows have nothing in them at all.

That is the whole argument of the module. There is no strongest option, because the options answer different questions, and the products are all marketed as answers to the same one.

**So name the observer first. Then look at the table. Then choose, and be able to say what you gave up.**

A recommendation that does not name the observer it removes is not advice. It is a preference, and a preference cannot be checked.

---

## 5. What Module 6 Was Actually About

Every lesson ended in the same place, and it was not a coincidence.

**6.1** found that a tunnel moves an observation rather than removing one. **6.2** found the interesting protocol differences were not the ones the marketing named. **6.3** found that a provider's central claim is the one you cannot verify. **6.4** found that chaining tunnels multiplies cost for a threat model most people do not have, and that the separation depends on companies staying separate. **6.5** found that leaks are stable while the fixes attached to them expire, and that the worst leak class is invisible to every test in the standard list. **6.6** found that renting your own server replaces the party you trust rather than removing it.

Then **6.7** described a system built so that no party is in a position to be trusted, **6.8** found that what defeats it happens above the network in nearly every case, and **6.9** found that it publishes its own defeat conditions, which is why the rest of its claims are worth anything.

**The through-line: every measure in this module moved an observation rather than removing one.** That is not a disappointment and it is not a reason to skip any of them. It is the design.

Knowing exactly where an observation moved to is the difference between using these tools and believing in them. That is what the module was for.

---

## Key Takeaways

- **Tor is right for reading public sources**, partly right for fetching a target's own pages, and wrong for active scanning.
- **Scanning through Tor puts a volunteer's address on your scan** and hands them the complaint and the legal exposure. Rent your own infrastructure.
- **Which resolver you used is a secondary question** when the connection carries your address to the target. Lesson 7.1 had this the wrong way round.
- **Tor changes attribution. It changes nothing about authorisation**, and it makes proving your scope harder.
- **Bridges and middle relays are low risk and fine at home.** Both are genuinely useful.
- **Exit relays carry real legal exposure.** Never from home, always with a provider who agreed, with a reduced policy and a prepared response.
- **More independent relay operators is the only fix for the concentration problem** in Lesson 6.9, and it cannot be bought.
- **Name the observer before choosing the tool.** No tool in this module covers every row, and two rows are covered by nothing.

---

## Knowledge Check

**Question 1.** For which reconnaissance task is Tor clearly the right tool?

A. Port scanning a target's infrastructure
B. Reading public sources such as registries, filings and archives
C. Anything requiring you to sign in to a service
D. Work where response latency is itself a finding

**Answer: B**

It removes your address from that source's logs, so nobody can determine later who was researching what, and latency does not matter for reading.

---

**Question 2.** What is the strongest objection to running an active scan through Tor?

A. Exit relays add latency that makes results unreliable
B. Many exits refuse the ports involved, so coverage is uneven
C. The scan appears to come from a volunteer's relay, so they receive the complaint and carry the exposure
D. The Tor client cannot handle the number of concurrent connections a scan requires

**Answer: C**

Options A and B are both true and both technical. The one that should settle it is that somebody else absorbs a consequence they never agreed to and cannot decline afterwards.

---

**Question 3.** What does Tor change about a piece of authorised security work?

A. It changes who the target sees, and nothing about whether you were authorised
B. It provides a verifiable record that the work was in scope
C. It removes the need to agree scope with the client in advance
D. It changes both attribution and authorisation, since the traffic is not attributable

**Answer: A**

The two get blurred. Anything that makes attribution harder also makes it harder to demonstrate afterwards that you stayed in scope, which mostly works against you.

---

**Question 4.** You want to fetch pages from a target's own website over Tor. What is the trade?

A. Nothing; this is straightforwardly the correct approach
B. Latency will make the pages incomplete
C. The target cannot serve pages to Tor exits at all
D. Relay addresses are published, so you traded being identified for being conspicuous

**Answer: D**

The target sees a relay rather than you, and also knows the request came through Tor. On a small site that may be notable in itself, so it is a decision rather than a default.

---

**Question 5.** Which relay positions are appropriate to run on a spare machine at home?

A. Bridges and guard or middle relays, both of which are low risk
B. Exit relays only, since they contribute most to the network
C. None; all relay operation requires commercial hosting
D. Any position, provided you publish an abuse contact

**Answer: A**

Neither attracts abuse complaints, because in neither case does your address appear as the source of traffic to anybody's site.

---

**Question 6.** Why should an exit relay never run from your home connection?

A. Home connections lack the upload bandwidth an exit requires
B. Residential addresses are rejected by the directory authorities
C. If law enforcement takes an interest, the traffic looks like it came from your house
D. Home routers cannot support a reduced exit policy

**Answer: C**

The response to that is a search rather than a phone call. This is the published guidance and it is worth following exactly.

---

**Question 7.** What does the network need more of, and why can it not be bought?

A. Bandwidth, because relay capacity is the limiting factor
B. Directory authorities, because nine is too few to be reliable
C. Exit relays in a single well-connected datacentre, for performance
D. Independent operators in independent places, because concentration is what weakens the separation argument

**Answer: D**

Lesson 6.9 showed a hostile operator taking large shares of the entry and middle positions. The only structural answer is more separate parties, which is volunteer work rather than a purchase.

---

**Question 8.** In the module's decision table, which observer is removed by a rented server and by nothing else?

A. The network you just joined
B. Your own internet provider
C. Anybody holding both halves of your connection
D. Whatever prevents you from having one fixed address that is yours

**Answer: D**

Allow lists and services that reject shared exits. Commercial providers sell the opposite, and Tor cannot supply it at all.

---

**Question 9.** Two rows of the decision table have nothing in the middle column. Which are they?

A. Appearing in a particular country, and profiling across visits
B. An adversary watching both ends, and anything done while signed in
C. Your own provider, and the network you joined
D. A fixed address, and anybody holding both halves

**Answer: B**

One is a limit the project states plainly. The other is behavioural rather than technical, and no routing system addresses it.

---

**Question 10.** What is the module's central claim about every tool it covered?

A. Each one removes a different observer entirely, so layering them removes all of them
B. Each is a partial implementation of what Tor does properly
C. Each moved an observation rather than removing one, which is the design rather than a failure
D. Each is adequate on its own if configured correctly

**Answer: C**

Knowing exactly where an observation moved to is the difference between using these tools and believing in them.

---

## References

1. Tor Project, "Types of relays on the Tor network," on bridges, guard and middle relays, and their respective exposure. https://community.torproject.org/relay/types-of-relays/
2. Electronic Frontier Foundation and Tor Project, "The Legal FAQ for Tor Relay Operators." https://community.torproject.org/relay/community-resources/eff-tor-legal-faq/
3. Tor Project support, "What to expect when running a Tor exit relay." https://support.torproject.org/relays/legal-and-abuse/exit-relay-expectations/
4. Tor Project, relay operations guide, including reduced exit policy and provider selection. https://community.torproject.org/relay/

---

## Module 6 Complete: Network Anonymity

Ten lessons.

**Tunnels, Lessons 6.1 to 6.6.** What a tunnel moves. What the protocols actually differ on. Why a provider's central claim cannot be verified. What chaining buys and what it costs. Why leaks are stable while their fixes expire. And what renting your own server does and does not change.

**Anonymity networks, Lessons 6.7 to 6.10.** A circuit built so that no party holds both halves. What defeats it, nearly all of which happens above the network. What it does not claim to stop, published by the people who built it. And how to apply it to real work, including when not to.

**The question the module was teaching, in one line: which observer are you removing, and what did you give up to remove them?**

Ask it of the next tool anybody recommends to you. The answer will usually be narrower than the page claims and more useful than nothing, and you will be able to say which.

**Next: Module 7, DNS Privacy and Encrypted DNS**, which asks the same question one layer further down.
