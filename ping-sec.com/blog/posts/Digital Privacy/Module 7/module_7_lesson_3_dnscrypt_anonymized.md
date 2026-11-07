---
title: "Module 7 Lesson 3: Anonymised DNS: A Good Design, Running on Seven Operators"
course: Digital Privacy for Ethical Hackers
module: 7
lesson: 7.3
format: article
reading_time: 35 minutes
tags:
  - digital-privacy-course
  - privacy
  - dns
  - dns-privacy
  - dnscrypt
  - anonymized-dns
  - odoh
---

# Anonymised DNS: A Good Design, Running on Seven Operators

## About This Lesson

Lesson 7.1 ended on a problem it could not solve. Lesson 7.2 ended on the same one. **Encrypting your lookups takes the names away from your internet provider and hands all of them to a resolver instead**, tied to your address, in real time. Both lessons named that as the unresolved item and pointed here.

This is the lesson that attacks it, and the design is genuinely good. A relay sits between you and the resolver. The relay sees your address but cannot read your queries. The resolver reads your queries but sees the relay's address rather than yours. **One party knows who, the other knows what, and neither knows both.**

That is the first thing in Module 7 that goes after the resolver's position rather than relocating it.

Three things need saying about it.

**The guarantee rests on three conditions, and only one of them is checkable.** The other two are the unverifiable promises this course keeps running into.

**The checkable condition is the one nobody checks, and the published lists do not look good.** Three hundred and thirteen relays sounds like a crowd. It is seven operators, two of whom run ninety-six per cent of them, and both of those also run resolvers. The lesson's own example configuration can route you through one company's relay to the same company's resolver.

**All four facts the lesson gives about the newer oblivious alternative are wrong**, and three of them are wrong in the same direction.

### A Note on the Source

The script and deck are complete, with no truncation artefacts, and the configuration syntax it teaches is correct. The problems are in the claims, not the commands.

One item is outside the lesson's control and worth adding: the proxy software gained **post-quantum protection for query confidentiality in July 2026, enabled by default**, which answers a question Lessons 6.2 and 6.6 raised.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Describe what the relay learns and what the resolver learns, precisely.
- State the three conditions the separation depends on, and say which one you can verify.
- Read the published lists and determine whether your two hops are actually two parties.
- Explain why the lesson's example configuration can defeat itself.
- Write a configuration that pins both hops, and verify it.
- State the real status of the oblivious alternative and the real size of its relay pool.

### Prerequisites

- Lessons 7.1 and 7.2.
- Module 6 Lessons 6.7 and 6.9, because the argument here is the same shape.

---

## 1. What Each Hop Learns

![[module_7_lesson_3_the_split.svg]]

*Figure 1. The split, and the three conditions it depends on.*

A local proxy runs on your machine. Your system resolves through it. The proxy encrypts each query so that **only the resolver can read it**, then sends it to a relay.

**The relay learns** your address, which resolver you chose, and how often you query. It cannot read the queries, because they are encrypted to the resolver's key and the relay does not hold it.

**The resolver learns** every name you look up and the relay's address. It does not learn yours.

That is the design, and it is the right design. It is also conditional.

### The three conditions

**One: the relay and the resolver are run by different parties.** If one operator holds both hops, they hold your address, your queries and the link between them. The split bought nothing.

**Two: those two parties do not compare records.** Relay timestamps plus resolver timestamps reassemble the picture. Nothing in the protocol prevents it. This is the Lesson 6.3 problem, now twice over, because you are trusting two unverifiable no-logging claims instead of one.

**Three: nobody is watching both links at once.** An observer who sees your link to the relay and the relay's link to the resolver matches them on timing. **This is Lesson 6.9's correlation attack, unchanged**, and no amount of relaying addresses it.

### One of the three is checkable

Conditions two and three are the familiar ones. You cannot audit a promise and you cannot survey the internet.

**Condition one is different.** The relay list and the resolver list are both published, as plain text, and both name their operators. You can read them and find out whether your two hops are two parties.

Section 2 does that.

### One detail the lesson omits

The relay sees **which resolver you chose**. If you pick an unusual one, that choice is itself an identifier, in the same way Lesson 6.8 described for browser settings. The relay cannot read your queries and can still tell you apart from its other users.

---

## 2. Who Actually Runs the Relays

![[module_7_lesson_3_who_runs_relays.svg]]

*Figure 2. The published list, counted in October 2026.*

The relay list holds **313 entries** (Reference 6). That sounds like a large, well-distributed network spread across fifty countries, and the country spread is real.

**It is seven operators.**

| Operator | Relay entries |
|---|---|
| dnscry.pt | 236 |
| CryptoStorm | 66 |
| The other five combined | 11 |

**Two operators hold about 96 per cent of every relay.** Many of the 313 are the same host listed twice for two address families, so the effective count is lower still.

### And the same names run resolvers

This is the part that matters for condition one. The two largest relay operators are also two of the largest resolver operators in the public list: **dnscry.pt runs roughly 107 resolvers alongside its 236 relays, and CryptoStorm roughly 73 alongside its 66** (Reference 7).

So a single operator can occupy both of your hops. Not through an attack. **Through ordinary configuration.**

### The lesson's example configuration can defeat itself

The script gives this, and it is the one most readers will copy:

```toml
routes = [
  { server_name='*', via=['anon-cs-de', 'anon-cs-fr'] }
]
```

Both named relays belong to one operator. `server_name='*'` means **any compatible resolver**, chosen by the software on grounds of speed. That operator runs dozens of compatible resolvers.

Nothing prevents the selection landing on one of them. When it does, one company holds your address, your queries, and the link between them, **and you are worse off than with plain encrypted resolution**, because you added a hop and latency while believing you had separated the two halves.

The configuration is not wrong as syntax. It is wrong as a privacy claim, and the software does not warn you.

### The fix

**Pin both ends, to different operators:**

```toml
routes = [
  { server_name='<a resolver from operator A>', via=['<a relay from operator B>'] }
]
```

Then verify it. Open the two published lists, find both of your choices, and confirm the operator names differ. They are plain text files and this takes minutes.

```
https://download.dnscrypt.info/dnscrypt-resolvers/v3/relays.md
https://download.dnscrypt.info/dnscrypt-resolvers/v3/public-resolvers.md
```

**Different countries is the usual advice and it is the weaker test.** Two hosts in two countries operated by one company satisfy the usual advice and fail the actual condition. Different operators is what the design depends on.

### On `require_nolog`

The script's configuration sets `require_nolog = true`, which filters the server list to entries that **claim** not to log. That claim is a field in a published file, written by the operator.

It is worth setting, because it narrows the pool to operators who at least made the statement. It is not verification, and Lesson 6.3 is the reason. A configuration flag that reads somebody's self-description is a filter, not an audit.

### What this does and does not mean

It does not mean the design is bad. **It means the default configuration is unsafe and the deployed network is far more concentrated than the entry count suggests.**

This is Lesson 6.9's concentration finding one layer down, and sharper here: seven operators rather than thousands of volunteers.

And note the thing that is better than anything in Module 6: **you can check.** Two files and a text search. No commercial provider in that module permitted any verification at all.

---

## 3. The Oblivious Alternative

![[module_7_lesson_3_odoh_size.svg]]

*Figure 3. Four claims, corrected.*

The lesson presents oblivious resolution over HTTPS as the better future and gives four facts. All four need correcting.

**"IETF standard, versus proprietary."** Given as its main advantage. It is an **Experimental RFC, published as an Independent Submission**, and the document states plainly that it is not endorsed by the IETF (Reference 1). The stated advantage does not exist. Both schemes sit outside the standards track.

**"Proxy software support is planned."** Given as a reason to wait. It has **shipped since version 2.0.46**, with its own server and relay lists and a separate setting (Reference 2). You could use it today.

**A browser preference to switch it on.** Given as a concrete instruction. The documented preference takes values 0 to 5 and none of them selects oblivious mode, and the name the script gives does not appear in the vendor's documentation (Reference 3). **Treat it as unverified.** Do not paste configuration settings you cannot find documented.

**"Use it when more widely deployed."** The closing recommendation, with no number attached. The published relay list for the protocol holds **two entries**. Not two operators running many relays. Two entries in total (Reference 4).

### So which should you run

**The relay scheme in Figure 1, for now**, and not because it is better designed. Because 313 entries across seven operators is a larger set to hide in than two.

Both schemes depend on the same three conditions, and the oblivious one satisfies the first one worse today. Revisit when that list grows: it is one file to check.

### The pattern

Three of those four errors push the same way. **They make the newer option sound more official and more available than it is.** That is the normal direction of drift in security writing, and the correction is always the same question: ask for the number. How many operators, and who are they?

"Standardised" and "deployed" are different claims. A protocol can be well designed, documented, implemented, and still have an anonymity set of two.

---

## 4. One Thing the Lesson Could Not Have Known

The proxy software added **post-quantum protection for query confidentiality in version 2.1.17, released 13 July 2026, enabled by default** and disabled with a single setting (Reference 5).

That is the harvest-now-decrypt-later concern from Lessons 6.2 and 6.6, arriving in DNS before it arrived in most tunnels. Lookups are exactly the traffic worth recording now and reading later, because a decade-old record of what somebody looked up is still a decade-old record of what they were interested in.

The release notes do not name the algorithms. If that matters for your work, read the source rather than the notes.

---

## 5. Where This Leaves Module 7

| If your concern is | Does this lesson help? |
|---|---|
| Your provider seeing your queries | Already solved in 7.2. This adds nothing. |
| Your resolver seeing queries tied to you | Yes, and only this. It is the point of the lesson. |
| Your resolver and relay being one company | Only if you pin both ends yourself. |
| Two operators comparing records | No. Unverifiable, and now you trust two. |
| An observer watching both links | No. Lesson 6.9, unchanged. |
| The destination address your provider still sees | No. Lesson 7.1 Figure 1, row one, still standing. |

**One row improves.** That is a real improvement and it is the row 7.1 and 7.2 both left open, so the lesson earns its place. It is one row.

---

## Key Takeaways

- **The design is sound and it is the right answer to the problem 7.1 and 7.2 left open.** A relay that cannot read, a resolver that cannot see you.
- **It depends on three conditions.** Different operators, no record-sharing, and no observer on both links. Only the first is checkable.
- **313 relays is seven operators**, two of whom run about 96 per cent of them.
- **Those same two run large numbers of resolvers**, so one company can hold both of your hops.
- **The lesson's example configuration leaves the resolver as a wildcard**, which permits exactly that. Pin both ends.
- **Verify by operator, not by country.** Two countries, one company passes the usual test and fails the real one.
- **`require_nolog` filters on a self-declared field.** A filter, not an audit.
- **The oblivious alternative is an Experimental RFC, not an IETF standard**, has shipped in the proxy for years rather than being planned, and has two relays in total.
- **Post-quantum query protection arrived in July 2026**, on by default.

---

## Knowledge Check

**Question 1.** What does the relay learn in an anonymised setup?

A. Your address, which resolver you chose, and your query timing, but not the names
B. The names you look up, but not your address
C. Both your address and the names, which is why relay choice matters
D. Nothing; it forwards opaque traffic without seeing either endpoint

**Answer: A**

The queries are encrypted to the resolver's key, which the relay does not hold. Option C describes the failure this lesson is about, not the normal case.

---

**Question 2.** Which of the separation's three conditions can you actually verify?

A. That the two operators do not compare their records
B. That the relay and the resolver are run by different parties
C. That no observer is watching both links at once
D. None of them; all three are promises

**Answer: B**

The relay list and the resolver list are published plain text and both name their operators. The other two are the unverifiable kind this course keeps meeting.

---

**Question 3.** The relay list holds 313 entries. How many operators run them?

A. Around fifty, matching the country spread
B. Around thirty
C. Seven, two of whom run about 96 per cent
D. Several hundred, since each entry is an independent volunteer

**Answer: C**

The country spread is real and it is not the same thing as operator diversity. Many entries are also one host listed twice for two address families.

---

**Question 4.** Why can the lesson's example configuration defeat its own purpose?

A. It uses relays in a single legal jurisdiction
B. It disables the no-logging requirement
C. It routes through relays that do not support anonymised mode
D. It names relays from one operator and leaves the resolver as a wildcard, and that operator runs dozens of compatible resolvers

**Answer: D**

The selection can land on the same company's resolver. Then one party holds your address, your queries and the link, and the extra hop has cost you latency for nothing.

---

**Question 5.** What is the correct test that your two hops are two parties?

A. Confirm the relay and resolver are in different countries
B. Confirm the relay and resolver are run by different operators, by reading both published lists
C. Confirm the resolver advertises no logging
D. Confirm the relay supports the anonymised protocol

**Answer: B**

Two hosts in two countries run by one company passes the country test and fails the condition the design depends on. Option C is worth doing and is a separate matter.

---

**Question 6.** What does `require_nolog = true` actually do?

A. Audits each resolver's logging behaviour before use
B. Causes the proxy to discard query logs on your own machine
C. Filters the server list to entries that declare they do not log, which is a self-description
D. Requires the relay operator to sign a no-logging commitment

**Answer: C**

Worth setting, because it narrows the pool to operators who made the statement. It is not verification, for the reason Lesson 6.3 gave.

---

**Question 7.** What is the real standards status of the oblivious alternative?

A. An IETF Proposed Standard, which is the advantage the lesson claims
B. An expired Internet-Draft with no published RFC
C. A vendor specification with no RFC number
D. An Experimental RFC published as an Independent Submission, stating it is not endorsed by the IETF

**Answer: D**

The lesson lists "IETF standard" as its main advantage over the relay scheme. That advantage does not exist, and both schemes sit outside the standards track.

---

**Question 8.** How many relays does the oblivious protocol's published list contain?

A. Two entries in total
B. Around sixty
C. Roughly the same as the DNSCrypt relay list
D. Several hundred, spread across major cloud providers

**Answer: A**

This is the genuine reason to wait, and it is a much stronger reason than the one the lesson gives. The crowd you would be hiding in is two.

---

**Question 9.** The lesson says proxy support for the oblivious protocol is "planned". What is the actual situation?

A. Support was added and then removed over security concerns
B. It has shipped since version 2.0.46, with its own server and relay lists
C. Support exists only in a separate command-line tool
D. It remains unimplemented in any client

**Answer: B**

So availability is not the obstacle. The obstacle is the size of the relay pool, which is a different problem with a different answer.

---

**Question 10.** Looking at the whole module, how many of the observers listed in Lesson 7.1 does this lesson remove?

A. All of them, which is why it is the module's strongest technique
B. None; it relocates the resolver's knowledge without reducing it
C. One: the resolver seeing your queries tied to your address
D. Two: the resolver, and the destination address your provider sees

**Answer: C**

That one row is exactly what 7.1 and 7.2 both left open, so the lesson earns its place. The destination address your provider sees is untouched, as is correlation by an observer on both links.

---

## References

1. RFC 9230, Oblivious DNS over HTTPS, Experimental, Independent Submission stream. https://www.rfc-editor.org/rfc/rfc9230.html
2. DNSCrypt project, dnscrypt-proxy wiki, Oblivious DoH, on support from version 2.0.46 and the separate server and relay lists. https://github.com/DNSCrypt/dnscrypt-proxy/wiki/Oblivious-DoH
3. Mozilla, DNS over HTTPS FAQs, on the documented preference and its permitted values. https://support.mozilla.org/en-US/kb/dns-over-https-doh-faqs
4. DNSCrypt project, published Oblivious DoH relay list. https://download.dnscrypt.info/dnscrypt-resolvers/v3/odoh-relays.md
5. DNSCrypt project, dnscrypt-proxy releases, version 2.1.17 of 13 July 2026, introducing post-quantum query confidentiality on by default. https://github.com/DNSCrypt/dnscrypt-proxy/releases
6. DNSCrypt project, published relay list, counted October 2026. https://download.dnscrypt.info/dnscrypt-resolvers/v3/relays.md
7. DNSCrypt project, published public resolver list, counted October 2026. https://download.dnscrypt.info/dnscrypt-resolvers/v3/public-resolvers.md

---

## Where This Goes Next

**Lesson 7.4** builds a resolver you run yourself, which removes the resolver from the trust list entirely and puts the recursive chain in your hands. Read it against Lesson 6.6's trust ledger first: running it yourself moves the observer, and the machine it runs on still belongs to somebody.

**Lesson 7.5** tests all of it. Lesson 7.1's Figure 3 is the thing to bring, because the question there was never what you configured but which resolver actually answered.
