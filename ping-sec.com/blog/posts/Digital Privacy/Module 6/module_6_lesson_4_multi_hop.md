---
title: "Module 6 Lesson 4 - Multi-Hop and Chains: Splitting Knowledge, and Where It Stops Helping"
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.4
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - vpn
  - tor
  - multi-hop
  - traffic-analysis
---

# Multi-Hop and Chains: Splitting Knowledge, and Where It Stops Helping

## About This Lesson

Lesson 6.1 established that a VPN transfers visibility rather than removing it. **Multi-hop is the attempt to split that visibility between two parties so neither holds both halves.** It is a real property, it works, and it is narrower than it sounds.

Three things in the standard treatment need fixing. The terminology for combining a tunnel with Tor is inverted, and the phrasing itself is one the projects that maintain these tools deliberately avoid. The reason not to put a VPN in front of Tor is asserted without the technical argument that makes it convincing. And the limit that no number of hops changes gets one bullet in a list of ten, when it is the thing that decides how many hops are worth having.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Say who knows your address, who knows your destination, and who knows both, for five arrangements.
- Explain the difference between a provider's own multi-hop and a chain of two providers, in terms of who can be compelled.
- Describe both tunnel-and-Tor arrangements without using an ambiguous phrase.
- State the guard relay argument, which is why adding a tunnel to Tor changes its threat model.
- Explain the end-to-end correlation limit and why hop count does not address it.
- Name what actually does address it.
- Recognise the three ways a chain fails in practice.

### Prerequisites

- Lesson 6.1, particularly Figure 1, which this lesson extends.
- Lesson 6.2, particularly Figure 3 on traffic analysis defence, which Section 6 returns to.
- Lesson 3.4 on timing and metadata.

---

## 1. Who Knows What

![[module_6_lesson_4_who_knows_what.svg]]

*Figure 1. Multi-hop splits knowledge between parties. The column that matters is the third.*

**With no tunnel**, your network operator knows both your address and your destinations.

**With a single tunnel**, the provider knows both. That is Lesson 6.1's entire subject.

**With a provider's own multi-hop**, the entry server sees your address, the exit server sees your destinations, and **the provider operates both.** One company, one legal identity, reachable by one legal process. The knowledge is split across machines and not across parties.

**With two providers chained**, provider A sees your address and provider B sees your destinations, and neither holds both unless the two combine their records. **This is the arrangement that earns the name.**

**With a tunnel then Tor**, the provider knows your address and that you use Tor, the exit relay knows your destination, and no single party holds both by design.

### Read the Third Column

**The distinction between rows three and four is the whole point of the feature**, and marketing collapses them. A provider's built-in multi-hop is a genuine improvement on a single hop, because compromising one server no longer reveals both halves. It is not a defence against the provider, because the provider is both servers.

If the party you are worried about is the provider, only row four addresses it. If the party you are worried about is somebody who might compromise a single server, row three is sufficient and much easier.

**Say which one it is before you choose**, because the two answer different questions and cost very different amounts of effort.

### What Every Row Has in Common

- **Your account and your payment.** Whoever you bought from can connect the subscription to you unless the architecture from Lesson 6.3 prevents it. Two hops does not.
- **Signing in.** The destination knows exactly who you are the moment you log into an account, whatever address the request arrived from.
- **Your browser fingerprint, and the application components from Lesson 5.2.** None of this happens at the network layer, so none of it is affected.

**So multi-hop answers one question well and a narrow one.** If your problem is in that list, adding hops is the wrong tool and no number of them will help.

---

## 2. Why This Matters More For You

**Because you will be asked to justify the cost.** Multi-hop halves your throughput and doubles your subscriptions, and a client or an editor will reasonably ask what it buys. "No single party knows both halves" is a defensible answer. "More privacy" is not.

**And because the terminology in Section 4 appears in things you will write.** Getting it wrong is the sort of error a knowledgeable reader spots immediately, and it undermines everything around it.

---

## 3. The Chain, in Practice

**Two clients on one machine is presented as the easy method and it is the least reliable one.** Both applications want to control the routing table, both want to control name resolution, and both want to cut the network when their own tunnel drops. The result is a set of states neither was designed for, and the failures are frequently silent.

**The arrangements that work reliably** put a boundary between the two hops: one tunnel on a router and the second on the device, or one on the host and the second inside a virtual machine. The boundary is what stops the two clients fighting, and it is the reason these are more robust rather than merely more complicated.

**Choosing the two providers.** The relevant question is not which countries they are in, for the reasons Lessons 6.1 and 6.3 gave at length. It is:

- **Are they actually separate companies?** Lesson 6.1's ownership map matters here more than anywhere else, because chaining two brands owned by one group produces row three of Figure 1 while paying for row four.
- **Can one legal process reach both?** This is the useful version of the jurisdiction question: not alliance membership, but whether a single request could compel both parties.
- **Do both hold as little as possible?** Two providers that each require an email address and a card are two records linking you to the chain.

---

## 4. Say the Direction

This section corrects a naming problem, and the correction matters because the two arrangements have opposite properties.

![[module_6_lesson_4_say_the_direction.svg]]

*Figure 2. Write the arrow diagram. It removes the ambiguity entirely.*

**The phrases "VPN over Tor" and "Tor over VPN" are used inconsistently and mean opposite things to different writers.** The documentation for the systems that actually implement these arrangements explicitly discourages the phrasing as ambiguous and writes the chain out instead: connecting to a VPN *before* Tor, or connecting to Tor *before* a VPN. (Reference 1)

**The source material for this lesson attaches both labels to the wrong arrangement**, which is exactly the failure the convention exists to prevent, and is a good argument for abandoning the phrasing rather than trying to remember which way round it goes.

### You → VPN → Tor → Internet

**What it is for: hiding Tor usage from your network.** Your operator sees a connection to a provider and not that you use Tor. That is the whole benefit and in some places it is a serious one.

**Who sees what.** Operator: a tunnel, nothing else. Provider: your real address, and that you use Tor. Tor entry relay: the provider's address. Exit relay and destination: unchanged from plain Tor.

**Acceptable where that reason applies**, and note that the provider becomes a permanent observer of your Tor use, which Section 5 is about.

### You → Tor → VPN → Internet

**What it is for: a stable address at the far end.** The destination sees the provider rather than a Tor exit, which helps only where a service refuses Tor exits outright.

**What it costs.** Your operator sees that you use Tor. The provider sees everything you do, and now sees it consistently, **which gives you a persistent identity across sessions that Tor was specifically designed to prevent.**

You have paid Tor's costs and given away its main property. The configuration is also genuinely difficult to get right, and the source material's instruction to "configure Tor to use VPN B as exit" describes something Tor does not offer as a setting. What people actually do involves running a tunnel client inside an isolated workstation, which is a different and considerably more fragile arrangement than the sentence implies.

---

## 5. The Guard Relay Argument

Every guide says the Tor Project does not recommend adding a VPN. Almost none says why, and the reason is the interesting part.

**Tor deliberately keeps a small, slowly rotating set of first relays.** The reasoning is that if you picked a fresh first relay for every circuit, you would eventually pick a hostile one, and repeated exposure across many sessions is worse than sustained exposure to a few. The design is a considered answer to a specific attack rather than an implementation convenience.

**Putting a provider in front of Tor replaces that considered answer** with a single permanent first hop that you chose, that sees every session, and that knows who you are because you pay it.

**That is a change to Tor's threat model rather than an addition to it.** Whether it is a good change depends entirely on whether hiding Tor usage from your network operator is worth more to you than the property you gave up, and that is a question you can now answer rather than defer to an authority.

**What the project itself says, quoted rather than paraphrased:** it does not recommend using a VPN with Tor unless you are an advanced user who knows how to configure both in a way that does not compromise your privacy. (Reference 2)

**Read that carefully.** It is narrower than a prohibition and narrower than an endorsement, and guidance that reports it as either has flattened it.

---

## 6. What No Hop Count Changes

![[module_6_lesson_4_diminishing_returns.svg]]

*Figure 3. What each hop buys, and the limit that sits underneath all of it.*

### What Each Hop Buys

**The first hop** removes your network operator from the picture and gives the destination a different address. **This is most of the value**, and it is where the diminishing begins.

**The second hop** splits knowledge so no single party holds both halves, and only if the two are separate companies under separate control.

**The third and beyond** adds latency, cost and failure modes, and very little else.

### The Limit

**An adversary who can see traffic entering the chain and see traffic leaving it can match the two by timing and volume alone.** The contents are irrelevant. So is the number of hops in between.

**This is stated openly by the people who designed the strongest system available.** Tor's documentation does not claim to defend against somebody who can observe both ends of a connection. Nothing in this lesson does better than Tor here, so hops are not the answer to that problem.

### Three Things That Do Change the Answer

**Reshaping the traffic.** Cover traffic and regularised packet sizes attack the timing and volume matching directly, which is the only thing that does. Lesson 6.2 Figure 3 covered a shipping implementation.

**Many relays with rotating circuits.** Tor's design, which is a different answer to the same problem and a far better tested one than any chain you assemble yourself.

**Not being interesting at both ends.** Compartmentalisation and account discipline decide more than routing does, which is Module 5 arriving in a networking lesson.

**And a verified single hop beats an unverified double one**, because a chain you have not tested for leaks is a chain whose shape you do not know.

### The Three Failures

**Two clients, one routing table.** Covered in Section 3.

**Two connection guards.** Each wants to cut the network when its own tunnel drops, and together they produce states neither was designed for. Testing this is the part people skip and it is the part that matters.

**Name resolution escaping the chain.** The commonest silent failure, and the one that undoes the whole arrangement without any visible symptom.

---

## 7. Deciding

The source material offers a five-level threat matrix and eight scenarios. Here is the shorter version.

**If you cannot say which specific party you are hiding the other half from, you have not yet found a reason for the second hop.** That sentence is the whole decision procedure.

Worked through:

- **"I do not want my network operator to see my destinations."** One hop. Done.
- **"I do not want any single machine to hold both halves, in case one is compromised."** A provider's own multi-hop. One click, one subscription.
- **"I do not want my provider to hold both halves."** Two providers, separately owned, chained with a boundary between them.
- **"I do not want my network operator to know I use Tor."** You → VPN → Tor, with Section 5's caveat understood.
- **"I need anonymity."** Tor, on its own, which is Lesson 6.7. Not a chain you built.

**And the honest answer for most readers of this course is one hop**, or none on a network they control. Multi-hop is a specific tool for a specific split, not a higher tier of the same product.

---

## 8. What to Take From This

**Multi-hop splits knowledge; it does not remove it.** Read the third column of Figure 1 and know which row you are buying.

**A provider's own multi-hop is not a defence against that provider.** It is a defence against one of its servers being compromised, which is a real thing and a different thing.

**Write the arrow diagram**, because the usual phrasing means opposite things to different writers and the source material for this lesson gets both labels backwards.

**The guard relay argument is the reason**, not an appeal to authority, and it lets you make the decision yourself.

**And no hop count defeats an adversary watching both ends.** Traffic shaping does something about that. Hop count does not.

**Lesson 6.7:** Tor, which is the properly designed answer to the problem chains are approximating. The guard argument in Section 5 is developed in full there.

---

## Knowledge Check

**Question 1.** With a provider's own built-in multi-hop, who knows both your address and your destination?

- A) Nobody, that is the purpose of the feature
- B) The provider, which operates both the entry and the exit server
- C) Only the exit server
- D) Your network operator

**Answer: B.** The knowledge is split across machines rather than across parties, so it defends against one server being compromised rather than against the provider.

**Question 2.** What makes a chain of two providers different from one provider's multi-hop?

- A) It is faster
- B) It uses different protocols at each hop
- C) Two separate companies each hold one half, so neither can reconstruct the path without combining records
- D) It requires no configuration

**Answer: C.** Which is why chaining two brands owned by the same group produces the weaker arrangement while paying for the stronger one.

**Question 3.** Why do the projects that maintain these tools avoid the phrases "VPN over Tor" and "Tor over VPN"?

- A) Because the arrangements are equivalent
- B) Because the phrases are trademarked
- C) Because they refer only to older software
- D) Because writers use them inconsistently to mean opposite arrangements, so they write the chain order out instead

**Answer: D.** The source material for this lesson attaches both labels to the wrong arrangement, which is the failure the convention exists to prevent.

**Question 4.** In a You to VPN to Tor arrangement, what does the provider see?

- A) Nothing, the traffic is encrypted by Tor
- B) Your real address, and the fact that you use Tor
- C) Your destinations, but not your address
- D) Only the volume of traffic

**Answer: B.** The benefit is that your network operator does not see Tor usage. The cost is that the provider does, permanently.

**Question 5.** What is the guard relay argument?

- A) That Tor deliberately keeps a small, slowly rotating set of first relays, and a provider replaces that with one permanent first hop that knows who you are
- B) That Tor relays are operated by volunteers who cannot be vetted
- C) That the first relay in a circuit is always the slowest
- D) That guard relays are frequently blocked by censors

**Answer: A.** Repeated exposure across many sessions is worse than sustained exposure to a few, which is why the design is a considered answer rather than an implementation detail.

**Question 6.** What does the Tor Project actually say about using a VPN with Tor?

- A) It recommends it for all users
- B) It prohibits it entirely
- C) It does not recommend it unless you are an advanced user who can configure both without compromising your privacy
- D) It takes no position

**Answer: C.** Narrower than a prohibition and narrower than an endorsement, and guidance reporting it as either has flattened it.

**Question 7.** An adversary can observe traffic entering your chain and leaving it. How much does adding a third hop help?

- A) Almost nothing. The two ends can be matched by timing and volume regardless of what sits between them
- B) It triples the difficulty
- C) It helps only if the hops are in different countries
- D) It makes the attack impossible

**Answer: A.** Tor's own documentation does not claim to defend against an adversary who can observe both ends.

**Question 8.** What actually addresses end-to-end correlation?

- A) More hops
- B) Stronger encryption at each hop
- C) Choosing servers in privacy-friendly jurisdictions
- D) Reshaping the traffic with cover traffic and regularised packet sizes

**Answer: D.** Lesson 6.2 Figure 3 covered a shipping implementation of exactly this.

**Question 9.** Why is running two tunnel clients on one machine the least reliable way to chain?

- A) It is not supported by any provider
- B) Both compete to control routing and name resolution, and both want to cut the network when their own tunnel drops
- C) It is prohibited by most terms of service
- D) The encryption layers conflict

**Answer: B.** The arrangements that work put a boundary between the hops, such as a router and a device, or a host and a virtual machine.

**Question 10.** What is the shortest useful test of whether you need a second hop?

- A) Whether your threat level is medium or above on a five-point scale
- B) Whether your provider offers it at no extra cost
- C) Whether your connection can spare the bandwidth
- D) Whether you can name the specific party you are hiding the other half from

**Answer: D.** If you cannot name them, you have not yet found a reason for the second hop.

---

## Lab: Split the Knowledge, Then Break It

**Time: 2 to 3 hours. Parts 1, 4 and 5 need no subscription.**

---

### Part 1: Fill In the Table (25 minutes)

Reproduce Figure 1 for your own situation, from your own settings rather than from the figure.

| Arrangement | Knows your address | Knows your destination | Knows both | Available to you today? |
|---|---|---|---|---|
| What you use now | | | | |
| Your provider's multi-hop | | | | |
| A chain of two providers | | | | |
| You to VPN to Tor | | | | |

Then answer: **for the row you would choose, name the specific party you are hiding the other half from.**

**Deliverable.** The table and your named party. "Nobody in particular" is an honest and common answer, and it is the correct result for many readers.

---

### Part 2: Configure a Provider Multi-Hop (25 minutes)

If your provider offers it, turn it on and measure the cost.

| | Single hop | Multi-hop |
|---|---|---|
| Download throughput | | |
| Latency to a fixed host | | |
| Exit address shown | | |
| Entry location, if selectable | | |
| Time to connect | | |

**Deliverable.** The table, and one sentence on whether the measured cost matches what the provider's marketing implies.

---

### Part 3: Build and Break a Chain (40 minutes)

Only if you have two providers. If you do not, do Part 4 instead and say so.

1. Build the chain using a boundary: host and virtual machine, or router and device. **Do not use two clients on one host**, except as step 4.
2. Verify: exit address, resolver in use, whether anything escapes the chain.
3. **Then break it deliberately.** Drop the inner tunnel and observe what happens. Drop the outer one and observe. Record whether traffic stopped, continued, or continued through only one hop.
4. Now try two clients on one host and repeat step 3. Record the difference.

| Test | Boundary method | Two clients on one host |
|---|---|---|
| Exit address correct | | |
| Resolver inside the chain | | |
| Inner tunnel dropped: what happened | | |
| Outer tunnel dropped: what happened | | |

**Deliverable.** The table. **Step 3 is the exercise**, and a chain that leaks when broken is a valid and useful result to report.

---

### Part 4: Audit the Terminology (25 minutes)

Find four published guides covering tunnels combined with Tor.

| Source | Phrase used | Arrangement it describes | Correct, inverted, or ambiguous? |
|---|---|---|---|

**Deliverable.** The table, and one sentence on how many of the four you could resolve without guessing.

---

### Part 5: The Guard Relay Question (20 minutes)

Read the Tor Project's own material on guard relays and answer in your own words:

1. What attack are guard relays a defence against?
2. What does putting a provider in front of Tor do to that defence?
3. Under what circumstances would you accept that trade, and for what reason?

**Deliverable.** Three short paragraphs. The third one is the assessed part and either answer is acceptable with reasoning.

---

### Part 6: Write the Decision (15 minutes)

Write the advice you would give a reader asking whether they need multi-hop.

It must contain: the question from Section 7, at least one case where the answer is no, and the honest cost.

**Deliverable.** Half a page at most.

---

### Submission

1. **The table**, Part 1, with your named party.
2. **Provider multi-hop**, Part 2, with measurements.
3. **Chain built and broken**, Part 3, or a note that you did Part 4 instead.
4. **Terminology audit**, Part 4, four sources.
5. **Guard relays**, Part 5, three paragraphs.
6. **The decision**, Part 6.
7. **Reflection**, half a page: before this lesson, did you believe more hops meant more privacy, and what changed?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| The table | 20 | Filled from own settings, and a specific party named or its absence stated honestly |
| Provider multi-hop | 15 | Measured rather than assumed |
| Chain built and broken | 25 | The breaking step actually performed, with both methods compared |
| Terminology audit | 15 | Four sources, arrangement resolved rather than guessed |
| Guard relays | 15 | Explained in own words, with a reasoned position on the trade |
| The decision | 5 | Includes a case where the answer is no, and the cost |
| Reflection | 5 | Honest about the prior belief |
| **Total** | **100** | |

**Note the weighting.** Breaking the chain carries the most, because a chain nobody has tested under failure is the single most common way this goes wrong, and the failure is silent.

---

## Additional Resources

### Primary Sources

- Whonix, on connecting to a VPN before Tor: https://www.whonix.org/wiki/Tunnels/Connecting_to_a_VPN_before_Tor
- Whonix, on connecting to Tor before a VPN: https://www.whonix.org/wiki/Tunnels/Connecting_to_Tor_before_a_VPN
- Tor Project support, can I use a VPN with Tor: https://support.torproject.org/faq/faq-5/

### On the Terminology

Read both Whonix pages together. They are the clearest published treatment of these arrangements, and the reason they read carefully is that the project has spent years correcting people who used the ambiguous phrasing.

### Discussion Questions

1. Part 4 asked you to audit four guides for terminology. How many were unambiguous, and what does that suggest about how this topic gets written?
2. Section 5 gives a technical argument rather than an appeal to authority. Which would you use when writing for beginners, and does that choice have a cost?
3. Part 3 asked you to break your own chain. Why is that step almost always omitted from published setup guides?

---

## References

1. [Whonix, on tunnel terminology and why the project writes the chain order out](https://www.whonix.org/wiki/Tunnels/Introduction)
2. [Tor Project support, can I use a VPN with Tor](https://support.torproject.org/faq/faq-5/)
