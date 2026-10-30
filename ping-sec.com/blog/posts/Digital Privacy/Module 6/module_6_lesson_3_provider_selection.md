---
title: "Module 6 Lesson 3 - Choosing a VPN Provider: Reading the Evidence Instead of the Chart"
course: Digital Privacy for Ethical Hackers
module: 6
lesson: 6.3
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - vpn
  - audits
  - evaluation
  - opsec
---

# Choosing a VPN Provider: Reading the Evidence Instead of the Chart

## About This Lesson

Lesson 6.1 established the shape of this problem: a VPN transfers visibility rather than removing it, so the provider is the thing you are actually choosing, and the signals about a provider are worth very different amounts.

**This lesson is the practical follow-through.** How to read an audit report, which is the thing everybody recommends and nobody explains. What providers actually did after being caught, which is more informative than the incident. And how to build a scorecard whose weights match how much each signal proves, rather than one that awards more points for a kill switch than for a police raid that came up empty.

It also corrects the same jurisdiction framing that appeared in the previous lesson, because the source material for this one expands it into a full ranked list of countries, and the list contradicts itself twice.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Distinguish three different things all called an audit, and say what each can establish.
- Ask the four questions that make an audit report informative.
- Explain why a no-logging engagement cannot prove what people think it proves.
- Describe what three providers did after being tested, and why the incident is the least useful part.
- Read a logging policy for the distinction that actually mattered in those cases.
- Build a scorecard weighted by evidential value, and say why each weight is what it is.
- Date the statistics you quote about free services.

### Prerequisites

- Lesson 6.1, particularly Figure 3 on signal strength, which this lesson goes deeper on rather than repeating.
- Lesson 5.2's section on writing recommendations that age well.

---

## 1. Three Different Things Are Called "Audited"

Every guide tells you to check for an audit. Almost none of them says which kind, and there are three.

![[module_6_lesson_3_reading_an_audit.svg]]

*Figure 1. Which audit, of what, by whom, and when, are the whole of the information.*

**An application security review** examines the client software: memory handling, key storage, whether the kill switch actually holds, leak behaviour. It establishes that the thing on your machine was reviewed by somebody competent at a point in time. **It says nothing whatsoever about logging.**

**An infrastructure review** examines server build and hardening, how machines are provisioned and wiped, access control, and whether persistent storage is used at all. It establishes that the estate was configured as described, on the machines the auditor was shown.

**A no-logging assurance engagement** examines whether the system as configured is capable of retaining the data the provider says it does not retain, usually under a formal assurance standard. It establishes that on the day the auditor looked, the configuration matched the claim.

**The most common error in comparison articles is presenting the first as evidence for the third.** They are not the same exercise and they do not answer the same question.

### The Four Questions

**Which of the three is it?** Start here, because it determines what the report can possibly tell you.

**What date?** Infrastructure changes constantly. A report from four years ago describes a company that no longer exists in that configuration.

**Who commissioned it, and what was excluded?** Almost all of these are paid for by the subject, which is normal and not disqualifying. **The exclusions are the most useful paragraph in the document** and careful auditors state them plainly.

**Is the full report published, or only a summary?** A press release describing an audit is not an audit. If you cannot read the scope section, you do not know what was tested.

### The Limit No Amount of Auditing Removes

A no-logging engagement observes a configuration. It cannot observe intent, it cannot observe what happens after the auditor leaves, and it cannot detect a change made the following week. That is not a criticism of the auditors, who usually say so themselves in the report you were not shown.

**Which is why repetition matters more than any single report.** One provider has now completed six such engagements since 2018, the first two with one large accountancy firm and the subsequent four with another, on an annual cadence since 2022. (Reference 1) That pattern says something a first audit cannot. **And an unannounced engagement says more again**, which Section 3 comes back to.

**One correction to the source material here.** It lists that provider as audited by the first firm, which was true in 2018 and 2020 and has not been the arrangement since 2022. Naming a stale auditor is a small thing that signals the whole comparison was assembled rather than checked.

---

## 2. Reading a Logging Policy

The source material scores privacy policies for clarity. The more useful skill is knowing which distinction to look for, because the same distinction decided both of the cases in Section 3.

**Connection records and traffic records are different things.** Connection records are the metadata: which address connected, when, for how long, how much moved. Traffic records are what you did: destinations, name lookups, contents.

**A provider can truthfully say it keeps no traffic records while keeping connection records**, and connection records are sufficient to identify somebody. That gap is exactly where the well-known failures happened.

**So read for the categories, not the adjective.** A policy that says "we keep no logs" without naming what a log is has told you nothing. A policy that lists what it retains, for how long, and why, has told you a great deal even when the list is longer than you would like.

**And check what the account itself requires**, because that is architecture rather than promise. A provider that issues an account number, takes cash, and never learns your name has less to hand over regardless of its policy. Lesson 6.1 made this point and it is worth repeating as the single most reliable of the medium signals.

---

## 3. What Providers Did After Being Tested

The source material keeps a permanent avoid list. **The incident is the least informative part of each of these stories.**

![[module_6_lesson_3_after_being_caught.svg]]

*Figure 2. Three providers tested by reality, and what happened next.*

### Caught, Then Changed

In 2017 a provider whose policy said it kept no logs supplied connection records that helped identify a suspect in a stalking case. It later said its policy had meant browsing activity rather than connection metadata, which is the distinction from Section 2 arriving as an excuse rather than as a disclosure.

**What it did next is the interesting part.** It commissioned an independent review in 2019, and a second in 2021 with the firm permitted to check without prior notice. (Reference 2)

**Unannounced checking is a stronger regime than most providers submit to**, including several on the standard recommended list. A reader who stops at the 2017 headline never learns that.

### Caught, Then Sold

In 2016 another provider advertising no logging supplied connection records to investigators. **And then it changed hands, twice.** The company that kept those records is not the company operating the service today. (Reference 3)

**So what is the incident evidence of?** Something about the brand's history, and very little about the operation you would be buying from now. The same guidance treats ownership as decisive when a company it dislikes acquires a brand, and forgets ownership entirely when it moves the other way.

### Tested, and Empty

In 2023 police executed a search warrant at a provider's offices intending to seize computers holding customer data. The data did not exist and they left without taking anything. Lesson 6.1 covered this and it remains the strongest evidence available about any provider.

**Notice where it happened.** In a country the standard jurisdiction list tells you to avoid, which is Section 4's argument in one sentence.

### Why "Caught Once, Avoid Forever" Is a Lazy Rule

- **It discards the informative half.** What a provider does after an incident tells you about its current management. The incident tells you about a policy in 2016 or 2017.
- **It rewards never being tested.** A provider nobody has come looking for sits above one that was tested and responded, purely because there is nothing on its record.
- **It ignores ownership changes** in one direction while treating them as decisive in the other.

**None of which means the incidents do not matter.** They do. They are one input, dated, alongside what came after.

**One claim in the source material to set aside.** It states that multiple providers claiming no logs supplied data to authorities during the Hong Kong protests and that users were arrested. I could not find sourcing for this. It may be true and it is presented without a reference in material that is otherwise careful to cite its examples, so treat it as unverified until somebody produces the source.

---

## 4. The Jurisdiction List, Again

Lesson 6.1 covered why intelligence alliance membership is a weak signal. The source material for this lesson expands it into a ranked list of countries, which makes two further problems visible.

**The list contradicts itself.** Sweden appears under acceptable and again under problematic. The Netherlands appears under acceptable and again under problematic. A ranking that places the same country in two tiers has not been checked.

**And the top recommendation has changed underneath it.** Switzerland heads the list of best jurisdictions. Lesson 6.1 covered the proposed revision to that country's surveillance ordinance, which would require communication services above a low user threshold to record identity documents and retain connection data for six months, and one large provider based there has already begun relocating infrastructure. It is not law yet. It is also not a stable foundation for a permanent ranking.

**What to measure instead:**

- **Does this country currently compel data retention for this kind of service?** A specific, checkable question with a dated answer.
- **Can it compel assistance under secrecy?** Which determines whether a provider could tell you if something changed.
- **What happened when a provider there was actually tested?** Which brings you back to Section 3.

**The country lists persist because they are easy to write and easy to publish.** A ranking of legal regimes takes research and needs revisiting; an alliance membership list never changes and never needs updating, which is precisely why it tells you so little.

---

## 5. Free Services, With the Numbers Dated

The source material says 38 percent of free Android VPN applications contain malware and 18 percent do not encrypt traffic at all, and presents both as current.

**Those figures come from a study carried out in August 2016** of 283 applications, published the following year. (Reference 4) They were striking then and they are a decade old now, which matters because this is a fast-moving market and because quoting an undated decade-old statistic is the exact failure Lesson 5.2 was about.

**There is much better current material.** A 2026 study examined 281 free Android VPN applications with an automated testing framework and found name resolution leaks, unencrypted transmissions, weak configurations, and over 80 percent contacting trackers. The applications affected by at least one of those issues had been installed more than 2.4 billion times between them. (Reference 5)

**And a stronger single example than the botnet story usually told.** In August 2025 researchers at a university internet observatory and a second university found several popular Android VPN applications, with more than 700 million combined downloads, that were covertly linked to one another, shared hard-coded passwords, and collected location data. (Reference 6)

**The conclusion is unchanged and the evidence for it should be current.** If a service is free and has no other revenue, the traffic is the product. The exception remains a free tier funded by paid subscriptions from a provider that is audited on the same basis as its paid service, and Lesson 6.1 covered why blanket condemnation pushes people who cannot pay towards nothing at all.

---

## 6. The Scorecard, Reweighted

The source material offers a hundred-point rubric. The arithmetic is fine and the weights encode a set of beliefs that do not survive examination.

![[module_6_lesson_3_reweighted.svg]]

*Figure 3. Same hundred points, different question being answered.*

**In the original**, technical features are worth 20, jurisdiction 10 with three of those for alliance membership, and **evidence from an actual legal test is worth 3.** A kill switch and leak protection together outscore a documented occasion when somebody demanded data and none existed.

### The Reweighted Version

| Category | Points | What it measures |
|---|---|---|
| Evidence from real testing | 25 | Seizures, warrants, court records. Never tested scores zero, not full marks |
| Audit quality | 20 | Type, date, published scope, how many, whether unannounced |
| Architecture that limits what exists | 20 | Anonymous payment, no identity required to hold an account |
| Traceable ownership | 15 | Who ultimately owns it, and whether that changed recently |
| Technical baseline | 10 | A floor to clear rather than a differentiator |
| Jurisdiction | 5 | Current retention law only. Alliance membership scores nothing |
| Marketing honesty | 5 | A cheap signal and a surprisingly reliable one |

**Three changes, and the reasoning:**

**Evidence went from 3 to 25**, because a documented demand that produced nothing is the only signal that settles anything.

**Technical features went from 20 to 10.** In 2015 modern protocols and leak protection separated providers. Today every provider worth considering has them, so the row no longer discriminates between candidates. A category everybody scores full marks in is not doing any work.

**Jurisdiction went from 10 to 5, and alliance membership to zero**, for the reasons in Section 4.

**And "never tested" now scores zero rather than being silently ignored**, because an untested claim is an absence of information rather than a clean record. That is the change most likely to annoy people, and it is the one that most improves the rubric.

**Use whichever weighting you like.** The point is that a rubric encodes beliefs about what evidence is worth, and most published ones never state theirs. **If you publish a scorecard, publish the reasoning for the weights.** That is the part a reader cannot reconstruct and the part that makes it useful rather than decorative.

---

## 7. What to Take From This

**"Audited" is a prompt to go and read something**, not a fact about a provider. Three different exercises share the name, and the most common error is treating a client review as evidence about logging.

**No-logging engagements observe configuration at a point in time.** Frequency and unannounced checking are what turn that into a pattern worth trusting.

**Read policies for the categories**, because connection records and traffic records are different things and the gap between them is where the famous failures happened.

**What a provider did after being caught is more informative than the incident**, and a permanent avoid list rewards providers nobody has ever tested.

**Country rankings are easy to write and tell you little.** Current retention law is the checkable question, and it moves.

**And date your statistics.** The most-quoted number about free services was measured in 2016.

**Next lesson:** Tor, where the anonymity properties none of this provides are the entire subject.

---

## Knowledge Check

**Question 1.** A provider publishes an audit of its client applications. What does that establish about its logging?

- A) Nothing. An application security review examines the software on your machine, not the servers or their retention
- B) That the provider keeps no connection logs
- C) That the provider keeps no traffic logs
- D) That the infrastructure was configured correctly

**Answer: A.** Presenting an application review as evidence about logging is the most common error in comparison articles.

**Question 2.** What is the most useful paragraph in an audit report?

- A) The executive summary
- B) The list of what was excluded from scope
- C) The auditor's credentials
- D) The conclusion

**Answer: B.** Careful auditors state exclusions plainly, and they determine what the report can possibly tell you.

**Question 3.** What can a no-logging assurance engagement establish?

- A) That the provider has never retained user data
- B) That the provider will not retain data in future
- C) That on the day the auditor looked, the configuration matched the claim
- D) That the provider would refuse a legal demand

**Answer: C.** It observes configuration. It cannot observe intent, or what happens after the auditor leaves, which is why frequency matters more than any single report.

**Question 4.** Which distinction decided both of the well-known logging failures?

- A) Whether the provider used modern protocols
- B) Whether the provider was based in an intelligence alliance country
- C) Whether the client software was open source
- D) The difference between connection records and traffic records, which the marketing collapsed into "no logs"

**Answer: D.** A provider can truthfully claim to keep no traffic records while keeping connection records, and connection records are sufficient to identify somebody.

**Question 5.** A provider supplied connection records in 2017 and then commissioned an unannounced audit in 2021. How should this be scored?

- A) Permanent exclusion, since the claim was once false
- B) Ignore the incident entirely, since it is now old
- C) Both matter: the incident dated, alongside what the provider did afterwards, which is a stronger regime than most providers accept
- D) Score only the audit, since it is more recent

**Answer: C.** A permanent avoid list discards the informative half and rewards providers nobody has ever tested.

**Question 6.** Why is a provider that has never faced a legal demand not automatically trustworthy?

- A) It is, an absence of incidents is the best evidence available
- B) Because untested providers are usually newer
- C) Because legal demands are always public
- D) Because an untested claim is an absence of information rather than a clean record

**Answer: D.** Which is why the reweighted scorecard gives it zero rather than silently ignoring it.

**Question 7.** What is wrong with the country ranking in the source material?

- A) It ranks too few countries
- B) It places the same countries in two different tiers, and its top recommendation is currently proposing mandatory retention for these services
- C) It omits the largest intelligence alliance
- D) Nothing, jurisdiction is the strongest available signal

**Answer: B.** Sweden and the Netherlands each appear under both acceptable and problematic, and the list has clearly not been checked.

**Question 8.** What should be measured instead of alliance membership?

- A) The number of server locations offered
- B) The country's general reputation for privacy
- C) Whether the country currently compels retention for this kind of service, and whether it can compel assistance in secret
- D) Whether the provider is headquartered offshore

**Answer: C.** Specific, checkable, and dated, which is exactly why it is harder to publish than an alliance list.

**Question 9.** The figure that 38 percent of free Android VPN applications contain malware comes from when?

- A) A study carried out in August 2016 of 283 applications
- B) An ongoing survey updated annually
- C) A 2024 industry report
- D) Provider transparency reports

**Answer: A.** Better current material exists, including a 2026 study of 281 applications whose affected members had over 2.4 billion installations between them.

**Question 10.** In the reweighted scorecard, why do technical features drop from 20 points to 10?

- A) Because they are no longer relevant to security
- B) Because they are difficult to verify
- C) Because open source clients matter more
- D) Because every provider worth considering now has modern protocols and leak protection, so the category no longer discriminates between candidates

**Answer: D.** A category in which everybody scores full marks is not doing any work in a comparison.

---

## Lab: Read the Report, Trace the Owner, Build the Rubric

**Time: 2 to 3 hours. No subscription required.**

---

### Part 1: Read an Actual Audit Report (40 minutes)

Find one provider's most recent published audit and read it, not an article about it.

| | |
|---|---|
| Provider and report title | |
| Which of the three types is it? | |
| Date of fieldwork, not date of publication | |
| Who commissioned it | |
| Standard or framework used, if any | |
| What was in scope | |
| **What was explicitly excluded** | |
| Full report or summary only? | |
| In one sentence: what does this let you conclude? | |

**If you cannot find a full report for your first choice, record that and try another.** How many attempts it took is itself a finding.

**Deliverable.** The table, and your one sentence.

---

### Part 2: The Category Test (25 minutes)

Take two providers' logging policies and fill this in from the policy text alone.

| | Provider A | Provider B |
|---|---|---|
| Does it define what it means by a log? | | |
| Connection metadata: retained? For how long? | | |
| Destinations or name lookups: retained? | | |
| What is retained for billing or abuse handling | | |
| Account data required to sign up | | |
| Payment methods that do not identify you | | |

Then answer: **which policy would survive the situation in Section 3, and which one is a phrase?**

**Deliverable.** The table and your answer.

---

### Part 3: Trace an Incident Forward (30 minutes)

Pick one provider that appears on a published avoid list.

| | |
|---|---|
| What the incident was, and the year | |
| Who owned the company then | |
| Who owns it now | |
| What the provider did afterwards | |
| Any audits since, and of what type | |
| Your assessment today, with reasons | |

**The last row is the exercise**, and either conclusion is acceptable if the reasoning is in it.

**Deliverable.** The table.

---

### Part 4: Check a Jurisdiction Properly (25 minutes)

Pick the country your preferred provider operates from and answer these from primary or legal sources rather than from a VPN article.

| | |
|---|---|
| Is there a data retention obligation covering this kind of service? | |
| If so, what data and for how long? | |
| Can assistance be compelled under secrecy? | |
| Any proposed changes currently in consultation or before a legislature? | |
| Date you checked | |

**Deliverable.** The table with sources, and one sentence on how this compares to what the country lists say.

---

### Part 5: Score Three Providers (30 minutes)

Use the reweighted rubric from Section 6, or your own weighting if you state your reasoning.

| Category | Weight | Provider A | Provider B | Provider C |
|---|---|---|---|---|
| Evidence from real testing | 25 | | | |
| Audit quality | 20 | | | |
| Architecture that limits what exists | 20 | | | |
| Traceable ownership | 15 | | | |
| Technical baseline | 10 | | | |
| Jurisdiction | 5 | | | |
| Marketing honesty | 5 | | | |
| **Total** | **100** | | | |

Then score the same three with the original weighting from the source material and compare.

**Deliverable.** Both scorings, and one paragraph on where the two disagreed most and why.

---

### Part 6: Publish Your Reasoning (20 minutes)

Write the weighting statement that would accompany your scorecard if you published it.

It must say what each weight is for, which signals you deliberately excluded and why, and what would cause you to change the weights.

**Deliverable.** Half a page at most.

---

### Submission

1. **Audit report**, Part 1, including how many attempts it took to find a full one.
2. **Category test**, Part 2, with your judgement.
3. **Incident traced**, Part 3, with your assessment.
4. **Jurisdiction**, Part 4, with sources and date.
5. **Scoring**, Part 5, both weightings.
6. **Weighting statement**, Part 6.
7. **Reflection**, half a page: which provider did you expect to come out ahead before Part 5, and did it?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Audit report | 25 | An actual report read, exclusions found, honest conclusion drawn |
| Category test | 20 | Filled from policy text, and the two policies genuinely distinguished |
| Incident traced | 15 | Ownership at both dates established, and a reasoned current assessment |
| Jurisdiction | 15 | Answered from legal sources rather than VPN articles, and dated |
| Scoring | 15 | Both weightings applied, disagreement identified and explained |
| Weighting statement | 5 | States exclusions and what would change it |
| Reflection | 5 | Honest about the prior expectation |
| **Total** | **100** | |

**Note the weighting.** Reading one real audit report is worth a quarter of the marks, because it is the single skill this lesson exists to teach and almost nobody who recommends checking for audits has ever done it.

---

## Additional Resources

### Primary Sources

- A provider's own account of its no-logging assurance engagements, which names the firm and the standard: https://nordvpn.com/blog/nordvpn-no-logs-assurance-engagement-2025/
- The 2016 study whose figures are still quoted: https://www.icsi.berkeley.edu/icsi/news/2017/02/android-vpn
- A 2026 study of free Android VPN applications: https://thehackernews.com/2026/07/study-of-281-free-android-vpn-apps.html

### On Reading Reports

Find the full audit report for whichever provider you use and read the scope section. It takes twenty minutes and it is the only exercise in this lesson that cannot be done by reading about somebody else's conclusions.

### Discussion Questions

1. Part 1 asked you to find a full audit report. How many providers publish one, and what does the ratio tell you about the industry?
2. Section 3 argues that a permanent avoid list rewards providers nobody has tested. Is that a fair criticism of how the security community writes about vendors generally?
3. Section 6 asks you to publish the reasoning behind a scorecard's weights. What stops most published comparisons from doing this?

---

## References

1. [NordVPN, sixth no-logs assurance engagement, naming the standard and the firm](https://nordvpn.com/blog/nordvpn-no-logs-assurance-engagement-2025/)
2. [ProPrivacy, on a provider commissioning independent no-logs review after a 2017 incident](https://proprivacy.com/privacy-news/purevpn-puts-its-no-logs-claims-to-the-test-with-independent-audit)
3. [VPN Testing, on no-logs policies tested in court and the ownership history of the providers involved](https://vpntesting.com/no-logs/)
4. [ICSI Berkeley, study of Android VPN permission-enabled applications, carried out August 2016](https://www.icsi.berkeley.edu/icsi/news/2017/02/android-vpn)
5. [The Hacker News, on a 2026 study of 281 free Android VPN applications](https://thehackernews.com/2026/07/study-of-281-free-android-vpn-apps.html)
6. [Engadget, on research finding covertly linked free VPN applications with shared hard-coded credentials](https://www.engadget.com/cybersecurity/vpn/malware-apps-posing-as-free-vpns-are-on-the-rise-175629088.html)
