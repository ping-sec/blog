---
title: "App Permissions: Necessary, Insufficient, and Only One of Three Channels"
course: Digital Privacy for Ethical Hackers
module: 5
lesson: 5.2
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - mobile-privacy
  - permissions
  - trackers
  - data-brokers
  - opsec
---

# App Permissions: Necessary, Insufficient, and Only One of Three Channels

## About This Lesson

The standard model of mobile app privacy is simple and appealing: a permission granted is data given away, so grant fewer permissions and less data leaves. Every guide on this subject is built on it, including the outline this lesson is built from.

**The model is correct and it accounts for about a third of what actually happens.** Two other channels carry data out of an application, and one of them requires no permission at all, was never granted by you, and is the source of the largest location data leak on record.

So this lesson does the permission work properly, because that work is real and worth doing. Then it covers what the permission work does not reach, because a student who finishes the audit believing they are finished has been misled by the shape of the advice rather than by anything false in it.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Name the three channels data leaves an application by, and say which control addresses each.
- Explain how location data reaches a broker from an application that never held location permission.
- State what a privacy label is, what a study found when the labels were checked, and what they remain useful for.
- Use all five rungs of permission scope rather than the two most people use.
- Check what is actually inside an application, using something the developer did not write.
- Judge a published app recommendation list for staleness, and say why several common ones are wrong now.

### Prerequisites

- Lesson 5.1, particularly the three-tier model of who holds the key.
- Lesson 1.3 on metadata, which is why photograph permissions matter more than they look.
- Lesson 2.5 on compartmentalization, which Section 7 applies to app selection.

---

## 1. Three Channels

![[module_5_lesson_2_three_channels.svg]]

*Figure 1. Least privilege closes the first one completely, the second partly, and the third not at all.*

### Channel One: The Permission You Granted

You tapped allow. The application can read that thing, within the scope you granted, until you revoke it.

**Least privilege genuinely closes this channel.** Deny what makes no sense, reduce the scope of what does, revoke what you stopped using. It works, it is visible on both platforms, and it is reversible. Section 5 is the procedure.

This is the channel every guide is about, and it gets the attention precisely because it is the one you can see and control. That is a good reason to do the work, and a bad reason to stop there.

### Channel Two: The Code Inside the Application

A modern application is assembled rather than written. Analytics, crash reporting, advertising, payments, attribution, and support chat all arrive as third party components.

**Each one runs inside the application, so each one inherits every permission the application holds.** The operating system grants permissions to a process, and it cannot distinguish the developer's code from a component the developer added. When you grant location to a game, you grant it to the game and to whichever advertising components ship inside it.

Least privilege limits the damage here but does not close the channel, because the components inherit whatever you did grant. **The control is different in kind: it is choosing applications with fewer components**, which is research rather than a settings screen. Section 4 covers how to check.

### Channel Three: The Advertising Request

This is the one the standard advice omits, and it changes the conclusion.

To display an advert, an application sends a bid request to an ad exchange. That request carries a device identifier, a network address, and frequently a location field. The exchange forwards it to demand side platforms, which bid.

**Every bidder receives the request, whether or not they win.** Anyone able to register as a buyer sees the stream. Data brokers have used exactly this route to assemble location datasets at scale, and a regulator found that one broker sourced roughly 60 percent of its data from real time bidding auctions. (Reference 1)

**The concrete case.** In January 2025 a location data broker was breached. It was tracking over a billion devices and ingesting billions of signals a day. Researchers examining the leaked sample extracted the names of thousands of applications that had fed it, including games, a fitness tracker, dating applications and religious applications, and found records tied to sensitive government sites. (Reference 2)

**Read the important part carefully.** The great majority of those applications never held location permission. The data did not come from a permission you granted. It came from an advertising request that carried enough to place you, sent by an application you allowed to show you an advert.

### What Follows

Each channel needs a different control, and only the first is a settings screen.

- **Permissions:** deny, reduce scope, revoke. Free, fast, effective.
- **Components:** choose differently. Fewer applications, open source where it exists, the website instead of the app.
- **The bidstream:** reset or disable the advertising identifier, and block at the network. Neither is complete.

**Notice what appears in two of those three rows.** Removing an application is the only control that closes every channel at once, which makes the most effective item in this lesson not a permission setting but the number of applications on your phone.

---

## 2. Why This Matters More For You

Two reasons specific to the work.

**Your movements are engagement data.** If you are on site with a client, your location history is a record of which organisations hired you and when. That is frequently under a confidentiality clause that you signed and a data broker did not. The bidstream channel means you can leak that without granting anything.

**And you will be asked about this.** Clients ask whether their staff's phones are a risk. The useful answer is not a permission checklist. It is that permissions are one channel, that the advertising supply chain is another, and that the practical mitigation for a sensitive role is fewer applications on the device rather than better settings on the same ones.

---

## 3. The Privacy Label Is a Declaration, Not a Measurement

Both stores show standardised privacy disclosures. The usual caveat is that they are self reported and developers can lie. Somebody checked, and the result is worse than that phrasing implies.

![[module_5_lesson_2_label_problem.svg]]

*Figure 2. Forty of the most popular applications, examined against their own policies.*

**How the label is produced.** The developer completes a form declaring what the application collects and shares. Nobody verifies the declaration against the binary. The platform's own documentation states that applications are responsible for making complete and accurate declarations.

**What happened when a research foundation checked.** Examining forty of the most popular applications and comparing the labels against the applications' own published policies, nearly 80 percent of the labels were false or misleading. Sixteen of the forty were graded poor, around fifteen more needed improvement, and six were acceptable. (Reference 3)

**The exemption that makes much of this technically truthful.** Data shared with "service providers" does not have to be declared. The definition is narrow on paper and enormous in practice, so an application can transfer a great deal about you to other companies and still declare that it shares nothing with third parties. Two of the studied applications declared no third party sharing while their own policies described sharing with advertisers and platforms.

### What Labels Are Still Good For

They are not worthless, and the honest use is narrow.

- **Comparing two applications that do the same job.** Both declarations are unverified, but a large gap between them is still informative.
- **As a floor, never a ceiling.** An application that admits to extensive collection is telling the truth about the minimum. One that admits to none has told you nothing at all.

Treat a good label as an absence of evidence rather than evidence of absence, and check it against something the developer did not write.

---

## 4. Checking What Is Actually In There

The check that is not self declared is analysis of the application itself.

**Tracker analysis.** Independent projects decompile Android applications and list the third party components inside them, along with the permissions declared. This gives you channel two directly: not what the developer says they share, but which companies' code is running with your grants. Two applications that do the same job frequently differ by an order of magnitude in component count, and that difference is visible in minutes.

**Network observation.** Both platforms will show you which domains an application has contacted. On iPhone this is an opt-in report you must enable before it collects anything, so enable it now and read it in a week rather than expecting history you never recorded. On Android a local firewall or DNS filter shows you the same thing and lets you block it.

**Independent recommendation projects** maintain evaluations that are updated when something changes, which matters more than the recommendation itself, for reasons Section 7 makes concrete.

**A caution about all three.** These tell you what an application contains and where it connects. They do not tell you what is in the bid requests it sends, because that traffic looks like ordinary advertising traffic and is. Channel three remains difficult to observe from the device.

---

## 5. The Scope Ladder

Most people treat permissions as a choice between allow and deny. There are five rungs and the useful answers are all in the middle.

![[module_5_lesson_2_scope_ladder.svg]]

*Figure 3. Five rungs. Most people use the first and the last.*

**1. Deny.** The correct default, and the one you can afford to be wrong about. You can grant later when something visibly breaks. You cannot un-collect what was already taken, so the asymmetry runs one way.

**2. Ask every time.** The right rung for anything used occasionally, and the most under-used option on both platforms. Available for location, camera and microphone. The grant lasts that session and lapses when you leave, so an application you open twice a year never accumulates standing access.

**3. While using.** Access only while the application is open in front of you. **This is the right answer for the large majority of location grants**, and it is not the rung the application asks for. A navigation application works perfectly here. So does a delivery application, which asks for the rung above anyway because the rung above is worth money to somebody.

**4. A limited subset.** You choose the specific items and the application receives those and nothing else. Selected photographs have existed for some years. Choosing individual contacts arrived on iPhone in 2024, and several developers publicly complained that new social applications would struggle without full address book access. (Reference 4) That complaint is a useful measurement of what the full grant was worth to them.

**5. Full, or always.** The whole library, the whole address book, or location while the application is closed. Very few applications genuinely need this.

**The address book deserves a separate sentence.** It is not your data. It is a list of other people, their numbers, their addresses and sometimes their birthdays, and none of them were asked. Rung four exists for this and almost nobody uses it.

### Three Things That Follow

**The prompt is not the ladder.** An application asks at the rung it wants. The other rungs are in settings afterwards, which is where most of this work happens.

**Both platforms now revoke automatically** from applications you have stopped opening. Useful, and a reason to check what got revoked rather than a substitute for looking.

**Rung four is where the recent gains are.** If you configured your permissions two years ago, the limited options you now have did not exist then.

---

## 6. Combinations Worth Noticing

Individual permissions are less informative than sets of them. Two worth flagging beyond the obvious.

**Location plus contacts** produces association data: not only where you were, but who else was there, inferred across users of the same application. Neither permission alone gives that.

**Photographs plus network** is quietly one of the strongest. Lesson 1.3 covered what a photograph carries beyond its contents, and full library access hands over every one of those records at once, historically, in a single grant.

**And a caution about a common claim.** You will read that microphone permission means an application is listening to your conversations to target adverts. Investigations have repeatedly failed to demonstrate this, and the ordinary explanation is duller and worse: the targeting is good enough without audio, because channels two and three already supply location, timing and association. Microphone access is still worth denying, on the ordinary grounds that it is not needed. Claiming the phone is listening is the kind of thing that costs you credibility, and it distracts from a mechanism that is documented.

---

## 7. Recommendation Lists Go Stale, Including This One

The source material for this lesson ends with a list of applications to use and avoid. Several entries are now wrong, and the pattern of how they went wrong is more useful than the corrections.

**An authenticator that was acquired.** A widely recommended open source iPhone authenticator was acquired in 2023 by a company that publishes ad-supported utilities and a virtual private network, whose privacy policy describes advertising trackers and log collection. Independent recommendation projects removed it. (Reference 5) The application did not change on the day of the acquisition. The thing you were trusting did.

**A maps application that was sold.** One list recommends both a mapping application and its own fork in the same breath. The fork exists precisely because the original was sold and became commercial, and recommending both is a sign the list was assembled from other lists. (Reference 6)

**An authenticator described as having no backup.** That was true and has not been since 2023, when cloud sync was added. The correct current criticism is different: the sync was not end to end encrypted at launch, and the promised encryption has not become universally available. (Reference 7) Criticising it for the wrong reason means somebody who checks will conclude you are out of date, and stop reading.

**A messenger whose policy changed.** One entry is criticised for lacking default end to end encryption, which is correct. What is missing is that in September 2024 the service changed its policy to hand over user network addresses and telephone numbers on valid legal requests for a broad range of offences, where previously that applied to confirmed terrorism cases only, and disclosures rose sharply afterwards. (Reference 8) That is a larger change than the original criticism.

**And one correction carried from Lesson 5.1**, because the list repeats it: consumer mail content has not been scanned for advertising personalisation since 2017.

### The Lesson, Which Is Not About Any Of These

**Every one of these entries was correct when written.** They went stale through acquisition, sale, policy change and feature addition, and none of that is visible from inside the application.

So when you write recommendations, and you will:

- **Date them.** A recommendation without a date is a claim about a permanent property, and none of these are permanent.
- **Say why, not just what.** "Use this because it is open source and the developers are independent" survives contact with an acquisition, because a reader can check whether the reason still holds. "Use this" does not.
- **Prefer properties over products.** No trackers, reproducible builds, no account required, independent ownership. Those are checkable by the reader later.

---

## 8. What to Take From This

**Do the permission work. It is real.** One pass down the ladder for every application holding location, photographs, contacts, microphone or camera. About twenty minutes, and it closes channel one properly.

**Then understand what it did not do.** The components inside your applications inherit your grants, and the advertising requests your applications send carry data you never granted at all.

**The strongest single control is not a setting.** It is the number of applications on the device, because deleting one closes all three channels at once.

**Labels are a floor, not a measurement.** Roughly four in five of the ones checked were false or misleading, and the exemption for service providers makes much of that technically truthful.

**And recommendation lists rot.** Date them, give reasons, and prefer checkable properties over product names.

**Next lesson:** hardened Android distributions, which change what channels two and three can even do.

---

## Knowledge Check

**Question 1.** How does location data reach a broker from an application that never held location permission?

- A) The application secretly requests the permission without showing a prompt
- B) By reading it from other applications on the device
- C) Through the advertising bid request, which carries identifiers and often location, and which every bidder receives whether or not they win
- D) It does not, this is not possible

**Answer: C.** A regulator found one broker sourced roughly 60 percent of its data this way, and a 2025 breach at another named thousands of feeding applications.

**Question 2.** Why do third party components inside an application get your data?

- A) They request separate permissions at install time
- B) They run inside the application process, so they inherit every permission the application holds, and the system cannot distinguish them
- C) The operating system grants them elevated access by default
- D) They do not, components are sandboxed from the host application

**Answer: B.** Granting location to a game grants it to the game and to whichever advertising components ship inside it.

**Question 3.** A research foundation checked the privacy labels of forty of the most popular applications. What did it find?

- A) The labels were accurate in the large majority of cases
- B) Only paid applications had inaccurate labels
- C) The labels were accurate for data collection but not for data sharing
- D) Nearly 80 percent were false or misleading, with sixteen graded poor and six acceptable

**Answer: D.** The exemption for data shared with "service providers" makes much of that technically truthful, which is the more useful finding.

**Question 4.** What is the correct scope for the large majority of location grants?

- A) While using, meaning only when the application is open in front of you
- B) Always, so the application works reliably in the background
- C) Deny, since no application needs location
- D) It does not matter, since location is inferred anyway

**Answer: A.** Navigation and delivery applications both work at this rung. They ask for the one above because the one above is worth money to somebody.

**Question 5.** Why does the address book deserve separate treatment from other permissions?

- A) It is encrypted differently by the operating system
- B) It cannot be revoked once granted
- C) It is not your data, it is a list of other people who were never asked
- D) It is the only permission with no limited option

**Answer: C.** Selecting individual contacts became possible on iPhone in 2024, and it is the rung almost nobody uses.

**Question 6.** What is the single most effective control covered in this lesson?

- A) Denying microphone access to all applications
- B) Reading privacy labels before installing
- C) Enabling the network activity report
- D) Reducing the number of applications on the device, because deleting one closes all three channels

**Answer: D.** Permissions close channel one, application choice affects channel two, and only removal reaches channel three.

**Question 7.** How should you treat a privacy label that declares no data collection?

- A) As evidence the application collects nothing
- B) As a floor rather than a ceiling, and as an absence of evidence rather than evidence of absence
- C) As proof the application was independently audited
- D) As grounds to install without further checks

**Answer: B.** An application that admits to extensive collection is telling the truth about its minimum. One that admits to none has told you nothing.

**Question 8.** A widely recommended open source authenticator was acquired in 2023. What is the general lesson?

- A) Recommendations go stale through changes invisible from inside the application, so date them and give checkable reasons
- B) Open source applications should not be recommended
- C) Acquisitions always improve privacy practices
- D) Authenticator applications are inherently unsafe

**Answer: A.** "Use this because it is independently owned and has no trackers" survives an acquisition, because a reader can check whether the reason still holds. "Use this" does not.

**Question 9.** What is the accurate position on applications listening through the microphone for advertising?

- A) It is confirmed and widespread across major applications
- B) It only happens on one of the two platforms
- C) Investigations have repeatedly failed to demonstrate it, and the documented mechanisms are already sufficient without audio
- D) It happens only when the application is in the foreground

**Answer: C.** Deny microphone access on the ordinary grounds that it is not needed. Claiming the phone is listening costs credibility and distracts from mechanisms that are documented.

**Question 10.** What does the "ask every time" permission rung provide?

- A) A grant that never expires but logs each use
- B) A grant lasting one session, which lapses when you leave the application
- C) A grant limited to specific items you select
- D) A permanent denial that cannot be changed

**Answer: B.** It is the right rung for anything used occasionally, and the most under-used option on both platforms.

---

## Lab: The Ladder, the Components, and the Stale List

**Time: 2 to 3 hours.**

---

### Part 1: The Count (15 minutes)

Before any settings work.

| | |
|---|---|
| Total applications installed | |
| Opened in the last 30 days | |
| Opened in the last 7 days | |
| Installed and never intentionally opened | |

Then delete everything in the gap between the first row and the second, unless you can say out loud why you are keeping it.

**Deliverable.** The table, the number deleted, and one sentence on the application you were most surprised to still have.

**Why this is first.** Figure 1: deletion is the only control that closes all three channels. Do the highest yield action before the fiddly one.

---

### Part 2: The Ladder Pass (35 minutes)

For every remaining application holding location, photographs, contacts, microphone or camera, walk the ladder.

| App | Permission | Rung before | Rung after | Anything break? |
|---|---|---|---|---|

**Specifically check for these, which are the common findings:**

- Location at "always" on anything that is not a navigation or safety application.
- Full photograph library where selected photographs would do.
- Full address book access, including on applications you have used once.
- Permissions on applications you never opened, granted during setup.

**Deliverable.** The table with at least fifteen rows, and a count of grants reduced.

---

### Part 3: What Is Inside Them (30 minutes)

Pick five applications you kept. For each, find out what is in it using something the developer did not write.

| App | Components found | Permissions declared | Alternative with fewer? |
|---|---|---|---|

If you are on iPhone, **enable the network activity report now** even though it will have nothing in it yet, and set a reminder to read it in a week. It records forward, not backward.

**Deliverable.** The table, and one sentence on the largest difference you found between two applications doing the same job.

---

### Part 4: Label Versus Reality (25 minutes)

Pick two applications that do the same job. For each, record what the store label declares, then what the privacy policy says, then what Part 3 found.

| | App A | App B |
|---|---|---|
| Label declares | | |
| Policy describes | | |
| Components found | | |
| Gap between them | | |

**Deliverable.** The table, and your answer to: which of the three sources would you cite to a client, and why?

---

### Part 5: Audit a Recommendation List (30 minutes)

Find any published list of privacy-respecting mobile applications, including the one in the source material for this lesson if you have it.

For at least eight entries, mark each:

- **Still correct.**
- **Stale**, and say what changed: acquisition, sale, policy change, feature added, or the reason given was never right.
- **Wrong when written.**

| Entry | Verdict | What changed, and when |
|---|---|---|

Section 7 gives you five to calibrate against. Find at least two of your own.

**Deliverable.** The table, the list's date if you can find one, and one paragraph: what would you change about how it is written so it ages better?

---

### Part 6: Your Own Entry (15 minutes)

Write one recommendation, in the style Section 7 argues for, for an application you actually use.

It must include: the date, the reason stated as a checkable property rather than a verdict, and one sentence naming what would make this recommendation stop being true.

**Deliverable.** The recommendation. Three to four sentences.

---

### Submission

1. **The count**, Part 1, with the number deleted.
2. **Ladder pass**, Part 2, fifteen rows minimum.
3. **Components**, Part 3, five applications.
4. **Label versus reality**, Part 4.
5. **List audit**, Part 5, eight entries.
6. **Your own entry**, Part 6.
7. **Reflection**, half a page: of the three channels in Figure 1, which had you been treating as the whole problem, and what would you now tell a client who asked whether their staff's phones are a risk?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| The count | 15 | Genuinely counted, and applications actually removed |
| Ladder pass | 25 | Fifteen rows, rung four used where available, breakage recorded honestly |
| Components | 20 | Checked with an independent tool rather than from the store listing |
| Label versus reality | 15 | Three sources compared, and the citation question answered with a reason |
| List audit | 15 | Eight entries, at least two staleness findings reached independently |
| Your own entry | 5 | Dated, property-based, and states its own expiry condition |
| Reflection | 5 | Answers the client question in terms of channels rather than settings |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources

- Electronic Frontier Foundation, on how behavioural advertising supplies the surveillance industry: https://www.eff.org/deeplinks/2025/01/online-behavioral-ads-fuel-surveillance-industry-heres-how
- Mozilla Foundation, See No Evil, on the accuracy of store data safety labels: https://www.mozillafoundation.org/en/privacynotincluded/articles/mozilla-study-data-privacy-labels-for-most-top-apps-in-google-play-store-are-false-or-misleading/

### On the Bidstream

- Malwarebytes, on the January 2025 location data broker breach: https://www.malwarebytes.com/blog/news/2025/01/massive-breach-at-location-data-seller-millions-of-users-affected

Read this one for the application list rather than the breach narrative. The interesting part is how ordinary those applications are.

### Discussion Questions

1. Part 1 asked you to count applications before changing any settings. Did the count change what you thought the problem was?
2. Section 7 argues that recommendations should be dated and reason-based. Look at something you have already published. Would it survive the same audit?
3. Channel three is difficult to observe from the device and cannot be closed by settings. What would you actually advise a client whose staff visit sensitive sites, and what does that advice cost them?

---

## References

1. [Electronic Frontier Foundation, on real time bidding and the surveillance supply chain](https://www.eff.org/deeplinks/2025/01/online-behavioral-ads-fuel-surveillance-industry-heres-how)
2. [Malwarebytes, massive breach at location data seller, January 2025](https://www.malwarebytes.com/blog/news/2025/01/massive-breach-at-location-data-seller-millions-of-users-affected)
3. [Mozilla Foundation, See No Evil, on data safety label accuracy](https://www.mozillafoundation.org/en/privacynotincluded/articles/mozilla-study-data-privacy-labels-for-most-top-apps-in-google-play-store-are-false-or-misleading/)
4. [TechRadar, on iOS 18 letting users choose which contacts an application can access](https://www.techradar.com/computing/software/apple-to-empower-privacy-on-ios-18-by-letting-users-choose-who-can-have-access-to-their-contacts)
5. [gHacks, on the 2023 acquisition of an open source iOS authenticator](https://www.ghacks.net/2023/12/19/psa-raivo-otp-for-ios-was-acquired-by-mobime-a-few-months-ago/)
6. [OpenStreetMap wiki, on the mapping application and the fork created after its sale](https://wiki.openstreetmap.org/wiki/MAPS.ME)
7. [BleepingComputer, on cloud backup for an authenticator application and its encryption](https://www.bleepingcomputer.com/news/google/google-will-add-end-to-end-encryption-to-google-authenticator/)
8. [Help Net Security, on the September 2024 messenger policy change covering network addresses and telephone numbers](https://www.helpnetsecurity.com/2024/09/24/telegram-legal-requests/)
