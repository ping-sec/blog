---
title: "Signal and Session: Configuring for a Specific Observer"
course: Digital Privacy for Ethical Hackers
module: 3
lesson: 3.2
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - encrypted-communications
  - signal
  - session
  - verification
  - opsec
---

# Signal and Session: Configuring for a Specific Observer

## About This Lesson

Lesson 3.1 established the concepts. This lesson configures the tools, and it does so by asking a question that configuration guides usually skip: **which observer does this setting address?**

That reframing matters. A list of switches to turn on produces a feeling of thoroughness. It does not tell you what you are protected from, and it leaves people surprised when a setting they enabled turns out not to cover the thing they were worried about.

Signal has also changed substantially since most guidance was written. Two of the changes affect the first thing you do when you install it.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Configure Signal's identity settings, and say what each one exposes and to whom.
- Map each privacy setting to the specific observer it addresses.
- Verify a contact, using both manual comparison and the automated mechanism.
- Describe Session's actual trust model rather than its reputation.
- Choose between them on the one dimension where they genuinely differ.

### Prerequisites

- Lesson 3.1, particularly the content and metadata distinction.
- Your threat model from Lesson 1.4.

---

## 1. Identity: Three Controls That Get Confused

Older guidance says your phone number is your Signal identity, visible to everyone you speak to, and that a second number is the only remedy. The first half of that stopped being true in 2024.

![[module_3_lesson_2_identity_settings.svg]]

*Figure 1. Three separate controls, and they answer different questions.*

**Usernames** arrived in February 2024. You choose a handle, share it as text or as a scannable code, and somebody can start a conversation with you without ever having your number. (Reference 1)

**Your number is now hidden by default** from the people you chat with. People who already have it in their address book still see it, because they already had it. (Reference 2)

**"Who can find me by my number"** is separate again, and controls whether somebody holding your number can discover that you are on the service. The default allows it. Set it to nobody if that matters to you.

### What This Does Not Change

A phone number is still required to register. These settings hide it from other users. They do not remove it from the account, and the service still holds it.

So a username does not give you an anonymous account. If the number itself must not connect back to you, that is a different problem, solved before registration rather than in the settings screen afterwards, and Section 4 covers the alternative.

**Why this correction matters.** Guidance written before 2024 sends people to buy a second number for a problem that three settings now solve. A second number is still the right answer to the harder question. It is no longer the answer to the easy one.

---

## 2. Every Setting Has One Observer

Here is the reframing that makes the configuration list useful.

![[module_3_lesson_2_settings_vs_threats.svg]]

*Figure 2. Read across a row. Most rows help against exactly one column.*

**Read receipts and typing indicators** address the person you are messaging. They tell that person when you read and when you are composing. Disabling them changes nothing about what the servers see.

**Link previews** address the operator. When your client fetches a preview, something retrieves that address on your behalf. Disabling previews stops that fetch.

**Always relay calls** addresses the person you are calling. A direct call reveals your network address to them. Relaying routes it through the service instead, at a small latency cost.

**Sealed sender** addresses the operator. It removes the sender identity from what the service can see about a message in transit, so the operator handles a delivery without learning who sent it.

**Screen lock, notification content, and disappearing messages** all address the same observer: somebody with physical access to your unlocked device. Not the network, not the operator, not the recipient.

### The Column That Barely Moves

Look at the last column in Figure 2. Almost nothing you switch on changes what a provider can be compelled to hand over.

That was decided when the service was designed. Lesson 3.1 showed the two ends of that spectrum with the Signal and WhatsApp subpoena comparison. (Reference 11) It is a platform choice, and the settings screen does not touch it.

**So switching everything on is coverage, not depth.** Four settings that each address a different observer give you four kinds of protection. Four settings that all address the same observer give you one, four times over. Know which you have.

### The Rest of the Configuration

Two settings that are worth doing and do not fit the matrix neatly:

**Registration lock.** A PIN required before your number can be registered on a new device. This addresses somebody taking over your phone number, which is a real and common attack and is not otherwise covered by anything in this lesson. Set it, and store the PIN in your password manager.

**Screen security.** Behaves differently by platform. On one it blocks screenshots; on another it mainly hides the conversation from the application switcher. Useful, and check what yours actually does rather than assuming.

---

## 3. Verification, Manual and Automatic

Lesson 3.1 covered the mechanism. Here is the practice.

**Manual comparison** remains the strongest thing you can do. Open the safety number for a contact and compare it over a channel the attacker does not control: in person, or over a video call where you can see and hear them. Scanning the code in person is fastest and least error-prone. Then mark it verified.

**Do not compare inside the conversation you are verifying.** If somebody is sitting in the middle of that conversation, they can substitute the numbers you are comparing. This sounds obvious written down and is a common mistake in practice.

**Automatic key verification** is the newer mechanism, and Signal shipped it in August 2026. It uses key transparency: key associations enter a publicly auditable log, and unexpected changes are detected without you meeting anybody. (Reference 5)

**Use both, at different tiers.** For most contacts, automatic verification is the realistic option and it is a real improvement over the manual verification that most people never did. For a contact where an active attacker is genuinely in your threat model, do the manual comparison as well.

### When a Safety Number Changes

The alert means the contact's key changed. Almost always that is a reinstall or a new device. Occasionally it is not.

The correct response is to ask them, **over a different channel**, whether they reinstalled. Not in the conversation that just changed, because if the change was hostile you would be asking the attacker.

Then re-verify.

---

## 4. Session, Described Accurately

The earlier version of this lesson describes Session as using the Signal Protocol with onion routing, no metadata at all, and forward secrecy. Most of that needs correcting.

![[module_3_lesson_2_signal_vs_session.svg]]

*Figure 3. Two trust models, not two grades of the same thing.*

**What is true.** Session needs no phone number and no mail address. Your identity is a generated identifier, and there is no directory to look you up in. Messages route through three nodes so that no single node sees both ends, and your address is not visible at the destination. (Reference 8)

**What is not the Tor network.** The routing runs over a set of independently operated service nodes, funded through a cryptocurrency. That is a genuine design and it is not Tor, and the difference matters when you assess who operates the infrastructure. (Reference 6, Reference 8)

**What was wrong.** Session moved off the Signal Protocol and **removed forward secrecy in 2021**, citing the difficulty of running the Signal design over a decentralised network. It restored forward secrecy, with post-quantum protection, in late 2025. (Reference 6, Reference 7)

So a recommendation written between 2021 and 2025 that lists Session as having forward secrecy was wrong at the time, and Lesson 3.1 used this as the example of reputation outliving specification.

### "No Metadata" Is Marketing

Session collects far less than a centralised service, and that is real. It is not nothing. Messages are stored on a swarm of nodes until collected, and the network still sees traffic timing and volume. Decentralisation moves trust from one operator to a set of node operators and an incentive scheme. That may be a trade you want. It is a trade, not an elimination.

### Choosing Between Them

They differ on one dimension that actually matters: **whether the account requires a phone number**.

If an account tied to a number is acceptable, Signal has the longer record of independent examination, the better-documented response to legal process, and continuous forward secrecy.

If it is genuinely unacceptable, that is the case for Session, and you take on its trust model to get it.

**And neither hides who you are.** An identifier with no name attached is still linkable through what you say, when you say it, and where you connect from. That is Lesson 1.2's aggregation argument, applied to messaging. The messenger is one component of anonymity and is not anonymity by itself.

---

## 5. Backups, Which Changed

Signal had no backup on some platforms for years, and guidance reflects that.

**Signal Secure Backups** launched in 2025. Backups are opt-in and end-to-end encrypted. A free tier covers text messages and recent media; a paid tier extends media history. (Reference 3, Reference 4)

Two things matter operationally:

**The recovery key is separate from your PIN.** It is a long key, it is the only way to decrypt the backup, and Signal cannot recover it for you. That is the Lesson 3.1 recovery question answered correctly by design, and it means losing the key means losing the backup.

**Backups change your threat model.** A backup that exists is a copy that can be compelled, stolen or seized. For most people that trade is worth it. For an operational account it may not be, and "no backup, accept the loss" remains a legitimate configuration.

---

## 6. Other Platforms, Briefly

Lesson 3.1 gave you the evaluation questions. Applying them:

**Wire and Element.** Both end-to-end encrypt by default in the right configuration, both are open, and both offer more collaboration features than Signal. Element federates and can be self-hosted, which changes who you are trusting. With Element, confirm the room is actually encrypted, because not all are.

**Threema.** No phone number required, paid once, based in Switzerland, and open-sourced its clients in 2020.

It also carries a lesson worth more than the recommendation. In 2023 researchers at ETH Zurich published seven attacks against Threema across three threat models, including recovering private keys through a side channel on backup size. Threema fixed them and publicly downplayed their significance. (Reference 9, Reference 10)

**The general point.** "Open source and audited" is a description of a moment, not a permanent property. A protocol can be published, examined, and still contain flaws that a later examination finds. Treat an audit as evidence that somebody looked, not as evidence that nothing is there.

**What to avoid for sensitive work.** Anything without end-to-end encryption on by default. Ordinary workplace chat platforms have none, and the correct assumption is that everything in them is readable, logged and searchable by the operator and by whoever compels the operator.

---

## 7. Operational Practice

**Compartmentalize the same way you did with browsers.** Lesson 1.2's layers apply. Personal, professional and operational communication belong in separate identities, and mixing them is the same silent failure that Lesson 2.5 described.

**Match the tool to the sensitivity**, not to your maximum capability. A verified Signal conversation is proportionate for client findings. It is not proportionate for arranging lunch, and a control that feels excessive gets abandoned.

**Understand what disappearing messages do.** They limit what is on a device later. They do not stop the recipient photographing the screen, and they do not remove what the provider recorded. Useful for the device-seizure case and frequently overtrusted for everything else.

**Two failure patterns worth naming.**

The first is **moving content out of the secure channel**. Capturing a conversation and sending it onward through mail undoes everything in this lesson in one action. The secure channel is only secure while the content stays in it.

The second is **publishing the link between your identities**. Posting an operational contact address alongside your professional identity connects the two permanently, and no setting reverses it.

---

## 8. Summary

- Your phone number is hidden from people you chat with by default since 2024, and usernames let people reach you without it. A second number answers a different and harder question.
- Every setting addresses one observer. Read Figure 2 across the rows and know which one each is covering.
- What a provider can be compelled to disclose is a platform choice, not a setting.
- Verify manually for contacts that matter, and let automatic key verification cover the rest. Never compare inside the conversation you are verifying.
- Session needs no phone number, routes over an independently operated node network rather than Tor, and removed forward secrecy from 2021 until late 2025.
- "No metadata" is a comparison, not a fact. Decentralisation moves trust rather than removing it.
- Signal Secure Backups are end-to-end encrypted with a recovery key that nobody can recover for you. A backup is also a copy that can be compelled.
- An audit describes a moment. Threema was published, examined, and still had seven attacks found against it.

**Action item.** Open your messenger's settings today and, for each privacy option you have enabled, name the observer it addresses. Anything you cannot assign to a column in Figure 2 is worth looking up.

The next lesson covers encrypted mail, which is harder than messaging for reasons worth understanding before you attempt it.

---

## Knowledge Check

**Question 1.** Since 2024, what is the default for showing your phone number to people you chat with on Signal?

- A) Everyone you message sees it
- B) It is shown only inside groups
- C) It has always been hidden
- D) It is hidden by default, though people who already hold your number in their address book still see it

**Answer: D.** Guidance written before this change sends people to buy a second number for a problem that settings now solve.

**Question 2.** A Signal username gives you:

- A) An anonymous account with no phone number behind it
- B) A way for people to reach you without giving them your number, on the same account
- C) A second, separate account
- D) Protection against key changes

**Answer: B.** A username is a second name for one account. Registration still requires a number, and the service still holds it.

**Question 3.** Which observer does "always relay calls" address?

- A) The service operator
- B) Somebody holding your unlocked phone
- C) The person you are calling, by keeping your network address from them
- D) Legal process against the provider

**Answer: C.** A direct call would otherwise reveal your address to the other party.

**Question 4.** In the settings matrix, which column is barely affected by anything you can switch on?

- A) What the provider can be compelled to hand over, because that was decided when the service was designed
- B) What the person you message can see
- C) What somebody holding your phone can see
- D) What the operator can see in transit

**Answer: A.** Lesson 3.1 showed both ends of that spectrum with the Signal and WhatsApp comparison.

**Question 5.** What is Session's forward secrecy history?

- A) It has always had forward secrecy
- B) It has never had forward secrecy
- C) It removed forward secrecy in 2021 when it left the Signal Protocol, and restored it in late 2025
- D) Forward secrecy does not apply to Session

**Answer: C.** Recommendations published during those four years described a property the product did not have.

**Question 6.** Session routes messages through three nodes. What is that network?

- A) A set of independently operated service nodes funded through a cryptocurrency, which is not the Tor network
- B) The Tor network
- C) Servers operated by the Session project itself
- D) A direct connection between the two devices

**Answer: A.** The distinction matters, because it determines who operates the infrastructure you are trusting.

**Question 7.** What should you know about the recovery key for Signal Secure Backups?

- A) Signal can reset it if you lose it
- B) It is the same value as your registration PIN
- C) It is optional and backups work without it
- D) It is a separate long key, and losing it means losing the backup, because Signal cannot recover it

**Answer: D.** That is the recovery question from Lesson 3.1, answered correctly by design.

**Question 8.** Disappearing messages primarily protect against:

- A) The provider reading your messages
- B) Somebody who gains access to a device later, because the content is no longer there
- C) The recipient keeping a copy
- D) Legal process against the provider

**Answer: B.** They do not stop the recipient capturing the screen, which is the most commonly overestimated part of this feature.

**Question 9.** Researchers published seven attacks against Threema in 2023. What is the general lesson?

- A) A published and examined protocol can still contain flaws, so "audited" describes a moment rather than a permanent property
- B) Threema should never be used
- C) Products from that jurisdiction are unsafe
- D) Only open source software contains flaws

**Answer: A.** Treat an audit as evidence somebody looked, not evidence that nothing is there.

**Question 10.** You must receive information from somebody whose identity must not become known. What is the honest scope of the messenger's contribution?

- A) The right messenger solves it entirely
- B) No messenger helps at all
- C) The messenger addresses the content and the account identifier, and does nothing about the network path, the device, or how the person writes
- D) Any end-to-end encrypted application is sufficient

**Answer: C.** This is Lesson 1.2's aggregation argument. The messenger is one component and not the whole answer.

---

## Exercise: Configure, Then Justify Every Switch

**Time: 100 minutes, plus 20 minutes to write up.**

### Safety Notes

- **Never write a recovery phrase or recovery key into a document you will submit or share.** Record only that you stored it, and where. This exercise asks you to confirm, not to transcribe.
- Do not ask a partner to reinstall their messenger to demonstrate a key change. That destroys their message history. Section 3 explains what a change means without requiring you to cause one.

---

### Part 1: Identity Settings (20 minutes)

Open your messenger and find the three controls from Figure 1.

| Control | Current value | What it exposes | Changing it? |
|---|---|---|---|
| Username | | | |
| Who can find me by number | | | |
| Who can see my number | | | |

Then answer: **does your account need to not be connected to your phone number at all?** If yes, note that no setting here achieves that, and say what would.

**Deliverable.** The table plus that answer.

---

### Part 2: Assign Every Setting an Observer (25 minutes)

Go through your messenger's privacy settings. For each one you have enabled, name the observer it addresses, using Figure 2's four columns.

| Setting | Enabled? | Which observer | How I know |
|---|---|---|---|

**Two rules for this table.**

Anything you cannot assign to a column, look up before you fill the row. That is the point of the exercise.

And count your columns at the end. **If every setting you enabled addresses the same observer, you have one protection repeated, not several.** Say which observer you have left uncovered.

**Deliverable.** The table, plus your coverage count.

---

### Part 3: Verify a Contact, Both Ways (20 minutes)

Pick one contact who matters.

1. **Manual.** Find the safety number and compare it over a channel that is not the conversation itself. In person or on a video call. Mark verified.
2. **Automatic.** Find out whether your messenger offers key transparency or automatic key verification, and whether it is active for you.
3. **Record what each told you**, and what neither told you.

Point 3 again: neither mechanism confirms the person is who they claim to be. It confirms that the key did not change unexpectedly. Being precise about that boundary is the learning.

**Deliverable.** What you did, what it confirmed, and the limitation.

---

### Part 4: Session, Assessed Rather Than Installed (20 minutes)

You do not need to adopt Session. You need to be able to describe it accurately.

Answer these from primary sources:

1. What identifies an account, and what is required to create one?
2. What network carries the messages, who operates it, and how are those operators funded?
3. Does the current version have forward secrecy, and since when?
4. What does the project itself claim about metadata, and what does that claim exclude?
5. What is the one thing Session gives you that Signal does not?

**Question 4 is the one that separates a real assessment from repeating a marketing line.**

**Deliverable.** Five answers with sources.

---

### Part 5: Backups, Decided Deliberately (15 minutes)

1. Find out what backup options your messenger currently offers, and whether you have any enabled.
2. If backups are enabled, establish where the recovery material is and confirm it is stored somewhere you would still have after losing the device. **Confirm only. Do not transcribe it.**
3. Answer: **is a backup a good idea for this account?** A backup is a copy that can be compelled, stolen or seized. For a personal account, usually yes. For an operational one, often not.

**Deliverable.** Your current state, and a decision with one line of reasoning.

---

### Part 6: Map Tools to Work (15 minutes)

| Task | Platform | Verified contact? | Disappearing timer | What I am accepting |
|---|---|---|---|---|
| Client coordination | | | | |
| Sending findings to a client | | | | |
| Coordinating with a researcher | | | | |
| Receiving from a source who must stay unknown | | | | |

**The last row again.** If your answer is only a platform name, re-read Section 4. That row needs a network path, a device, and a plan, and the messenger is one part of it.

**Deliverable.** The table.

---

### Submission

1. **Identity settings**, Part 1.
2. **Observer assignment**, Part 2, with your coverage count.
3. **Verification**, Part 3, with the limitation stated.
4. **Session assessment**, Part 4, sourced.
5. **Backup decision**, Part 5, with reasoning. **No recovery material.**
6. **Tool mapping**, Part 6.
7. **Reflection**, half a page: which setting did you have enabled without knowing what it protected against?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Identity settings | 15 | All three understood as separate, and the account-level question answered honestly |
| Observer assignment | 30 | Every enabled setting assigned correctly, uncovered observer identified |
| Verification | 20 | Both mechanisms used, and the limitation stated |
| Session assessment | 20 | Sourced from primary material, and the metadata claim examined rather than repeated |
| Backup decision | 10 | Deliberate, with reasoning, and no recovery material included |
| Reflection | 5 | Names a specific setting whose purpose was unclear |
| **Total** | **100** | |

Note the weighting. Assigning settings to observers carries the most, because that is the skill that survives the next interface change.

---

## Additional Resources

### Primary Sources

- Signal, phone number privacy and usernames: https://signal.org/blog/phone-number-privacy-usernames/
- Signal, Secure Backups: https://signal.org/blog/introducing-secure-backups/
- Signal, Automatic Key Verification: https://signal.org/blog/automatic-key-verification/
- Session Protocol, technical information: https://getsession.org/session-protocol-technical-information

### Reading

- Paterson, Scarlata and Truong, Three Lessons From Threema, USENIX Security 2023: https://www.usenix.org/system/files/usenixsecurity23-paterson.pdf

Worth reading even if you never use Threema. It is a clear account of how a published, examined protocol can still fail, and the title is the point.

### Discussion Questions

1. Which observer is least covered by your current configuration, and is that deliberate?
2. Signal now hides your number by default. What other piece of advice you follow might have been fixed at the source without your noticing?
3. Session removes the phone number and adds a different set of operators to trust. For your work, which of those two matters more?

---

## References

1. [Signal, Keep your phone number private with Signal usernames](https://signal.org/blog/phone-number-privacy-usernames/)
2. [Signal Support, Phone Number Privacy and Usernames, deeper dive](https://support.signal.org/hc/en-us/articles/6829998083994-Phone-Number-Privacy-and-Usernames-Deeper-Dive)
3. [Signal, Introducing Signal Secure Backups](https://signal.org/blog/introducing-secure-backups/)
4. [Signal Support, Signal Secure Backups](https://support.signal.org/hc/en-us/articles/9708267671322-Signal-Secure-Backups)
5. [Signal, Introducing Automatic Key Verification](https://signal.org/blog/automatic-key-verification/)
6. [Session, Session Protocol technical information](https://getsession.org/session-protocol-technical-information)
7. [Privacy Guides, Session messenger adds forward secrecy and post-quantum encryption, December 2025](https://www.privacyguides.org/news/2025/12/03/session-messenger-adds-pfs-pqe-and-other-improvements/)
8. [Oxen documentation, Session message routing and the service node network](https://docs.oxen.io/oxen-docs/products-built-on-oxen/session/message-routing)
9. [Paterson, Scarlata and Truong, Three Lessons From Threema, USENIX Security 2023](https://www.usenix.org/system/files/usenixsecurity23-paterson.pdf)
10. [ETH Zurich, Vulnerabilities in secure messenger Threema discovered, January 2023](https://inf.ethz.ch/news-and-events/spotlights/infk-news-channel/2023/01/threema.html)
11. [Signal, grand jury subpoena response, District of Columbia](https://signal.org/bigbrother/district-of-columbia/)
