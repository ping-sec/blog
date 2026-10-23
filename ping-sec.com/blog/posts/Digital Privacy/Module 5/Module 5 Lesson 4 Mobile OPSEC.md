---
title: "Mobile OPSEC: The Boring Column First"
course: Digital Privacy for Ethical Hackers
module: 5
lesson: 5.4
format: article
reading_time: 32 minutes
tags:
  - digital-privacy-course
  - privacy
  - mobile-privacy
  - opsec
  - compartmentalization
  - travel
  - incident-response
---

# Mobile OPSEC: The Boring Column First

## About This Lesson

Material on this subject has a reliable shape. It opens by telling you your phone is a high value target, sketches five escalating threat levels, and then spends most of its length on level four: burner phones bought with cash, Faraday bags, dead drops, counter-surveillance awareness, a lawyer on speed dial.

**That material is not wrong, and it is badly proportioned.** The failures that actually happen to working security professionals are mundane, require no attacker at all, and get a footnote. Meanwhile the advanced advice is incomplete in ways that would fail somebody who genuinely needed it, which is worse than not giving it.

So this lesson does the boring column first and at length, then covers the dramatic column accurately rather than dramatically, and says plainly where the honest answer is "this is beyond what a lesson can give you."

### Learning Objectives

After you read this lesson, you can do these tasks:

- Name the four mundane failures that account for most real mobile OPSEC incidents, and close them.
- Explain why compartmentalization is the single highest yield control, and implement it without a second phone.
- State what actually defeats a burner phone, including the two mechanisms most guidance omits.
- Distinguish a basic border search from an advanced one, and say what your status changes.
- Use the current built-in protections against cellular interception rather than obsolete tooling.
- Write a mobile procedure you will follow, rather than a ten section document you will not.

### Prerequisites

- All three previous Module 5 lessons. This one assembles them.
- Lesson 2.5 on compartmentalization, which is the backbone of Section 2.
- Lesson 1.3 on metadata, which Section 2 applies to photographs taken at work.

---

## 1. Two Columns

![[module_5_lesson_4_where_it_fails.svg]]

*Figure 1. Both columns are real. Only one is likely to happen to you this year.*

**The left column is what the guidance covers.** Fake cell towers, brief physical access by a stranger, forced unlock at a border, targeted surveillance. All real. Sections 4 and 5 handle them, accurately.

**The right column is where it actually fails**, and none of these need an attacker:

- **A screenshot of a finding, in a synchronised library.** You photograph a console, a network diagram, a whiteboard at a client site. The photo library uploads it. That is Lesson 5.1 Tier 2, now holding client data.
- **Client contacts in a personal address book**, which synchronises, and which you granted to applications years ago. Their names, roles and numbers, and they were not asked.
- **A work account on a device the household uses.** Notifications on a lock screen in a shared room. A tablet signed into the same account.
- **The engagement itself, in photograph metadata.** Coordinates and timestamps recording which organisation hired you and when.

**Every one of those is a confidentiality breach**, and a confidentiality clause does not distinguish between a breach and a synchronisation setting.

**Why the right column gets a footnote.** It requires no attacker, so it does not read as a threat. The consequences are contractual rather than technical, so they do not read as security. And it is boring, which is the actual reason: nobody writes an exciting section about turning off photo sync on a work phone.

---

## 2. Separation, Which Is Most of the Job

If you do one thing from this lesson: **separate the thing that touches client work from the thing that holds your life.** That single change closes most of the right column, and no amount of counter-surveillance technique closes any of it.

### Three Ways to Do It, in Increasing Cost

**A separate profile or private space.** Lesson 5.1 covered the feature that arrived in Android 15: a separate profile behind its own authentication, whose applications and notifications are invisible when locked, and which can use a separate account. This is free, it is on the device you already own, and for most people it is enough.

**A separate device.** Cleanest, and it costs money and pocket space. Worth it if you handle client material regularly, and Figure 2 explains what a second device is genuinely for.

**A separate identity.** Different email, different number, different accounts. Lesson 2.5's argument, applied to work.

### What Actually Has to Be On the Work Side

Work them out explicitly rather than by drift, because drift is how the right column happens.

- **The camera roll.** This is the big one. Either the work profile has its own camera and no cloud sync, or you accept that everything you photograph at a client site goes to a consumer cloud account. There is no third option and most people have never made the choice.
- **Contacts.** Client contacts stay on the work side. This protects them rather than you, which is why it gets skipped.
- **Messaging.** A separate account for client communication, so a personal device compromise is not a client communication compromise.
- **Notes and credentials.** Covered by Module 4. The vault on the work side, or a separate vault.

### The Test

**Hand your personal phone to somebody for ten minutes. What client material could they find?** If the answer is anything at all, the separation is not done. Run the same test on the work device and ask what personal material is on it, because contamination runs both ways and the second direction is the one that catches people out during an investigation.

---

## 3. Physical Possession, Briefly

Lesson 5.1 Figure 2 covered this properly and it does not need repeating. Three reminders because they belong in a procedure:

**Power the device off when you expect it to leave your hand.** Before a border, a search, a client site where it will sit in a locker. Three seconds, and it is the single most effective physical control available.

**Know the gesture that disables biometrics instantly.** Both platforms have one. Practise it.

**The legal position on compelled unlock is not settled.** Lesson 5.1 has the detail: passcodes are treated as protected testimonial speech with reasonable consistency in the United States, and the courts are split on biometrics, with one federal appeals court holding in 2024 that a fingerprint could be compelled and another holding in 2025 that it could not. Guidance stating flatly that biometrics can be compelled and passcodes cannot is describing a tendency, not a rule, and the tendency varies by jurisdiction.

**On the evil maid ritual.** Tape over the ports and tamper-evident seals are cheap and mostly theatre against a competent adversary. The controls that actually work are the ones from Lesson 5.1 and 5.3: verified boot, a locked bootloader, the sealed state, and on a hardened system the option to refuse new port connections once locked.

**And on device disposal.** The elaborate ritual of overwriting, repeated resets and physical destruction is inherited from spinning disk thinking. A modern phone is encrypted, and a factory reset destroys the key, which makes the data unrecoverable in one step. Destroy the device if you want to, and understand that you are buying reassurance rather than security.

---

## 4. The Burner Phone, Honestly

This section exists because the standard advice is incomplete in a way that matters.

![[module_5_lesson_4_burner_honestly.svg]]

*Figure 2. Four things that defeat it, and two of them are usually missing.*

### What Defeats It

**One: in most of the world you cannot buy one.** More than 155 countries now require identity documents to register a prepaid subscriber module, and some require biometrics. (Reference 1) A handful do not, and they are the countries this advice was written in. The guidance almost never says so, which means a reader outside those countries is planning around something unavailable to them.

**Two: the purchase is recorded regardless.** Cash removes the payment record and nothing else. The shop has cameras, the till has a timestamp, and the device has a serial tied to that consignment. That links a face and a minute to a device, later, if anybody looks.

**Three, and this is the important one: your pattern, not your name.** A second device that travels your routes at your hours is linked to you by shape. **"Never powered on at the same time" is not the same as "never in the same places."** Investigators combine call detail records, tower dumps and movement patterns precisely to find devices that move together, and research on mobile network data finds that most pairs of people who call each other have shared a tower. (Reference 2) The usual instruction addresses simultaneity and leaves correlation over weeks entirely open.

**Four: one slip ends it permanently.** One power-on at home. One call to a number you have called before. One connection to a network you use. The record is permanent, and you will not know you made the mistake.

### What a Second Device Is Actually For

**Compartmentalization.** A device that holds client work and nothing else. This works, it is boring, and it is the actual use for almost everybody reading this.

**Travel with reduced exposure.** A device carrying nothing sensitive across a border, so a search finds nothing. Section 5.

**A clean device for an untrusted environment.** Conference networks, client sites, anywhere you would rather not connect your real phone.

### What It Is Not For

**Genuine anonymity against a capable investigator.** Not because the technique is wrong, but because it has to be executed perfectly, over a long period, by somebody who has never done it before, in a country whose registration rules the guidance did not mention.

**Anybody whose safety depends on that needs a specialist and a lawyer, not a checklist.** Saying otherwise is the characteristic failure of this entire topic, and it is a failure with consequences: a person who believes a lesson made them anonymous behaves as though they are.

Use a second device for compartmentalization. Do not confuse that with anonymity, and do not let anything convince you that you achieved anonymity because you paid cash.

---

## 5. The Border, With the Distinctions Kept In

Most guidance says assume it will be searched and bring a clean device. Correct, and it collapses two distinctions that decide what actually happens to you.

![[module_5_lesson_4_the_border.svg]]

*Figure 3. Two search types, two statuses, one reliable preparation.*

### Two Kinds of Search

**A basic search** is an officer reviewing the device by hand: scrolling, opening applications, reading what is there. No external equipment. Permitted at the officer's discretion, with or without any suspicion. **This is the one that happens.**

**An advanced search** attaches external equipment, not merely to unlock the device but to review, copy or analyse its contents. Under the United States directive updated in January 2026, it requires supervisory approval together with reasonable suspicion of a violation, or a national security concern. (Reference 3) Far rarer, and the outcome is a copy of the device rather than a look through it.

### The Distinction Most Guidance Collapses

**If you are a citizen of the country you are entering**, you cannot be refused entry for declining to unlock a device. You can be delayed, questioned at length, and **the device itself can be detained**, sometimes for weeks. (Reference 4)

**If you are a visitor or a visa holder**, refusal can mean refusal of entry, and that decision is largely unreviewable at the point it is made.

**The calculation is completely different, and the script version of this advice flattens the two.** Know which you are before you decide what to do at the desk.

### The Preparation That Works

**Do not have it on the device.** Not hidden, not encrypted, not in a container. Absent. Retrieve it after arrival from somewhere that needs credentials you carry in your head.

**Power the device off before you reach the desk.** Lesson 5.1 Figure 2: the sealed state is what an advanced search runs into, and it takes three seconds.

**Two things that make it worse:**

- **Do not rely on hidden volumes or deniable storage.** Examiners know which tools support them, so denial invites escalation rather than ending it. The source material recommends this technique for high-risk scenarios without the caveat, and the caveat is the important half.
- **Do not make false statements to an officer.** That is a separate offence in most jurisdictions, and a considerably worse one than whatever was on the phone.

Under the 2026 directive you should also be told the purpose and authority for the search and how to raise a concern afterwards. (Reference 3)

**And one thing nobody mentions.** If you carry client material under a confidentiality clause, ask the client what they expect at a border **before** you travel. Some have a policy. Discovering that afterwards is a bad conversation.

Rules and practice vary by country and change. Check the current position for your route and your status rather than trusting any lesson, including this one.

---

## 6. Cellular Interception, Currently

The source material recommends a root-requiring detection application from an earlier era. There is now a better answer built into the platform.

**Android 16 added a mobile network security section**, under the safety centre. It provides notifications when the device connects to an unencrypted network or when a network requests the device's unique identifiers, a toggle to disable 2G entirely, and protection against null cipher connections which arrived in Android 14. Google reported the notification feature detecting about 95 percent of simulated interception attacks on compatible devices. (Reference 5)

**Two caveats.** The features need modem support, so availability varies by device, and 2G disabling is the most widely available of the three. And detection is not prevention: knowing you connected to something suspicious is useful, and it does not undo the connection.

**The practical position is unchanged and simpler than the tooling suggests.** Interception of the cellular link is defeated by encrypting above it, which you already do by using an end to end encrypted messenger. Disable 2G, turn on the notifications if your device offers them, and stop worrying about it. The residual risk is metadata and location, and that is not solved by anything in this section.

---

## 7. Corrections to Communication Advice

Several entries in the standard list are wrong or dated, and the errors matter because people repeat them.

**Video calls on the built-in Apple application are end to end encrypted.** The source material lists it under "do not use, the vendor can access." That is incorrect. The audio and video are end to end encrypted and the vendor cannot decrypt them; what it retains is limited call invitation records for a short period. (Reference 6) Criticise it for the metadata if you like. Do not claim the content is readable.

**"Always use a VPN on public wireless" is 2015 advice.** Nearly all traffic is encrypted in transit now, so the coffee shop interception threat that justified this is largely historical. The honest modern case for a VPN is that it hides your traffic from the network operator and shifts that visibility to the VPN provider instead. That is sometimes a good trade and sometimes not, and it deserves to be argued rather than asserted.

**A VPN does not provide location privacy.** It hides your network address. Your carrier still knows which tower you are on, and Lesson 5.2 established that the advertising supply chain does not need your address to place you. Guidance that lists a VPN under location privacy is confusing two different things.

**On metadata minimisation**, the substance is right and Lesson 3.4 covered it properly. The one addition worth making: **varying your communication patterns is advice almost nobody can follow**, because you cannot behave unpredictably on purpose for months. Separate accounts and separate devices are structural and they work while you are tired. Behavioural discipline is not, and it does not.

---

## 8. A Procedure You Will Actually Follow

The source material ends with a ten section standard operating procedure covering device setup, compartmentalization, communication, network, physical, location, data, incident response, travel and review.

**Nobody maintains that document.** It gets written once, filed, and never opened, which is worse than not having it because it produces the feeling of having a procedure.

Here is the version that survives contact with a busy quarter.

### One Page, Four Questions

**1. What is on the work side, and what is on the personal side?** One list each. The camera roll and the contacts are the two that decide most of it.

**2. What do I do before a device leaves my hand?** Power off. Two words, and the most valuable line on the page.

**3. What do I do if it is lost or seized?** Who to tell, in what order, and how fast. If a client is involved, their notification clock probably started before yours did.

**4. When do I check this?** One calendar entry. Quarterly. Fifteen minutes.

### The Quarterly Fifteen Minutes

- Is anything from the work side now on the personal side? Check the camera roll specifically.
- Has anything new been installed on the work device, and does it need to be there?
- Is the separation still holding, or has convenience eroded it? It usually has.
- One item removed. There is always one.

**That is the whole procedure.** It fits on a page, it takes fifteen minutes a quarter, and unlike the ten section version you will actually do it.

---

## 9. What to Take From This

**The boring column is where it fails.** Client material on a personal device, synchronised somewhere you did not intend, without anybody attacking anything.

**Separation is most of the job**, and a separate profile on the phone you already own does most of it for free.

**A second device is for compartmentalization, not anonymity.** The gap between those is where people get hurt, and it is exactly the gap this kind of material blurs.

**At a border, your status changes the calculation completely**, and the only preparation that reliably works is not having the material with you.

**And write the one page version.** A procedure you follow beats a procedure you wrote.

**This concludes Module 5.** Lesson 5.1 established what actually decides mobile exposure, 5.2 showed that permissions are one channel of three, 5.3 covered what a hardened system does and what changed underneath that category, and this lesson is the operational layer over all of it.

---

## Knowledge Check

**Question 1.** Which of these accounts for most real mobile OPSEC failures among working security professionals?

- A) Targeted surveillance by capable adversaries
- B) Client material reaching a personal device or a synchronised cloud account, with no attacker involved
- C) Fake cell tower interception
- D) Physical device theft by strangers

**Answer: B.** These require nothing to go wrong. They are the default behaviour of a correctly working phone, and the consequences are contractual rather than technical.

**Question 2.** What is the single highest yield mobile OPSEC control for somebody handling client work?

- A) A Faraday bag for transport
- B) Disabling 2G on the cellular radio
- C) Separating the thing that touches client work from the thing that holds your life
- D) An always-on VPN

**Answer: C.** A separate profile on the device you already own does most of it at no cost, and no counter-surveillance technique addresses any of what it closes.

**Question 3.** Why is "never power the burner on at the same time as your real phone" insufficient?

- A) Because phones cannot be fully powered off
- B) Because the advice only applies to older network generations
- C) Because the burner still needs a registered subscriber module
- D) Because a device that travels your routes at your hours is linked by pattern, and simultaneity is not the mechanism

**Answer: D.** Investigators combine call records, tower dumps and movement patterns to find devices that move together, over weeks rather than moments.

**Question 4.** What proportion of countries now require identity documents to register a prepaid subscriber module?

- A) More than 155 countries, with some requiring biometrics
- B) Fewer than 20 countries
- C) Only countries in the European Union
- D) None, registration is voluntary everywhere

**Answer: A.** A handful do not, and they are the countries most published burner guidance was written in, which it rarely says.

**Question 5.** What distinguishes a basic border device search from an advanced one?

- A) The advanced search takes longer
- B) An advanced search connects external equipment to review, copy or analyse contents, and requires supervisory approval plus reasonable suspicion
- C) A basic search requires a warrant
- D) There is no formal distinction

**Answer: B.** A basic search is an officer reviewing the device by hand, permitted at their discretion with or without suspicion, and it is the one that actually happens.

**Question 6.** How does your citizenship status change the border calculation?

- A) It makes no difference to the outcome
- B) Visitors have stronger protections than citizens
- C) Citizens cannot be refused entry for declining to unlock, though the device can be detained; visitors and visa holders can be refused entry
- D) Only citizens can have devices searched

**Answer: C.** The calculation is completely different, and guidance that collapses the two is giving one group advice written for the other.

**Question 7.** Why is relying on hidden volumes or deniable storage at a border a poor idea?

- A) They are illegal to possess in most countries
- B) They slow the device down noticeably
- C) They cannot be created on modern phones
- D) Examiners know which tools support them, so denial invites escalation rather than ending it

**Answer: D.** And making a false statement to an officer is a separate offence in most jurisdictions, usually worse than whatever was on the device.

**Question 8.** What is the current built-in answer to cellular interception on Android?

- A) A root-requiring detection application
- B) The mobile network security section added in Android 16, with unencrypted network notifications, a 2G toggle and null cipher protection
- C) There is no protection available
- D) Airplane mode at all times

**Answer: B.** Detection is not prevention, and the practical defence is unchanged: encrypt above the link by using an end to end encrypted messenger.

**Question 9.** What is the correct statement about video calls on the built-in Apple application?

- A) They are end to end encrypted, and the vendor retains limited call invitation records but not content
- B) The vendor can access the content of calls
- C) They are encrypted only on the same network
- D) They use no encryption at all

**Answer: A.** The source material lists it under "do not use, the vendor can access," which is incorrect. Criticise the metadata if you like, not the content.

**Question 10.** Why does a ten section standard operating procedure fail?

- A) It is too technical for most readers
- B) The sections contradict each other
- C) Nobody maintains it, so it produces the feeling of having a procedure without the effect
- D) It cannot be stored securely

**Answer: C.** One page, four questions, and fifteen minutes a quarter is a procedure you will actually follow.

---

## Capstone Lab: Separation, Honesty, and One Page

**Time: 2 to 3 hours.**

The final lab of Module 5. It is deliberately weighted toward the boring column.

---

### Part 1: The Ten Minute Test (25 minutes)

Hand your personal phone to somebody, or search it as though you were somebody else, for ten minutes.

| What you found | Client or personal? | How it got there | Removed? |
|---|---|---|---|

**Search specifically:** the camera roll for screenshots and photographs taken at work, the address book for client contacts, the messaging applications for work threads, the notes application, the downloads folder, and the browser history.

Then do the same in reverse on the work device, looking for personal material.

**Deliverable.** Both tables, and a count of items found in each direction. A count of zero is a valid result and is rarer than people expect.

---

### Part 2: Draw the Line (30 minutes)

Decide explicitly what lives on each side.

| Category | Work side | Personal side | How it is enforced today |
|---|---|---|---|
| Camera and photo library | | | |
| Contacts | | | |
| Messaging | | | |
| Email | | | |
| Notes | | | |
| Credentials | | | |
| Browser | | | |

**The last column is the point.** "I am careful" is not an enforcement mechanism. A separate profile, a separate device, or a setting is.

**Deliverable.** The table, and one sentence on which row you have been relying on carefulness for.

---

### Part 3: Implement One Separation (35 minutes)

Pick the highest risk row from Part 2 and actually implement the separation, today.

Record: what you did, what broke, and what it now costs you in daily friction.

**If your platform offers a separate profile or private space**, set it up and move at least one application into it. If it does not, use whatever the platform provides: a separate account, a second browser profile, a work-only messaging account.

**Deliverable.** What you implemented, what broke, and the friction cost in a sentence.

---

### Part 4: The Camera Roll Question (20 minutes)

This gets its own part because it is the most common single failure.

1. How many photographs on your device were taken for work purposes? Count them.
2. Where do they synchronise to, and who holds the key to that? Lesson 5.1 Figure 3.
3. Do any carry location metadata identifying a client site? Check one directly.
4. What is your process for work photographs from now on?

**Deliverable.** All four answers. The fourth is the one that matters and must be a mechanism rather than an intention.

---

### Part 5: Travel Reality (25 minutes)

Answer for a trip you might actually take, not a hypothetical hostile environment.

| | |
|---|---|
| Destination and your status there | |
| Would you be refused entry for declining to unlock? | |
| What client material would be on the device today? | |
| What would you remove, and how would you get it back? | |
| Does any client have a stated policy on this? | |

**The last row usually requires asking somebody**, and asking is the exercise.

**Deliverable.** The table. "I do not know, and here is who I would ask" is an acceptable answer for the last row.

---

### Part 6: The One Page (25 minutes)

Write the four question procedure from Section 8. One page maximum.

It must contain: what is on each side, what you do before the device leaves your hand, what you do if it is lost or seized including who you tell and how fast, and when you check this.

Then put the quarterly check in your calendar with the checklist in the body of the entry.

**Deliverable.** The page, and confirmation the calendar entry exists.

---

### Part 7: Honest Threat Assessment (20 minutes)

Half a page. Answer directly:

**Which of the five threat levels in the standard version of this material actually applies to your work, and what would have to change for the next one up to apply?**

Then: **which parts of the advanced advice had you assumed you understood well enough to use, and does Figure 2 change that?**

The marks here are for accuracy about yourself, not for claiming a high threat level or a low one.

**Deliverable.** The half page.

---

### Submission

1. **Ten minute test**, Part 1, both directions with counts.
2. **The line**, Part 2, with the enforcement column filled in.
3. **One separation implemented**, Part 3, with the friction cost.
4. **Camera roll**, Part 4, with a mechanism for the fourth answer.
5. **Travel**, Part 5.
6. **The one page**, Part 6, with the calendar entry.
7. **Threat assessment**, Part 7.

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Ten minute test | 20 | Genuinely searched in both directions, findings reported honestly |
| The line | 15 | Every row has an enforcement mechanism, or admits it does not |
| One separation | 25 | Actually implemented, with breakage and friction recorded |
| Camera roll | 15 | Counted, sync destination established, and a mechanism proposed |
| Travel | 10 | Status established correctly, client policy question asked |
| The one page | 10 | One page, four questions, calendar entry exists |
| Threat assessment | 5 | Accurate about the reader's own situation |
| **Total** | **100** | |

**Note the weighting.** Implementing one separation is worth more than any other single part, because it is the only item on this list that changes anything.

---

## Additional Resources

### Primary Sources

- United States border search of electronic devices directive: https://www.dhs.gov/sites/default/files/2025-02/2024_1126_cbp_border_searches_of_electronic_devices_at_ports_of_entry.pdf
- GSMA policy handbook on mandatory registration of prepaid subscriber modules: https://www.gsma.com/solutions-and-impact/connectivity-for-good/public-policy/mobile-policy-handbook/consumer-protection/mandatory-registration-of-prepaid-sims/
- Apple platform security documentation on video call encryption: https://support.apple.com/guide/security/facetime-security-seca331c55cd/web

### On Cellular Protections

- Android Authority, on the mobile network security features added in Android 16: https://www.androidauthority.com/android-16-mobile-network-security-3571497/

Read this one for the caveats rather than the headline. Which of the three features you actually get depends on your modem.

### Discussion Questions

1. Part 1 asked you to search your own phone as somebody else. What did you find, and how long had it been there?
2. Section 4 argues that the gap between compartmentalization and anonymity is where people get hurt. Have you read guidance that blurred it, and would you have noticed before this lesson?
3. Section 8 replaced a ten section procedure with a one page one. Is that a real improvement or a lowering of standards, and how would you tell the difference a year from now?

---

## References

1. [GSMA, mandatory registration of prepaid subscriber modules](https://www.gsma.com/solutions-and-impact/connectivity-for-good/public-policy/mobile-policy-handbook/consumer-protection/mandatory-registration-of-prepaid-sims/)
2. [PrivacyPhones, on how a second device is linked by movement and contact patterns](https://privacyphones.com/articles/burner-phone-traced/)
3. [United States Customs and Border Protection, border searches of electronic devices at ports of entry](https://www.dhs.gov/sites/default/files/2025-02/2024_1126_cbp_border_searches_of_electronic_devices_at_ports_of_entry.pdf)
4. [Greenberg Traurig, what international travellers and employers should know about border device searches](https://www.gtlaw-insidebusinessimmigration.com/cbp/cbp-electronic-device-searches-at-the-border-what-international-travelers-and-employers-should-know/)
5. [Android Authority, Android 16 mobile network security and fake cell tower warnings](https://www.androidauthority.com/android-16-mobile-network-security-3571497/)
6. [Apple, platform security documentation on video call encryption](https://support.apple.com/guide/security/facetime-security-seca331c55cd/web)
