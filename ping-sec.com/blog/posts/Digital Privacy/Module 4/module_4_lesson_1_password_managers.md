---
title: "Module 4 Lesson 1: Password Managers: Where the Vault Lives and What Breaks It"
course: Digital Privacy for Ethical Hackers
module: 4
lesson: 4.1
format: article
reading_time: 28 minutes
tags:
  - digital-privacy-course
  - privacy
  - password-security
  - password-managers
  - keepassxc
  - passkeys
  - opsec
---

# Password Managers: Where the Vault Lives and What Breaks It

## About This Lesson

Module 4 opens on authentication, and it carries one idea across from the three modules before it: ask precisely what a control protects against, and be honest about what it leaves.

For a password manager the answer is narrow and important. It solves password reuse, which is a large real problem, and it does so completely. It does not make your credentials safe in every other sense, and the way people discover that is by reading about a breach.

This lesson is organised around three things: where the vault physically lives, what a real vault breach cost people, and what changes now that the same vault holds passkeys.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Say who ends up holding a copy of your vault under each architecture, and why that matters more than the architecture label.
- Explain what turned a 2022 vault breach into years of losses, and check your own settings against it.
- Choose key derivation settings deliberately rather than accepting a default.
- State accurately what auto-type does and does not protect against.
- Describe what a passkey changes, and what it does not.

### Prerequisites

- Module 1, particularly the threat modeling in Lesson 1.4.
- The compartmentalization model from Lesson 1.2, which this lesson applies to credentials.

---

## 1. Why This Matters More For You

The standard framing is that password reuse causes account compromise. That is broadly right and worth being precise about.

The 2026 breach data, which Lesson 1.4 used for its roadmap, found that exploitation of a software vulnerability had become the leading route into a breach, passing credential theft for the first time. Credential abuse still appeared somewhere in roughly 39 percent of full breach chains. (Reference 6)

**Read that carefully, because it cuts both ways.** Reuse is no longer the single leading initial vector, so "password reuse is the number one cause" overstates it. But credential abuse remains present in a large share of intrusions overall, which means solving it removes a component from a great many attacks even when it was not the way in.

**Why a security professional is a worse target than most.** Your credentials reach client systems under contract, unpublished research, disclosure platforms, and infrastructure you built for engagements. A compromise of your accounts is frequently a compromise of somebody else's data, which turns a personal incident into a contractual one. That is the Lesson 1.2 argument about layers, applied to credentials.

---

## 2. Where the Vault Lives

The three architectures get compared on features. The more useful comparison is who ends up holding a copy.

![[module_4_lesson_1_where_the_vault_lives.svg]]

*Figure 1. The architectures differ less than the labels suggest.*

**Local first.** An encrypted file you own, on the devices you put it on, plus whatever you synchronise it through. No vendor to trust, and no vendor to do the work you would rather not do: synchronisation, conflict handling, backups and mobile access all become yours.

**A vendor service.** They hold your vault, encrypted, along with everybody else's. In exchange you get synchronisation that works, mobile applications, recovery and sharing. A breach of the vendor exposes every customer vault at once.

**Self-hosted.** The software on infrastructure you run. You are the vendor now, with the vendor's obligations. **The honest caution is that a neglected instance is worse than a maintained vendor**, so choose this only if you already run servers well and will keep doing so.

### What They Share, Which Is the Important Part

Once anybody holds a copy of your encrypted vault, the only things between them and its contents are your master password and the work factor protecting it. They attack it **offline**: no rate limiting, nobody watching, and no deadline.

That is equally true of a vendor breach and of a file you put on a consumer sync service.

**Which makes a very common piece of advice wrong.** The outline version of this lesson says to store the encrypted vault file in cloud storage because "the file is already encrypted." That states the premise of the offline attack as though it were the answer to it. The file being encrypted is exactly the situation the attacker is in, and the question is how long the encryption holds under sustained offline attack.

Cloud sync of a vault file is a reasonable choice. It is not a free one, and the thing that makes it survivable is the next section.

---

## 3. The Breach That Should Shape Your Settings

A footnote in most course material, and the single most instructive incident available on this topic.

![[module_4_lesson_1_lastpass.svg]]

*Figure 2. Encrypted vaults were taken. Three earlier decisions determined what that cost.*

In 2022, attackers exfiltrated backup copies of customer vaults. The vaults were encrypted, exactly as the architecture promised. (Reference 2)

**Decision one: old accounts were never migrated.** The work factor protecting a vault had been raised for new accounts over the years. Existing accounts were not moved to it, despite a commitment to do so. Many remained at five thousand rounds of key derivation, and some were found at five hundred. At those settings a moderately strong master password is reachable with consumer hardware given time, and time is what an offline attacker has.

**Decision two: not all of it was encrypted.** Usernames and passwords were encrypted. Some fields were not, including the addresses of the sites each entry belonged to. So before cracking anything, an attacker could sort the stolen vaults by which ones held accounts worth the effort. That is the metadata argument from Module 3, deciding who gets targeted.

**Decision three: a vault does not expire.** A stolen copy stays valid for as long as its contents do, and there is no equivalent of forward secrecy for a password vault. Rotating a password does not recall the copy somebody already has.

### What Followed

Researchers linked a series of cryptocurrency thefts to the breach, with a common factor among victims: recovery phrases stored in the vault. Federal investigators later attributed a much larger theft to the same stolen data, and a class action was settled in 2025. (Reference 1)

**The gap between the breach and the losses was measured in years**, because offline cracking has no deadline.

### Three Things to Do About It

**Check the work factor on your own vault**, rather than trusting whatever the current default is. A default that improved is not a default that was applied to you. This applies to a local vault as much as a hosted one.

**Assume anything not explicitly encrypted is readable.** Ask your product what it encrypts, not whether it encrypts.

**Keep anything unrotatable out of the vault entirely.** Recovery phrases, answers to security questions, and anything else you cannot change after the fact. A stolen copy of those never expires, and the vault is the wrong place for them.

**And note what the lesson is not.** It is not "avoid one vendor". Every architecture in Figure 1 has the offline attack property, and the three decisions above are ones any product can get wrong.

---

## 4. Settings That Actually Matter

Most configuration guidance for password managers is a long list where two items carry nearly all the weight.

**The master password.** It protects everything, it is attacked offline, and no other setting compensates for a weak one. Long, unique, and never reused anywhere. A passphrase you can actually recall beats a shorter string you will write down somewhere worse.

**The key derivation function and its work factor.** This is what turns a stolen vault from a day's work into an infeasible one, and it is the setting the 2022 breach turned on.

Prefer **Argon2id**. The difference from Argon2d is not academic: Argon2d uses data-dependent memory access and is exposed to timing side channels, while Argon2id behaves differently across its passes to defend against both side channels and brute force. It is the general recommendation and it is what the KeePassXC documentation points to. (Reference 4, Reference 5)

Then turn the work factor up until unlocking is slightly annoying on your slowest device. That delay is the whole defense, and you pay it once per unlock while an attacker pays it per guess.

### Auto-Type, Described Correctly

The outline version says auto-type "prevents keyloggers from capturing passwords." **That is not true, and it is worth correcting properly because it could lead somebody to trust a compromised machine.**

Auto-type works by sending simulated keypresses. A target application cannot distinguish those from real ones, which is the point, and neither can a keylogger. KeePass's own documentation says exactly this: keyloggers can eavesdrop on the simulated keys. (Reference 3)

What auto-type actually gives you:

- **It avoids the clipboard**, which other applications on the machine can read, and which has its own history of leaking.
- **Window title matching gives some phishing resistance**, because it will not type your credentials into a window that does not match the entry.

Both are real benefits. Neither is protection against a compromised endpoint, and **nothing in a password manager is**. If the machine is compromised, the credentials you use on it are compromised, and the correct response is a different machine rather than a different fill method.

---

## 5. Organising for Compartmentalization

Lesson 1.2 gave four layers. Credentials belong in the same structure.

Separate personal, professional, research and client credentials, and organise the vault so the boundary is visible when you are working. The failure mode is Lesson 2.5's silent one: reaching for the wrong credential is not signalled by anything, and the mistake is made in a moment of convenience.

**One structural decision worth making deliberately.** Client credentials, held under contract, are a different category from your own. Consider whether they belong in a separate vault entirely rather than a folder in the same one, because the two answer different questions about who may hold a copy and what happens at the end of an engagement.

**And what does not go in.** The credential you need before the manager is available: a disk encryption passphrase, or the login for the machine the vault sits on. That is a bootstrapping problem rather than a security one, and the answer is memory plus a sealed physical copy somewhere safe.

---

## 6. Passkeys, and What They Change

This is the largest development in authentication since password managers became normal, and the outline version predates it entirely.

![[module_4_lesson_1_passkeys.svg]]

*Figure 3. Your vault now holds two different kinds of thing.*

A password is a shared secret. You send it to the site every time you sign in, which means it can be phished by a convincing lookalike, stolen from the site's own store, and reused across sites.

A passkey is a key pair bound to one site. Signing in produces a signature over a fresh challenge; the private key never leaves your device, and nothing replayable is transmitted. The credential is scoped to the site's identity, enforced by the browser and the authenticator together, so a lookalike domain cannot request it and you cannot hand it over by mistake. The site stores only a public key, which is worth nothing to whoever steals it. (Reference 7)

The specification reached full W3C Recommendation status in August 2026, and the major password managers now store passkeys alongside passwords.

**This is a genuine improvement rather than a rebrand**, and the phishing resistance in particular is structural rather than advisory.

### What Does Not Change

**The vault matters more, not less.** It now holds credentials that cannot be reset by changing a password.

**Most accounts keep a password behind the passkey.** If the recovery path is still a password and a code sent by message, then that is the actual strength of the account, whatever the sign-in screen offers.

**Synchronised passkeys are copies.** A passkey that follows you across devices lives wherever your vault lives, and everything in Figure 1 applies to it.

**So the action is: adopt passkeys where they are offered, then check what happens when you tell that site you have lost your device.** That path is the one an attacker will use, and Lesson 4.4 is about exactly that.

---

## 7. Choosing

For most people, including most security professionals, **a well-run vendor service with a strong master password and a high work factor is the right answer**, because the synchronisation and mobile access are what make the system get used at all. Lesson 1.4's rule holds: a control you abandon protected nothing.

**Local first is the right answer** when you genuinely want no third party holding a copy, and when you will actually do the synchronisation and backup work rather than intending to.

**Self-hosting is the right answer** when you already operate infrastructure to a standard you would accept from a vendor.

**And separately from all of that**, keep client credentials and anything unrotatable outside whatever you chose.

---

## 8. Summary

- A password manager solves reuse completely. It does not make credentials safe against a compromised endpoint, and nothing in it does.
- The architectures differ less than their labels. What differs is how many parties hold an encrypted copy.
- Once somebody holds a copy, only your master password and the work factor stand between them and the contents, attacked offline with no deadline.
- "The file is already encrypted, so cloud sync is fine" states the premise of the attack as though it answered it.
- The 2022 breach turned into years of losses because of three earlier decisions: unmigrated work factors, unencrypted fields, and the fact that a vault does not expire.
- Prefer Argon2id, and raise the work factor until unlocking is slightly annoying.
- Auto-type does not defeat keyloggers. It avoids the clipboard and gives some phishing resistance through window matching.
- Keep anything unrotatable out of the vault. A stolen copy of a recovery phrase never expires.
- Passkeys remove phishing and site-breach exposure structurally. They make the vault more important, and the recovery path is still where the risk sits.

**Action item.** Open your password manager's security settings and find the key derivation work factor. If you cannot find it, that is a finding. If you can, check when it was last changed.

The next lesson covers hardware security keys, which are the strongest answer to the phishing problem Figure 3 introduced.

---

## Knowledge Check

**Question 1.** Once an attacker holds a copy of your encrypted vault, what stands between them and its contents?

- A) The vendor's rate limiting
- B) The encryption algorithm alone
- C) Your master password and the work factor protecting it, attacked offline with no rate limit and no deadline
- D) Nothing, the vault is immediately readable

**Answer: C.** This is equally true of a vendor breach and of a vault file on a consumer sync service.

**Question 2.** In the 2022 vault breach, why were many accounts weaker than the current defaults implied?

- A) The work factor had been raised for new accounts, but existing accounts were never migrated, leaving many at far lower settings
- B) The encryption algorithm was broken
- C) The vaults were stored in plaintext
- D) The master passwords were stolen alongside the vaults

**Answer: A.** A default that improved is not a default that was applied to you, which is why you check your own.

**Question 3.** Some fields in the stolen vaults were unencrypted. Why did that matter before any cracking began?

- A) It did not matter
- B) It let attackers sort the stolen vaults by which ones held accounts worth the effort
- C) It revealed the master passwords
- D) It made the files larger to exfiltrate

**Answer: B.** Metadata deciding who gets targeted, which is the Module 3 argument reappearing in a different setting.

**Question 4.** What should be kept out of a password vault, and why?

- A) Nothing, a vault is for everything
- B) Only work credentials
- C) Only passwords shorter than twenty characters
- D) Anything that cannot be rotated, such as a recovery phrase, because a stolen copy of it never expires

**Answer: D.** Victims of the 2022 breach who lost the most had recovery phrases in their vaults.

**Question 5.** Does auto-type protect you from a keylogger?

- A) Yes, that is its main purpose
- B) No. It sends simulated keypresses, which a keylogger records like any others. It avoids the clipboard, and window matching gives some phishing resistance.
- C) Yes, because it uses the clipboard instead of the keyboard
- D) Only on some operating systems

**Answer: B.** The application cannot tell simulated keypresses from real ones, which is the point of the feature and also its limit.

**Question 6.** Which key derivation setting should you prefer for a vault?

- A) It makes no meaningful difference
- B) The oldest available option, for compatibility
- C) Argon2d, because it is faster
- D) Argon2id, because it defends against timing side channels as well as brute force

**Answer: D.** Then raise the work factor until unlocking is slightly annoying on your slowest device.

**Question 7.** A guide says storing your encrypted vault on a consumer sync service is fine because the file is already encrypted. What is wrong with that?

- A) Nothing, it is correct as stated
- B) The file is not actually encrypted
- C) It states the premise of the offline attack as though it were the answer to it. A copy in somebody else's hands can be attacked indefinitely.
- D) Sync services reject those files

**Answer: C.** It can still be a reasonable choice. It is not a free one, and what makes it survivable is the master password and work factor.

**Question 8.** What does a passkey send to the site when you sign in?

- A) A signature over a fresh challenge, while the private key never leaves your device
- B) Your password, encrypted
- C) The private key itself
- D) A one-time numeric code

**Answer: A.** Nothing replayable is transmitted, and the site only ever stores a public key.

**Question 9.** Why does a password manager matter more once it holds passkeys, rather than less?

- A) It does not, passkeys make managers unnecessary
- B) Because passkeys consume more storage
- C) Because passkeys expire and need renewing
- D) Because it now holds credentials that cannot be reset by changing a password, and a synchronised passkey lives wherever the vault lives

**Answer: D.** Everything in Figure 1 applies to a synchronised passkey exactly as it applies to a password.

**Question 10.** You have added a passkey to an account. What should you check next?

- A) Nothing further is needed
- B) What happens when you tell that site you have lost your device, because the recovery path is what an attacker will use
- C) Whether the passkey exceeds twenty characters
- D) Whether the site supports auto-type

**Answer: B.** If recovery is still a password and a code by message, that is the account's real strength.

---

## Exercise: Audit the Vault You Already Have

**Time: 90 minutes, plus 20 minutes to write up.**

### Safety Notes

- **Never write a master password, recovery key, or vault export path into anything you submit.** Record that you checked, not what you found.
- Do the export and restore test in a scratch location and delete it afterwards.

---

### Part 1: Find Out Where Your Copies Are (15 minutes)

List every place a copy of your vault currently exists. Be thorough, including places you set up and forgot.

| Where a copy exists | How it got there | Who else could reach it |
|---|---|---|

Include devices, sync services, backups, exports you made once, and any vendor storage.

**Most people find at least one they had forgotten.** If your list has one entry, check again.

**Deliverable.** The table, plus a count.

---

### Part 2: Check Your Work Factor (20 minutes)

This is the section the 2022 breach makes necessary.

1. Find the key derivation setting in your password manager. Record which function is in use and at what work factor.
2. Find out when that was last changed, and whether it was set when you created the vault or has been raised since.
3. If your product does not expose this, record that, and find out from its documentation what it uses.

| Question | Answer | Where I found it |
|---|---|---|
| Which function | | |
| What work factor | | |
| Set when | | |
| Migrated since | | |

Then act: raise it if you can, and time an unlock on your slowest device afterwards.

**Deliverable.** The table and what you changed. **No values from the vault itself.**

---

### Part 3: Find What Is Not Encrypted (15 minutes)

From your product's own documentation, establish which fields are encrypted and which are not.

| Field | Encrypted? | Source |
|---|---|---|
| Passwords | | |
| Usernames | | |
| Site addresses | | |
| Entry titles | | |
| Notes | | |
| Attachments | | |

**If the documentation does not say, that is your finding.** Record it as unknown rather than assuming.

**Deliverable.** The table, and one line on whether anything surprised you.

---

### Part 4: Find What Should Not Be In There (20 minutes)

Search your vault for things that cannot be rotated.

| Item | Can it be changed after exposure? | Where should it live instead |
|---|---|---|

Look specifically for: recovery phrases, backup codes, answers to security questions, private keys, and anything a client gave you that you were not asked to keep.

**Then move or delete at least one thing.** The exercise is not complete if nothing came out.

**Deliverable.** How many items you found and what you did with them. **Do not list the items themselves.**

---

### Part 5: Test the Restore (15 minutes)

A backup you have not restored is a backup you do not have.

1. Take a backup by whatever method you normally would.
2. Restore it into a scratch location, separate from your live vault.
3. Confirm it opens and the contents are intact.
4. Delete the scratch copy, and note where it was so you can confirm it is gone.

**Deliverable.** Confirmation that the restore worked, and that the scratch copy was removed.

---

### Part 6: Passkeys and Recovery (15 minutes)

Pick three accounts that matter to you.

| Account | Passkey available? | Enrolled? | What the recovery path is |
|---|---|---|---|

The last column is the exercise. Find out what that site does when you say you have lost your device.

Then answer: **for each account, is the recovery path weaker than the sign-in path?** If it is, the recovery path is the account's real strength, and adding a passkey did not change that.

**Deliverable.** The table and your three answers.

---

### Submission

1. **Copies inventory**, Part 1, with the count.
2. **Work factor**, Part 2, with what you changed.
3. **Encrypted fields**, Part 3, including unknowns.
4. **What came out**, Part 4, counts only.
5. **Restore test**, Part 5.
6. **Passkeys and recovery**, Part 6.
7. **Reflection**, half a page: which of the three decisions from Figure 2 does your current setup repeat?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Copies inventory | 15 | Thorough, including forgotten locations |
| Work factor | 25 | Actually found and checked rather than assumed, and raised where possible |
| Encrypted fields | 15 | Established from documentation, unknowns recorded as unknown |
| Removing the unrotatable | 20 | At least one item found and moved, no item contents disclosed |
| Restore test | 15 | Genuinely performed, scratch copy removed |
| Passkeys and recovery | 10 | Recovery path established for all three, and compared honestly |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources

- Web Authentication Level 3, W3C Recommendation, August 2026: https://www.w3.org/TR/webauthn-3/
- RFC 9106, Argon2: https://datatracker.ietf.org/doc/html/rfc9106
- KeePass documentation on auto-type obfuscation, which states plainly that keyloggers can read simulated keystrokes: https://keepass.info/help/v2/autotype_obfuscation.html

### On the Breach

- Krebs on Security, on the losses later linked to the 2022 breach: https://krebsonsecurity.com/2025/03/feds-link-150m-cyberheist-to-2022-lastpass-hacks/

Read this one even though the incident is old. The interval between the breach and the losses is the part worth absorbing.

### Discussion Questions

1. Of the three decisions in Figure 2, which does your current setup repeat, and can you tell without checking?
2. Your vault is the single point of failure for everything else in this module. What is your plan for the day it is unavailable rather than compromised?
3. Passkeys remove phishing from the sign-in path. Where did the risk move to, and have you checked it?

---

## References

1. [Krebs on Security, Feds Link $150M Cyberheist to 2022 LastPass Hacks, March 2025](https://krebsonsecurity.com/2025/03/feds-link-150m-cyberheist-to-2022-lastpass-hacks/)
2. [2022 LastPass data breach, overview and timeline](https://en.wikipedia.org/wiki/2022_LastPass_data_breach)
3. [KeePass, Two-Channel Auto-Type Obfuscation, on keyloggers and simulated keystrokes](https://keepass.info/help/v2/autotype_obfuscation.html)
4. [RFC 9106, Argon2 Memory-Hard Function for Password Hashing and Proof-of-Work Applications](https://datatracker.ietf.org/doc/html/rfc9106)
5. [KeePassXC project discussion on Argon2id](https://github.com/keepassxreboot/keepassxc/discussions/5804)
6. [Verizon, 2026 Data Breach Investigations Report](https://www.verizon.com/business/resources/reports/dbir/)
7. [W3C, Web Authentication: An API for accessing Public Key Credentials, Level 3](https://www.w3.org/TR/webauthn-3/)
