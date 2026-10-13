---
title: "End-to-End Encryption: Who Holds the Key, and What It Does Not Cover"
course: Digital Privacy for Ethical Hackers
module: 3
lesson: 3.1
format: article
reading_time: 28 minutes
tags:
  - digital-privacy-course
  - privacy
  - encrypted-communications
  - e2ee
  - forward-secrecy
  - post-quantum
  - metadata
  - opsec
---

# End-to-End Encryption: Who Holds the Key, and What It Does Not Cover

## About This Lesson

Module 3 begins where Lesson 1.1 left a promise unpaid. Myth 3 said that encryption protects content and leaves metadata exposed, and that the two are separate questions. Messaging is where that distinction gets expensive, and this lesson makes it precise.

It also covers something the earlier version of this lesson could not, because it happened afterwards. Between 2023 and 2025 every major encrypted messenger changed its cryptography in response to an attack that has not happened yet. Section 4 explains what that attack is and why the response was reasonable.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Separate three architectures using one question, and apply it to any product.
- Explain why two platforms with identical content encryption give very different answers to legal process.
- Describe forward secrecy, and say precisely which attack it defeats.
- Explain harvest-now-decrypt-later, and why forward secrecy alone does not answer it.
- Evaluate a platform's current specification rather than its reputation.

### Prerequisites

- Module 1, particularly Lesson 1.1 on the myths and Lesson 1.4 on threat modeling.
- Module 2, particularly the habit of checking whether a claim has expired.

---

## 1. One Question, Three Architectures

Most treatments of this subject begin by listing encryption types. That produces a taxonomy you have to memorise. There is a shorter route.

**Everything is encrypted.** Transport encryption is encryption. Encryption at rest is encryption. Vendor-managed encryption is encryption. The word tells you nothing, which is exactly why marketing uses it so freely.

The question that separates them is: **who holds a key that can decrypt the content?**

![[module_3_lesson_1_where_plaintext_lives.svg]]

*Figure 1. Three architectures, one discriminator.*

**Transport encryption.** The server holds the key. Your message is encrypted to the server, decrypted there, processed, and re-encrypted onward. Plaintext exists on the server. This protects you from the network and not from the provider. It covers most mail, most workplace chat, and most of what calls itself secure.

**Vendor-held keys.** The message is stored encrypted, and the vendor can decrypt it. This is the architecture that sounds like the third column in marketing and behaves like the first column under a subpoena.

**End-to-end encryption.** Keys exist only on the two devices. The server relays ciphertext and cannot open it.

### The Question That Actually Works on a Vendor

Do not ask whether a product is encrypted. Ask this:

**"If I lose my password, can you restore my messages?"**

If the answer is yes, they hold a key. There is no way to be genuinely end-to-end encrypted and also able to recover somebody's content for them. A recovery feature is not a customer service decision. It is an architecture disclosure, and it is usually made in a help article rather than on the security page.

### Phrases That Answer a Different Question

**"Bank-grade" and "military-grade" encryption** describe an algorithm. All three architectures in Figure 1 can use the same algorithm. The phrase is compatible with the server reading everything.

**"Encrypted at rest"** is the middle column stated positively. **"Encrypted in transit"** is the left column. Neither names a key holder.

**"Zero-knowledge"** should mean the provider never sees keys or plaintext. Check it against the recovery question, because vendors use the phrase while offering recovery.

---

## 2. What End-to-End Encryption Does Not Cover

Content protection is not communication privacy. The gap between them is the subject of most of this module.

![[module_3_lesson_1_content_vs_metadata.svg]]

*Figure 2. Same protocol for content, very different answers under legal process.*

The record around a message names which two accounts, at what moment, how large, how often, from which addresses, and on which application and device. None of that is content, so none of it is protected by encrypting content.

### The Comparison That Makes This Concrete

Signal and WhatsApp both encrypt message content using the same underlying protocol. Their answers when compelled are not comparable.

**Signal**, responding to a grand jury subpoena, supplied two values: the account creation timestamp and the last connection timestamp. That was the complete response. No contacts, no groups, no message records, because the service was built not to hold them. (Reference 1)

**WhatsApp**, according to a law enforcement training document reported in 2021, can supply metadata on a target every fifteen minutes under a pen register order. Under a warrant it can supply the target's address book contacts, and the details of other users who hold the target in theirs. Content stayed encrypted throughout. (Reference 2)

**Read those two together.** The difference is not encryption strength. It is a decision about what to collect and keep. A platform can answer "is it end-to-end encrypted" perfectly and answer "what does it know about me" badly, and most of them do.

So ask both questions, every time. Lesson 1.1 called this Myth 3. This is the same idea with the field names filled in.

### Why This Matters More Than It Sounds

The Hayden line from Lesson 1.3 belongs here too, because messaging is what it was about. A former director of both the National Security Agency and the Central Intelligence Agency said in a public debate: "We kill people based on metadata." (Reference 11)

For your work the consequences are smaller and the mechanism is identical. A burst of messages to a company's security address, then to its legal address, then to a disclosure platform, tells the whole story with no content at all.

---

## 3. Getting Verification Right

End-to-end encryption is only as good as your confidence that you are encrypting to the right person.

The attack is straightforward. If somebody can substitute their key for your contact's, they sit in the middle, decrypting and re-encrypting. The encryption works perfectly throughout, to the wrong party.

The classic answer is out-of-band verification: compare the safety numbers over a channel the attacker does not control, in person or by voice. That is still correct, and it is still the strongest thing you can do.

### What Changed

The classic answer has a well-known problem, which is that almost nobody does it. So the platforms automated it.

**Key transparency** puts every key association into a cryptographically verifiable, publicly auditable log. If a key changes, the change is recorded, and clients and independent auditors can check that the provider is not quietly substituting keys.

WhatsApp deployed key transparency in 2023. (Reference 8) Apple Messages checks it automatically when a contact's key changes, alongside its Contact Key Verification feature. (Reference 6) Signal shipped Automatic Key Verification in August 2026, which checks whether a contact's keys changed unexpectedly without requiring you to meet anybody. (Reference 7)

**What this changes for you.** Manual comparison is no longer the only mechanism, and for most contacts the automatic check is now the realistic option. For a contact where an active attacker is genuinely in your threat model, do both. Automation raises the floor; it does not replace deliberate verification at the top.

**What it does not change.** A verification mechanism only tells you the key did not change unexpectedly. It says nothing about whether the person at the other end is who they claim to be in the first place, which is a different problem with a human answer.

---

## 4. The Attack That Happens Later

This section covers the largest change in messaging cryptography since the Signal Protocol itself, and the earlier version of this lesson predates all of it.

![[module_3_lesson_1_harvest_now.svg]]

*Figure 3. Recorded today, read later.*

### Forward Secrecy, Precisely

Without forward secrecy, an adversary records your encrypted traffic, obtains your long-term key at some later point, and reads the whole archive retrospectively. Years of communication compromised by one seizure.

With forward secrecy, each message uses a key derived for that message and then discarded. The long-term key does not reconstruct the discarded ones. Seizing your device stops the archive from opening.

Note the older term "perfect forward secrecy" and its modern shortening to "forward secrecy". They mean the same thing.

### Where Forward Secrecy Stops

Forward secrecy assumes the adversary gets your key. It does not cover the case where the adversary breaks the **method used to agree** the keys.

That is the harvest-now-decrypt-later problem. An adversary who cannot read your traffic today records it anyway, betting that a capability will arrive later which breaks the key exchange. Discarding per-message keys does not help, because the exchange that established them is what gets broken.

**This is why every serious messenger changed its cryptography, and the timeline is recent:**

- **September 2023.** Signal upgraded its key agreement from X3DH to PQXDH, combining the existing elliptic curve exchange with a post-quantum mechanism so that an attacker must break both. (Reference 3)
- **February 2024.** Apple shipped PQ3 for iMessage, a hybrid protocol with post-quantum key establishment and ongoing rekeying, mixing a new post-quantum key in periodically rather than only at session setup. (Reference 5)
- **2025.** Signal added a post-quantum ratchet alongside the existing double ratchet, so the post-quantum protection extends through the conversation rather than only its beginning. (Reference 4)

**Note the shape of all three.** They are hybrid: the new mechanism runs alongside the established one rather than replacing it, so the result is no weaker than what it replaces even if the new mechanism turns out to be flawed. That is a sound engineering pattern and worth recognising when you evaluate any cryptographic migration.

**And note the honest framing.** Nobody is claiming the capability exists. The argument is that traffic recorded today can be stored indefinitely, so the defence has to be deployed before the capability arrives rather than after. If your work involves material that is still sensitive in fifteen years, that argument applies to you.

---

## 5. Reputation Is Not a Specification

Here is a correction that carries a general lesson.

The earlier version of this lesson lists Session as using the Signal Protocol with forward secrecy. Neither has been reliably true.

Session moved off the Signal Protocol to its own protocol, and in doing so **removed forward secrecy in 2021**, citing difficulty running the Signal design over a decentralised network. It restored forward secrecy, along with post-quantum protection, in late 2025. (Reference 9, Reference 10)

So Session had no forward secrecy for roughly four years, during a period when it was widely recommended in privacy guides on the strength of a description that had stopped being accurate.

**Three things follow from that.**

**Protocols lose properties as well as gain them.** The direction of travel is not always forward, and a project can trade a security property for an engineering goal.

**A reputation lags a specification by years.** Session's privacy-focused reputation survived the removal intact, because nobody re-checked.

**Check the version, not the name.** Ask what the build you are actually running does, which is a different question from what the project is known for. This is the same discipline Module 2 applied to browser preferences, and it applies harder here because the details are less visible.

### Applying It

A short evaluation, in the order that eliminates fastest:

1. **Who holds a key?** Use the recovery question from Section 1. Most candidates fail here.
2. **Is it on by default, or is it a mode you have to select?** A feature you must remember to turn on will sometimes be off.
3. **What does the provider keep?** Read the transparency report and any published response to legal process. This is Figure 2, and it is where otherwise-equivalent products separate.
4. **Is the protocol published and independently examined?** Not merely open source. Examined.
5. **Does the current version have forward secrecy, and post-quantum key agreement?** Current version. See above.
6. **How do you verify a contact?** Manual comparison, key transparency, or nothing at all.

---

## 6. Matching the Tool to the Work

Applying Lesson 1.4's discipline: decide what the task requires, then pick.

**Routine professional correspondence.** Transport encryption is what you have and usually what you will keep, because the other party chooses too. Know that the provider can read it, and write accordingly.

**Client findings.** End-to-end encryption, on by default, with a verified contact. The material is somebody else's confidential data under a contract, and Lesson 1.3's file-path case showed how it leaks.

**Coordinated disclosure.** End-to-end encryption plus attention to metadata. The pattern of who you contacted and when tells the story even if the content does not.

**Anonymous disclosure.** This needs more than encryption. The identity behind the account and the network path both matter, and encryption addresses neither. Treat it as a threat modeling problem first.

**Two cautions.**

**On disappearing messages.** They limit exposure if a device is later seized or compromised. They do not stop the other party from taking a photograph, and they do not remove anything the provider recorded. Useful, and frequently overtrusted.

**On encrypted mail.** Providers that advertise end-to-end encrypted mail generally deliver it between their own users, or with a correspondent who has exchanged keys with you. Mail to an ordinary external address is usually not end-to-end encrypted, whatever the provider's branding suggests. Check what applies to the specific message you are about to send. Lesson 3.3 covers this properly.

---

## 7. Summary

- Everything is encrypted. The question is who holds a key that can decrypt it.
- The practical test is the recovery question. If they can restore your content, they hold a key.
- Content encryption and metadata retention are separate design decisions. Signal and WhatsApp encrypt content with the same protocol and give very different answers under legal process.
- Forward secrecy defeats the adversary who obtains your key later. It does not defeat the adversary who breaks the key agreement later.
- Harvest-now-decrypt-later is why Signal, Apple and others deployed hybrid post-quantum key agreement between 2023 and 2025.
- Verification has been partly automated through key transparency. Use it, and still verify manually where an active attacker is in your threat model.
- Session removed forward secrecy in 2021 and restored it in late 2025. Reputation is not a specification.

**Action item.** Take the three platforms you use most for work and apply the recovery question to each. Ten minutes, and at least one answer will probably surprise you.

The next lesson goes hands-on with Signal and Session: configuration, verification, and the settings that matter.

---

## Knowledge Check

**Question 1.** What single question separates transport encryption, vendor-held-key encryption and end-to-end encryption?

- A) Which algorithm is used
- B) Whether the connection uses a secure transport
- C) Who holds a key that can decrypt the content
- D) Where the servers are located

**Answer: C.** All three are encrypted, and all three can use the same algorithm. Only the key holder distinguishes them.

**Question 2.** A provider offers to restore your messages if you forget your password. What does that tell you?

- A) They have good support
- B) They use a strong cipher
- C) Nothing in particular
- D) They hold a key, so the content is not end-to-end encrypted

**Answer: D.** There is no way to be end-to-end encrypted and also able to recover somebody's content for them.

**Question 3.** Signal and WhatsApp encrypt content with the same underlying protocol, yet their responses to legal process differ enormously. Why?

- A) What a service collects and keeps around the content is a separate design decision from how the content is encrypted
- B) One uses a stronger cipher
- C) One operates under different mathematics
- D) The difference exists only in marketing

**Answer: A.** This is Myth 3 from Lesson 1.1, made concrete.

**Question 4.** What does forward secrecy protect against?

- A) An adversary breaking the cipher itself
- B) An adversary who obtains your long-term key later reading traffic that was recorded earlier
- C) Interception of messages in transit
- D) Malicious software on your device

**Answer: B.** Per-message keys are derived and discarded, so a seized long-term key does not reconstruct them.

**Question 5.** Harvest now, decrypt later describes:

- A) Recording encrypted traffic today and reading it once a key is obtained or the key agreement is broken
- B) Deleting messages after they are read
- C) Backing up encrypted data to remote storage
- D) A property of disappearing messages

**Answer: A.** The recording is cheap and storage is patient, which is what makes it worth doing.

**Question 6.** Why does forward secrecy not fully answer that problem?

- A) It does answer it completely
- B) Forward secrecy applies only to mobile devices
- C) If the method used to agree the keys is itself broken later, discarding per-message keys does not help, which is why a quantum-resistant exchange is now mixed in alongside the classical one
- D) Forward secrecy has been deprecated

**Answer: C.** Forward secrecy assumes the adversary gets your key. It does not cover the adversary breaking the exchange.

**Question 7.** What is Session's forward secrecy history?

- A) It has always had forward secrecy
- B) It removed forward secrecy in 2021 when it moved off the Signal Protocol, and restored it in late 2025
- C) It has never had forward secrecy and still does not
- D) Forward secrecy does not apply to Session

**Answer: B.** For roughly four years it was recommended on the strength of a description that had stopped being accurate.

**Question 8.** Manually comparing safety numbers now has an automated counterpart. What is it?

- A) Trusting the application silently, with no mechanism behind it
- B) Sending the fingerprint to your contact by mail
- C) Comparing the numbers inside the application itself
- D) Key transparency, where key changes enter a publicly auditable log that clients and independent auditors check

**Answer: D.** Options B and C both run over the channel an attacker would already control.

**Question 9.** Which claim tells you least about whether a platform protects you?

- A) "Only the sender and recipient hold decryption keys"
- B) "Our protocol is published and has been independently examined"
- C) "Military-grade encryption"
- D) "Here is our transparency report showing what we hand over"

**Answer: C.** It describes an algorithm, and every architecture in Figure 1 can use the same one.

**Question 10.** Before recommending a messenger, what should you check beyond its reputation?

- A) Which version they will actually run, and what properties that version has, because protocols gain and lose properties over time
- B) Nothing, reputation is sufficient
- C) The company's financial position
- D) The size of the user base

**Answer: A.** Session is the worked example, and it will not be the last one.

---

## Exercise: Evaluate What You Actually Use

**Time: 90 minutes, plus 20 minutes to write up.**

### Objective

Determine what your real communication tools actually do, rather than what they are believed to do.

---

### Part 1: The Recovery Question (20 minutes)

List every platform you use for work communication. For each, find the answer to one question: **if you lose your password, can the provider restore your message history?**

Find the answer in their own documentation, usually a help article about account recovery rather than the security page.

| Platform | Can they restore your messages? | Where you found it | Which column of Figure 1 |
|---|---|---|---|

**Deliverable.** The table. Note any platform where the security page and the help article imply different answers, because that gap is itself a finding.

---

### Part 2: What They Keep (25 minutes)

For the three platforms you use most for sensitive work, find out what they retain about your communications rather than in them.

Look for a transparency report, a law enforcement guide, or a published response to legal process. Marketing pages will not have this.

| Platform | Metadata retained | Evidence source | Comparable to Signal or to WhatsApp? |
|---|---|---|---|

**If you cannot find any published information for a platform, record that.** An absence of a transparency report is a finding, not a blank.

**Deliverable.** The table, plus one sentence on which platform surprised you.

---

### Part 3: Current Specification, Not Reputation (20 minutes)

Pick two platforms you would have recommended before reading this lesson. For each, establish from primary sources:

1. Which protocol the current version uses.
2. Whether the current version has forward secrecy.
3. Whether the current version uses post-quantum key agreement, and whether it covers only session setup or the ongoing conversation.
4. When each of those last changed.

| Platform | Protocol | Forward secrecy | Post-quantum | Last changed |
|---|---|---|---|---|

**Use the project's own documentation and engineering posts.** Third-party comparison articles are where Session's outdated description survived for four years.

**Deliverable.** The table, with a source for every cell.

---

### Part 4: Verify a Contact, Both Ways (15 minutes)

Pick one contact who matters.

1. Find the manual verification mechanism in your messenger, and use it.
2. Find out whether your platform offers key transparency or automatic key verification, and whether it is active.
3. Record what each one actually told you, and what neither of them told you.

Point 3 is the one worth thinking about. Neither mechanism confirms that the person is who they say they are, and being clear about that boundary is the point.

**Deliverable.** What you did, what it confirmed, and the one thing it could not.

---

### Part 5: Match Tool to Task (10 minutes)

For each of these, name the platform you will actually use and the one thing about it you are accepting:

| Task | Platform | What I am accepting |
|---|---|---|
| Routine correspondence with a client | | |
| Sending findings to a client | | |
| Coordinating with another researcher | | |
| Reporting something where I need not to be identified | | |

**The last row deserves care.** If your answer is only a messenger name, re-read Section 6, because encryption does not address the identity behind the account or the network path.

**Deliverable.** The table.

---

### Submission

1. **Recovery question results**, Part 1, with sources.
2. **Retention findings**, Part 2, including any absences.
3. **Current specifications**, Part 3, sourced per cell.
4. **Verification**, Part 4, including the limitation.
5. **Task mapping**, Part 5.
6. **Reflection**, half a page: which platform did you believe was end-to-end encrypted and is not, or believed had a property it has lost?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Recovery question | 20 | Answers found in documentation rather than assumed |
| Retention findings | 25 | Primary sources used, absences recorded as findings |
| Current specification | 25 | Sourced per claim, dates identified, primary sources preferred |
| Verification | 15 | Both mechanisms understood, and the limitation stated |
| Task mapping | 10 | Honest about what is being accepted |
| Reflection | 5 | Names a specific belief that changed |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources

- Signal, Quantum Resistance and the Signal Protocol: https://signal.org/blog/pqxdh/
- Signal, Signal Protocol and Post-Quantum Ratchets: https://signal.org/blog/spqr/
- Apple Security Research, iMessage with PQ3: https://security.apple.com/blog/imessage-pq3/
- Meta Engineering, Deploying key transparency at WhatsApp: https://engineering.fb.com/2023/04/13/security/whatsapp-key-transparency/

Read the engineering write-ups rather than the news coverage. These are the documents where a protocol's actual properties are stated, and they are also where you will find the properties a project quietly gave up.

### Discussion Questions

1. Which of your platforms would fail the recovery question, and would you have guessed correctly before checking?
2. Signal and WhatsApp protect content identically and answer legal process very differently. Which of those two facts drives your platform choice, and should it?
3. Post-quantum migration is defending against a capability that does not yet exist. Is that reasonable for your own threat model, and how long does your sensitive material stay sensitive?

---

## References

1. [Signal, grand jury subpoena response, District of Columbia](https://signal.org/bigbrother/district-of-columbia/)
2. [Just Security, We Now Know What Information the FBI Can Obtain from Encrypted Messaging Apps](https://www.justsecurity.org/79549/we-now-know-what-information-the-fbi-can-obtain-from-encrypted-messaging-apps/)
3. [Signal, Quantum Resistance and the Signal Protocol, PQXDH, September 2023](https://signal.org/blog/pqxdh/)
4. [Signal, Signal Protocol and Post-Quantum Ratchets, the triple ratchet](https://signal.org/blog/spqr/)
5. [Apple Security Research, iMessage with PQ3, February 2024](https://security.apple.com/blog/imessage-pq3/)
6. [Apple Security Research, iMessage Contact Key Verification](https://security.apple.com/blog/imessage-contact-key-verification/)
7. [Signal, Introducing Automatic Key Verification](https://signal.org/blog/automatic-key-verification/)
8. [Meta Engineering, Deploying key transparency at WhatsApp, April 2023](https://engineering.fb.com/2023/04/13/security/whatsapp-key-transparency/)
9. [Session, Session Protocol technical information](https://getsession.org/session-protocol-technical-information)
10. [Privacy Guides, Session messenger adds forward secrecy and post-quantum encryption, December 2025](https://www.privacyguides.org/news/2025/12/03/session-messenger-adds-pfs-pqe-and-other-improvements/)
11. [Just Security, Michael Hayden: "We Kill People Based on Metadata"](https://www.justsecurity.org/10311/michael-hayden-kill-people-based-metadata/)
