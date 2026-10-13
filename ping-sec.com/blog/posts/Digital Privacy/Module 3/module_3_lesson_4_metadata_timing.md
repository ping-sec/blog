---
title: "Metadata and Timing: The Shape of a Conversation"
course: Digital Privacy for Ethical Hackers
module: 3
lesson: 3.4
format: article
reading_time: 28 minutes
tags:
  - digital-privacy-course
  - privacy
  - encrypted-communications
  - metadata
  - traffic-analysis
  - timing-attacks
  - opsec
  - module-3-capstone
---

# Metadata and Timing: The Shape of a Conversation

## About This Lesson

This closes Module 3, and it closes a thread that opened in Lesson 1.1.

Myth 3 said encryption protects content and leaves metadata exposed. Every lesson since has run into that boundary from a different direction: the file path in a delivered report, the subject line on an encrypted email, the difference between two messengers that encrypt content identically. This lesson takes the boundary seriously and asks what an observer actually does with what is left.

It also does something the earlier version does not, which is to be honest about the defenses. Most metadata advice is technically correct and behaviourally impossible, and telling those apart is worth more than another list of techniques.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Say what each of three observers learns from an encrypted conversation, and which one you can change.
- Read the shape of a conversation the way an analyst does.
- State the honest limits of traffic analysis research, rather than the headline figures.
- Distinguish defenses you will sustain from defenses you will abandon.
- Reduce your exposure by removing patterns rather than by disguising them.

### Prerequisites

- All of Module 3, and Lesson 1.1's third myth.

---

## 1. Three Observers

Module 2 asked which observer a control addresses. The same question, asked about a message rather than a page load, produces a different set of answers.

![[module_3_lesson_4_three_observers.svg]]

*Figure 1. Only the middle column is a choice.*

**The network path** sees that you connected to a service, when, for how long, and how much data moved in each direction. It does not see who you spoke to or what you said. Only routing your traffic elsewhere touches this column.

**The provider** sees whatever it decided to record, and this varies enormously. Lesson 3.1 gave both ends of that range: one service answered a grand jury subpoena with two timestamps, while a law enforcement document described another supplying address book contacts and activity on a target at fifteen minute intervals. (Reference 2, Reference 3) Both encrypt content with the same protocol.

**The other endpoint** sees everything, because the message is decrypted there by design. Encryption protects a message from everybody except the person you chose to send it to, their device, and anyone who later reaches that device.

**The first and third are structural.** Every service has a network path and every conversation has a recipient. The middle one was decided by whoever built the service, and it is the only column where switching products makes a large difference.

Which is worth stating plainly, because most metadata advice aims at the middle column while most of what identifies people in practice comes out of the first.

---

## 2. Reading the Shape

Here is what somebody watching the network column can work out.

![[module_3_lesson_4_traffic_signals.svg]]

*Figure 2. Every bar is encrypted. The picture is legible anyway.*

**Regularity.** The same hour, the same length, every day. That is a standing arrangement, and it is precise enough to predict and to compare against another account.

**Volume.** The larger bars mean something was transferred rather than discussed. A document, an archive, or code, rather than a few lines of text.

**The break.** Thirteen days of routine and then one that is not. The anomaly is the signal, and the steady baseline is what makes the departure legible. Your routine is what makes your exception readable.

**Sequence.** A burst, then a different service, then another. Order carries meaning. Discovery, escalation, submission is a shape an analyst has seen before.

**Correlation.** If another account you hold went quiet during that window, the two are now linked. Silence is as observable as presence.

**None of that required breaking anything.** The content was protected throughout, exactly as designed. The story was legible because a conversation has a shape and the shape travels in the clear.

That is the Lesson 1.1 boundary, drawn out to its conclusion at the end of the module that opened with it. It is also what a former director of both the National Security Agency and the Central Intelligence Agency meant in saying, publicly, that they killed people based on metadata. (Reference 1) The mechanism he was describing is the one in Figure 2, operating at a different scale and with worse consequences.

---

## 3. What the Research Actually Says

Two techniques get quoted with confident numbers. Both numbers need qualifying, and the qualifications are the interesting part.

### Website Fingerprinting

The claim is that an observer can identify which site you visited from encrypted traffic patterns, with accuracy in the high eighties or nineties.

Those figures come from **closed-world** evaluations, where the attacker trains on every site the user might visit and only has to decide which of them it was. That is not the situation an attacker is in.

In **open-world** conditions, where the user may visit anything, performance degrades sharply. Juarez and colleagues showed that earlier work succumbed to the base rate fallacy in that setting: if the pages you care about are a tiny fraction of what people visit, even a very accurate classifier produces mostly false positives. They also showed that variables usually left out of the model, including browsing habits and software version, materially affect the result. (Reference 6)

Later work evaluating attacks under realistic conditions found performance holding up when monitoring a handful of sites and degrading substantially as the monitored set grows. (Reference 7)

**What to take from this.** The technique is real and it is a genuine concern for a targeted individual with a small, predictable set of destinations. It is not the general-purpose deanonymisation tool the headline numbers imply. And the pattern of quoting the closed-world figure without the qualification is the same pattern as the browser uniqueness figure in Lesson 2.1: a number from an artificial setting, repeated as though it described the world.

### Keystroke Timing

The foundation here is real and old. Song, Wagner and Tian showed in 2001 that SSH sent each keystroke in its own packet, revealing inter-keystroke timing, and padded only to a fixed boundary, revealing approximate length. Together those leak the length of a password and enough timing structure to narrow what was typed. (Reference 4)

**And here is the part the earlier version could not include.** OpenSSH added keystroke timing obfuscation in version 9.5, released in October 2023. It sends interactive traffic at fixed intervals and adds decoy keystrokes after real ones. (Reference 5)

Then the detail worth more than the feature: **the implementation had a defect in its first releases**. The real keystroke packets were slightly larger than the decoys, so they could be told apart by anybody capturing traffic. It was corrected in a later version.

**That is the lesson, not the trivia.** A defense that exists is not the same as a defense that works, and a defense that works today is a claim with a version number attached. This module has now produced that finding three times: Session's forward secrecy, the keyserver network, and now this.

---

## 4. Defenses, Sorted Honestly

The standard list of metadata protections is not wrong. It is unsorted, which makes it useless, because the items differ enormously in whether a person will still be doing them next month.

![[module_3_lesson_4_what_defends.svg]]

*Figure 3. The sorting principle is not effectiveness. It is sustainability.*

### Decided Once

**Choose a provider that keeps little.** The largest single change available, and it is one decision. Figure 1's middle column, addressed directly.

**Separate your identities.** Different accounts for different work, so the social graph never joins up. Lesson 1.2's layers, applied to messaging.

**Route the traffic.** The only thing that touches the network column.

**Do not create the pattern.** If a standing weekly call is legible, move it rather than disguising it. This is the one most often missed, and it is the strongest item in the column.

### Requires You, Every Time

**Randomise when you send.** Correct, and it means holding a message you have written and want to send. People do this for a fortnight.

**Vary your active hours.** Correct, and it asks you to work at times you would not otherwise work, indefinitely, for a benefit you cannot observe.

**Delete after reading.** Genuinely useful, and the only item here that automation can carry. Set a timer rather than relying on discipline, and it moves into the first column.

### Not Yours to Deliver

**Cover traffic.** Sending decoys so real messages do not stand out. This has to be built into the protocol and paid for in bandwidth by everybody. An individual cannot add it, and no mainstream messenger does it. It appears in almost every metadata guide as advice to the reader, and it is not advice to the reader.

**Padding message sizes.** Same problem, same reason.

**Defeating an observer who watches both ends.** Somebody positioned to see traffic entering and leaving an anonymity network can correlate the two. That is a stated limit of those systems, not a gap in your configuration, and no amount of care on your part closes it.

### The Conclusion

**You cannot obscure the shape by trying harder.** What you can do is make the shape uninteresting: fewer distinct patterns, fewer links between identities, and less that has to be defended at all.

Lesson 1.4 said a control you abandon protected nothing. The middle column is where that happens most often, and it is also where the advice sounds most rigorous.

---

## 5. Platform Positions, Stated Accurately

For consistency with the rest of the module, and because the earlier version overstates one of these:

**Signal.** Minimal retention, demonstrated rather than claimed. Sealed sender removes the sender identity from what the service handles in transit. The account is tied to a phone number, which the service holds even though other users no longer see it by default.

**Session.** No phone number, and routing through a network of independently operated nodes so that no single node sees both ends. (Reference 8)

The earlier version of this lesson says Session stores "nothing, truly nothing." That is the marketing position rather than the technical one. Messages sit on a group of nodes until collected, and the network still sees timing and volume. It collects far less than a centralised service, which is a real and useful property and is not the same as nothing.

**And the version caveat still applies.** Session removed forward secrecy in 2021 and restored it in late 2025. (Reference 9) Lessons 3.1 and 3.2 both made this point. A platform's metadata position and its cryptographic position are separate questions, and both carry dates.

**Email.** Everything in Lesson 3.3's Figure 1 travels in the clear, including the subject line. Encrypted mail is the worst metadata position of anything in this module, and it remains necessary in the cases Lesson 3.3 identified.

---

## 6. Applying It

Match the effort to what the task actually requires, which is Lesson 1.4 again.

**Routine professional work.** The social graph is not a secret and does not need defending. Use a decent platform, do not build an elaborate regime around it, and spend the attention elsewhere.

**Coordinated research.** The fact of the collaboration may matter more than its content. Separate accounts, and think about whether the timing of a burst around a disclosure tells anybody something you would rather they did not know.

**Receiving from somebody who must stay unknown.** Every column of Figure 1 matters, and the messenger addresses one of them. This needs a network path, a device, a plan, and a conversation with the other person about what they should and should not do. Encryption is a component here and it is not the answer.

**And say what you are accepting.** Lesson 1.4's discipline: a risk you have decided to accept is a decision, and one you never considered is an oversight. Write down which patterns of yours are legible and which of those you have chosen to live with.

---

## 7. Summary

- Three observers, and only the provider is a choice. The network path and the recipient are structural.
- A conversation has a shape: regularity, volume, breaks, sequence and correlation. All of it travels in the clear.
- Your routine is what makes your exception readable.
- Website fingerprinting figures in the high nineties are closed-world results. Open-world performance degrades sharply, and the base rate fallacy is why.
- Keystroke timing is real and old. The defense arrived in 2023 and did not work correctly in its first releases.
- Sort defenses by whether you will sustain them. Decisions made once beat vigilance you will abandon.
- Cover traffic is not advice to an individual. It is a protocol property nobody has deployed.
- Session collects far less than a centralised service, which is not the same as nothing.
- You cannot obscure the shape by trying harder. Make it uninteresting instead.

**Action item.** Draw your own version of Figure 2 for one week of your own communication, from memory. The shape you can recall is the shape somebody else can see.

---

## Module 3 Is Complete

Four lessons, and one argument running through them.

Lesson 3.1 reduced encryption to a single question about who holds a key, and showed that content protection and metadata retention are separate design decisions. Lesson 3.2 configured the tools by asking which observer each setting addresses. Lesson 3.3 found the encryption boundary sitting below the headers, and found that most of the surrounding guidance had expired. This lesson showed what an observer reads from what is left, and sorted the responses by whether a person will keep doing them.

**The thread was Myth 3, from Lesson 1.1.** Encryption protects content. It does not protect the record around the content, and that record is frequently the more useful of the two. Every correction in this module came from somebody stating the first half of that and stopping.

**In Module 4** the subject becomes authentication: password managers, hardware keys, and multi-factor authentication. The thread there is different, and one idea carries over unchanged. Ask what a control actually protects against, and be precise about what it leaves.

---

## Knowledge Check

**Question 1.** Of the three observers, which one is a design decision rather than structural?

- A) The provider, because what it retains was decided by whoever built the service
- B) The network path
- C) The other endpoint
- D) None of them

**Answer: A.** Every service has a network path and every conversation has a recipient. Only the middle column changes when you change products.

**Question 2.** Thirteen days of identical short sessions, then one long burst. Where is the signal?

- A) In the regular sessions, because routine is inherently suspicious
- B) Nowhere, because the traffic is encrypted
- C) In the break. A steady baseline is what makes a departure from it legible.
- D) In the total volume across the fortnight

**Answer: C.** Your routine is what makes your exception readable, which is an argument for having fewer distinct routines.

**Question 3.** Website fingerprinting research reports very high accuracy. What is the qualification?

- A) The research was withdrawn
- B) The high figures are closed-world results. In open-world conditions performance degrades sharply, and the base rate fallacy is why.
- C) It works only against unencrypted traffic
- D) It works only on mobile devices

**Answer: B.** The same pattern as the browser uniqueness figure in Lesson 2.1: an artificial setting quoted as though it described the world.

**Question 4.** What did the 2001 work on SSH keystroke timing establish?

- A) That the encryption was broken
- B) That passwords could be read directly from the traffic
- C) That timing attacks are theoretical
- D) That sending each keystroke in its own packet leaks inter-keystroke timing, and padding to a fixed boundary leaks approximate length

**Answer: D.** Together those narrow a password considerably without any decryption.

**Question 5.** OpenSSH added keystroke timing obfuscation in 2023. What happened next?

- A) The problem was solved completely
- B) The feature had a defect in its first releases that made real keystrokes distinguishable from the decoys by packet size, corrected later
- C) It was removed again
- D) Nobody enabled it

**Answer: B.** A defense that exists is not a defense that works, and this module has now produced that finding three times.

**Question 6.** Why is cover traffic in the third column of Figure 3?

- A) It does not work
- B) It is prohibited
- C) It has to be built into the protocol and paid for in bandwidth by everybody, so an individual cannot add it
- D) It is too slow to be practical

**Answer: C.** It appears in almost every metadata guide as advice to the reader, and it is not advice to the reader.

**Question 7.** What is the honest assessment of "randomise your send times"?

- A) Technically correct, and dependent on continuous human effort, so most people stop within weeks
- B) Useless in all circumstances
- C) The single most effective defense available
- D) Automatic in all modern messengers

**Answer: A.** Lesson 1.4's rule applies: a control you abandon protected nothing.

**Question 8.** How should Session's metadata position be described?

- A) It stores nothing at all
- B) It stores as much as a centralised service
- C) It is identical to Signal
- D) It collects far less than a centralised service, which is not the same as nothing. Messages sit on nodes until collected, and the network still sees timing and volume.

**Answer: D.** A real and useful property, overstated by the marketing and by most guides that repeat it.

**Question 9.** One of your accounts goes quiet exactly when another is active, repeatedly. What is that?

- A) Coincidence with no analytic value
- B) Relevant only if one of them is unencrypted
- C) A correlation signal. Silence is as observable as presence, and repetition across occasions links the two.
- D) Prevented by end-to-end encryption

**Answer: C.** Encryption has nothing to say about when you are and are not present.

**Question 10.** What is the practical conclusion of this lesson?

- A) Metadata protection is impossible, so do nothing
- B) You cannot obscure the shape by trying harder. Make it uninteresting: fewer distinct patterns, fewer links between identities, less that needs defending.
- C) Adopt cover traffic
- D) Encrypt more thoroughly

**Answer: B.** Removing a pattern beats disguising one, and it is the only approach that survives a busy week.

---

## Capstone Exercise: Read Your Own Shape

**Time: 90 minutes, plus 20 minutes to write up.**

### Objective

Work out what your own communication looks like from outside, then reduce it by decisions rather than by discipline.

---

### Part 1: Draw Your Own Figure 2 (25 minutes)

For one recent week, reconstruct your communication as a shape. You do not need logs. What you can remember is the point, because a pattern you can recall is a pattern that is legible.

| Day | Roughly when | Roughly how long | Anything unusual |
|---|---|---|---|

Then identify, from your own table:

1. Your most regular pattern.
2. Any day that broke it, and what the break would suggest.
3. Any sequence across services that carries meaning in its order.

**Deliverable.** The table and three answers. If you cannot find a regular pattern, say so, because that is a genuinely good result.

---

### Part 2: Assign Each Exposure to an Observer (20 minutes)

Take five things about your communication that you would rather were not visible.

| What I would rather was not visible | Which observer from Figure 1 | Can I change that observer? |
|---|---|---|

**The third column is the exercise.** For the network path, the answer is routing. For the provider, it is a different provider. For the recipient, there is no technical answer at all.

**If everything lands in one column, say which**, because that tells you where your effort belongs.

**Deliverable.** The table, plus one line on where your exposure is concentrated.

---

### Part 3: Sort Your Own Defenses (20 minutes)

List every metadata protection you currently practise or intend to. Sort each into one of Figure 3's three columns.

| Defense | Column | Honest assessment |
|---|---|---|

Then answer directly:

1. **Which column two items have you already stopped doing?** Be honest. This is the useful part.
2. **Can any of them be automated into column one?** Disappearing message timers are the obvious case.
3. **Is anything on your list actually in column three?** If you have written down cover traffic or padding as something you will do, strike it.

**Deliverable.** The sorted table and three answers.

---

### Part 4: Remove One Pattern (15 minutes)

Pick the single most legible pattern from Part 1.

Then design the removal rather than the disguise:

- What is the pattern?
- Why does it exist?
- What would remove it entirely, rather than obscure it?
- What does removing it cost, and is that acceptable?

**The framing matters.** "Send at random times" is disguise. "Move this off a channel where the pattern is visible" or "stop having a standing call at a fixed hour" is removal. Removal survives; disguise does not.

**Deliverable.** One pattern, and a removal plan with its cost.

---

### Part 5: Write Down What You Accept (10 minutes)

| Exposure I am accepting | Why it is acceptable | What would change that |
|---|---|---|

At least three rows. A realistic answer has several.

**This is the part that makes the rest defensible.** Lesson 1.4 said a risk you decided to accept is a decision, and one you never considered is an oversight. This table converts the second into the first.

**Deliverable.** The table.

---

### Submission

1. **Your shape**, Part 1, with the three readings.
2. **Observer assignment**, Part 2, with where your exposure concentrates.
3. **Sorted defenses**, Part 3, including what you have already abandoned.
4. **One removal**, Part 4, with its cost.
5. **Accepted exposures**, Part 5.
6. **Reflection**, half a page: which metadata advice had you absorbed that turns out to be column three?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Reading your own shape | 25 | A real pattern identified, and what a break in it would suggest |
| Observer assignment | 20 | Exposures correctly attributed, and the concentration identified |
| Honest sorting | 25 | Column two items you have abandoned are named, and column three items are struck |
| Pattern removal | 20 | Removal rather than disguise, with the cost stated |
| Accepted exposures | 10 | At least three, with reasoning |
| **Total** | **100** | |

Note the weighting. Honest sorting carries as much as reading the shape, because admitting what you stopped doing is the finding that changes behaviour.

---

## Additional Resources

### Primary Sources

- Song, Wagner and Tian, Timing Analysis of Keystrokes and Timing Attacks on SSH, USENIX Security 2001: https://www.usenix.org/conference/10th-usenix-security-symposium/timing-analysis-keystrokes-and-timing-attacks-ssh
- OpenSSH 9.5 release notes, keystroke timing obfuscation: https://www.openssh.org/txt/release-9.5
- Cherubin and others, Online Website Fingerprinting, USENIX Security 2022: https://www.usenix.org/conference/usenixsecurity22/presentation/cherubin

Read the 2001 paper if you read only one. It is short, it is twenty-five years old, and everything in this lesson is a consequence of what it established.

### Discussion Questions

1. Which of your patterns would be most legible to somebody watching only the network column, and could you remove it rather than disguise it?
2. This module found three cases where a security property was present, then absent, then present again. What does that suggest about how you should read any recommendation, including this one?
3. Cover traffic appears in most metadata guidance as something the reader should do. Why do you think that persists, given that no mainstream tool offers it?

---

## References

1. [Just Security, Michael Hayden: "We Kill People Based on Metadata"](https://www.justsecurity.org/10311/michael-hayden-kill-people-based-metadata/)
2. [Signal, grand jury subpoena response, District of Columbia](https://signal.org/bigbrother/district-of-columbia/)
3. [Just Security, We Now Know What Information the FBI Can Obtain from Encrypted Messaging Apps](https://www.justsecurity.org/79549/we-now-know-what-information-the-fbi-can-obtain-from-encrypted-messaging-apps/)
4. [Song, Wagner and Tian, Timing Analysis of Keystrokes and Timing Attacks on SSH, USENIX Security 2001](https://www.usenix.org/conference/10th-usenix-security-symposium/timing-analysis-keystrokes-and-timing-attacks-ssh)
5. [OpenSSH 9.5 release notes, October 2023](https://www.openssh.org/txt/release-9.5)
6. [Juarez and others, A Critical Evaluation of Website Fingerprinting Attacks](https://www.research.ed.ac.uk/en/publications/a-critical-evaluation-of-website-fingerprinting-attacks/)
7. [Cherubin and others, Online Website Fingerprinting: Evaluating Website Fingerprinting Attacks on Tor in the Real World, USENIX Security 2022](https://www.usenix.org/conference/usenixsecurity22/presentation/cherubin)
8. [Session, Session Protocol technical information](https://getsession.org/session-protocol-technical-information)
9. [Privacy Guides, Session messenger adds forward secrecy and post-quantum encryption, December 2025](https://www.privacyguides.org/news/2025/12/03/session-messenger-adds-pfs-pqe-and-other-improvements/)
