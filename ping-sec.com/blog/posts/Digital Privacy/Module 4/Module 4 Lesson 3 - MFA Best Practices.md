---
title: "Module 4 Lesson 3: Multi-Factor Authentication: One Line, and the Housekeeping Below It"
course: Digital Privacy for Ethical Hackers
module: 4
lesson: 4.3
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - password-security
  - authentication
  - mfa
  - phishing-resistance
  - opsec
---

# Multi-Factor Authentication: One Line, and the Housekeeping Below It

## About This Lesson

The usual treatment of this subject is a tier list. Hardware keys get five stars, authenticator applications get four, push gets three, text messages get two, and email codes get one. It looks rigorous and it teaches the wrong shape.

**A five star method and a four star method are not one step apart.** One of them cannot be defeated by a perfect replica of a login page and the other can. That is not a difference of degree. Everything below that line shares a single defeat, and rearranging methods below it does not address that defeat at all.

This lesson is organised around that line: what is above it, why everything below it fails the same way, and what still gets through even when you have done everything right. It also does something the tier list makes hard, which is to give proper credit to the upgrades that do not cross the line and are still worth making.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Sort authentication methods by the property that matters rather than by a star rating.
- Explain why one passkey can count as two factors, and what setting decides it.
- Name the seven known bypasses and say which change closes each one.
- Describe the 2022 push fatigue case accurately, including the step usually left out.
- State what number matching fixes and what it does not.
- Use the two most quoted statistics in this field correctly, or decide not to quote them.
- Audit your own accounts and produce a ranked list of what to change.

### Prerequisites

- Lesson 4.1 and Lesson 4.2, particularly Figure 2 of Lesson 4.2 on what a hardware key does not stop.
- The threat modeling method from Lesson 1.4.
- The compartmentalization model from Lesson 2.5, which Section 8 applies to authentication.

---

## 1. Two Statistics, Used Carefully

Both of the numbers this topic runs on are misused constantly, and you will meet both in the next report you read.

**"Multi-factor authentication prevents 99.9 percent of account compromise attacks."** This comes from a 2019 vendor blog post, and what was actually measured is that 99.9 percent of the compromised accounts in that dataset did not have multi-factor authentication enabled. (Reference 1)

Read those two sentences again, because they are not the same claim. The first is a statement about how effective the control is. The second is a statement about who had it switched on, in a population where hardly anybody did, at a time when the attacks that defeat it were rare. Turning a base rate into an efficacy figure is a selection effect, and it has been criticised at length by people who work in this field. (Reference 2)

The same vendor's later research gives figures that are defensible: a reduction in compromise risk of roughly 99.2 percent across the population studied, and about 98.6 percent in cases involving leaked credentials. (Reference 3) Those are still large numbers and they are still worth acting on. Use those instead.

**"An organisation of 85,000 people had zero phishing after hardware keys."** Lesson 4.2 covered this one. It is a 2018 report about a 2017 change, phrased in the original as no reported or confirmed account takeovers. (Reference 4) It is good evidence that the control works at scale, and it says nothing about the attacks that grew afterwards.

**Why this section is first.** You are going to write about this, and you are going to be asked about it by clients. Getting a number right is a small thing that compounds, and repeating the 99.9 percent figure as an efficacy claim marks the writer as someone who did not check.

---

## 2. Factors, Briefly, and the Part That Actually Bites

Three categories: something you know, something you have, something you are.

Multi-factor means two of them from **different** categories. A password plus a security question is two things you know, which is one factor asked twice. That distinction is standard and worth stating once.

**The part that gets skipped.** A single device can satisfy two categories on its own. A credential held on a device, released only after a fingerprint or a PIN, is a possession factor and a second factor in one gesture. Current guidance permits exactly this: one multi-factor authenticator can meet the assurance level that would otherwise need two separate ones. (Reference 5)

This is why a passkey can be genuinely multi-factor rather than a rebrand of "saved password." **And it is also where the honest caveat lives.** That property depends on user verification being required at registration. A passkey registered without it is possession only, which is one factor. (Reference 6) The setting is real, it varies by service, and almost nobody looks at it.

---

## 3. The Line

![[module_4_lesson_3_the_only_line.svg]]

*Figure 1. Above the line, a perfect replica obtains nothing usable. Below it, everything shares one defeat.*

### Above the Line

**A hardware security key**, as covered in Lesson 4.2. A second factor, or the whole thing.

**A passkey with user verification.** The same protocol, with the credential in your password manager or on a device instead of on a separate piece of hardware.

They share exactly one property and it is the only one that matters here: a page that renders a flawless copy of the login screen obtains nothing it can use. Not because you noticed. Not because you were careful. Lesson 4.2 Figure 1 has the mechanism.

### Below the Line

Everything else fails the same way. **You hand over a value, and an attacker relays it to the real site while it is still valid.** That is true of a code from an application, a push you approve, a code by text message, and a code by email. The differences between them are real, and they are differences in what *else* can go wrong.

Ranked by that:

**Codes from an application.** The best of the phishable set and a genuine improvement on what most people have. No phone number is involved, so SIM swapping cannot reach it. Works with no signal. Its real weakness is not security, it is backup: one lost phone and every code is gone, which Section 7 covers.

**Push with number matching.** You read a number from the screen and type it into the application. Bombardment stops working, because there is nothing to approve by reflex. A relay still works, because a relay can show you the number.

**Plain push approval.** Adds the fatigue attack to everything above. Section 6 is about that specifically.

**Codes by text message.** Adds SIM swapping, which requires no access to you, your accounts, or your phone. Only to a support agent at your carrier. The current NIST digital identity guidance treats text message codes as a **restricted** authenticator, removed from the list of methods that satisfy the second assurance level on their own, and permitted only alongside a documented risk assessment, a migration plan, and notification to users. (Reference 7)

That is a meaningful change from the earlier guidance, and it is the strongest citation available for what most people already suspected.

### The Two That Are Not Second Factors

**Codes to your email address** use the same channel your account recovery already uses. They protect nothing your email does not already protect, and for securing an email account the arrangement is circular.

**Security questions** are still something you know, so not a second category at all, and the answers are frequently things you have published yourself. If a service forces them, invent the answers and store them in the vault as though they were passwords, which is what they are.

---

## 4. Which Bypass Each Upgrade Actually Closes

Sorting the known bypasses by which change fixes them is more useful than listing them, because it shows which of your work is finished and which has not started.

![[module_4_lesson_3_which_bypass_survives.svg]]

*Figure 2. Three groups. The middle one is the one most guidance skips.*

### Closed by Crossing the Line

**Real time relay phishing.** A replica page collects the password and the code and uses both inside the validity window. This is the common attack, it works against every method below the line, and it works against none above it.

**Push fatigue.** Repeated approval requests until one is approved. Number matching closes most of it. Crossing the line removes the prompt entirely, so there is nothing to approve by mistake.

### Closed by Leaving Text Messages, Without Crossing the Line

This group is why the tier list does harm, because it presents these upgrades as small.

**SIM swapping.** Somebody persuades your carrier to move your number to their device and your codes follow it. Note what this needs: no access to you, no access to your accounts, no access to your phone. Only a support agent having a bad day. The 2019 compromise of a social platform's own chief executive worked this way, through a carrier and a legacy feature that let a phone number post to an account. (Reference 8)

**Network interception.** Weaknesses in the signalling between carriers, which you have no visibility of and no control over.

**Moving from text messages to an authenticator application removes both of these, for free, in about four minutes per account.** Both methods stay below the line and stay phishable, and two real attacks are gone anyway. If a service supports nothing above the line, this is still worth doing, and framing it as "still only four stars" discourages exactly the change most people can actually make today.

### Closed by Neither

**Theft of the session.** The cookie proving you already signed in is copied and replayed from elsewhere. No authentication prompt is ever reached, so no authentication method applies. Lesson 4.2 covered this and there is now something to say about it, in Section 5.

**Recovery by persuasion.** Somebody contacts support, says they have lost everything, and is given a way back in. This defeats every method on the list, and nothing technical fixes it. Lesson 4.4 is about it.

**Malware on the device.** Reads codes as you generate them, or asks a key to sign while it is plugged in and unlocked. Not an authentication problem, and no authentication change addresses it. That is Module 5.

---

## 5. What Changed About Session Theft

Lesson 4.2 left this as an open problem with device health as the only answer. That is now out of date in a useful way.

**Device bound session credentials** tie a session to hardware on the machine that established it, using the platform security module. The browser must prove possession of a private key that cannot be exported before the server will issue fresh session cookies, and those cookies are short lived. A copied cookie therefore expires quickly and cannot be renewed anywhere else. The protocol went through the W3C web application security working group, and it reached general availability in Chrome in April 2026. (Reference 9)

**What it does and does not do.** It addresses local theft of cookies, which is the infostealer case, and that is a large share of the problem. It is not a defence against a relay attack that captures the session as it is created, and it depends on the service adopting it as well as the browser. Treat it as a real improvement rather than a closure. (Reference 10)

**Why this belongs in your notes.** It shipped five months ago, which means most of the guidance you will read on this topic predates it and still describes session theft as unaddressed. Check whether the services you care about have turned it on.

---

## 6. Push Fatigue, Told Properly

The 2022 breach that everybody cites is usually described as a person who got annoyed by notifications and gave in. That telling removes the step that made it work, and the removed step is the whole lesson.

![[module_4_lesson_3_push_fatigue_anatomy.svg]]

*Figure 3. Four steps. Step three is the one that goes missing.*

**Step one.** The attacker already had the contractor's password, obtained from an earlier compromise. Every attack in this lesson begins after the first factor has already failed, which is the entire reason a second one exists.

**Step two.** Approval requests were sent repeatedly, roughly forty of them in thirty minutes. **On its own this usually fails.** Most people ignore it and some report it, which is why bombardment alone is a poor attack.

**Step three.** The attacker contacted the target on a separate messaging channel, presented themselves as internal support, and said the alerts were a known fault and that approving one would stop them. (Reference 11)

**Step four.** One approval, on a contractor account, which then reached internal tools and further credentials.

### Why "Never Approve an Unrecognised Request" Is Not Enough

By step three the request is no longer unrecognised. It has been explained, by somebody who appears to work there, in a way that accounts for exactly what the target can see on their screen.

**The advice assumes the target has no context. The attack works by supplying context.**

The person who approved was not careless. They were given an explanation that fitted the evidence in front of them, which is what a good pretext is. Any control that depends on a person correctly distrusting a plausible message will eventually fail on somebody, and you should assume that somebody is you on a bad day rather than a hypothetical careless colleague.

### What Number Matching Changes

**It closes this attack.** You must read a number from the screen and type it into the application, so there is no reflex to exploit and bombardment stops paying. One large provider made it mandatory across its authenticator in May 2023. (Reference 12) Enable it wherever it is offered.

**It does not make push phishing resistant.** A relay page displays the number to you, and you type it. National guidance is explicit that number matching is an interim mitigation for organisations that cannot yet deploy phishing resistant methods, and not a substitute for them. (Reference 13)

---

## 7. Doing It Properly

### Rule 1: Sort by What an Attacker Gets

This is the same ordering as Lesson 4.2 and it is worth repeating, because people sort by how important an account feels rather than by what it unlocks.

**Above the line, no exceptions:** the email address that resets everything else, your password manager, source repositories and package registries, cloud infrastructure, client and disclosure platforms.

**Application codes as the minimum:** secondary email, cloud storage, financial accounts where nothing better is offered, work systems, social accounts.

**Anything at all is fine:** accounts you would not mind losing.

### Rule 2: Two Ways In Before You Remove Any

Lesson 4.2 made this the whole point and it applies to every method, not just hardware keys. Register the second method, test it on its own, collect the recovery codes, and only then remove the weak ones.

The order is not a formality. Reversed, it produces a lockout.

### Rule 3: Adding a Method Does Not Raise the Floor

An account with a hardware key and an active text message fallback is a text message account. The attacker picks. This was Lesson 4.2 Figure 2 and it is the single most common reason a correct setup produces no benefit.

**So the work is removal, not addition**, and removal is what people postpone.

### Rule 4: Look at Where Recovery Leads

A hardware key on an account whose recovery is a code by text message is a text message account with an expensive accessory. Check what each service actually does when you say you have lost everything. Lesson 4.4 goes into this properly.

### Rule 5: Compartmentalize the Methods Too

Lesson 2.5 argued for separating identities. Authentication is part of that surface.

Separate applications, or separate profiles, for personal, work and research contexts, so that a compromise of one device does not enumerate every account you hold across all three. A single authenticator application listing personal banking beside client infrastructure beside a research identity is a map, and it is a map somebody can photograph.

### Rule 6: Alert on Changes to the Methods Themselves

Enable notifications for a method being added, a method being removed, and recovery codes being generated. **A method added that you did not add is a compromise in progress**, and it is often the earliest signal you will get, because adding a method is what an attacker does to keep access after you change the password.

### Rule 7: Back Up the Codes Application Deliberately

Losing the phone should not mean losing everything, and this is the practical weakness of application codes.

Options, in rough order of preference: a manager that holds the codes and is itself backed up, an application with encrypted backup that you have tested restoring, a second device enrolled at setup time, or the recovery codes on their own.

**One correction on a common recommendation.** Older guidance names a particular application specifically for its backup feature. Its desktop applications were discontinued in 2024, and later that year a phone number list associated with its users was leaked through an unauthenticated interface, which is a privacy exposure rather than a compromise of anybody's codes. (Reference 14) It remains usable. It is no longer the obvious default it was when that advice was written, and if you followed that advice on a desktop it stopped working some time ago.

---

## 8. What to Take From This

**There is one line, and crossing it is the decision.** Above it, a perfect replica of a login page obtains nothing. Below it, every method fails the same way and the differences are about what else can go wrong.

**Below the line still has real work in it.** Moving an account from text messages to an application removes SIM swapping and network interception entirely, and that is available on almost every account you hold, today, at no cost. Perfect is available on some accounts. Better is available on all of them, and better is not a consolation prize.

**Removal is the step that converts effort into security**, and it is the step that gets postponed because it feels risky. It is risky, which is why the second method comes first.

**And three attacks survive all of it.** Session theft, which now has a partial answer in device bound session credentials. Recovery by persuasion, which has none and is the next lesson. Malware on your machine, which is Module 5.

That is the threat modeling method again: name what the control closes, then go and look at what is still open.

**Next lesson:** account recovery, which is the door every one of these controls sits beside.

---

## Knowledge Check

**Question 1.** What is wrong with the claim that multi-factor authentication prevents 99.9 percent of account compromise attacks?

- A) Nothing, it is a well supported figure from a large study
- B) The measurement was that 99.9 percent of compromised accounts did not have it enabled, which is a base rate rather than an efficacy figure
- C) The real figure is higher
- D) It applies only to enterprise accounts

**Answer: B.** The same vendor's later research gives defensible figures of roughly 99.2 percent risk reduction across the population, and about 98.6 percent for leaked credentials. Use those.

**Question 2.** Why can a single passkey count as two factors?

- A) Because the service prompts twice
- B) Because the credential is stored in two places
- C) Because it combines a possession factor with a local verification gesture, and one multi-factor authenticator can meet the assurance level on its own
- D) It cannot, a passkey is always a single factor

**Answer: C.** The caveat is that it depends on user verification being required at registration. Without it, the passkey is possession only.

**Question 3.** What do all methods below the phishing resistance line have in common?

- A) They all depend on a phone
- B) They all require an internet connection
- C) They are all free
- D) You hand over a value, and an attacker can relay it to the real site while it is still valid

**Answer: D.** The differences between them are real and they are differences in what else can go wrong, not in whether a replica page defeats them.

**Question 4.** Moving an account from text message codes to an authenticator application does not cross the phishing resistance line. Why is it still worth doing?

- A) It is not worth doing, only crossing the line matters
- B) It removes SIM swapping and network interception entirely, which are two real attacks needing no access to you at all
- C) Authenticator codes are longer
- D) Text message codes expire faster

**Answer: B.** SIM swapping needs access to a support agent at your carrier, and nothing else. This is the upgrade available on almost every account you hold.

**Question 5.** How does current NIST guidance treat one time codes sent by text message?

- A) As a restricted authenticator, removed from the methods that satisfy the second assurance level on their own
- B) As fully acceptable for all assurance levels
- C) As prohibited entirely
- D) As equivalent to a hardware security key

**Answer: A.** Continued use is permitted alongside a documented risk assessment, a migration plan, and notification to users.

**Question 6.** In the 2022 push fatigue breach, what step is usually left out of the retelling?

- A) The attacker had physical access to the office
- B) The notifications continued for several days
- C) The attacker contacted the target on a separate channel, posed as internal support, and said approving one request would stop the alerts
- D) The target had no multi-factor authentication enabled

**Answer: C.** Bombardment on its own usually fails. The pretext is what made it work, which is why "never approve an unrecognised request" does not hold: by that point the request had been explained.

**Question 7.** What does number matching on push notifications change?

- A) It makes push phishing resistant
- B) It encrypts the notification in transit
- C) It ties the notification to a specific device
- D) It closes the fatigue attack, because there is no reflex approval, but a relay page can still show you the number to type

**Answer: D.** National guidance describes it as an interim mitigation for organisations that cannot yet deploy phishing resistant methods.

**Question 8.** An account has a hardware key registered and text message codes still enabled as a fallback. How strong is it?

- A) It is a text message account, because the attacker chooses which method to use
- B) As strong as the hardware key
- C) Stronger than either alone, because there are two methods
- D) It depends which method you use most often

**Answer: A.** Adding a strong method does not raise the floor. Removal is the step that converts the work into security.

**Question 9.** What are device bound session credentials, and what problem do they address?

- A) A replacement for passkeys
- B) A way to store authenticator codes on a hardware key
- C) A method of binding a session to hardware on the machine, so a copied cookie expires quickly and cannot be renewed elsewhere
- D) A carrier level protection against SIM swapping

**Answer: C.** It reached general availability in Chrome in April 2026 and addresses local cookie theft. It does not close a relay that captures the session as it is created.

**Question 10.** Why should you enable alerts for authentication methods being added to your accounts?

- A) To track how many devices you use
- B) Because services charge for additional methods
- C) It is required by most services
- D) Because a method you did not add is a compromise in progress, and it is often the earliest signal you will get

**Answer: D.** Adding a method is what an attacker does to keep access after you change the password.

---

## Lab: The Audit, the Upgrade, and the Removal

**Time: 2 hours, plus one quarterly repeat.**

The point of this lab is not to enable more things. It is to find out what is actually enabled on your accounts right now, which is almost always different from what you remember, and then to remove what should not be there.

---

### Part 1: What Is Actually Enabled (30 minutes)

Go into the security settings of at least ten accounts and record what is really there. Do not fill this in from memory. The gap between memory and reality is the finding.

| Account | Methods enabled | Above or below the line | Where recovery leads |
|---|---|---|---|

**Then count three things:**

1. Accounts with a method above the line.
2. Accounts with text message codes still enabled, **including as a fallback beside something stronger.**
3. Accounts where you found a method you had forgotten about.

**Deliverable.** The table and the three counts.

---

### Part 2: The Free Upgrade (25 minutes)

For every account in Part 1 with text message codes and nothing above the line available, move it to an authenticator application.

| Account | Was | Now | Text message removed, or why not |
|---|---|---|---|

This is Figure 2's middle column. It does not cross the line and it removes two entire attacks per account.

**Deliverable.** The table, with at least three accounts upgraded, or a clear statement of why fewer were possible.

---

### Part 3: The Removal (30 minutes)

The highest weighted part, and the one that produces the actual security change.

For every account with something above the line, remove every method weaker than it. Where a service refuses to allow removal, record that instead, because a service that forces a weak fallback has told you something useful about itself.

**Before you start:** confirm you have a second way into each of these accounts and that you have tested it. This is not a formality.

| Account | Method removed | Or: why the service would not permit it |
|---|---|---|

**Deliverable.** The table, and one sentence on which account you were most reluctant to do this for.

---

### Part 4: Number Matching and Alerts (20 minutes)

- For every account using push approval, switch on number matching if the service offers it, and record which ones do not.
- For your five most important accounts, enable alerts for a method being added, a method being removed, and recovery codes being generated. Record which of the three each service actually supports.

**Deliverable.** Both lists, including the gaps. The gaps are the interesting part.

---

### Part 5: The Backup Question (20 minutes)

Answer this for real, not in principle: **if your phone stopped working in the next hour, which accounts could you not get into?**

| Account | Codes recoverable how? | Tested? |
|---|---|---|

Then pick one account and actually test it. Sign in using the recovery code or the second device rather than the primary method.

**Deliverable.** The table and the result of the one real test.

---

### Part 6: Compartments (15 minutes)

Look at your authenticator application and answer two questions.

1. **If somebody photographed this list, what would they learn about you?** Not the codes. The list of account names.
2. Does that list cross the compartments you set up in Lesson 2.5?

Then write what you would change. You do not have to make the change today.

**Deliverable.** Both answers and your proposed change.

---

### Submission

1. **The audit**, Part 1, with the three counts.
2. **Upgrades**, Part 2.
3. **Removals**, Part 3, including refusals.
4. **Number matching and alerts**, Part 4, including the gaps.
5. **Backup**, Part 5, with the one real test.
6. **Compartments**, Part 6.
7. **Reflection**, half a page: of the three bypasses in Figure 2 that this lab does not close, which one is most realistic for you, and what would actually address it?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| The audit | 20 | Checked rather than recalled, ten accounts, forgotten methods reported honestly |
| The free upgrade | 15 | Performed on the accounts where it applies |
| The removal | 30 | Actually performed, refusals recorded, and a second method confirmed first |
| Number matching and alerts | 10 | Enabled where available, gaps recorded |
| Backup | 15 | One test genuinely performed rather than assumed |
| Compartments | 5 | The account list examined as an intelligence product |
| Reflection | 5 | Identifies a real remaining exposure rather than restating the lesson |
| **Total** | **100** | |

**Note the weighting again.** Removal is worth double the upgrade, because an account with a strong method and a weak one still enabled has gained nothing at all.

---

## Additional Resources

### Primary Sources

- NIST SP 800-63B-4, Digital Identity Guidelines, on restricted authenticators: https://pages.nist.gov/800-63-4/sp800-63b.html
- Device Bound Session Credentials, W3C Web Application Security Working Group: https://github.com/w3c/webappsec-dbsc
- CISA, Implementing Number Matching in MFA Applications: https://www.cisa.gov/sites/default/files/publications/fact-sheet-implement-number-matching-in-mfa-applications-508c.pdf

### On the Statistics

- Microsoft Research, How effective is multifactor authentication at deterring cyberattacks: https://www.microsoft.com/en-us/research/publication/how-effective-is-multifactor-authentication-at-deterring-cyberattacks/

Read this rather than the 2019 blog post that the well known figure comes from. The methodology section is the point.

### Discussion Questions

1. Part 1 asked what is actually enabled on your accounts. How far was that from what you expected, and what does the gap suggest about auditing anything from memory?
2. Figure 2's middle column contains upgrades that do not cross the line. Does the tier list framing make people less likely to make them, and is that a fair criticism of it?
3. The 2022 case worked because the target was given an explanation that fitted what they could see. Design a version of that pretext aimed at you specifically. What would it say?

---

## References

1. [Microsoft Security Blog, One simple action you can take to prevent 99.9 percent of account attacks, August 2019](https://www.microsoft.com/en-us/security/blog/2019/08/20/one-simple-action-you-can-take-to-prevent-99-9-percent-of-account-attacks/)
2. [Cybersecurity Ventures, Multi-Factor Authentication Is (Not) 99 Percent Effective](https://cybersecurityventures.com/multi-factor-authentication-is-not-99-percent-effective/)
3. [Microsoft Research, How effective is multifactor authentication at deterring cyberattacks](https://www.microsoft.com/en-us/research/publication/how-effective-is-multifactor-authentication-at-deterring-cyberattacks/)
4. [Krebs on Security, Google: Security Keys Neutralized Employee Phishing, July 2018](https://krebsonsecurity.com/2018/07/google-security-keys-neutralized-employee-phishing/)
5. [NIST SP 800-63B-4, Digital Identity Guidelines, Authentication and Authenticator Management](https://pages.nist.gov/800-63-4/sp800-63b.html)
6. [Corbado, Are passkeys considered a form of two factor authentication](https://www.corbado.com/blog/psd2-passkeys/are-passkeys-two-factor-authentication)
7. [NIST SP 800-63B-4, on restricted authenticators and out of band authentication](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63B-4.pdf)
8. [CNBC, Hack of Jack Dorsey's Twitter account highlights SIM swapping threat, September 2019](https://www.cnbc.com/2019/09/06/hack-of-jack-dorseys-twitter-account-highlights-sim-swapping-threat.html)
9. [Help Net Security, To counter cookie theft, Chrome ships device bound session credentials, April 2026](https://www.helpnetsecurity.com/2026/04/10/google-chrome-device-bound-session-credentials/)
10. [W3C Web Application Security Working Group, Device Bound Session Credentials specification](https://github.com/w3c/webappsec-dbsc)
11. [Dark Reading, Uber: Lapsus$ Targeted External Contractor With MFA Bombing Attack](https://www.darkreading.com/cyberattacks-data-breaches/uber-breach-external-contractor-mfa-bombing-attack)
12. [BleepingComputer, Microsoft enforces number matching to fight MFA fatigue attacks, May 2023](https://www.bleepingcomputer.com/news/microsoft/microsoft-enforces-number-matching-to-fight-mfa-fatigue-attacks/)
13. [CISA, Implementing Number Matching in MFA Applications](https://www.cisa.gov/sites/default/files/publications/fact-sheet-implement-number-matching-in-mfa-applications-508c.pdf)
14. [SecurityWeek, Twilio Confirms Data Breach After Hackers Leak 33M Authy User Phone Numbers, July 2024](https://www.securityweek.com/twilio-confirms-data-breach-after-hackers-leak-33m-authy-user-phone-numbers/)
