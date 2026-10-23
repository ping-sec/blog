---
title: "iOS and Android: The Wrong Question, and Three Better Ones"
course: Digital Privacy for Ethical Hackers
module: 5
lesson: 5.1
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - mobile-privacy
  - ios
  - android
  - threat-modeling
  - opsec
---

# iOS and Android: The Wrong Question, and Three Better Ones

## About This Lesson

Module 4 ended on authentication. This module moves to the device that holds most of it, and it opens with the argument everybody has already had.

**"Which is more private, iOS or Android?"** is not a meaningless question. It is a low yield one. The two platforms have converged on almost everything the comparison charts measure: permission models, tracking prompts, on-device processing, and now even the length of the update window. Answering it changes your actual exposure less than any of three other questions, and those three are about the specific handset in your pocket rather than about a logo.

This lesson asks those three instead. One of them covers the single most likely serious event that will happen to your phone, and the source material for this lesson does not mention it at all.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Say what the platform comparison does and does not decide, and why it is the wrong first question.
- Find your device's end of support date and judge whether hardening it is worth the effort.
- Explain the difference between a phone that has been unlocked since it last booted and one that has not, and why that difference dominates the physical threat.
- Describe the two ways a court may treat a passcode differently from a fingerprint.
- Sort your own data into three tiers by who holds the key, and name the two settings that move the most.
- Explain why "we cannot read your data" is a claim about a product in a country, not a permanent property.

### Prerequisites

- Lesson 1.4, threat modeling. This lesson is an application of it.
- Lesson 1.3 on metadata, which is why photograph synchronisation gets its own line in Section 5.
- Module 4, particularly Lesson 4.4 on lockout, which Section 5 hands you again in a new form.

---

## 1. Why the Platform Question Yields So Little

![[module_5_lesson_1_wrong_question.svg]]

*Figure 1. Three questions about your handset, in the order worth asking them.*

Set out the case for the platform comparison fairly first, because parts of it are real.

**Apple's business does not run on advertising**, so it can afford privacy as a product feature, and it has shipped several. **Google's does**, with advertising supplying the large majority of revenue, which is a genuine structural conflict when the same company writes the operating system. That is a real difference in incentives and it is worth knowing.

**And then it stops explaining much.** Both platforms now have granular permissions, one-time grants, automatic revocation of permissions for unused applications, partial photograph access, and prompts before an application can track across others. Both flagship lines are now committed to seven years of updates. The gap that existed when this comparison was written has largely closed on the features the comparison measures.

**So the honest summary is shorter than the scorecard:**

- A well configured iPhone and a hardened Pixel are close together. Close enough that choosing between them is a preference rather than a security decision.
- An unpatched inexpensive handset is a long way below both, whichever logo is on it, and that comparison is the one the platform argument never runs.
- **Configuration moves you further than the platform does**, which is convenient, because configuration is the part you control today at no cost.

The rest of this lesson is the three questions that do the work.

---

## 2. Question One: How Long Is It Patched?

This is the largest difference between two handsets and it is almost never on the chart.

**At the top of the range it is now a tie.** Google committed to seven years of updates from the Pixel 8 generation, and Samsung to seven years from the Galaxy S24 generation. (Reference 1) That matches or exceeds Apple's practical record, which has historically run six to eight years without a formal published commitment. Pixel 6 and 7 were retroactively extended to five years in December 2024.

**Lower down the range it is not close at all.** Inexpensive handsets may receive one or two years, or nothing dependable. A device outside its support window accumulates publicly documented vulnerabilities indefinitely, and no privacy setting compensates for that.

**Which produces an uncomfortable but useful conclusion.** If your device is out of support, or within a year of it, the honest advice is not to spend an afternoon on privacy toggles. It is to plan a replacement. Hardening an unpatched device is arranging the furniture in a building with the front wall missing.

**Find the date before you change any other setting.** Both vendors publish end of support information per model. Write it down along with how long you have owned the device, because the question is not only how long the window is, it is how much of it you have already spent.

---

## 3. Question Two: Somebody Has It In Their Hand

The most likely serious event that will happen to your phone is that somebody else ends up holding it. Lost, stolen, seized at a border, or taken during an arrest.

**The source outline for this lesson does not cover this at all**, which is worth naming, because a guide that spends thirty minutes on advertising identifiers and none on physical possession has ranked threats by how much is written about them rather than by how likely they are.

![[module_5_lesson_1_somebody_has_it.svg]]

*Figure 2. Two states, and almost none of it depends on which platform you chose.*

### The Distinction That Dominates Everything Else

A phone is in one of two states, and the difference between them is larger than any setting in this lesson.

**Before first unlock.** Powered on, but not unlocked since it last booted. The keys protecting most user data have not been derived, so the data stays sealed. Current commercial extraction tooling largely cannot get past this state. Some system data and a few cached fragments remain reachable. (Reference 2)

**After first unlock.** Unlocked at least once since it last booted. Keys are resident in memory and far more of the device is reachable by somebody holding it.

**One unlock moves you from the first state to the second, and it stays there.** Locking the screen does not move it back. A phone you locked and pocketed is a phone that was unlocked this morning, and it is in the second state.

**Only a power off or a reboot returns it to the first.** Current iPhone software does this automatically after 72 hours without a successful unlock, managed by the secure processor, and it cannot be defeated by airplane mode, shielding, or keeping the device on a charger. (Reference 3) That feature exists precisely because the window between seizure and extraction is where the outcome is decided.

### The Three Things To Do About It

**Turn it off, deliberately.** This is the entire control and it takes three seconds. Power off before a border crossing, a protest, a search, or anywhere the phone might leave your hand. Learn the shortcut on your own device now, so you can do it in a pocket, without looking, under stress.

**Know what unlocks it.** A face or a fingerprint is convenient and can be applied to you while somebody holds you still.

In the United States, courts treat a passcode as protected testimonial speech with reasonable consistency, and are split on biometrics. A federal appeals court held in April 2024 that compelling a fingerprint did not violate the Fifth Amendment, treating it as a physical act. A different federal appeals court held in January 2025 that it could, because the act may communicate control over the device's contents. The Supreme Court has not resolved the split, and state courts diverge as well. (Reference 4)

**The practical consequence, whichever way that eventually settles, is that you should know how to disable biometrics on your own device in one gesture.** Both platforms have one. Find it and practise it.

**Lockdown Mode, if it fits your situation.** A built in hardening setting that switches off features known to carry targeted spyware, at the cost of some conveniences and nothing else. Apple stated in March 2026 that it is aware of no successful mercenary spyware attack against a device with it enabled, a claim that has now held for four years against the best funded attackers in this field. (Reference 5)

That is an unusually strong claim and worth taking seriously if you are in a category that gets targeted: journalists, activists, and people working on politically sensitive material. Citizen Lab forensically confirmed in 2025 that journalists who received threat notifications had been targeted with commercial spyware, including through an attack requiring no interaction at all. (Reference 6)

**Everything on this list is free, takes minutes, and works the same on both platforms.** Do it before you touch a single privacy toggle.

---

## 4. Question Three: What Leaves By Default

Your handset is largely a client for a cloud account. What synchronises, and under whose key, decides what a legal request or a breach actually reaches.

![[module_5_lesson_1_who_holds_the_key.svg]]

*Figure 3. Three tiers, and the middle one is where most people's data actually sits.*

**Tier one: it never leaves.** Nothing to request, nothing to breach, nothing to hand over. The cost is that a lost device is a lost copy, so this tier needs its own backup discipline: an encrypted local backup that you have actually restored from at least once.

**Tier two: synchronised, and the provider can decrypt it.** This is the default on both platforms for most categories, and it is where the great majority of people's photographs, messages and backups live. It is reachable three ways: a lawful request, a breach of the provider, and a change of policy you were not consulted about.

**One consequence that catches people out.** A backup in this tier can expose a conversation that was end to end encrypted in transit, because the backup holds the plain text. Lesson 3.1 made this argument about messaging. This is the same argument arriving through the device.

**Tier three: synchronised, and only you can decrypt it.** Available on both platforms as an option, and switched off by default on the one where it is most discussed. It moves most of tier two up here in a single setting.

**And it hands you the lockout problem from Lesson 4.4 in exchange**, because if only you hold the key then nobody can recover it for you. That is a trade worth making deliberately, with a recovery key printed and stored, rather than switching on because a guide said to.

### The Part That Makes This a Jurisdiction Question

Tier three is not a permanent property of a product, and there is a current example.

In February 2025, Apple withdrew Advanced Data Protection for users in the United Kingdom rather than comply with a government demand for access. New users there cannot enable it, and existing users were required to switch it off. (Reference 7) As of September 2026 it has still not been restored. The original worldwide demand was withdrawn in August 2025, a narrower notice aimed at UK users replaced it, and the matter remains in litigation more than eighteen months after the withdrawal. (Reference 8)

**This is not an argument about one company**, and it is not a reason to prefer the other platform, which is subject to the same legal instruments. It is the point that matters for your threat model: **"we cannot read your data" is a statement about the current product, in your current country, under current law.** Treat it as a configuration that can change rather than a property that cannot.

### The Two Settings That Move The Most

- **The device backup.** The single largest object you own, and it usually contains everything else including message histories. Establish which tier it is in before anything else.
- **Photograph synchronisation.** The largest volume, the richest metadata, and the category people most often forget is switched on. Lesson 1.3 covered why photographs carry more than their contents.

---

## 5. Corrections to the Standard Advice

Several widely repeated instructions in mobile privacy guidance are out of date. These matter because following a stale checklist produces the feeling of having done something.

**"Google scans your Gmail for advertising."** Google stopped using consumer Gmail content for advertising personalisation in 2017. (Reference 9) It still processes mail for product features, so "Google reads your email" is not simply false, but the specific ad scanning claim has been wrong for nine years and repeating it costs you credibility with anyone who knows.

**"On-device photo scanning is an iOS privacy feature."** This refers to a content detection system Apple announced in 2021 and **abandoned in December 2022** after sustained criticism from security researchers. (Reference 10) It is not a privacy feature and it does not exist. A guide listing it as a strength has not been revised in four years.

**"Turn on Do Not Track."** The specification was deprecated in 2018, it is ignored by essentially every advertiser including the largest, and Safari and Firefox have removed it. Global Privacy Control is the successor, and unlike its predecessor it has explicit legal effect under some privacy legislation. (Reference 11) Enabling Do Not Track is a placebo.

**"All iOS browsers use WebKit."** True everywhere except the European Union, where alternative engines have been permitted since iOS 17.4 in March 2024. In practice no vendor has shipped one yet, and the UK competition regulator has ruled the restriction unlawful with compliance required by January 2027. (Reference 12) So the claim is currently true in most places and is on its way out.

**"Disable Find My Device for privacy."** For most people this is bad advice. Remote location and remote wipe are genuine security controls against the physical threat in Section 3, and losing them to avoid a location record you have already granted in a dozen other places is a poor trade. If your threat model genuinely includes the platform vendor, that conclusion changes, and you should reach it deliberately.

**"Private Relay is similar to a VPN."** It is not. It covers Safari browsing, DNS queries, and some unencrypted application traffic. It does not cover traffic from other applications. Describing it as a VPN leads people to believe they are covered when they are not.

**"Airplane mode by default" for high threat users.** This is largely theatre. The useful version of this advice is to leave the phone somewhere else, which is inconvenient and actually works.

---

## 6. What Has Genuinely Improved

Two features worth knowing that postdate most of the guidance you will read.

**Private Space on Android**, introduced in Android 15, creates a separate profile on the device for sensitive applications, behind its own authentication. When locked, those applications and their notifications do not appear in the launcher, in recent applications, or in system settings. It can use a separate account. Android 16 added the ability to move files into it. (Reference 13)

**This is the compartmentalization argument from Lesson 2.5 delivered as an operating system feature**, and it is the most useful thing to happen to mobile privacy in several years for the way this course thinks about the problem. If you keep separate identities, this is where the separation can live on a phone.

**Inactivity reboot on iOS**, covered in Section 3, which turns the physical threat model from something you have to remember into something the device handles.

---

## 7. Choosing, By Threat Model

The recommendation is thinner than the usual one, because most of the decision is not the platform.

**Most people, including most security professionals.** Either platform, current, in support, configured. Spend the effort on Section 3 and Section 4 rather than on switching. If you are buying, buy something with a long remaining support window, which now includes options on both sides.

**If you handle client systems or engagement material.** The same, plus separation. A separate device is cleanest. Private Space, or a work profile, is the realistic version for most people. This is Lesson 2.5 applied to the object you carry everywhere.

**If you are plausibly individually targeted.** This is where the platforms genuinely diverge, and in two directions.

- Lockdown Mode has an unusually strong empirical record, described in Section 3, and it is available today with one setting.
- A hardened Android distribution removes the vendor services entirely and offers controls that stock platforms do not. **Lesson 5.3 covers that properly**, and this lesson deliberately does not attempt to summarise it.

The honest position is that both are defensible, that the choice depends on which trust you are less willing to extend, and that anybody who tells you the answer is obvious has not thought about it for long.

**One thing that is not a threat model.** "I have nothing to hide, so it does not matter" is a statement about content. Everything in Section 4 is about who holds a copy, and everything in Section 3 is about what happens when you are not holding the device. Neither depends on what is in the photographs.

---

## 8. What to Take From This

**The platform question is the argument, not the work.** The two are close enough at the top of the range that the choice is a preference.

**Find the support date first.** It may tell you that the rest of the effort belongs on a different device.

**The physical case is the likely one**, it is nearly platform independent, and three free actions cover most of it: power it off deliberately, know how to disable biometrics fast, and enable Lockdown Mode if you are in a targeted category.

**Sort your data into the three tiers.** The backup and the photographs move the most, and most people find the tiers are not where they assumed.

**And treat vendor privacy promises as configurations rather than properties.** The strongest option on one platform has been unavailable in one country for eighteen months, by government demand, with the argument still running. That is the shape of this problem generally.

**Next lesson:** application permissions, where the model has improved considerably and the remaining gaps are specific.

---

## Knowledge Check

**Question 1.** Why is "which platform is more private" described as a low yield question?

- A) Because privacy cannot be measured
- B) Because the platforms have converged on nearly everything the comparison measures, so the answer changes your exposure less than three other questions
- C) Because both platforms are equally bad
- D) Because privacy depends only on which applications you install

**Answer: B.** A well configured iPhone and a hardened Pixel are close together. An unpatched inexpensive handset is a long way below both, and that comparison never gets run.

**Question 2.** What is the current state of long term update commitments at the flagship tier?

- A) Apple offers substantially longer support than any Android vendor
- B) No vendor publishes a commitment
- C) Android vendors offer at most three years
- D) Current flagship Pixel and Galaxy lines are committed to seven years, matching or exceeding Apple's practical record

**Answer: D.** The gap is at the inexpensive end of the range, where devices may get one or two years or nothing dependable, and no privacy setting compensates for being out of support.

**Question 3.** What is the difference between a phone that has been unlocked since it last booted and one that has not?

- A) Battery consumption differs significantly
- B) There is no meaningful difference once the screen is locked
- C) In the unlocked state the keys are resident in memory and far more is reachable, and locking the screen does not leave that state
- D) The difference applies only to Android devices

**Answer: C.** Only a power off or a reboot returns the device to the sealed state, which is why powering it off deliberately is the whole control.

**Question 4.** Current iPhone software reboots a device automatically after 72 hours without a successful unlock. Why?

- A) To install pending updates
- B) To clear memory leaks and improve performance
- C) To force the user to re-authenticate for billing purposes
- D) To return it to the sealed state, closing the window in which extraction tooling is most effective

**Answer: D.** It is managed by the secure processor and is not defeated by airplane mode, shielding, or keeping the device charged.

**Question 5.** How do United States courts currently treat passcodes compared with biometrics?

- A) Passcodes are treated as protected testimonial speech with reasonable consistency, while the courts are split on biometrics and the split is unresolved
- B) Both are uniformly protected
- C) Both may be compelled without restriction
- D) Biometrics are uniformly protected and passcodes are not

**Answer: A.** One federal appeals court held in 2024 that a fingerprint could be compelled; another held in 2025 that it could not. Know how to disable biometrics on your device in one gesture.

**Question 6.** What is the significance of Apple's Lockdown Mode record?

- A) It has never been tested against real attacks
- B) Apple stated in March 2026 that it is aware of no successful mercenary spyware attack against a device with it enabled, over four years
- C) It blocks all network traffic when enabled
- D) It is only available to enterprise customers

**Answer: B.** That is an unusually strong claim, and it matters most for categories that actually get targeted, which independent researchers confirmed in 2025 includes journalists.

**Question 7.** A message was end to end encrypted in transit. Why might it still be readable by a provider?

- A) End to end encryption is decorative
- B) Providers hold a master key to all conversations
- C) Because the device backup can hold the plain text, and the backup may sit in a tier the provider can decrypt
- D) Because encryption keys expire after ninety days

**Answer: C.** This is the messaging argument from Lesson 3.1 arriving through the device rather than through the application.

**Question 8.** What does the withdrawal of Advanced Data Protection in one country demonstrate?

- A) That the encryption was broken
- B) That the other platform is a safer choice
- C) That the feature was never genuinely end to end encrypted
- D) That "we cannot read your data" is a statement about a current product in a current country under current law, rather than a permanent property

**Answer: D.** It was withdrawn in February 2025, remains unavailable in September 2026, and the matter is still in litigation. The other platform is subject to the same legal instruments.

**Question 9.** Which of these pieces of standard advice is still current?

- A) Enable Do Not Track in your browser
- B) On-device photo scanning is an iOS privacy feature
- C) Establish which tier your device backup sits in
- D) Google scans consumer Gmail content for advertising personalisation

**Answer: C.** Do Not Track was deprecated in 2018 and removed from two major browsers. The photo scanning system was abandoned in December 2022. Gmail ad scanning stopped in 2017.

**Question 10.** What does Private Space on Android provide?

- A) A separate profile behind its own authentication, whose applications and notifications are invisible when locked
- B) Encrypted storage for photographs only
- C) A built in virtual private network
- D) An anonymous browsing mode

**Answer: A.** Introduced in Android 15, it is the compartmentalization argument from Lesson 2.5 delivered as an operating system feature.

---

## Lab: Three Questions, Asked About Your Own Phone

**Time: 2 hours.**

Structured as the three questions, in order, because the first may change how much of the rest is worth doing.

---

### Part 1: The Support Window (20 minutes)

| | |
|---|---|
| Device and model | |
| Current OS version and security patch level | |
| Vendor's published end of support date | |
| Date you acquired it | |
| Years of support remaining | |

Then answer: **is this device worth hardening, or is the honest answer that it needs replacing?**

**Deliverable.** The table and your answer. A genuine "this device is out of support" is a complete and correct result for this part, and the rest of the lab is then a plan for the next device.

---

### Part 2: The Physical Case (30 minutes)

Practical, not written.

1. **Find and practise the power off gesture** on your device until you can do it without looking. Time yourself.
2. **Find and practise the gesture that disables biometrics immediately.** Both platforms have one. Confirm it worked by trying to unlock with your face or finger.
3. **Reboot the device**, then observe what is available before you unlock it: what notifications show content, what widgets render, whether any application data is reachable from the lock screen.
4. **Review the lock screen** for what it exposes to somebody holding the phone but not unlocking it. Notification previews, reply from the lock screen, the control centre, the wallet.

| Item | Result | Changed anything? |
|---|---|---|
| Power off gesture, time taken | | |
| Biometric disable gesture | | |
| What is visible before first unlock | | |
| What the lock screen exposes | | |

**Deliverable.** The table, and one sentence on what surprised you in step three or four.

---

### Part 3: The Tier Sort (35 minutes)

List every significant category of data on your phone and place each in a tier from Figure 3.

| Category | Tier now | Tier you want | Setting that moves it |
|---|---|---|---|
| Device backup | | | |
| Photographs | | | |
| Messages | | | |
| Contacts | | | |
| Files and documents | | | |
| Health data | | | |
| Notes | | | |

**Then answer two questions.**

1. Was anything in a different tier from where you assumed? Which?
2. If you move something to tier three, **what is your recovery plan if you lose the key?** Lesson 4.4 applies here in full, and the answer "I will remember it" is not one.

**Deliverable.** The table and both answers.

---

### Part 4: The Checklist Audit (20 minutes)

Find any published mobile privacy checklist. Any one. Work through it and mark each instruction:

- **Current**, still correct.
- **Stale**, refers to something that has changed or no longer exists.
- **Placebo**, does nothing measurable.

| Instruction | Verdict | Evidence |
|---|---|---|

Section 5 gives you six examples to calibrate against. Find at least three of your own.

**Deliverable.** The table with at least ten instructions assessed, and a note of the checklist's publication date if you can find one.

**Why this part exists.** You will write guidance of this kind for your blog and for clients. Auditing somebody else's is the fastest way to learn what makes yours age badly, and the answer is usually a specific product feature stated as a permanent fact.

---

### Part 5: Separation (15 minutes)

Answer for your actual situation.

1. Do work and personal contexts share this device? List three specific things that cross.
2. Does your platform offer Private Space, a work profile, or an equivalent? Establish it from documentation.
3. If you set one up, what would go in it, and what would stop you?

**Deliverable.** All three answers. Setting it up is optional in this lab and covered properly in Lesson 5.4.

---

### Submission

1. **Support window**, Part 1, with your judgement.
2. **Physical case**, Part 2, with timings.
3. **Tier sort**, Part 3, with the recovery answer.
4. **Checklist audit**, Part 4, ten instructions.
5. **Separation**, Part 5.
6. **Reflection**, half a page: before this lesson, how much of your mobile privacy effort was aimed at the advertising threat, and how much at the physical one? Was that proportion right for your situation?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Support window | 15 | Found from the vendor, not guessed, with an honest judgement about the device |
| Physical case | 30 | Gestures actually practised, pre-unlock state genuinely observed |
| Tier sort | 25 | Checked in settings rather than assumed, and the recovery question answered concretely |
| Checklist audit | 20 | Ten instructions assessed, at least three stale or placebo items found independently |
| Separation | 5 | Specific crossings named rather than a general answer |
| Reflection | 5 | Honest about the proportion, rather than restating the lesson |
| **Total** | **100** | |

**Note the weighting.** The physical case carries the most marks and it is the part the source material omits entirely. It is also the only part of this lab that will matter to you on a specific day, at short notice, with no time to look anything up.

---

## Additional Resources

### Primary Sources

- Apple, About threat notifications and protecting against mercenary spyware: https://support.apple.com/en-us/102174
- Apple Support, on Advanced Data Protection availability in the United Kingdom: https://support.apple.com/en-us/122234
- Android Open Source Project, Private Space: https://source.android.com/docs/security/features/private-space

### On the Physical Case

- Magnet Forensics, on the security impact of the iOS inactivity reboot: https://www.magnetforensics.com/blog/understanding-the-security-impacts-of-ios-18s-inactivity-reboot/

Read this one from the perspective it is written from. Material written for forensic examiners tells you what actually works against a device far more precisely than material written for consumers.

### Discussion Questions

1. Part 4 asked you to audit a published checklist. What made its stale entries stale, and how would you write the same advice so it ages better?
2. Section 4 argued that a vendor's encryption promise is a configuration rather than a property. Does that change which platform you would recommend to a client, or only what you would tell them about it?
3. The physical threat is more likely than the targeting threat for almost everybody, and gets a fraction of the coverage. Why do you think that is, and does the same imbalance appear in your own writing?

---

## References

1. [TechRadar, how many updates does your phone have left, on Apple, Samsung and Pixel support windows](https://www.techradar.com/phones/how-many-updates-does-your-phone-have-left-the-longevity-of-apple-samsung-pixel-and-more-phones-explained)
2. [Cellebrite device states, on before and after first unlock and what each yields](https://voodootomato.medium.com/afu-vs-bfu-understanding-device-states-in-cellebrite-inseyets-and-why-the-72-hour-clock-changes-bd02fa603fd1)
3. [Magnet Forensics, understanding the security impacts of the iOS inactivity reboot](https://www.magnetforensics.com/blog/understanding-the-security-impacts-of-ios-18s-inactivity-reboot/)
4. [Center for Democracy and Technology, on the circuit split over compelled biometric unlocking](https://cdt.org/insights/circuit-court-split-lays-the-groundwork-for-scotus-case-on-biometric-cell-phone-unlocking/)
5. [Apple, About Apple threat notifications and protecting against mercenary spyware](https://support.apple.com/en-us/102174)
6. [Freedom of the Press Foundation, on Apple's mercenary spyware notifications and confirmed journalist targeting](https://freedom.press/digisec/blog/apple-mass-alerts-users-to-mercenary-spyware/)
7. [Apple Support, Advanced Data Protection is no longer available to new users in the United Kingdom](https://support.apple.com/en-us/122234)
8. [CNN, UK users are losing a key Apple security feature, February 2025](https://www.cnn.com/2025/02/25/tech/apple-advanced-data-protection-uk-encryption)
9. [TIME, Google's Gmail will no longer scan emails for advertisements, 2017](https://time.com/4831200/google-gmail-ads-advertising-email/)
10. [MacRumors, Apple abandons controversial plans to detect known CSAM in iCloud Photos, December 2022](https://www.macrumors.com/2022/12/07/apple-abandons-icloud-csam-detection/)
11. [Do Not Track, deprecation and the move to Global Privacy Control](https://en.wikipedia.org/wiki/Do_Not_Track)
12. [Open Web Advocacy, on the browser engine restriction and its regulatory status](https://open-web-advocacy.org/blog/apples-browser-engine-ban-persists-even-under-the-dma/)
13. [Android Open Source Project, Private Space](https://source.android.com/docs/security/features/private-space)
