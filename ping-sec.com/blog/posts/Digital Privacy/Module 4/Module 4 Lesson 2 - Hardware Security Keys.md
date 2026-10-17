---
title: "Module 4 Lesson 2: Hardware Security Keys: What They Solve Completely, and What They Leave Open"
course: Digital Privacy for Ethical Hackers
module: 4
lesson: 4.2
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - password-security
  - authentication
  - fido2
  - webauthn
  - yubikey
  - opsec
---

# Hardware Security Keys: What They Solve Completely, and What They Leave Open

## About This Lesson

Lesson 4.1 ended on a vault that holds everything, and on the observation that passkeys move risk rather than remove it. This lesson is about the device that makes the strongest version of that possible, and about the two questions people skip when they buy one.

A hardware security key does one thing better than any other control in this course. It makes phishing structurally impossible rather than merely unlikely, and it does so without depending on you noticing anything. That is a genuinely rare property, and it is worth the money.

It is also the only control in this course that can permanently destroy your own access to your accounts. Most material on hardware keys spends its length on which model to buy. The two things that decide whether this goes well for you are what you configure at registration time, and what you leave enabled beside the key. Neither is a purchase decision.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Explain accurately where the phishing check happens, and why the usual shorthand hides something useful.
- Name the four attacks a hardware key does not address, and say which of them are settings you control.
- Choose between FIDO2 and the older protocols, and know why an older key still works.
- Register a key correctly, which means registering the second one at the same time.
- Map every route by which you can lock yourself out, and arrange the recovery for each in advance.
- State what the challenge and response feature in an offline password database actually is, and the permanent risk it carries.

### Prerequisites

- Lesson 4.1, particularly the passkeys section and the point about recovery paths.
- The threat modeling method from Lesson 1.4, which this lesson applies to a single device.
- A general understanding of public key cryptography. You do not need the mathematics.

### What You Need

- One hardware key, and a second one. The second is not optional, for reasons Section 7 covers in detail.
- Accounts that support the standard. Most of the ones that matter now do.

---

## 1. The Problem This Solves

A password is a shared secret. A code from an authenticator application is also a shared secret, with a short life.

Both have the same weakness, and it is not a cryptographic one. **Anything you can read out, you can be persuaded to read out.** A page that renders a perfect copy of a login screen collects the password and the code, relays both to the real site within the code's validity window, and is signed in as you. The code being valid for only thirty seconds sounds like a defence and is not, because the attacker needs about four of those seconds.

So the second factor is defeated by the same technique as the first, which means the two factors are not as independent as the phrase "two factor" suggests.

![[module_4_lesson_2_where_the_check_happens.svg]]

*Figure 1. Both paths ask for a second factor. Only one of them puts the check somewhere a page cannot reach.*

### Where the Check Actually Happens

You will read that a hardware key "checks the website domain and refuses to authenticate on a fake site." That is a useful shorthand and it is not what happens, and the accurate version is more informative.

Four separate parties each apply a check. (Reference 1)

1. **The browser records the true origin.** Not the origin the page claims. The one the browser actually loaded the page from, which the page cannot influence.
2. **The browser constrains the site identifier to that origin.** A page served from a lookalike domain cannot ask for a credential that belongs to the real domain.
3. **The key looks for a credential matching a hash of that identifier.** On the lookalike domain there is no match, so there is nothing to sign. This is worth being precise about: the key does not refuse, and it does not detect an attack. It simply has nothing that fits.
4. **The server verifies both values on arrival**, checking the origin and the identifier hash against what it expects, so a forged client cannot skip step two.

**Why the accurate version matters to you.** The browser is the enforcement point. That is what makes the guarantee hold against a perfect replica, and it is also what tells you the limits: a compromised browser, or a client that does not enforce origin correctly, removes steps one and two. The key is not the entire control. It is the part of the control that cannot be talked into anything, which is exactly the part that fails in every other method.

Nothing replayable ever leaves the device. The site receives a signature over a fresh challenge, and it holds only a public key, which is worth nothing to whoever steals it.

### The Result Everybody Cites

One organisation of roughly 85,000 employees moved to mandatory security keys in early 2017 and reported no confirmed account takeovers by phishing afterwards. (Reference 2)

**Use this carefully.** It is a strong result and it is a 2018 report about a 2017 change, which makes it eight years old at the time of writing. It is evidence that the control works as designed, at scale, for a population that gets phished constantly. It is not evidence about the attacks that grew afterwards, which Section 3 is about.

---

## 2. Why This Matters More For You

Lesson 4.1 made the argument about credentials in general, and it applies more sharply here.

Your accounts are the route into other people's systems. Disclosure platforms, client portals, source repositories that build into somebody's production, and infrastructure you stood up for an engagement. A phishing email aimed at you is aimed through you.

**The specific reason a key suits this situation.** Phishing resistance that depends on your attention degrades under exactly the conditions that produce mistakes: tired, in a hurry, on a phone, on the third day of an engagement, expecting a message that looks like the one you just received. A control that does not depend on your attention keeps working under all of those.

That is the honest case for the expense. Not that you are more likely to be phished than anybody else, but that the consequences reach further, and the control removes the dependence on a good day.

---

## 3. What a Hardware Key Does Not Stop

Most treatments of this topic finish at Section 1 and go straight to shopping. That produces a person with a key, a false sense of completion, and three doors open.

![[module_4_lesson_2_what_it_does_not_stop.svg]]

*Figure 2. Four attacks that survive a correctly configured key. Three of them are settings.*

### It Does Not Stop Theft of the Session

After you sign in successfully, your browser holds a cookie proving it. Malware on your machine copies that cookie, and the attacker replays it from their own browser.

**No authentication prompt is ever reached.** The cookie represents an authentication that already succeeded, so nothing the key does applies to it. Research through 2025 and 2026 describes stolen browser cookies circulating in enormous volume from information stealing malware, precisely because this route sidesteps the entire question of how strong your sign in was. (Reference 3)

The defence is device health, plus token binding where a service offers it, which ties a session to the device that received it. Neither of those is the key, and no amount of key buying substitutes for them. This is the point at which the weakest part of the chain becomes your endpoint, which is where Module 5 goes.

### It Does Not Stop a Weaker Factor You Left Enabled

You add the key. You leave the code by message switched on as a fallback, because removing it feels risky.

**The account is now exactly as strong as the worst method still enabled on it**, because an attacker will simply choose that one. Adding a strong method to a set of methods does not raise the floor. It adds a ceiling nobody is obliged to walk into.

This is the single most common way a correctly configured key produces no security benefit at all. The fix is to remove the weaker methods, and the reason people do not is that removing the fallback without a backup key is genuinely dangerous. So the sequence matters: register two keys, then remove the fallbacks. Section 7 is about why that order is not negotiable.

### It Does Not Stop the Recovery Path

Every service has a procedure for a person who has genuinely lost their key. So does yours.

If that procedure is a link to an email address plus a date of birth, then that is your account's real strength, whatever is at the front door. Lesson 4.1 made this point about passkeys and it is the same point: **the strongest sign in method on an account is not the account's strength. The weakest way in is.**

Lesson 4.4 is about this specifically. For now, treat it as an item on the audit rather than a thing to fix.

### The Physical Attack, Reported Honestly

In 2024, researchers published a side channel result affecting a widely used key series with firmware before version 5.7, exploiting a non constant time operation in the underlying cryptographic library to extract a private key. (Reference 4)

**The parts that decide how much this should worry you:**

- It requires physical possession of the key, plus laboratory equipment and expertise. This is a targeted attack against a specific person, not something that happens at scale.
- The vendor addressed it in firmware 5.7 by moving to a different cryptographic library.
- **Key firmware cannot be updated.** A device shipped before that version stays as it is for its whole life. (Reference 5)

So this is a purchase decision rather than a patching decision, which is unusual and worth internalising. If you already own an older key, this is not a reason to stop using it, because the attack it enables is far harder than the attacks it prevents. If you are buying now, check the firmware version, and do not buy old stock from a marketplace to save money.

**Read all four of these the right way round.** None of them argues against hardware keys. The attack a key removes is the one that actually happens to people, at the moment it happens. Two and three are configuration problems rather than protocol problems, which means they are yours to fix in an afternoon.

---

## 4. The Standards, Briefly

You will meet three names and they describe one lineage.

**U2F**, published in 2014, was second factor only. It always sat behind a password, and it introduced the origin binding that everything since has kept.

**FIDO2** is the current generation. It is a pair of specifications: WebAuthn, the browser side, which reached Level 3 as a W3C Recommendation in August 2026, and CTAP, the protocol between the browser and the key. (Reference 6) It supports the same second factor role as U2F, and it additionally supports signing in with no password at all.

**What happened to your old key.** U2F's framing was reclassified as CTAP1, and modern authenticators implement it, so an older U2F key still authenticates through WebAuthn today. (Reference 7) What was retired is the old browser side JavaScript interface, removed from Chrome in 2023 and superseded in Firefox well before that. So a U2F era key keeps working as a second factor, and cannot do the passwordless or discoverable credential parts.

| | U2F | FIDO2 |
|---|---|---|
| Password required | Always | Optional |
| Phishing resistant | Yes | Yes |
| Can be a passkey | No | Yes |
| Verifies it is you, not just the key | No | Yes, by PIN or biometric |
| Status | Works through CTAP1 compatibility | Current |

**The practical read.** Both are phishing resistant, which is the property you are buying, so an older key is not insecure. FIDO2 adds user verification, which is what makes a key a second factor rather than a possession check, and it adds the passwordless option.

### The Other Applications on the Same Device

A full featured key is several independent devices in one package, and the applications do not share anything.

- **OATH TOTP.** The key can hold the same time based secrets an authenticator application holds, and generate the codes offline. Useful for accounts that support no better method. Note what this means: these codes are phishable exactly like any other code, so putting them on a hardware key improves where the secret is stored, not what an attacker can do with a code you read out.
- **Smart card, or PIV.** Certificate based authentication for enterprise and government systems.
- **OpenPGP.** Holds a PGP private key so signing and decryption happen on the device and the key never touches the computer. This connects directly to Lesson 3.3.
- **Vendor OTP.** A proprietary one time password format, useful for older systems.

**Why this matters for Section 6.** These applications are separate, with separate PINs and separate reset behaviour. The consequence catches people out, and it has a whole section.

---

## 5. Choosing One

Prices below are the vendor's own list prices at the time of writing, and they are noticeably higher than most course material assumes. (Reference 8) Check them before you plan a budget.

| Option | Price | Protocols | Sensible use |
|---|---|---|---|
| Full featured, USB-A or USB-C with NFC | 58 USD | FIDO2, U2F, TOTP, PIV, OpenPGP, vendor OTP | The default choice if you want the extra applications |
| Nano form factor | 68 USD | Same | Lives in a port permanently, which is a security decision as much as a convenience one |
| Basic security key, USB-A or USB-C with NFC | 29 USD | FIDO2 and U2F only | Entirely sufficient if you only want phishing resistant sign in |
| Biometric | 98 USD | FIDO2 only, with a fingerprint sensor | Passwordless where typing a PIN is awkward |

**The recommendation, which is not the usual one.** If your goal is phishing resistant authentication, two basic keys at 29 USD each cost 58 USD in total and do that job identically to the expensive ones. The protocol is the same and the phishing resistance is the same. You pay more for the other applications: storing TOTP secrets, PGP operations, smart card certificates.

So decide by whether you want those applications, and be aware of the trap: **two full featured keys cost 116 USD, and one full featured key costs 58 USD, and the second is not optional.** People buy the good one, notice the total for a pair, and end up with a single key. That is the worst of the three outcomes. A pair of basic keys beats one expensive key for almost everybody reading this.

A reasonable middle: one full featured key for daily use and the applications, one basic key registered everywhere as the backup. Around 87 USD, and no single point of failure.

### Other Vendors

Open hardware and firmware options exist and remain active, and one large platform vendor sells its own keys with passkey storage. (Reference 9) The dominant vendor's advantage is breadth of application support and documentation depth rather than better cryptography, so if open firmware matters to your threat model, the alternatives are legitimate choices.

### Before Registering Anything

Counterfeit keys exist. The vendor provides a verification page that confirms a genuine device, and it is worth the two minutes. (Reference 10) Buy from the vendor or an authorised reseller rather than a marketplace listing, which also avoids the old firmware problem from Section 3.

**One tooling note.** Much existing material tells you to install a graphical management application. That application reached end of life in February 2026 and is no longer supported. The vendor now points to its authenticator application for desktop and mobile, and the command line tool remains supported for scripted tasks. (Reference 11) If a guide tells you to download the old graphical tool, the guide predates this.

---

## 6. The Feature That Is Not FIDO2, and the Warning It Needs

This section exists because the common instruction for it is wrong in a way that can permanently destroy a database.

You will read that an offline password manager "supports FIDO2" and that you enable it under Add Challenge Response. The first half is incorrect.

**What is actually happening.** FIDO2 and TOTP both need a changing component, a counter or a timestamp. That works for authenticating to a service and cannot work for an offline database, which needs to be decrypted with a fixed key. So the feature uses a different application on the same device entirely: an HMAC-SHA1 challenge and response credential programmed into an OTP slot. (Reference 12)

The design is neat. The database uses its own master seed as the challenge, so the required response changes every time you save.

**And here is what almost no guide says.** The response is derived from a secret that lives inside the key. There is no service to appeal to, no account recovery, and no reset that helps. The project's own documentation is unambiguous: if you lose or brick the key you permanently lose access to your database. It recommends keeping a copy of the secret when you program it, or programming a second key with the same secret. Multiple keys are supported, and they must all carry the identical secret. (Reference 12)

**So the instruction "enable challenge and response on your database" is incomplete to the point of being dangerous.** Before you enable it:

1. Program the secret yourself rather than accepting a generated one you never see.
2. Keep a copy of that secret somewhere secure and offline.
3. Program a second key with the same secret, and test opening the database with that key alone.
4. Only then remove the copy from daily reach, if you want to.

This is the entry in Figure 3 with no recovery row, and it is the only one in this lesson where being late means the data is simply gone.

---

## 7. Registration, and Why the Second Key Is Not Optional

The correct unit of work is not "register a key." It is "register two keys, then remove the fallbacks."

Doing it in that order takes one extra evening. Doing it in the wrong order produces one of two failures: a key that adds nothing because the weak fallback is still enabled, or a lockout because the fallback was removed with only one key registered.

### The Order

**Set a PIN on the key first.** Without one, the key is a possession check: whoever holds it can use it. With one, it is genuinely a second factor. The PIN can be four to sixty three characters. (Reference 13) Read Section 8 before you choose it.

**Register both keys on each account in the same sitting.** The prompts differ by service and the shape is the same: find the security or two step settings, add a security key, touch it when it flashes, name it something that identifies the physical object. "Primary" and "Backup" are fine. "YubiKey" is not, because in two years you will have three.

**Collect the recovery codes.** Services offer them once, and Section 8 covers where they go.

**Then remove the weaker methods.** This is the step that converts the purchase into security, and it is the step people postpone indefinitely. Test signing in with each key first.

**Then test the backup key on its own.** Sign out, sign in using only the backup, put it back. A key you have never signed in with is an assumption rather than a backup.

### Which Accounts, In Which Order

Sequence by what an attacker gets, not by what feels important.

1. **The email address that resets everything else.** This one first, always. Every other account's recovery path probably ends here, so protecting anything else first protects a door with the window open beside it.
2. **The password manager**, if it supports keys, for the reason Lesson 4.1 established: it holds everything.
3. **Source repositories and package registries**, which for you means somebody else's build pipeline.
4. **Cloud storage**, which holds engagement material more often than people admit.
5. **Disclosure platforms and client portals**, where the compromise is contractual as well as personal.
6. **Financial accounts**, where support for keys remains patchy and worth checking annually.
7. **Social accounts**, which matter for impersonation rather than for what is inside them.

**One caution about number one.** Removing weaker methods from your primary email is the highest value change in this list and the one with the worst failure mode. Two keys registered, both tested, recovery codes stored outside that account. Then remove.

### Where the Keys Live

Primary on your daily keys or in your bag. Backup somewhere you can reach in a day but not in a minute: a safe at home, a locked drawer at an office, a deposit box.

The failure to avoid is both keys in the same bag, which converts a stolen bag into a total lockout. Do not label them with what they unlock.

**On travel.** Carry the primary in hand luggage, and leave the backup at home. Advice to put a backup in checked luggage gets this exactly backwards: checked bags are the ones that go missing and get opened, and a backup in another country is not a backup. Hardware keys are legal to carry, they hold no readable user data, and border staff generally recognise them.

---

## 8. Every Way You Lock Yourself Out

This is the section that determines whether you still have your accounts in three years.

![[module_4_lesson_2_lockout_map.svg]]

*Figure 3. Every recovery here is arranged in advance or not at all.*

### You Lose the Key

You lose access to every account it was the only factor for, at once, with no warning, usually while travelling.

**Recovery: the second key, registered on the same accounts.** Then revoke the lost one from every account it was on, which is why the naming discipline in Section 7 matters. Arranged at registration. A key you own but never registered is a spare part, not a backup.

### You Forget the Device PIN

This is the one that surprises people, because the failure is unlike anything else in this course.

**Eight wrong attempts block the FIDO2 application entirely.** The limit is fixed in the specification and cannot be raised. (Reference 13)

**Recovery: a factory reset of that application, which is the only route back, and it erases every passkey and credential on the key.** (Reference 14) So the recovery restores your device and does not restore your access. You get a working blank key and a set of accounts that no longer recognise it.

Put the PIN into the password manager at the moment you choose it. Not later.

**And note the interaction with Lesson 4.1.** If you moved passkeys onto the key, a reset destroys them, and a passkey stored only on that device does not exist anywhere else. A key holding twenty passkeys and a forgotten PIN is twenty accounts to re-enrol, assuming each of them still has another way in.

**A related limit worth knowing before you plan.** Devices on firmware 5.0 to 5.6 store 25 discoverable credentials. Firmware 5.7 and later store 100. (Reference 15) If you intend to use a key as your main passkey store, that number is a design constraint rather than a detail.

### Every Key Is Gone at Once

Fire, a stolen bag holding both, or simply being a long way from where you stored the spare.

**Recovery: the service's recovery codes**, which are credentials in their own right and deserve the same care as a password. Store them where you can reach them without the account they unlock. Codes saved inside the email account those same codes would recover are not a backup, and this is a common and quiet mistake.

### The Offline Database

Covered in Section 6. **No recovery.** It is in the figure because it belongs in the same mental model, and because it is the one people enable most casually.

### Testing

Once a quarter, sign in with the backup key on the two accounts that matter most, and confirm you can still reach the recovery codes. Keys get removed from accounts accidentally, services change their authentication options, and a backup you have not exercised in two years is a belief rather than a fact.

---

## 9. Two Uses Worth the Extra Effort

Both are optional and both are genuinely useful for the work you do.

### SSH Authentication

A key generated against the hardware cannot be copied off the machine it is stored on, and every authentication needs a physical touch. That converts a stolen laptop from a server compromise into an inconvenience.

```bash
# Requires OpenSSH 8.2 or later. Add -O resident to store the credential
# on the key itself, which needs OpenSSH 8.3 or later to retrieve, a
# FIDO2 PIN, and firmware 5.2.3 or later.
ssh-keygen -t ed25519-sk -O resident -O application=ssh:mykey
```

**Two things existing guides get wrong.** Much material still tells you to use `ecdsa-sk`, which was necessary on firmware before 5.2.3 and is not on anything current; `ed25519-sk` is the better choice today. And the resident option is presented as a convenience when it is a requirement for the recovery story: without it the credential handle lives in a file on that computer, and losing the computer loses the key. With it, `ssh-keygen -K` retrieves the credential onto a new machine. (Reference 16)

**The honest limit.** "Impossible to steal the SSH key" is overstated. The private key does not leave the device, which is a real and large improvement. Malware already running on your machine while the key is plugged in can still ask the agent to authenticate. Adding `-O verify-required`, which needs OpenSSH 8.4 or later, forces the PIN on every use and closes most of that.

### Commit Signing

Signing commits with a hardware backed key proves the commit came from something you physically touched. For work that ends up in somebody else's build, that is a meaningful claim. You can do it through the OpenPGP application, or more simply now through SSH signing using the same hardware key you already configured above.

---

## 10. What to Take From This

**The purchase is the easy part and the smallest part.** Two keys, roughly 60 to 120 USD depending on which applications you want, and the phishing resistance is identical across the range.

**The value arrives when you remove the weaker methods**, and not before. A key registered beside an active code by message fallback has changed nothing about what an attacker will do.

**The risk arrives at the same moment**, which is why the second key is registered in the same sitting and the recovery codes are stored somewhere the account cannot lock you out of.

**And the key answers one question completely.** "Somebody convinces me to hand over my credentials" is closed, permanently, without depending on your attention. "Somebody is already running code on my laptop" and "somebody phones the help desk claiming to be me" are untouched. Those need different controls, and buying a key does not buy them.

That is the threat modeling method from Lesson 1.4 applied to a single device, and it is the whole lesson: name what the control closes, then go and look at what is still open.

**Next lesson:** the full range of authentication methods, ranked honestly, including which ones are worth using when a hardware key is not supported.

---

## Knowledge Check

**Question 1.** Where does the check that defeats a phishing page actually happen?

- A) The browser records the true origin and constrains the request to it, and the key then finds no matching credential
- B) The server compares your IP address against your usual location
- C) The key inspects the address bar and refuses fake domains
- D) The key displays the domain name for you to confirm

**Answer: A.** The key never sees a domain. It sees a hash it was handed, and the browser is what guarantees that hash reflects where you really are.

**Question 2.** You add a hardware key to an account and leave the code by message option enabled as a fallback. How strong is the account?

- A) As strong as the hardware key, because it is the preferred method
- B) Stronger than either method alone, because there are now two
- C) As strong as the weakest method still enabled, because an attacker chooses that one
- D) It depends on which method you personally use most often

**Answer: C.** Adding a strong method to a set of methods does not raise the floor. This is the most common way a correctly configured key produces no benefit at all.

**Question 3.** Why does theft of a session cookie defeat a hardware key?

- A) The cookie contains the private key
- B) The cookie represents an authentication that already succeeded, so no authentication prompt is ever reached
- C) Cookies are transmitted without encryption
- D) The key only protects the password, not the session

**Answer: B.** The defence is device health and token binding where a service offers it, which is why the endpoint becomes the weakest link once sign in is solved.

**Question 4.** What happens after eight consecutive wrong PIN attempts on the FIDO2 application?

- A) The key waits an increasing amount of time between attempts
- B) Nothing, the attempt counter resets when the key is unplugged
- C) The key is permanently destroyed and cannot be reused
- D) The application is blocked, and the only route back is a reset that erases every credential on the key

**Answer: D.** The limit is fixed in the specification. The reset gives you a working blank key and a set of accounts that no longer recognise it.

**Question 5.** An older U2F key from 2016 is plugged into a current browser. What happens?

- A) It works as a second factor through CTAP1 compatibility, but cannot do passwordless or store passkeys
- B) It works fully, including passwordless sign in
- C) Nothing, U2F was removed from browsers and the key is now useless
- D) It works only on sites that specifically support legacy hardware

**Answer: A.** The old browser side JavaScript interface was retired. The hardware was reclassified as CTAP1 and still authenticates through WebAuthn.

**Question 6.** The challenge and response feature used to unlock an offline password database relies on which part of the key?

- A) The FIDO2 application, the same one used for passkeys
- B) The OpenPGP application
- C) An HMAC-SHA1 credential in an OTP slot, which is not FIDO2 at all
- D) The smart card application

**Answer: C.** FIDO2 and TOTP both need a changing component, which cannot work for a database that must be decrypted with a fixed key.

**Question 7.** You enabled that feature and then lost the key. What recovers the database?

- A) The service's account recovery process
- B) Your recovery codes
- C) A factory reset of the key
- D) Nothing, unless you kept a copy of the secret or programmed a second key with the same one

**Answer: D.** The project documentation states this outright. It is the only entry in Figure 3 with no recovery.

**Question 8.** Why is the 2024 side channel result on older key firmware a purchase decision rather than a patching decision?

- A) Key firmware cannot be updated, so an affected device stays affected for its whole life
- B) The vendor charges for the firmware update
- C) The update requires returning the key to the manufacturer
- D) The vulnerability only affects keys bought after 2024

**Answer: A.** It also needs physical possession and laboratory equipment, so it is a targeted attack rather than a broad one. Check the firmware version when buying, and avoid old marketplace stock.

**Question 9.** What is wrong with putting your backup key in checked luggage when travelling?

- A) Hardware keys are not permitted in checked luggage
- B) Checked bags go missing and get opened, and a backup in another country is not a backup
- C) The pressure change can damage the device
- D) Customs will confiscate an unregistered key

**Answer: B.** Carry the primary in hand luggage and leave the backup at home.

**Question 10.** What is the correct order of operations when adopting hardware keys?

- A) Register one key, remove the weaker methods, then buy a backup when convenient
- B) Remove the weaker methods first so the key becomes the only option, then register it
- C) Register one key and keep every existing method enabled as insurance
- D) Register two keys and test both, then remove the weaker methods, keeping recovery codes outside the account

**Answer: D.** Doing it in the wrong order produces either a key that adds nothing, or a lockout.

---

## Lab: Registration, Removal, and Every Way Back In

**Time: 2 to 3 hours, plus one quarterly repeat.**

This lab is deliberately weighted away from setup. Registering a key is the part everybody completes. The parts that decide the outcome are removing the fallbacks and mapping the recovery, so those carry the marks.

If you do not own a key yet, Parts 1, 2, 5 and 6 can be completed from account settings and vendor documentation alone.

---

### Part 1: The Account Map (20 minutes)

Before touching anything, list the accounts that matter, in the order from Section 7.

| Account | Key support? | Methods currently enabled | Where recovery leads |
|---|---|---|---|

**The third column is the point of this part.** Most people find at least one account where a strong method sits beside a code by message they forgot was on.

**Deliverable.** The table, at least eight accounts, and one sentence naming the weakest method you found still enabled.

---

### Part 2: Verify and Prepare the Keys (20 minutes)

- Verify both keys are genuine using the vendor's verification page.
- Record both serial numbers and the firmware version of each. Note whether either is before 5.7, and write one sentence on what that means for you given Section 3.
- Set a PIN on each key. **Record each PIN in the password manager at the moment you set it**, before you do anything else.

**Deliverable.** Serial numbers, firmware versions, your one sentence, and confirmation the PINs are recorded. Do not include the PINs themselves.

---

### Part 3: Register Both Keys (40 minutes)

For at least four accounts from Part 1, starting with the email address that resets the others.

- Register the primary key. Name it to identify the physical object.
- Register the backup key in the same sitting.
- Collect the recovery codes.
- **Sign out completely and sign back in using only the backup key.** Then do it again with only the primary.

**Deliverable.** A table of account, both key names, recovery codes collected yes or no, and both keys tested individually yes or no. Screenshots are not required and no credential values are ever submitted.

---

### Part 4: Remove the Fallbacks (30 minutes)

The highest value part of this lab.

For each account in Part 3, remove every authentication method weaker than the key, where the service permits it. Where it does not permit it, record that instead, because a service that forces a weak fallback has told you something useful about itself.

| Account | Method removed | Or: why it could not be removed |
|---|---|---|

**Do not start this part until Part 3 is complete and both keys have been tested individually.** That is not a formality. It is the whole reason the order exists.

**Deliverable.** The table, and one sentence on which account you were most reluctant to do this for, and why.

---

### Part 5: The Lockout Map (30 minutes)

For each of the four scenarios in Figure 3, write what would actually happen to you today.

| Scenario | What I lose | What gets me back in | Arranged? |
|---|---|---|---|
| Primary key lost | | | |
| Device PIN forgotten | | | |
| Both keys gone at once | | | |
| Offline database, if you enabled it | | | |

Then answer directly: **are your recovery codes stored somewhere you could reach without the account they unlock?** Check rather than assume. This is where the common mistake lives.

**Deliverable.** The completed table and your answer.

---

### Part 6: The Recovery Path (20 minutes)

Pick the two accounts that matter most. For each, find out what the service actually does when a person says they have lost every authentication method. Vendor documentation, not guesswork.

Then answer: **is the recovery path weaker than the sign in path you just hardened?**

If it is, you have found the thing Lesson 4.4 is about, and you found it before an attacker did.

**Deliverable.** Both recovery paths described in a few sentences each, and your answer for each.

---

### Part 7 (Optional): SSH or Signing (45 minutes)

Configure hardware backed SSH authentication using `ed25519-sk` with the resident option, or set up commit signing.

Then answer the question the guides skip: **if your laptop is stolen tomorrow with the key not plugged in, what does the thief have?** And: **if malware is running on your machine while the key is plugged in, what can it do?**

**Deliverable.** Your configuration, and both answers.

---

### Submission

1. **Account map**, Part 1, with the weakest method you found.
2. **Key preparation**, Part 2, without the PIN values.
3. **Registration**, Part 3, with both keys tested individually.
4. **Fallback removal**, Part 4, including what could not be removed.
5. **Lockout map**, Part 5, with the recovery codes answer.
6. **Recovery paths**, Part 6, for two accounts.
7. **Reflection**, half a page: which of the four items in Figure 2 is currently most open for you, and what would closing it require that buying a key did not?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Account map | 10 | Complete, and the existing methods are actually checked rather than recalled |
| Key preparation | 10 | Verified, firmware recorded, PINs stored at the moment they were set |
| Registration | 20 | Both keys registered together and each tested on its own |
| Fallback removal | 25 | Actually performed, with refusals recorded honestly |
| Lockout map | 20 | All four scenarios answered for your real setup, recovery code storage checked |
| Recovery paths | 10 | Established from documentation for two accounts, and compared honestly |
| Reflection | 5 | Identifies a genuine remaining gap rather than restating the lesson |
| **Total** | **100** | |

**Note the weighting.** Registration is worth 20 and removing the fallbacks is worth 25, because the second is what converts the first into security. A submission with four accounts perfectly registered and every weak method still enabled has completed the easy half.

---

## Additional Resources

### Primary Sources

- Web Authentication Level 3, W3C Recommendation, August 2026: https://www.w3.org/TR/webauthn-3/
- Client to Authenticator Protocol, FIDO Alliance: https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html
- The offline database documentation on hardware keys, including the warning quoted in Section 6: https://keepassxc.org/docs/

### On the Side Channel Result

- EUCLEAK, NinjaLab, September 2024: https://ninjalab.io/eucleak/

Read the summary rather than the full paper unless side channel analysis interests you. The part that matters for this lesson is the attack prerequisites, not the technique.

### Discussion Questions

1. Section 3 lists four things a key does not stop. Which of them did you assume it stopped before reading this?
2. You completed Part 4 and removed the fallbacks. Which account did you not remove them from, and is that a limitation of the service or a limitation of your nerve?
3. The offline database feature in Section 6 has no recovery at all. Does that change whether you would use it, or only how you would prepare for it?

---

## References

1. [W3C, Web Authentication: An API for accessing Public Key Credentials, Level 3](https://www.w3.org/TR/webauthn-3/)
2. [Krebs on Security, Google: Security Keys Neutralized Employee Phishing, July 2018](https://krebsonsecurity.com/2018/07/google-security-keys-neutralized-employee-phishing/)
3. [Menlo Security, The Art of MFA Bypass, on session token theft and adversary in the middle](https://www.menlosecurity.com/blog/the-art-of-mfa-bypass-how-attackers-regularly-beat-two-factor-authentication)
4. [NinjaLab, EUCLEAK side channel attack on the YubiKey 5 series, September 2024](https://ninjalab.io/eucleak/)
5. [GitHub Advisory Database, CVE-2024-45678](https://github.com/advisories/GHSA-cf4q-v7mm-g53q)
6. [W3C, Web Authentication Level 3 Recommendation](https://www.w3.org/TR/webauthn-3/)
7. [Yubico developer documentation, Client to Authenticator Protocol and U2F compatibility](https://developers.yubico.com/CTAP/)
8. [Yubico store, current list prices](https://www.yubico.com/store/)
9. [Titan Security Key, overview including passkey storage](https://en.wikipedia.org/wiki/Titan_Security_Key)
10. [Yubico, verify a genuine device](https://www.yubico.com/genuine/)
11. [Yubico, YubiKey Manager download page, noting the graphical application reached end of life on 19 February 2026](https://www.yubico.com/support/download/yubikey-manager/)
12. [KeePassXC documentation and FAQ, on HMAC-SHA1 challenge and response and the loss of the key](https://keepassxc.org/docs/)
13. [Yubico Authenticator user guide, FIDO2 PIN requirements and the eight attempt limit](https://docs.yubico.com/software/yubikey/tools/authenticator/auth-guide/fido2.html)
14. [Yubico Authenticator user guide, factory reset behaviour](https://docs.yubico.com/software/yubikey/tools/authenticator/auth-guide/reset.html)
15. [Yubico, passkey guidance and discoverable credential limits by firmware version](https://docs.yubico.com/hardware/yubikey-guidance/best-practices/sp-bestpractices-passkeys.html)
16. [Yubico developer documentation, Securing SSH with FIDO2](https://developers.yubico.com/SSH/Securing_SSH_with_FIDO2.html)
