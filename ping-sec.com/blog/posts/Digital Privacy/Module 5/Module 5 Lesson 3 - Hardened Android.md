---
title: "Hardened Android: Two Axes, and the One Nobody Draws"
course: Digital Privacy for Ethical Hackers
module: 5
lesson: 5.3
format: article
reading_time: 34 minutes
tags:
  - digital-privacy-course
  - privacy
  - mobile-privacy
  - grapheneos
  - calyxos
  - android
  - verified-boot
  - opsec
---

# Hardened Android: Two Axes, and the One Nobody Draws

## About This Lesson

The usual version of this subject reads as a shopping decision: alternative Android systems are more private, here are two good ones, pick whichever suits your tolerance for inconvenience.

Two things wrong with that. First, **"alternative system" is not a privacy category.** It describes movement along one axis, and there is a second axis where most of these projects move backwards, invisibly, in a way that only matters on the day it matters. Second, **the recommendation to pick either of two projects stopped being safe in August 2025**, and most published guidance including the source material for this lesson still says it.

So this lesson does the security argument properly, corrects a compatibility story that most guides get backwards, and then covers what changed underneath the entire category between March 2025 and now.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Name the two axes that matter and place any Android system on both of them.
- Explain why verified boot with the system's own keys is the axis nobody draws, and what it decides.
- Say why hardened Android runs on so few devices, without reaching for snobbery as the explanation.
- Compare the two compatibility approaches accurately, including which one holds more privilege.
- Describe the three integrity levels and which an alternative system can satisfy.
- Explain what Google changed about Android source availability in 2025 and 2026, and what it costs downstream projects.
- Judge a system by its release history rather than its feature table.

### Prerequisites

- Lesson 5.1, particularly the before and after first unlock distinction in Figure 2.
- Lesson 5.2, particularly the three channels and the section on dating your recommendations.
- Lesson 1.4 for threat modeling, which decides whether any of this applies to you.

---

## 1. Two Axes

![[module_5_lesson_3_two_axes.svg]]

*Figure 1. Most comparisons draw the horizontal axis and leave the vertical one out.*

**The horizontal axis is whether Google services have been removed.** This is the axis every comparison uses, it is a genuine privacy improvement, and it is why these projects exist.

**The vertical axis is whether the system boots with verified boot, using its own signing keys.** Installing any alternative system requires unlocking the bootloader. The question is what happens next.

### The Four Squares

**Stock Android on a Pixel.** Verified boot intact, firmware current, patches on the day they ship. The security floor is genuinely high, and every Google service is present and privileged, which is the complaint. Lesson 5.1 was about getting the most out of this square, and for most people it remains the right answer.

**Hardened Android on Pixel hardware.** You unlock the bootloader to install, then **relock it with the system's own signing keys**, and verified boot comes back. This is the only square that improves on both axes at once.

**Rooted stock.** Google present, verification given up. Rooting has legitimate uses; privacy is not usually one of them.

**Most other alternative systems, on most hardware.** Google removed, which is real. And on almost all supported hardware **the bootloader stays unlocked permanently**, because most manufacturers either do not permit relocking with a key that is not theirs, or brick the device if you try. (Reference 1) So nothing verifies what booted. Add firmware that lags the manufacturer's and patches that arrive later, and this square is a trade rather than an upgrade.

### Why the Vertical Axis Is Missing From Comparisons

**Because it is invisible in normal use.** A device with a permanently unlocked bootloader behaves exactly like one without, right up until somebody has it in their hands for twenty minutes. Somebody with brief physical access can flash modified firmware, and the device will boot normally and tell you nothing.

Lesson 5.1 argued that physical possession is the likeliest serious event that happens to a phone. **This axis is what decides that case**, and it is left off the chart because it never shows up in a feature comparison.

**Which reframes the question.** It is never "should I install an alternative system". It is "which square am I moving to, and did I mean to move down".

### Why the Hardware List Is So Short

The requirement is not snobbery about brands. A device has to permit relocking the bootloader with a user-supplied key, and very few do.

The project maintaining the most hardened option publishes its hardware requirements, and they go well beyond that: memory tagging support, isolated radio components, firmware and system updates delivered as an A and B pair with rollback protection, a hardware-backed keystore with attestation, and complete monthly patches within about a week of release. (Reference 2) Its own documentation puts the reasoning plainly: supporting a broad device list would mainly mean supporting badly secured devices that cannot run the features.

**Currently that means one manufacturer's phones**, spanning the Pixel 6 generation through the Pixel 10 series, plus the Fold and Tablet. Devices earlier than that, including several the source material for this lesson lists as supported, have reached end of life. (Reference 2)

---

## 2. What the Hardening Actually Consists Of

Removing Google is the part people talk about. It is not the part that distinguishes a hardened system from a merely degoogled one.

**Memory safety work.** A hardened allocator and related mitigations, which do not fix vulnerabilities but make a large class of them substantially harder to turn into working exploits. This is the least visible and probably the most valuable item on the list.

**Permissions the platform does not have.** Network access as a per-application toggle, so an application can be installed and denied the internet entirely. Sensor access as a toggle, covering the motion sensors that need no permission at all on stock Android. Storage and contact scopes, which let an application see a specific folder or a specific contact while believing it has full access, so it works rather than complaining.

**That last mechanism is worth pausing on.** Lesson 5.2's scope ladder ended at rung four, a limited subset. This is rung four applied to things that have no rung four on stock Android, and applied in a way that does not break the application.

### Two Features That Connect Directly to Lesson 5.1

**Automatic reboot on inactivity.** The device reboots itself after a period without a successful unlock, returning it to the sealed state from Lesson 5.1 Figure 2. The default is 18 hours, and it is adjustable from 10 minutes to 72 hours. (Reference 3)

Compare that to the 72 hours fixed on the other platform. Same idea, arrived at independently, four times more aggressive by default and configurable to be far more aggressive still.

**A duress credential.** A second PIN or password that, entered at any credential prompt including the lock screen, irreversibly wipes the device and any installed electronic SIMs. It does not require a reboot and cannot be interrupted. (Reference 3)

**Be careful with this one.** It is a genuine capability with obvious uses, and it is also a way to destroy your own data by mistyping, and destroying evidence under some circumstances is itself an offence. It belongs in a threat model that has been thought about, not in a list of settings to switch on.

**USB port control.** The data pins can be configured to refuse new connections once the device is locked, which closes the most common physical extraction path. (Reference 2)

---

## 3. Compatibility, Told the Right Way Round

Standard guidance frames this as a purity trade: a Google substitute is the private option, and running real Google services is the compromise you make for compatibility. **That is backwards.**

![[module_5_lesson_3_compatibility.svg]]

*Figure 2. Who actually holds privileges, and the ladder that decides what works.*

**A reimplementation of Google services** is open source code that answers the same interfaces, so applications believe Google services are present. To impersonate the real component it needs **system level signature spoofing**, a privilege the platform does not otherwise grant to anything. It runs with elevated access, and on some systems it can reach hardware identifiers.

**The real components in the ordinary sandbox** are Google's actual code, installed and run as a normal unprivileged application. It cannot see your other applications, read hardware identifiers, or leave the sandbox. It holds only the permissions you grant it, exactly like anything else on the device. (Reference 4)

**So running the real thing unprivileged exposes less than running a substitute privileged.** That is the point people find surprising, and it inverts the usual recommendation.

The substitute still has a legitimate place: it is fully open source, and if your objection to Google is about who wrote the code rather than about what it can reach, that is a coherent position. But it should be argued on those grounds, not on the grounds that it exposes less.

### The Integrity Ladder

Applications check device integrity through an interface that replaced the older, retired one. Three levels:

**Basic.** A recognised device, an application from the store. An alternative system passes. Most applications never check anything stronger.

**Device.** A device with Google services present and functioning. Generally passes with the sandboxed approach. Most banking applications sit here and most of them work.

**Strong.** Hardware-backed keys issued for that specific device running the stock system. An alternative system does not pass. A minority of applications, concentrated in particular markets. (Reference 5)

**And here is the part worth knowing, because it is the part a developer can act on.** The strongest level is built on the hardware key attestation interface, and that interface can allow the keys of an alternative operating system. An application calling it directly, rather than through the integrity wrapper, can verify the device genuinely and still accept a hardened system. The project publishes a guide for exactly this, and several banks adopted it across 2024 and 2025. (Reference 5)

**Which means the compatibility story is better than its reputation, and improving.** Check your own applications rather than a list, because the answer depends on your bank and your country, and keep browser access as the fallback.

---

## 4. The Ground Moved

This is the section the source material cannot have, and it is the most important one.

![[module_5_lesson_3_ground_moved.svg]]

*Figure 3. Eighteen months that changed what these projects cost to run.*

**March 2025: development goes private.** Google moved Android development entirely to internal branches. Source is still published at each release, so it remains open source. What ended is the public development process and the visibility that came with it. (Reference 6)

**June 2025: the device files stop appearing.** Android 16 shipped without Pixel device configuration files or driver binaries, and with the kernel history squashed into a single commit. Developers now work from the previous version's files and reverse engineer forward, every release. Google's stated reason is a shift of its reference target away from Pixel hardware to a virtual device. (Reference 7)

**August 2025: one of the two recommended projects paused.** CalyxOS stopped releasing after its institute founder and technical lead departed. **Anybody running it stayed on the last build, without security patches, for the next eleven months.** (Reference 8)

**During 2026: source arrives twice a year.** Public publication dropped from quarterly to the second and fourth quarters only, making two of the four quarterly updates effectively exclusive to stock devices, and widening the patch gap for everyone downstream. (Reference 9)

**July 2026: the paused project resumed**, with a maintained release and a stated intent to return to regular updates. (Reference 9) Good news, and two months old at the time of writing.

### What This Means

**For the projects:** every release now costs more work than the same release cost two years ago, for the same output, and that pressure falls hardest on the smallest teams. **The differentiator between these projects is no longer features. It is whether the team can sustain the pace.**

**For you:** you are not choosing a feature set. You are choosing a team to depend on for security patches for the life of the device. Check the release feed before you install, and check it again every few months afterwards. **A project that has gone quiet is not a project taking a break. It is an unpatched phone, and nobody will notify you.**

**None of this argues against a hardened system.** The security case in Section 1 is unchanged and the compatibility case in Section 3 has improved. What changed is that "pick either of these two, they are both good" stopped being a safe recommendation, and most published guidance still says it. Including the source material for this lesson, which is a fair illustration of the point Lesson 5.2 made about dating your recommendations.

---

## 5. Deciding

Shorter than the usual version, because most of the decision is two questions rather than a personality quiz.

### Question One: Which Square Are You Moving To?

If the answer is the bottom right of Figure 1, be explicit that you are trading verification for degoogling, and check whether that trade suits your threat model. For somebody whose concern is advertising and data collection it may well. For somebody whose concern is a device being seized or examined, it is the wrong direction.

### Question Two: Can You Sustain It?

Not "are you technical enough". Installation is a web-based process that takes well under an hour, and the difficulty of that step is consistently overstated.

The real question is the ongoing one. Will you check that updates are still arriving? Will you notice if they stop? Will you re-evaluate in a year? A hardened system that stopped updating is worse than the stock system you left, because you believe it is better.

### If Both Answers Are Good

**A hardened system on supported hardware is an excellent choice** and it is the strongest mobile position available. It is worth a specific phone, an afternoon, and a tolerance for things occasionally not working. Two considerations on the purchase:

- Buy a device with most of its support window remaining. Current list prices run from around 499 US dollars for the smaller current model to 799 and above for the standard one, and the support window is seven years from launch for recent generations. (Reference 10) A device three years into that window is not a bargain.
- Do not buy a device already at end of life to save money. Some projects offer extended releases for such devices, without firmware or driver updates. That is a lower tier of security wearing the same name.

### If Either Answer Is Poor

**Stock, configured, is a legitimate destination and not a consolation prize.** Lesson 5.1 and Lesson 5.2 cover it, and a well configured stock device sits in the top left of Figure 1 with verified boot intact. That beats an enthusiastically installed system in the bottom right, for most threat models, permanently.

### The Approach That Actually Works

**Buy a used supported device as a second phone, install, and run it for a month without depending on it.** You find out which of your applications actually break, you learn the update rhythm, and if you decide against it you have lost a modest sum rather than your daily driver. This is the only recommendation in this section that materially reduces the risk of the decision.

---

## 6. What to Take From This

**There are two axes and most comparisons draw one.** Degoogling is real, and verified boot with the system's own keys is what decides the physical case from Lesson 5.1.

**The hardware list is short for a reason** that is about relockable bootloaders and firmware guarantees, not brand preference.

**The compatibility framing is usually backwards.** Real Google components in an ordinary sandbox hold less privilege than a substitute that needs signature spoofing to work.

**And the category changed underneath everybody in 2025.** Source availability narrowed, per-release effort rose, and one of the two commonly recommended projects went eleven months without a security update. Judge these projects on release history, and check it yourself rather than trusting a comparison table, including this one.

**Next lesson:** mobile operational security, which applies regardless of which square you ended up in.

---

## Knowledge Check

**Question 1.** What is the second axis that most comparisons of Android systems leave out?

- A) Whether the system supports encrypted backups
- B) Whether the system boots with verified boot using its own signing keys, which requires relocking the bootloader
- C) Whether the system is open source
- D) How many applications are preinstalled

**Answer: B.** It is left out because it is invisible in normal use, right up until somebody has the device in their hands.

**Question 2.** On most hardware, what happens to the bootloader after installing a widely recommended alternative Android system?

- A) It relocks automatically during first boot
- B) It relocks once the system verifies its own integrity
- C) It stays unlocked permanently, because most manufacturers do not permit relocking with a key that is not theirs
- D) It is removed entirely

**Answer: C.** Nothing then verifies what booted, so somebody with brief physical access can flash modified firmware and the device will start normally.

**Question 3.** Why does the most hardened option support only one manufacturer's devices?

- A) A commercial arrangement with that manufacturer
- B) Because other manufacturers use incompatible processors
- C) Developer preference and limited interest in other hardware
- D) Because its hardware requirements, including a relockable bootloader with user keys and prompt firmware updates, are met by very few devices

**Answer: D.** Its documentation states that broad device support would mainly mean supporting badly secured devices that cannot run its features.

**Question 4.** Which holds more privilege on the device?

- A) A reimplementation of Google services, because it needs system level signature spoofing to impersonate the real component
- B) Real Google services running in the ordinary application sandbox
- C) They hold identical privileges
- D) Neither holds any privilege

**Answer: A.** Running the real thing unprivileged exposes less than running a substitute privileged, which inverts the usual recommendation.

**Question 5.** At which integrity level does an alternative Android system stop passing?

- A) Basic
- B) It fails at all levels
- C) Strong, which requires hardware backed keys issued for that device on the stock system
- D) It passes at all levels

**Answer: C.** Most banking applications check the middle level and generally work. Strong is a minority, concentrated in particular markets.

**Question 6.** How can an application verify a device genuinely and still accept a hardened system?

- A) By disabling integrity checks entirely
- B) By checking only that the application came from the store
- C) There is no way to do both
- D) By calling the hardware key attestation interface directly, which can allow the keys of an alternative operating system

**Answer: D.** The project publishes a guide for this, and several banks adopted it across 2024 and 2025.

**Question 7.** What did Google change about Android source availability in June 2025?

- A) It stopped publishing source code entirely
- B) Android 16 shipped without Pixel device configuration files or driver binaries, and with the kernel history squashed into one commit
- C) It began charging for access to the source
- D) It required developers to sign a licence before downloading

**Answer: B.** Downstream developers now work from the previous version's files and reverse engineer forward, every release.

**Question 8.** What happened to CalyxOS between August 2025 and July 2026?

- A) It was acquired by a commercial vendor
- B) It expanded to support several new manufacturers
- C) Development paused after leadership departures, so anybody running it went eleven months without security patches
- D) It merged with another project

**Answer: C.** A project that has gone quiet is not taking a break. It is an unpatched phone, and nobody notifies you.

**Question 9.** What is the correct comparison for the automatic reboot feature on a hardened Android system?

- A) It defaults to 18 hours and is adjustable from 10 minutes to 72 hours, against a fixed 72 hours on the other platform
- B) It does not exist on Android, only on the other platform
- C) It defaults to 7 days and cannot be changed
- D) It only triggers when the battery is low

**Answer: A.** Same idea as Lesson 5.1 Figure 2, arrived at independently, more aggressive by default and configurable further.

**Question 10.** What is the recommended way to reduce the risk of switching?

- A) Install on your daily device and keep detailed notes
- B) Read the documentation several times and then commit fully
- C) Wait until a future release removes all compatibility problems
- D) Buy a used supported device as a second phone, install, and run it for a month without depending on it

**Answer: D.** You find out which of your applications actually break, you learn the update rhythm, and a decision against it costs a modest sum rather than your daily driver.

---

## Lab: Placement, Verification, and the Release Record

**Time: 2 to 3 hours. No installation required.**

This lab is research and decision. Installation is optional, done at your own risk, and outside the assessment.

---

### Part 1: Place Four Systems (30 minutes)

Using Figure 1's two axes, place each of these on the grid and justify the placement from the project's own documentation, not from a review.

| System | Google removed? | Verified boot with own keys? | Square | Source for your answer |
|---|---|---|---|---|
| Your current phone as it is | | | | |
| The most hardened option, on supported hardware | | | | |
| A widely recommended general purpose alternative | | | | |
| One other alternative system of your choosing | | | | |

**The column that carries the marks is the last one.** For the vertical axis specifically, find where the project states whether the bootloader can be relocked on the hardware you would actually use.

**Deliverable.** The table, and one sentence on which placement was hardest to establish and why.

---

### Part 2: The Release Record (30 minutes)

Section 4 argued you are choosing a team rather than a feature set. Check the teams.

For two projects, go to the release feed or changelog and record:

| | Project A | Project B |
|---|---|---|
| Most recent release date | | |
| Releases in the last 6 months | | |
| Longest gap between releases in the last 2 years | | |
| Android version currently based on | | |
| How far behind the current stock patch level | | |

**Deliverable.** The table, and your answer to: if you had installed one of these in mid-2025, when would you have noticed something was wrong, and how?

---

### Part 3: Your Own Compatibility (25 minutes)

Do not use somebody else's compatibility list.

List the applications you genuinely could not do without, then establish for each whether it works, using the project's own forum or discussion archive.

| Application | Needed for | Works? | Evidence | Browser fallback exists? |
|---|---|---|---|---|

The last column matters more than the third. An application that fails but has a working website is an inconvenience. One that fails with no fallback is a blocker.

**Deliverable.** The table with at least eight applications, and a count of genuine blockers.

---

### Part 4: The Hardware Question (20 minutes)

| | |
|---|---|
| Devices currently supported by your preferred project | |
| Which you would buy, new or used | |
| That device's launch date and end of support date | |
| Support window remaining | |
| Approximate cost | |
| Cost per remaining supported year | |

The last row is the useful one and almost nobody calculates it.

**Deliverable.** The table, and whether the answer changed which device you would buy.

---

### Part 5: The Duress Question (20 minutes)

Section 2 described a duress credential that irreversibly wipes the device.

Write half a page answering: **would you configure one, and under what circumstances would you use it?**

Address specifically: the risk of triggering it by mistake, what you would lose, what your backup position is, and whether destroying data in the situation you are imagining could itself create a legal problem for you.

**Deliverable.** The half page. There is no correct answer and the marks are for actually reasoning about it rather than for the conclusion.

---

### Part 6: The Honest Recommendation (15 minutes)

Write the recommendation you would give a client who asked what phone their high-risk staff should carry.

Apply Lesson 5.2's rules: date it, give reasons as checkable properties, and state what would make it stop being true.

It must also say what the recommendation costs them, in money and in friction, because a recommendation without a cost is a wish.

**Deliverable.** The recommendation. Half a page at most.

---

### Submission

1. **Placement**, Part 1, with sources.
2. **Release record**, Part 2, with the detection question answered.
3. **Compatibility**, Part 3, with blocker count.
4. **Hardware**, Part 4, with cost per supported year.
5. **Duress**, Part 5.
6. **Recommendation**, Part 6.
7. **Reflection**, half a page: before this lesson, did you think of alternative Android systems as a security improvement, a privacy improvement, or both? Has that changed?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Placement | 25 | Both axes established from project documentation rather than reviews |
| Release record | 25 | Real data gathered, and the detection question answered concretely |
| Compatibility | 15 | Own applications checked, fallbacks distinguished from blockers |
| Hardware | 10 | Cost per remaining supported year actually calculated |
| Duress | 10 | Reasoned through, including the failure mode and the legal question |
| Recommendation | 10 | Dated, property-based, and states its cost |
| Reflection | 5 | Names a change in understanding rather than restating the lesson |
| **Total** | **100** | |

**Note the weighting.** Half the marks are the two axes and the release record, because those are the two things a feature comparison cannot give you and the two that decide whether this goes well.

---

## Additional Resources

### Primary Sources

- GrapheneOS frequently asked questions, including device support requirements: https://grapheneos.org/faq
- GrapheneOS features overview, covering the hardening, auto-reboot and duress credential: https://grapheneos.org/features
- GrapheneOS attestation compatibility guide, written for application developers: https://grapheneos.org/articles/attestation-compatibility-guide
- CalyxOS news feed, which is the release record referenced in Section 4: https://calyxos.org/news/

### On the Source Availability Changes

- Android Authority, on Google's AOSP changes and their effect on downstream developers: https://www.androidauthority.com/google-not-killing-aosp-3566882/

Read this alongside the CalyxOS progress reports. The two together are the clearest picture of what the last eighteen months cost these projects.

### Discussion Questions

1. Part 1 asked you to place systems on both axes from primary documentation. Which projects made the vertical axis hard to establish, and is that an accident?
2. Section 3 argued the usual compatibility framing is backwards. Did you believe the usual framing before, and what would have changed your mind sooner?
3. Section 4 says you are choosing a team rather than a feature set. What would you need to see from a project to trust it with your phone's security patches for seven years?

---

## References

1. [PrivacyPhones, on bootloader relocking and verified boot across alternative Android systems](https://privacyphones.com/articles/grapheneos-vs-lineageos/)
2. [GrapheneOS frequently asked questions, device support and hardware requirements](https://grapheneos.org/faq)
3. [GrapheneOS features overview, including auto-reboot and the duress credential](https://grapheneos.org/features)
4. [GrapheneOS documentation discussion comparing sandboxed Google Play with a reimplementation](https://github.com/GrapheneOS/grapheneos.org/issues/253)
5. [GrapheneOS attestation compatibility guide](https://grapheneos.org/articles/attestation-compatibility-guide)
6. [AlternativeTo, Google shifts Android development to private internal branches, March 2025](https://alternativeto.net/news/2025/3/google-shifts-android-development-to-private-internal-branches-for-faster-updates)
7. [Android Authority, on the Android 16 source release and its effect on custom system developers](https://www.androidauthority.com/google-not-killing-aosp-3566882/)
8. [CalyxOS, official release is back, July 2026](https://calyxos.org/news/2026/07/01/calyxos-official-release-is-back/)
9. [CalyxOS progress report, on source publication cadence and the work it creates](https://calyxos.org/news/2026/04/01/calyxos-progress-update-3/)
10. [Google Store, Pixel 10a product page](https://store.google.com/product/pixel_10a?hl=en-US)
