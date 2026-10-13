---
title: "PGP and Encrypted Mail: What It Covers, What Broke, and When It Is Still Required"
course: Digital Privacy for Ethical Hackers
module: 3
lesson: 3.3
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - encrypted-communications
  - pgp
  - gpg
  - email
  - efail
  - opsec
---

# PGP and Encrypted Mail: What It Covers, What Broke, and When It Is Still Required

## About This Lesson

This is the lesson where published guidance has aged worst, and where the gap between what guides describe and what actually works is widest.

Three things happened after most PGP tutorials were written. A major attack broke encrypted mail through the mail client rather than the cryptography. The key distribution model that every tutorial teaches stopped functioning. And a substantial part of the security community concluded that PGP should not be the default recommendation for most of what it is used for.

None of that means you can skip it. Disclosure programs still ask for PGP keys, and clients still send encrypted mail. It means you should learn it accurately, and know when something else is the better answer.

### Learning Objectives

After you read this lesson, you can do these tasks:

- State exactly which parts of an email PGP protects, and which it never touches.
- Explain EFAIL, and why your mail client settings still reflect it.
- Describe why key signing no longer builds a web of trust, and what replaced it.
- Generate and manage a key pair without following advice that has expired.
- Decide when PGP is genuinely required and when a modern tool fits better.

### Prerequisites

- Lessons 3.1 and 3.2.
- Comfort with a command line is useful and not essential.

---

## 1. What PGP Covers

Start with the boundary, because everything else follows from it.

![[module_3_lesson_3_what_pgp_covers.svg]]

*Figure 1. The encryption begins below the headers.*

An email has to be delivered, and the delivery system needs the routing information in the clear to do that. So the sender, the recipient, the timestamp, the message identifier, the thread references, the client software, and the record of every server that handled the message all stay readable. Only the body and its attachments are encrypted.

**The subject line is in the header block.** It is never encrypted by PGP, and years of habit push people to write a descriptive one. A subject reading "Critical injection flaw in the payment path" tells an observer what was found, where, and by whom, with no need to touch the encryption underneath.

Write nothing there, or write something that says nothing. This is the single most practical instruction in the lesson and it costs nothing.

### No Forward Secrecy

Lesson 3.1 defined this. PGP does not have it.

Messages are encrypted to a long-term key. That key is used for years. If somebody obtains it, everything ever encrypted to it becomes readable, including messages sent long before the compromise.

That is exactly the harvest-now-decrypt-later shape from Lesson 3.1, and PGP has no answer to it. Rotating keys periodically limits the window. It does not close it, because the old messages were encrypted to the old key and the old key still decrypts them.

**So encrypted mail is structurally weaker than encrypted messaging**, and the reason is architectural rather than a matter of key length.

---

## 2. EFAIL, and Why Your Client Settings Look Like That

The earlier version of this lesson does not mention EFAIL. It is the most significant thing that has happened to encrypted mail, and it explains configuration advice you have probably followed without knowing why.

![[module_3_lesson_3_efail.svg]]

*Figure 2. The cryptography held. The mail client did the exfiltration.*

Researchers published EFAIL in 2018 against both OpenPGP and S/MIME. (Reference 1, Reference 2)

An attacker who already holds a copy of an encrypted message wraps that ciphertext inside a new message, positioned so that whatever it decrypts to lands inside a web address. They mail it to you. Your client does its job: it decrypts the part it recognises and assembles the message for display. Rendering it fetches the remote resource, and that request carries your decrypted message to the attacker.

You never clicked anything.

**Note precisely what failed.** Not the encryption. What failed was a message format that let an attacker graft chosen content onto ciphertext, and a client that rendered the result and reached out to the network. Encryption is a component, and components sit inside systems that can undo them.

### The Three Responses

**Clients changed.** Mail clients stopped loading remote content by default and became far more careful about assembling messages from mixed parts. This is already done for you.

**Standards changed.** Integrity protection on encrypted data moved from optional to expected, so a modified message is detected rather than decrypted. This requires current software.

**Your setting.** Keep remote content loading off. Prefer plain text over formatted mail for anything encrypted. Both remain standing advice, and both cost you very little.

**A diagnostic for any guide you read.** If it walks you through configuring encrypted mail and never mentions remote content, it was written before 2018 or copied from something that was. That tells you what else to distrust in it.

---

## 3. The Web of Trust Does Not Propagate

Here is the correction that most directly contradicts the earlier version of this lesson, which instructs you to verify a key, sign it, and upload it to build the web of trust.

![[module_3_lesson_3_key_distribution.svg]]

*Figure 3. Steps 3 and 4 of the classic model no longer function.*

**The old keyserver network was attacked in 2019.** Nothing in the format limits how many signatures a certificate may carry. Attackers attached tens of thousands of signatures to the certificates of two well-known contributors. Importing a poisoned certificate could render a local installation unusable. The network was append-only by design, so nothing could be removed, and the affected certificates stayed poisoned. (Reference 3, Reference 4)

**The replacement keyserver strips third-party signatures deliberately.** The keyserver now recommended everywhere does not distribute them at all, on the stated grounds that they let anybody attach unlimited data to anybody's key. (Reference 5)

Third-party signatures are precisely what the web of trust is made of. So signing a colleague's key and uploading it does not deliver your endorsement to anybody, because the mechanism that would have carried it has been switched off.

### What Is Actually Used Now

- **Directly from the person**, over a channel you already trust. This is what you were verifying against anyway.
- **From their own domain**, through the directory mechanism a mail client can query automatically, which ties the key to control of the address.
- **From a keyserver that verifies the address** by mailing a confirmation before publishing, so a listing means something.
- **From a published fingerprint** on a site you have independent reason to trust, checked against what you downloaded.

**Verifying a fingerprint is still correct and still necessary.** What stopped working is the idea that your verification helps anybody else. Verify for yourself, and do not expect a trust network to carry it onward.

---

## 4. Generating a Key, Without the Expired Advice

The mechanics are straightforward. Several of the standard recommendations around them are not current.

```bash
gpg --full-generate-key
```

**On algorithm.** Older guides say 4096-bit RSA and treat it as the safe answer. Modern elliptic curve keys give equivalent or better security with far smaller keys and faster operations, and current OpenPGP requires support for them. (Reference 6) Choose elliptic curve unless you have a specific compatibility reason not to, and be aware that a very old correspondent's software may be that reason.

**On expiration.** Set one, one or two years out. You can always extend it. An unexpiring key is a key that stays valid after you have lost control of it.

**On the passphrase.** Long, unique, and in your password manager. It is the last defence if somebody obtains the key file.

**Generate a revocation certificate immediately** and store it separately from the key. If you lose access to the key you cannot make one later, and without it you cannot tell anybody the key is dead.

```bash
gpg --gen-revoke you@example.com > revocation.asc
```

**Back up the private key deliberately.** Export it, store it encrypted, and test that the restore works. A key you cannot restore is a key you will lose, and losing it means losing every message ever encrypted to it.

### A Note on the Standard Itself

Worth knowing because it affects interoperability: OpenPGP was updated in 2024, and the project behind the most widely used implementation declined to follow that update, continuing on its own line instead. (Reference 6)

The practical effect for you is small today and worth watching. It means "OpenPGP compatible" is a less precise statement than it used to be, and that a key or message produced by one implementation may not be handled identically by another. Test with your actual correspondent rather than assuming.

---

## 5. Provider Encryption, Described Accurately

The earlier version says Proton Mail is "end-to-end encrypted by default." That needs qualifying, and the qualification matters because this lesson is about vulnerability disclosure.

**Proton Mail's end-to-end encryption applies between its own users**, and to a correspondent whose PGP key you have configured. Mail to an ordinary external address is **not** end-to-end encrypted by that route. What Proton offers instead is a password-protected message: the recipient gets a link to a Proton-hosted mailbox and a password you agreed separately. (Reference 9, Reference 10)

That is a genuinely useful mechanism and it is not the same thing as sending an encrypted mail to somebody's normal inbox.

**Tuta**, formerly Tutanota, renamed in 2023. (Reference 11) It encrypts subject lines, which is the one thing PGP structurally cannot do and which Figure 1 identified as the most commonly leaked item. The trade is that this works within its own system rather than through PGP, so it does not interoperate with a correspondent using GnuPG.

**The general point.** Read a provider's own documentation on what applies to which recipient. "End-to-end encrypted" on a marketing page frequently means "between our users", and the message you are about to send may not be going to one.

---

## 6. Should You Use PGP At All

A substantial part of the security community says no, for most purposes, and a course that presents PGP as unambiguously necessary is not being straight with you.

The critique is that PGP carries decades of accumulated complexity, that it lacks forward secrecy, that its defaults have historically been poor, that it is difficult to use correctly, and that purpose-built modern tools do each of its jobs better. (Reference 7, Reference 8)

Applied to the jobs you actually have:

| What you need to do | Reach for | Rather than |
|---|---|---|
| Have a private conversation | A modern messenger, as in Lesson 3.2 | Encrypted mail |
| Encrypt a file for somebody | A purpose-built file encryption tool | PGP file encryption |
| Sign a release or an artefact | A modern signing tool, or your ecosystem's own mechanism | PGP signatures |
| Send a formal disclosure to a program that asks for PGP | PGP | Arguing about it |

**That last row is the honest answer to why this lesson exists.** The requirement is frequently somebody else's. A disclosure program that publishes a PGP key and asks you to use it has made a decision, and meeting their requirement is part of doing the work. Your view on the tooling is not the deciding input.

**So learn it, keep it working, and do not reach for it by default.** When you have a free choice, the tools in Lesson 3.2 are better for conversation and purpose-built tools are better for files. When you do not have a free choice, you need this to work on the day somebody asks.

---

## 7. Practice

**Subject lines.** Covered above and worth repeating because it is the most frequent failure. Generic or empty.

**Sign and encrypt for anything that matters.** Encryption gives confidentiality. The signature gives the recipient a reason to believe it came from you, which matters a great deal in disclosure, where the recipient has no other basis for trusting an unexpected message about their own security.

**Understand what a signature commits you to.** It is evidence that you wrote something, and it is durable. That is usually what you want in a disclosure and occasionally is not.

**Keep encrypted mail short.** Use it to establish contact and to deliver the formal record. Move the detailed back and forth to a channel with forward secrecy, and say so in the first message. Lesson 3.2 gave you the tool for that half.

**Never assume the recipient can read it.** Send a test, or ask. An encrypted message to somebody whose setup is broken produces a support conversation over plaintext, which is worse than having planned for it.

---

## 8. Summary

- PGP encrypts the body and attachments. Headers, including the subject line, stay readable. The subject line is the item people leak most.
- PGP has no forward secrecy. Compromise of a long-term key exposes everything ever encrypted to it.
- EFAIL broke encrypted mail in 2018 through the client rather than the cryptography, and it is why remote content loading is off by default and why plain text is preferred.
- The 2019 keyserver poisoning, and the recommended keyserver's decision not to distribute third-party signatures, mean key signing no longer propagates trust to anybody.
- Verify fingerprints for yourself. Obtain keys directly, from the correspondent's domain, or from an address-verified keyserver.
- Provider "end-to-end encryption" frequently means between that provider's own users. Check what applies to the message you are about to send.
- PGP is not the best tool for most of its jobs. It remains the required tool when somebody else requires it.

**Action item.** Look at the last five emails you sent that contained anything sensitive, and read only the subject lines. That is what an observer got for free.

The final lesson of Module 3 goes deeper into the thing every lesson in this module has kept running into: metadata, traffic analysis, and timing.

---

## Knowledge Check

**Question 1.** Which parts of an email does PGP encrypt?

- A) Everything, including the headers
- B) The body and attachments only. Headers, including the subject line, stay readable.
- C) Attachments only
- D) The subject line only

**Answer: B.** The delivery system needs the routing information in the clear in order to deliver the message.

**Question 2.** Why is the subject line the most commonly leaked item on an encrypted email?

- A) It sits in the header block so it is never encrypted, and habit pushes people to write a descriptive one
- B) A defect in the encryption software
- C) Mail clients transmit it separately
- D) It is encrypted, so this is not a concern

**Answer: A.** Fixing it costs nothing, which is why it belongs at the top of any practical checklist.

**Question 3.** EFAIL worked by:

- A) Breaking the encryption algorithm
- B) Guessing private keys
- C) Grafting attacker-chosen content onto ciphertext, so that the victim's client on decrypting and rendering fetched a remote resource carrying the plaintext
- D) Compromising keyservers

**Answer: C.** The cryptography held. The message format and the client did the rest.

**Question 4.** What standing user-level advice came out of EFAIL?

- A) Stop using email
- B) Use longer keys
- C) Change your passphrase more often
- D) Keep remote content loading off, and prefer plain text over formatted mail for anything encrypted

**Answer: D.** A guide that configures encrypted mail without mentioning remote content predates 2018 or was copied from something that did.

**Question 5.** What happened to the old keyserver network in 2019?

- A) Certificates were poisoned by attaching enormous numbers of signatures, and because the network was append-only nothing could be removed
- B) It was shut down by a court order
- C) It was replaced by a commercial service
- D) Nothing, it still works as designed

**Answer: A.** Importing a poisoned certificate could render a local installation unusable.

**Question 6.** Why does signing somebody's key and uploading it no longer build a web of trust?

- A) Signing was removed from the software
- B) Signatures now expire immediately
- C) It still works exactly as older guides describe
- D) The recommended keyserver does not distribute third-party signatures at all, so your endorsement never reaches anybody

**Answer: D.** Third-party signatures are what a web of trust is made of, and the mechanism that carried them has been switched off.

**Question 7.** Does PGP provide forward secrecy?

- A) Yes, equivalent to a modern messenger
- B) Yes, given a long enough key
- C) No. Messages are encrypted to a long-term key, so compromising that key later exposes everything encrypted to it
- D) Only for signed messages

**Answer: C.** Rotating keys narrows the window. It does not close it, because old messages were encrypted to the old key.

**Question 8.** Proton Mail's end-to-end encryption applies:

- A) To every message you send, to any recipient
- B) Between its own users, or to a correspondent whose PGP key you have configured. Mail to an ordinary external address is not end-to-end encrypted unless you use the password-protected option.
- C) Only to attachments
- D) Only on paid plans

**Answer: B.** "End-to-end encrypted" on a marketing page frequently means "between our users."

**Question 9.** You need to encrypt one file for a colleague. What is worth considering before reaching for PGP?

- A) Nothing, PGP is the only option
- B) The attachment size limit
- C) Whether their computer is fast enough
- D) Whether a purpose-built modern file encryption tool would do the job with far less that can go wrong

**Answer: D.** PGP is not the best tool for most of its jobs, and file encryption is one of the clearest cases.

**Question 10.** A disclosure program publishes a PGP key and asks for an encrypted report. What is the right response?

- A) Use PGP, because the requirement is theirs and meeting it is part of the engagement
- B) Refuse and send it in plaintext
- C) Insist they adopt a messenger instead
- D) Publish it instead

**Answer: A.** Your opinion of the tooling is not the deciding input. This is why the lesson exists.

---

## Exercise: Make It Work, Then Decide When To Use It

**Time: 100 minutes, plus 20 minutes to write up.**

### Safety Notes

- Your private key file and your revocation certificate are both sensitive. **Do not paste either into any document you will submit, and do not upload either anywhere.**
- Practise with a partner using test content. Do not use real client material.

---

### Part 1: Ask Whether You Need It (10 minutes)

Before installing anything, list the situations in the next six months where you might plausibly need PGP.

| Situation | Is PGP required, or just familiar? | Who requires it | What else would work |
|---|---|---|---|

**If every row says "just familiar", note that.** It is a legitimate finding and it should change how much effort you spend on the rest.

**Deliverable.** The table, plus one line on whether the requirement is yours or somebody else's.

---

### Part 2: Generate and Secure a Key (25 minutes)

```bash
gpg --full-generate-key
```

Record your choices and the reason for each:

| Choice | What you picked | Why |
|---|---|---|
| Algorithm | | |
| Expiration | | |
| Identity | | |

Then, in order:

1. Record your fingerprint. This is public and safe to write down.
2. Export the public key.
3. Export the private key, store it encrypted, and **test the restore into a throwaway keyring** to confirm the backup works.
4. Generate the revocation certificate and store it separately from the key.

**Deliverable.** Your fingerprint, confirmation that the restore test succeeded, and confirmation that both the key backup and the revocation certificate are stored. **Locations described, contents not included.**

---

### Part 3: Configure Against EFAIL (15 minutes)

In whichever mail client you will use:

| Setting | Current state | Changed to | Why |
|---|---|---|---|
| Remote content loading | | | |
| Message display format | | | |
| Client and version | | | |

Then find out, from the client's own release notes or documentation, whether your version postdates the EFAIL mitigations. Record what you found and where.

**If you cannot establish that, record that too.** An unresolved answer is a finding.

**Deliverable.** The table and your version finding.

---

### Part 4: Exchange and Verify With a Partner (25 minutes)

1. Exchange public keys with a partner, using one of the four methods from Section 3. Record which.
2. Verify their fingerprint over a channel that is not the one you exchanged keys on.
3. Send them a signed and encrypted test message with a **deliberately uninformative subject line**.
4. Have them reply the same way. Confirm you can decrypt, and confirm the signature verified.

Then the part that matters:

5. **Look at what an observer saw.** From the message you sent, list everything that was readable in transit. Compare against Figure 1.

**Deliverable.** The exchange method, the verification method, confirmation of both directions, and your list from step 5.

---

### Part 5: Test the Web of Trust Claim (15 minutes)

This part is short and the result is the point.

1. Sign your partner's key locally.
2. Find out whether your signature would reach anybody else. Check the documentation of the keyserver you would upload to, and establish whether it distributes third-party signatures.
3. Record what you found.

**Expected result:** your signature stays local and helps you alone.

Then answer: **given that, what is signing a key actually for now?** There is a real answer, and working it out is the exercise.

**Deliverable.** What the keyserver documentation says, and your answer.

---

### Part 6: Choose Per Job (10 minutes)

| Job | What I will use | Why not PGP, or why PGP |
|---|---|---|
| Private conversation with a colleague | | |
| Sending one encrypted file | | |
| Formal disclosure to a program requesting PGP | | |
| Formal disclosure to a program with a web form | | |

**The last row is a trap worth falling into once.** If a program provides a submission form, using it is usually correct, and reaching for PGP because you just configured it is the mistake this lesson is trying to prevent.

**Deliverable.** The table.

---

### Submission

1. **Requirement assessment**, Part 1.
2. **Key generation**, Part 2, with restore confirmed. **No key material.**
3. **Client configuration**, Part 3, with your version finding.
4. **Exchange and verification**, Part 4, with your observer list.
5. **Web of trust test**, Part 5, with your answer.
6. **Job mapping**, Part 6.
7. **Reflection**, half a page: which piece of PGP advice you had previously absorbed turned out to have expired?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Requirement assessment | 15 | Honest about whether the requirement is external |
| Key generation and backup | 20 | Choices justified, restore actually tested, revocation certificate made, no key material submitted |
| Client configuration | 15 | Settings changed and the version question pursued |
| Exchange and verification | 25 | Verified over a separate channel, and the observer list correct |
| Web of trust test | 15 | Documentation checked rather than assumed, and the real purpose of local signing identified |
| Job mapping | 10 | Reaches for PGP only where it is required |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources

- Poddebniak and others, Efail, USENIX Security 2018: https://www.usenix.org/conference/usenixsecurity18/presentation/poddebniak
- Hansen, SKS Keyserver Network Under Attack: https://gist.github.com/rjhansen/67ab921ffb4084c865b3618d6955275f
- keys.openpgp.org, on third-party signatures: https://keys.openpgp.org/about/faq/
- RFC 9580, the current OpenPGP specification: https://www.rfc-editor.org/rfc/rfc9580.html

### The Other Side of the Argument

- Latacora, The PGP Problem: https://www.latacora.com/blog/2019/07/16/the-pgp-problem/
- Soatok, What To Use Instead of PGP: https://soatok.blog/2024/11/15/what-to-use-instead-of-pgp/

Read at least one of these. Whether or not you accept the conclusion, you should be able to state the argument, because you will meet people who hold it and some of them will be your correspondents.

### Discussion Questions

1. Of the PGP advice you already knew before this lesson, how much came from material written before 2018?
2. Signing a key no longer helps anybody else. What is it still for?
3. A client insists on encrypted mail for something a messenger would handle better. How do you make that case, and when do you stop making it?

---

## References

1. [Poddebniak, Dresen, Müller and others, Efail: Breaking S/MIME and OpenPGP Email Encryption using Exfiltration Channels, USENIX Security 2018](https://www.usenix.org/conference/usenixsecurity18/presentation/poddebniak)
2. [EFAIL, Black Hat USA 2018 presentation](https://i.blackhat.com/us-18/Thu-August-9/us-18-Mueller-Dresen-EFAIL-Breaking-SMIME-And-OpenPGP-Email-Encryption-Using-Exfiltration-Channels.pdf)
3. [Robert J. Hansen, SKS Keyserver Network Under Attack, June 2019](https://gist.github.com/rjhansen/67ab921ffb4084c865b3618d6955275f)
4. [Red Hat, CVE-2019-13050, certificate spamming against SKS key servers and GnuPG](https://access.redhat.com/articles/4264021)
5. [keys.openpgp.org, frequently asked questions, on third-party signatures](https://keys.openpgp.org/about/faq/)
6. [RFC 9580, OpenPGP, July 2024](https://www.rfc-editor.org/rfc/rfc9580.html)
7. [Latacora, The PGP Problem, July 2019](https://www.latacora.com/blog/2019/07/16/the-pgp-problem/)
8. [Soatok, What To Use Instead of PGP, November 2024](https://soatok.blog/2024/11/15/what-to-use-instead-of-pgp/)
9. [Proton, email encryption documentation](https://proton.me/support/mail/email-encryption)
10. [Proton, password-protected emails](https://proton.me/support/password-protected-emails)
11. [Tuta, Tutanota is now Tuta](https://tuta.com/blog/tutanota-is-now-tuta)
