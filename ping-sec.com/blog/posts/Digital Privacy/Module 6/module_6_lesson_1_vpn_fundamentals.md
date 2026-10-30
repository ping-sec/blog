---
title: "Module 6 Lesson 1 - VPNs: What Moved, What Did Not, and What Expired"
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.1
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - vpn
  - network-privacy
  - threat-modeling
  - opsec
---

# VPNs: What Moved, What Did Not, and What Expired

## About This Lesson

There is a good instinct in most VPN teaching, and it is that the marketing is misleading. Military grade encryption, complete anonymity, protection from hackers: none of it survives contact with how the technology works.

**This lesson keeps that instinct and applies it one step further**, to two claims that survive in guidance written by people who are otherwise sceptical.

The first is that a VPN is essential on public wireless networks. That was excellent advice in 2013 and four things have happened since, the most recent of which was standardised six months ago. The second is that you should judge providers by which intelligence sharing alliance their country belongs to, which is a criterion the VPN industry invented and which does not measure what you need to know.

So: what a VPN moves, what it leaves untouched, what the passage of time did to its best-known use case, and how to judge a provider on evidence rather than on a comparison chart.

### Learning Objectives

After you read this lesson, you can do these tasks:

- State precisely who sees what, with and without a VPN, and name the observer that was added.
- Explain why four categories of thing a VPN is expected to fix are unaffected by it.
- Describe the four developments that closed most of the public network case, including the one from March 2026.
- Name the three reasons to use a VPN that are still sound.
- Rank the signals about a provider by what each is actually worth.
- Say why jurisdiction is a moving target, with a current example.
- Establish who owns a provider, and who owns the site recommending it.

### Prerequisites

- Lesson 1.4 for threat modeling, which decides whether any of this applies to you.
- Module 2, particularly fingerprinting, which Section 2 refers to.
- Lesson 5.2, whose three channels explain why a tunnel does not stop application tracking.

---

## 1. What Moves

![[module_6_lesson_1_what_moved.svg]]

*Figure 1. One observer was swapped for another. Nobody was removed.*

**Without a VPN**, your network operator sees which sites you reach and when. Not the contents, which are encrypted, but the destinations, the timing and the volume. In some countries it may sell that. The destination sees your network address and, the moment you sign in, exactly who you are.

**With a VPN**, an encrypted tunnel is added. Your network operator now sees one thing: that you connected to a VPN. The destination sees the provider's address instead of yours.

**And the provider sees everything the operator used to**, plus your real address and whatever your payment method reveals about you.

**That is the whole idea, stated honestly.** Your network operator lost the destinations. The provider gained them. Nobody was removed from the picture, so the question is not whether you are observed. It is which of the two you would rather be observed by.

**That is a real question with a real answer.** A privacy-focused provider that takes cash, holds no account beyond a number, and has demonstrated it keeps nothing may well be a better custodian than a large telecommunications company in a country that permits selling browsing data. But it is a comparison between two parties, not an escape from both.

---

## 2. What Does Not Move

Four things a VPN is commonly expected to fix, and does not touch. Most disappointment with VPNs comes from here.

**Signing in.** The moment you log into an account, the destination knows who you are, and the address it saw first stops mattering entirely. A VPN does not make you anonymous to a service you have an account with, and most browsing is to services you have accounts with.

**Cookies and fingerprinting.** Module 2 covered these. They identify the browser rather than the address, and a tunnel does not touch them. Changing your apparent country while carrying the same fingerprint is arguably worse than not changing it, because it makes you more distinctive rather than less.

**Application components.** Lesson 5.2, channel two. The advertising and analytics components inside applications report from inside the tunnel, carrying identifiers a new address does not change.

**The advertising bid request.** Lesson 5.2, channel three. It carries a device identifier regardless of which address it arrives from.

**The pattern here is worth naming.** A VPN is a network layer tool. Almost everything people want it to fix happens at the application layer, which is a different layer with different controls. That is not a flaw in VPNs. It is a mismatch between what they do and what they are sold as.

---

## 3. Why This Matters More For You

Two things specific to the work.

**You will be asked.** Clients, readers and colleagues ask whether they should get a VPN, and the standard answer is either a subscription recommendation or a flat dismissal. The useful answer is Figure 1: it moves the observer, here is who the new one is, and here is how to judge them.

**And the professional use case is real but narrow.** During authorised reconnaissance you may not want to expose your home address to a target, both because it is poor practice and because it links your household to an engagement. That is a genuine reason, it is in Section 5, and it is not the reason the marketing gives.

---

## 4. The Coffee Shop Case Has Largely Expired

This is the correction with the most practical consequence, and the source material contradicts itself about it: one section says a VPN is essential on public wireless, and a later section says that with HTTPS the benefit is marginal. The second is closer to right, and the reasons have grown since.

![[module_6_lesson_1_wifi_case_expired.svg]]

*Figure 2. Four changes, and the fourth is six months old.*

**One: encryption became the default.** More than 95 percent of page loads on desktop are encrypted, more than 97 percent on Android, and over 99 percent of browsing time. (Reference 1) The contents were the original worry, and they are no longer readable by somebody on the same network.

**Two: downgrade attacks stopped working.** Browsers ship a preloaded list of sites they will only reach over an encrypted connection, so stripping the encryption out fails before it starts. Guidance that still cites this attack as a live risk is describing 2012.

**Three: the lookup stopped leaking.** Encrypted domain name resolution is on by default in the major browsers, so the local network no longer sees which names you asked it to resolve. Free, built in, and requiring no provider to trust with everything.

**Four: the site name in the handshake.** The last thing in clear text was the server name sent when a connection opened, which is how a network could tell which site you were reaching even though everything else was encrypted. **Encrypted Client Hello conceals it, and it became a published standard in March 2026.** (Reference 2) It is default-on in the major browsers where encrypted DNS is enabled, and supported by the largest content networks.

### What Genuinely Remains

Stated without exaggeration, because there is something left:

- **The addresses you connect to** are still visible to the local network, though shared hosting means one address often serves many sites, which blunts it considerably.
- **Timing and volume.** When you are online and how much you move. A VPN hides destinations, not this.
- **Coverage gaps.** The fourth item needs encrypted DNS and a supporting host, so it is not yet universal, and badly maintained sites still fall short on the first.
- **A genuinely hostile network** can refuse to route you, or serve a captive portal that asks for things. Neither is fixed by a tunnel it will not let you open.

### The Three Reasons That Are Still Sound

**You would rather the provider had your browsing than your network operator.** A real preference, especially where operators may sell it. Figure 1 is the entire argument, and it does not need any exaggeration to stand up.

**Reaching something your network blocks.** Censorship, geography, or a restrictive corporate or campus network. This works when the tunnel itself is not blocked.

**Not showing a target your home address.** The professional case, during authorised work.

**Notice what all three have in common.** They are about who sees your traffic and where it appears to come from. None of them is about interception, which is what the coffee shop advice was for.

So the honest recommendation is narrower than "always on public wireless", and considerably easier to defend to somebody who asks why. The bad version survives because it is simple, and because the people repeating it are frequently selling the subscription.

---

## 5. Judging a Provider

![[module_6_lesson_1_signal_strength.svg]]

*Figure 3. Three tiers, and most guidance lives in the top one.*

### Weak Signals

**Intelligence alliance membership.** The Five, Nine and Fourteen Eyes arrangements describe intelligence sharing between agencies. **They are not the rules that decide whether a provider must keep records**, which is the thing you actually care about. What matters is whether the jurisdiction imposes mandatory data retention on this kind of service, whether it can compel assistance under secrecy, and what happened when a provider there was tested.

**The framing is worth noticing for what it is.** It is a criterion the VPN industry popularised, it sorts providers in a way that flatters the ones based in small jurisdictions, and it is reproduced by guidance that is otherwise sceptical of VPN marketing. The clearest illustration: Sweden appears on the standard avoid list, and Sweden produced the strongest piece of no-logging evidence in existence, which is in the strong signals section below.

**"Military grade encryption"** describes an ordinary cipher available to everyone and means nothing.

**Server counts** are a marketing number.

**A stated no-logging policy on its own** is a sentence somebody wrote. It is where analysis usually begins and ends.

### Medium Signals

Facts rather than promises, and they support each other.

**Ownership you can trace.** Section 6.

**Payment you can make anonymously.** A provider that takes cash and issues an account number instead of asking for an email address has structurally less to hand over, whatever its policy says. This is the strongest of the medium signals, because it is an architectural property rather than an intention.

**Clients you can read**, meaning open source applications that somebody outside the company can inspect.

**Audits you can date.** Read the scope and the date, not the badge. An audit is a snapshot of a narrow question, usually commissioned by the subject, and it cannot say anything about next week. A badge with no readable report behind it belongs in the weak tier.

### The Strong Signal

**A demand was made, and the provider had nothing to give.**

This is the only evidence that settles the question, and it is rare because it requires somebody to come looking. In April 2023, six officers of the Swedish national police executed a search warrant at a provider's offices in Gothenburg, intending to seize computers holding customer data, on a request for international cooperation from Germany. **The data did not exist. After the provider demonstrated how the service works and the officers consulted the prosecutor, they left without taking anything.** (Reference 3)

Court records and seizure reports are worth more than every item in the two tiers above put together. Search for them by provider name before you subscribe, and notice which providers have never been tested at all, because an untested claim is not a disproved one but it is not evidence either.

---

## 6. Two Things the Comparison Chart Will Not Tell You

### Jurisdiction Is a Moving Target

One country's reputation for privacy is the reason several well-known providers are based there. A revision proposed in 2025 to that country's surveillance ordinance would require communication services above a low user threshold to record identity documents and retain connection data for six months, and to remove encryption they themselves applied. (Reference 4)

**It is not yet law.** The consultation closed in May 2025 and it has not been formally adopted. **And one large provider has already begun moving infrastructure out of the country**, to Germany and Norway, which tells you what it expects. (Reference 5)

**This is the same lesson as Lesson 5.1's example** about a vendor withdrawing its strongest encryption option in one country. A jurisdiction's privacy reputation is a description of current law, not a property of the country, and it can change faster than your subscription renews.

### Three Brands, One Company, and the Site That Ranks Them

The source material tells you to research three providers and compare. Do that, and check who owns them first.

One group owns four well-known consumer brands. Another owns two more. **So a careful comparison of six providers can be a comparison of two companies.** (Reference 6)

**And the first group also owns review sites that rank VPN providers.** (Reference 6) Establish who owns the recommendation before you weigh it. This is not a claim that those services are bad; it is that a comparison assembled from sources with undisclosed common ownership is not the independent comparison it appears to be.

---

## 7. Corrections to the Standard Advice

**"HTTPS Everywhere"** appears in most alternatives lists. The extension entered maintenance in 2022 and was retired in January 2023, because browsers now ship HTTPS-only modes that do the same job natively. (Reference 7) Turn on your browser's HTTPS-only mode instead.

**"All free VPNs are dangerous."** Mostly true and stated too broadly. The business model question is the right one: if a service is free and has no other revenue, the traffic is the product. But at least one well-regarded provider funds a free tier from its paid subscriptions, with no advertising and no logging, and it is audited on the same basis as the paid service. Telling somebody who cannot pay that every free option is dangerous pushes them to no protection rather than to the reasonable one.

**"VPN plus Tor for enhanced privacy."** This is asserted without saying which order, and the two orders have completely different properties and failure modes. The Tor Project's own position is that adding a VPN generally does not improve anonymity and can reduce it, by making your traffic more distinctive and by adding a party who sees you are using Tor. Lesson 6.3 and 6.4 should handle this properly; it should not appear as a throwaway line here.

**On "no-logs" as a binary.** Connection records and traffic records are different, and the source material makes that distinction well. The addition worth making: **a provider that holds no account tied to your identity has less to correlate even if it does keep some connection data**, which is why the payment and account architecture matters more than the policy wording.

---

## 8. Do You Need One?

Shorter than the usual framework, because it is three questions.

**What specific problem are you solving?** If the answer is one of the three in Section 4, a VPN is the right tool. If the answer is "privacy" without a named observer, work out who the observer is first, because that determines whether this helps at all.

**Do you prefer the provider to your network operator?** If yes, subscribe. If you cannot say, you have not researched the provider, and Section 5 is the research.

**Would something else do it better?** Encrypted DNS and HTTPS-only mode are free, built in, and cover a good deal of what people buy a VPN for. Tor covers anonymity, which a VPN does not. A VPN is the right answer to a specific set of problems and the wrong answer to several adjacent ones.

**And one honest position that is missing from most guidance:** for a lot of people, on their own home network, the correct answer is no VPN. That is not a failure of diligence. Buying a subscription to solve a problem you do not have is what the marketing is for.

---

## 9. What to Take From This

**A VPN transfers visibility rather than removing it.** Everything else follows from that one sentence.

**Four things a VPN cannot touch**, all at the application layer, and they are where the disappointment comes from.

**The public network case has largely expired**, most recently through a standard published six months ago, and three narrower reasons remain that stand up without exaggeration.

**Judge providers on evidence.** Ownership, payment architecture, an audit you have read, and above all what happened when somebody actually came looking.

**And treat jurisdiction as current law rather than national character**, because it moves, and one well-known example is moving right now.

**Next lesson:** protocols and configuration, where the practical differences between implementations actually live.

---

## Knowledge Check

**Question 1.** What is the most accurate one-sentence description of what a VPN does?

- A) It makes your traffic anonymous
- B) It transfers visibility from your network operator to the VPN provider, rather than removing it
- C) It encrypts traffic that would otherwise be readable
- D) It protects you from attackers on the same network

**Answer: B.** Your operator loses the destinations, the provider gains them along with your real address and payment details. Nobody is removed from the picture.

**Question 2.** Which of these does a VPN not affect?

- A) Which address a website sees you arrive from
- B) Whether your network operator can see the destinations you reach
- C) Cookies, fingerprinting, application components and the advertising bid request
- D) Whether a geographically restricted service will serve you

**Answer: C.** These operate at the application layer. A VPN is a network layer tool, and the mismatch is where most disappointment comes from.

**Question 3.** What was standardised in March 2026 that further narrows the case for a VPN on an untrusted network?

- A) Encrypted Client Hello, which conceals the server name in the connection handshake
- B) A new cipher suite for wireless networks
- C) Mandatory encryption for all web traffic
- D) A replacement for encrypted domain name resolution

**Answer: A.** The server name was the last thing in clear text, and it is now hidden where the browser has encrypted DNS and the host supports it.

**Question 4.** Which of these is still a sound reason to use a VPN?

- A) To prevent interception of your traffic on a coffee shop network
- B) To stop websites tracking you
- C) To protect against malware
- D) To avoid showing a target your home address during authorised work

**Answer: D.** The three surviving reasons are all about who sees your traffic or where it appears to come from, not about interception.

**Question 5.** Why is intelligence alliance membership a weak signal about a provider?

- A) The alliances no longer exist
- B) All countries belong to one
- C) They describe agreements between intelligence agencies, not the rules that decide whether a provider must keep records
- D) They only apply to telephone networks

**Answer: C.** What matters is mandatory retention, powers to compel assistance in secret, and what happened when a provider there was actually tested.

**Question 6.** What is the strongest available evidence that a provider keeps no logs?

- A) A published no-logging policy
- B) A third-party audit badge
- C) Being based outside any intelligence alliance
- D) A documented occasion when a legal demand was made and the provider had nothing to hand over

**Answer: D.** In April 2023 police executed a search warrant at one provider's offices intending to seize customer data, found none existed, and left with nothing.

**Question 7.** What should you check about a third-party audit?

- A) Only that it exists
- B) The scope and the date, since it is a snapshot of a narrow question usually commissioned by the subject
- C) Whether it was performed in a privacy-friendly jurisdiction
- D) The number of servers covered

**Answer: B.** An audit cannot say anything about next week, and a badge with no readable report behind it is a marketing asset rather than evidence.

**Question 8.** Why does provider ownership matter when comparing options?

- A) It does not, since all providers use similar technology
- B) Foreign ownership is always a concern
- C) One group owns four well-known brands and another owns two, so comparing six providers can mean comparing two companies, and one group also owns review sites
- D) Ownership determines encryption strength

**Answer: C.** Establish who owns the recommendation before you weigh it.

**Question 9.** What is the current status of the proposed surveillance revision in the country several privacy providers are based in?

- A) It has been in force since 2023
- B) It was rejected and abandoned
- C) It applies only to telephone operators
- D) It is proposed and not yet adopted, and one large provider has already begun moving infrastructure out

**Answer: D.** Jurisdiction is a description of current law rather than a property of a country, and it can change faster than a subscription renews.

**Question 10.** What is wrong with recommending the HTTPS Everywhere extension?

- A) It was retired in January 2023 because browsers now ship HTTPS-only modes natively
- B) It was found to contain tracking code
- C) It conflicts with encrypted domain name resolution
- D) It only worked on desktop

**Answer: A.** Turn on your browser's built-in HTTPS-only mode instead.

---

## Lab: Observers, Evidence, and One Honest Recommendation

**Time: 2 hours.**

---

### Part 1: Name the Observers (25 minutes)

For three activities you actually do, fill this in. No VPN yet, just the current situation.

| Activity | Who sees the destination? | Who sees the contents? | Who knows it is you? |
|---|---|---|---|
| Reading a news site | | | |
| Signing into a work service | | | |
| An activity of your choosing | | | |

Then, for each row, answer: **would adding a VPN change any of these three columns, and which?**

**Deliverable.** The table, plus your three answers. The second row usually produces the useful realisation.

---

### Part 2: Test What Actually Leaks (30 minutes)

Establish empirically what your own setup exposes, rather than accepting Section 4 on trust.

1. **Is encrypted DNS on?** Find the setting in your browser and record its state. Turn it on if it is not.
2. **Is HTTPS-only mode on?** Same.
3. **Is Encrypted Client Hello working?** Several sites report this. Record the result, and record it again on a site that does not support it.
4. **What does a network observer still see?** Based on 1 to 3, write the list.

| Check | Before | After | How you verified it |
|---|---|---|---|

**Deliverable.** The table, and your list from step 4.

---

### Part 3: The Ownership Map (25 minutes)

Pick six VPN providers, including at least three you have seen advertised.

| Provider | Ultimate owner | Country of incorporation | Any shared ownership with others on this list? |
|---|---|---|---|

Then find one review or ranking site that covers them, and establish who owns that.

**Deliverable.** The table, the review site's owner, and one sentence on whether the map changed your view of any provider.

---

### Part 4: Find the Evidence (25 minutes)

For two providers, search specifically for occasions when a claim was tested: seizures, warrants, court filings, subpoena responses, transparency reports naming actual demands.

| Provider | Was it ever tested? | What happened | Source |
|---|---|---|---|

**"No evidence found" is a legitimate and common result**, and recording it honestly is the point. An untested claim is not a disproved one, and it is not evidence either.

**Deliverable.** The table for both providers.

---

### Part 5: The Audit, Read Properly (20 minutes)

Find one provider's most recent published audit and answer:

| | |
|---|---|
| Date of the audit | |
| Who commissioned it | |
| What was in scope | |
| What was explicitly out of scope | |
| Is the full report readable, or only a summary? | |
| What does it actually let you conclude? | |

**The out-of-scope row is the interesting one** and it is usually stated plainly in the report by auditors who are being careful.

**Deliverable.** The table.

---

### Part 6: One Honest Recommendation (15 minutes)

Write the answer you would give somebody who asks "should I get a VPN?"

Apply the rule from Lesson 5.2: date it, give reasons as checkable properties, and say what would make it stop being true.

It must include: a question back to them about what problem they are solving, at least one case where your answer is no, and what it costs.

**Deliverable.** Half a page at most.

---

### Submission

1. **Observers**, Part 1, with the three answers.
2. **Leak test**, Part 2, verified rather than assumed.
3. **Ownership map**, Part 3, including the review site.
4. **Evidence**, Part 4, with honest nulls.
5. **Audit**, Part 5, with the out-of-scope row filled in.
6. **Recommendation**, Part 6.
7. **Reflection**, half a page: before this lesson, which of the three surviving reasons in Section 4 did you think was the main one, and what were you actually buying?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Observers | 15 | Correctly identifies who sees what, and what a VPN would change |
| Leak test | 25 | Verified empirically, with the method recorded, and settings actually changed |
| Ownership map | 20 | Six providers traced to ultimate owners, review site included |
| Evidence | 20 | Searched properly, nulls reported honestly rather than filled in |
| Audit | 10 | Scope and out-of-scope both established from the report |
| Recommendation | 5 | Dated, property-based, includes a case where the answer is no |
| Reflection | 5 | Honest about what was previously assumed |
| **Total** | **100** | |

**Note the weighting.** The leak test and the ownership map carry the most, because they are the two things you can establish for yourself and that no comparison chart will tell you.

---

## Additional Resources

### Primary Sources

- IETF, Encrypted Client Hello deployment considerations: https://datatracker.ietf.org/doc/html/draft-campling-ech-deployment-considerations-10
- Mullvad's account of the April 2023 search warrant: https://mullvad.net/en/blog/mullvad-vpn-was-subject-to-a-search-warrant-customer-data-not-compromised
- Internet Society Switzerland Chapter, on the proposed surveillance ordinance revision: https://www.isoc.ch/swiss-surveillance-ordinance-encryption-threat-vupf-oscpt/

### On Ownership

- CyberInsider, on the consolidation of consumer VPN brands and review sites: https://cyberinsider.com/kape-technologies-owns-expressvpn-cyberghost-pia-zenmate-vpn-review-sites/

Read this one before any comparison exercise, not after. It changes how the comparison reads.

### Discussion Questions

1. Part 2 asked you to verify what actually leaks from your own setup. How much of what you found were you assuming rather than knowing?
2. Section 5 argues that the intelligence alliance framing is a criterion the industry invented. Had you used it? What would you replace it with when writing for others?
3. Section 8 says that for many people on their own network, the correct answer is no VPN. Is that a comfortable thing to publish, and does the discomfort tell you anything?

---

## References

1. [Internet Society Pulse, on HTTPS adoption and the retirement of HTTPS Everywhere](https://pulse.internetsociety.org/blog/https-is-almost-everywhere-so-goodbye-https-everywhere)
2. [Center for Democracy and Technology, Encrypted Client Hello and the server name metadata gap](https://cdt.org/insights/encrypted-client-hello-closing-the-sni-metadata-gap/)
3. [Mullvad VPN, on the April 2023 search warrant and its outcome](https://mullvad.net/en/blog/mullvad-vpn-was-subject-to-a-search-warrant-customer-data-not-compromised)
4. [Internet Society Switzerland Chapter, on the proposed revision to the surveillance ordinance](https://www.isoc.ch/swiss-surveillance-ordinance-encryption-threat-vupf-oscpt/)
5. [Heise, on Proton relocating infrastructure out of Switzerland](https://www.heise.de/en/news/Surveillance-Proton-relocates-parts-of-its-infrastructure-from-Switzerland-10538664.html)
6. [CyberInsider, on consumer VPN brand consolidation and commonly owned review sites](https://cyberinsider.com/kape-technologies-owns-expressvpn-cyberghost-pia-zenmate-vpn-review-sites/)
7. [The Record, EFF to deprecate HTTPS Everywhere as HTTPS becomes ubiquitous](https://therecord.media/eff-to-deprecate-https-everywhere-extension-as-https-is-becoming-ubiquitous)
