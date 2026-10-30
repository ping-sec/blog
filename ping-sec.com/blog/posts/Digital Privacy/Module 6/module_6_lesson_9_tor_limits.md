---
title: Module 6 Lesson 9 - What Tor Does Not Protect Against
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.9
format: article
reading_time: 35 minutes
tags:
  - digital-privacy-course
  - privacy
  - tor
  - traffic-analysis
  - threat-modeling
  - anonymity
---

# What Tor Does Not Protect Against

## About This Lesson

Every product in Module 6 made a claim. Lesson 6.3 was about the fact that you cannot check most of them, and that no provider ever names a thing its product fails at.

This lesson is different, because **the Tor Project publishes its own defeat conditions.** They are written down, in plain language, in the project's own documentation, and they have not been quietly revised as the system grew.

That is the reason to take the rest of it seriously. A system that tells you what beats it is giving you something you can plan around. A system that does not is giving you a mood.

Three limits, in order of how often they matter to real work, which is the reverse of how often they are discussed:

**Correlation by an observer who can see both ends.** The one Tor explicitly does not claim to stop. Most discussed, least likely to apply to you.

**Hostile relay operators.** Documented repeatedly in the real network, at meaningful scale, and the sophisticated version of this attack is not the one people worry about.

**Traffic fingerprinting.** Where the published numbers are real measurements of a task considerably easier than the real one, and where I need to correct something I wrote earlier in this course.

### A Note on This Lesson

Authored rather than converted, third of four.

**One correction to my own earlier work appears in Section 4.** In Lesson 7.2 I cited a result on identifying websites from encrypted name resolution traffic at over ninety per cent accuracy from very short traces, and presented it without qualification. That figure comes from a research tradition where evaluation conditions are known to flatter the attacker, and the qualification belongs with it. Section 4 states it properly.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Describe end-to-end correlation, and explain why adding relays does not affect it.
- State what the Tor Project says about its own limits, and explain why the trade was made deliberately.
- Turn the correlation question into a specific, answerable question about your own situation.
- Distinguish a hostile exit operator from a hostile position operator, and say which is the more serious.
- Explain why a documented adversary concentrated on entry and middle relays rather than exits.
- Read a published traffic-analysis accuracy figure critically, and say what question to ask of it.
- Name the traffic-analysis defences actually deployed, and what they do not cover.

### Prerequisites

- Lessons 6.7 and 6.8. Lesson 6.7's Figure 1 and its concentration figures are load-bearing here.
- Module 3 Lesson 4, which already teaches correlation in a messaging context.

---

## 1. The Attack Tor Does Not Claim to Stop

![[module_6_lesson_9_correlation.svg]]

*Figure 1. End-to-end correlation.*

An observer who can watch traffic entering the network near you, and traffic leaving it near your destination, does not need to break anything. Traffic goes in at one moment and comes out at another. The volumes match, the timings match, and the two flows can be linked.

**No encryption is broken. No relay is compromised. Nothing malfunctions.** The three relays work exactly as designed throughout.

### Why more hops do not help

The observer is standing at the two ends. What happens between them does not enter into the method.

A fourth relay, a fifth, a tunnel bolted on the front: none of it changes what the two endpoints look like. **This is the same limit Lesson 6.4 found for tunnel chains**, arriving here unchanged, and it is the reason hop counting stops being a useful way to think about any of this.

### What the project says

The design does not try to protect against an attacker who can see or measure traffic both entering and leaving the network (Reference 1).

That is published, in their own documentation, unhedged.

Their stated approach instead is to **reduce the chance that an adversary ends up in both of the right positions.** Which makes this a positional defence rather than a cryptographic one, and that distinction is what makes the next section answerable.

### The fix they chose not to build

A defence exists and has for decades. Hold messages, reorder them, and send steady cover traffic so timing carries no information. Remailer systems did exactly this.

The cost is delay measured in hours. **Tor chose interactive browsing over resistance to this attack, deliberately and in public** (Reference 1). It is a stated trade rather than an oversight, and a system that will not tell you which trades it made is a system you cannot reason about.

### Turning it into a question you can answer

Because the defence is positional, the question is positional: **who could plausibly see both ends of this particular connection?**

For most work the answer is nobody. Your traffic enters through one provider in one country and leaves through a relay somewhere else entirely, and no single party is watching both.

For a national adversary monitoring its own borders, with a destination inside that same country, the answer may well be somebody.

That is a specific question about your situation with a real answer, and it is worth considerably more than a general anxiety about whether Tor is broken.

---

## 2. Hostile Relay Operators

![[module_6_lesson_9_hostile_relays.svg]]

*Figure 2. Two documented campaigns, and why the better one avoided the exit.*

Anybody can run a relay. That is the design, and it has been abused at scale in ways that are worth knowing in detail, because the two documented campaigns show two entirely different levels of understanding.

### The exit operator

Ran exit relays and modified traffic passing through them. Specifically: downgraded encrypted connections to cryptocurrency services into unencrypted ones, then substituted the payment address so funds went to the operator (References 2, 3).

**At the peak, in February 2021, this reached roughly 27 per cent of all exit capacity**, and the operator was still holding several per cent months later.

What limited it: **it could only touch traffic that was not already encrypted end to end.** Ordinary encrypted browsing passed through untouched. The attack worked on connections that could be downgraded, which is why it targeted a specific class of service.

### The position operator

A separate campaign added relays in industrial quantities, with no contact details, across datacentres worldwide. The important detail is **which positions they took: mostly first hop and middle, with only a small exit presence.**

At the peak: roughly a 16 per cent chance of entering the network through one of theirs, about 35 per cent of passing through one in the middle, and up to 5 per cent at exit (References 4, 5).

**They were not trying to read anything.** Holding the first position tells you who the user is. Holding a large share of middle positions raises the odds of seeing enough of a circuit to correlate it. This is Figure 1's attack, attempted from inside the network rather than from the network's edges.

### What the pair tells you

**The exit is the obvious threat and the entry is the serious one.** A hostile exit reads whatever you failed to encrypt. A hostile entry, combined with visibility at the other end, tells somebody who you are.

**This is why Lesson 6.7's concentration figures matter.** The property that no single party holds both halves assumes the relays are separate parties. An operator running a large share of first and middle positions is buying precisely that assumption's failure, and doing it cheaply.

**And both were found, named and removed by volunteer researchers reading public relay data.** That is the auditability argument from Lesson 6.7 doing actual work. No equivalent exists for a commercial provider, where the same behaviour would simply not be visible from outside.

### Two practical consequences

**Use encrypted transport for everything.** The exit is the one position that sees your traffic in whatever state you left it. This matters more on Tor than off it, not less.

**Prefer onion addresses where a service publishes one.** Lesson 6.8 made this point and here is the reason in full: it removes the exit position from your circuit entirely, which removes the entire left-hand column of Figure 2.

---

## 3. What the Guard Design Was Defending Against

Lesson 6.7 explained why the first hop does not rotate, and the argument was probabilistic: constant rotation accumulates risk towards certainty, while a stable set decides the outcome once.

Figure 2's right-hand column is what that argument was about.

An adversary who adds hundreds of relays and waits is playing exactly the game that guard rotation would have handed them. Every time a client picks a fresh first relay, that adversary gets another chance. **Keeping the same guards for months means their 16 per cent is a one-time draw rather than a repeated one.**

The design was not defending against a hypothetical. It was defending against a thing that then happened, and the defence is the reason it did not work better than it did.

---

## 4. Traffic Fingerprinting, at the Right Size

![[module_6_lesson_9_fingerprinting_size.svg]]

*Figure 3. Why most published accuracy figures describe an easier task than the real one.*

Traffic fingerprinting means identifying which site somebody visited from the shape of the encrypted traffic: packet sizes, timing, volume. The published accuracy figures are frequently alarming, and they are real measurements. **They measure the wrong situation.**

### The closed world

In most published work the classifier is told the visit was to one of a small fixed list of sites, and only has to say which one.

Under those conditions: above 95 per cent accuracy when watching about five sites, **falling below 80 per cent at about twenty-five** (Reference 6). The number falls as the list grows, and real lists of interest are far longer than twenty-five.

The measurement is sound. The situation is artificial.

### The open world

In reality a person may visit any site at all, and almost always visits none of the ones being watched.

That changes which quantity decides the outcome. **Accuracy stops mattering and the false positive rate takes over**, because when the thing you are looking for is rare and the volume of ordinary traffic is large, even a small false positive rate buries every real detection under wrong ones.

The specific criticism came from measurement rather than argument. Earlier work was evaluated on synthetic datasets: one kind of traffic, simple models of user behaviour, fixed tooling, front pages only, and no attempt to reflect how often any given site is actually visited. Attacks tested against traffic genuinely collected at a working exit relay perform substantially worse, and need **orders of magnitude lower false positive rates** than the synthetic evaluations implied (References 6, 7).

**The finding is not that fingerprinting does not work.** It is that published figures describe a task easier than the real one, by a wide margin.

### The correction to Lesson 7.2

In Lesson 7.2 I cited a result on identifying websites from encrypted name resolution traffic: over ninety per cent accuracy from traces as short as ten packets, using far less data than fingerprinting the web traffic itself. I presented it flatly, as evidence that encrypting lookups hands a pattern-analysing observer a tidier signal.

**That figure comes from the same research tradition, and the same caution applies to it.** The structural point in that lesson stands: encrypted lookups are short and structured, and they occur in a predictable burst before each page load, which is genuinely good input for a classifier. But the accuracy number itself describes a laboratory task, and I should have said so.

The honest version: encrypting lookups does not remove the timing signal, and how well anybody can exploit that signal in practice depends on evaluation conditions that published figures usually flatter.

### What is actually deployed

Narrow, and worth knowing precisely.

**Padding that conceals the setup of client-side onion service circuits**, merged into the software some years ago. It covers the first stretch of circuit construction and is deliberately limited in scope (Reference 8).

**Additional restricted guard layers** that defend an onion service against having its entry relay discovered (Reference 9).

**Note what is not there: general cover traffic.** That is the Section 1 trade again. The defence that would work costs the interactivity the system exists to provide.

### The question to ask of any such number

**Was the classifier choosing from a short list, and what was its false positive rate at a realistic rate of occurrence?**

That is the same discipline this course has applied to product claims, applied to research claims. Research claims deserve it just as much, and they get it less often because they arrive with citations attached.

---

## 5. What This Adds Up To

Set the three limits against the threat models this course has used.

| If your concern is | Does Tor help? |
|---|---|
| Your network operator seeing your destinations | Yes, completely. This is the ordinary case. |
| A site building a profile across visits | Yes, provided Lesson 6.8's list is respected. |
| A hostile exit reading your traffic | Only if you encrypted it. Use onion addresses where offered. |
| An adversary running many relays | Partly. The guard design bounds it and does not eliminate it. |
| A national adversary watching both ends | No, and the project says so. |
| Anything you did while signed in | No, and nothing can. |

**Two of the six are outside what any routing system offers**, and one of those two is behavioural rather than technical.

That is a more useful map than "Tor is the strongest thing available," which is true and tells you nothing about whether it addresses your problem.

---

## Key Takeaways

- **Tor publishes its own defeat conditions.** That is unusual and it is the reason to trust the rest of the claims.
- **Correlation by an observer at both ends is not defended against**, by design. More hops do not help because the observer is at the ends.
- **The alternative was rejected deliberately**: delay and cover traffic work and cost hours of latency.
- **The correlation question is positional and therefore answerable.** Ask who could plausibly see both ends of your connection.
- **A hostile exit campaign reached 27 per cent of exit capacity in 2021**, and could only affect traffic that was not already encrypted.
- **A separate campaign took entry and middle positions instead**, up to 16 and 35 per cent, because it wanted correlation rather than content. That is the more serious attack.
- **Both were found by volunteers reading public data.** No equivalent visibility exists for commercial providers.
- **Published fingerprinting accuracy describes a closed-world task.** Above 95 per cent at five sites, below 80 at twenty-five, and the open world turns on false positive rates instead.
- **The figure I cited in Lesson 7.2 needed that qualification and did not get it.**

---

## Knowledge Check

**Question 1.** What does an observer need in order to perform end-to-end correlation?

A. Control of at least one relay in the circuit
B. Visibility of traffic entering the network near the user and leaving it near the destination
C. The ability to decrypt one layer of the circuit's encryption
D. A copy of the network consensus from the directory authorities

**Answer: B**

No encryption is broken and no relay is compromised. Traffic goes in at one moment and comes out at another, and the volumes and timings match.

---

**Question 2.** Why does adding a fourth or fifth relay not defend against correlation?

A. Because the additional relays would need to be operated by the same party
B. Because the directory authorities do not permit longer circuits
C. Because the observer is positioned at the two ends, and what happens between them is not part of the method
D. Because additional relays increase timing variance in the attacker's favour

**Answer: C**

This is the same limit Lesson 6.4 found for tunnel chains. It is the reason hop counting stops being a useful way to think about the problem.

---

**Question 3.** What defence against correlation exists, and why does Tor not use it?

A. Rotating guards on every circuit, which would break the network consensus
B. Encrypting the circuit a second time, which would double latency
C. Randomising relay selection by country, which would concentrate traffic in a few places
D. Holding and reordering messages with steady cover traffic, which costs delay measured in hours

**Answer: D**

Remailer systems did exactly this. Tor chose interactive browsing over resistance to the attack, deliberately, and published the trade rather than hiding it.

---

**Question 4.** Because the correlation defence is positional rather than cryptographic, what should you ask?

A. Whether the destination supports an onion address
B. Whether your guards have rotated within the last three months
C. Who could plausibly observe both ends of this particular connection
D. Whether the exit relay is in a privacy-friendly jurisdiction

**Answer: C**

For most work the answer is nobody. For a national adversary watching its own borders with a destination inside that country, it may be somebody. That is a real, answerable question about your situation.

---

**Question 5.** What limited the documented exit relay campaign that reached 27 per cent of exit capacity?

A. It could only affect traffic that was not already encrypted end to end
B. Directory authorities capped the share of capacity any operator could hold
C. It only worked against onion service connections
D. Guard rotation meant it saw each user only briefly

**Answer: A**

It downgraded connections to cryptocurrency services and substituted payment addresses. Ordinary encrypted browsing passed through untouched.

---

**Question 6.** Why did a separate documented adversary concentrate on entry and middle relays rather than exits?

A. Exit relays are more expensive to operate and attract complaints
B. Middle relays see more traffic than exits do
C. Exit positions are capped by the consensus while entry positions are not
D. It was not trying to read traffic. Holding the first position identifies the user, and many middle positions raise the odds of correlating a circuit

**Answer: D**

At peak this reached roughly 16 per cent of first-hop selection and 35 per cent of middle. Option A is true and is not the reason: the choice was about capability, not cost.

---

**Question 7.** How does this relate to the guard design from Lesson 6.7?

A. Guards would have been useless against an adversary of this size
B. Keeping the same guards for months makes the adversary's share a one-time draw rather than a repeated one
C. Guard rotation would have prevented the adversary from being selected at all
D. The guard design was introduced in response to this specific campaign

**Answer: B**

An adversary who adds hundreds of relays and waits is playing the game that constant rotation would have handed them. The design was defending against something that then happened.

---

**Question 8.** Published fingerprinting accuracy is above 95 per cent for about five sites. What happens as the list of watched sites grows?

A. It falls below 80 per cent at about twenty-five sites, and real lists are far longer
B. It rises, because more training data improves the model
C. It stays flat, because the classifier is trained per site
D. It becomes undefined, because closed-world testing requires a fixed list

**Answer: A**

The measurement is sound and the situation is artificial. Real adversaries are not choosing from a list of five.

---

**Question 9.** In the open world, which quantity decides whether fingerprinting is useful?

A. The classifier's overall accuracy on the training set
B. The number of relays the adversary operates
C. The average page size of the sites being watched
D. The false positive rate, because a rare target and a large volume of ordinary traffic bury real detections under wrong ones

**Answer: D**

Genuine-traffic measurements show attacks need orders of magnitude lower false positive rates than synthetic evaluations implied. Accuracy stops being the deciding number.

---

**Question 10.** What traffic-analysis defences are actually deployed in Tor?

A. General cover traffic across all circuits, added several years ago
B. Padding that conceals client-side onion service circuit setup, and restricted guard layers protecting onion services from entry relay discovery
C. Randomised delay on every relay, tunable by the user
D. None; all proposed defences remain unimplemented

**Answer: B**

Both are narrow by design. General cover traffic is specifically absent, because it costs the interactivity the system exists to provide.

---

## References

1. Tor Project support, on traffic confirmation and the limits of the design against an attacker who can observe both sides. https://support.torproject.org/about/attacks-on-onion-routing/
2. The Record, "Thousands of Tor exit nodes attacked cryptocurrency users over the past year." https://therecord.media/thousands-of-tor-exit-nodes-attacked-cryptocurrency-users-over-the-past-year
3. The Hacker News, on the share of exit capacity reached by the SSL stripping campaign. https://thehackernews.com/2021/05/over-25-of-tor-exit-relays-are-spying.html
4. The Record, "A mysterious threat actor is running hundreds of malicious Tor relays." https://therecord.media/a-mysterious-threat-actor-is-running-hundreds-of-malicious-tor-relays
5. Schneier on Security, "Someone Is Running Lots of Tor Relays," December 2021. https://www.schneier.com/blog/archives/2021/12/someone-is-running-lots-of-tor-relays.html
6. Cherubin, Jansen and Troncoso, "Online Website Fingerprinting: Evaluating Website Fingerprinting Attacks on Tor in the Real World," USENIX Security 2022. https://www.usenix.org/system/files/sec22-cherubin.pdf
7. Jansen et al., "A Measurement of Genuine Tor Traces for Realistic Website Fingerprinting." https://arxiv.org/html/2404.07892
8. Tor Project, circuit-level padding specification. https://spec.torproject.org/padding-spec/circuit-level-padding.html
9. Tor Project, "Announcing Vanguards Support in Arti," on restricted guard layers for onion services. https://blog.torproject.org/announcing-vanguards-for-arti/

---

## Where This Goes Next

**Lesson 6.10** closes the module: Tor for security work, when it is the wrong tool, what running a relay involves and what an exit relay commits you to legally, and a decision procedure covering everything Module 6 has built.

The table in Section 5 is the thing to carry into it. The question was never which tool is strongest. It was which observer you are trying to remove, and whether the tool in front of you removes that one.
