---
title: "Module 2 Lesson 4: Extensions: Two Costs, Four Questions, and a Platform That Moved"
course: Digital Privacy for Ethical Hackers
module: 2
lesson: 2.4
format: article
reading_time: 26 minutes
tags:
  - digital-privacy-course
  - privacy
  - browser-privacy
  - extensions
  - manifest-v3
  - content-blocking
  - opsec
---

# Extensions: Two Costs, Four Questions, and a Platform That Moved

## About This Lesson

Extension advice ages faster than any other kind of browser advice, and this topic has just been through the largest change in its history. A guide written before that change is not merely out of date. It tells you to install something that no longer exists in the browser it names.

So this lesson covers the platform change first, then the trade-off, then a test you can apply to any extension in future.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Explain what Manifest V3 changed and which browsers still run a full content blocker.
- State the two costs an extension carries, with the measured evidence for the first one.
- Apply a four-question test before installing anything.
- Recognise extensions that older guides recommend and that no longer earn their place.
- Build a stack appropriate to each of your browsers.

### Prerequisites

- Lessons 2.1 to 2.3, and your browser choice from 2.3.

---

## 1. The Platform Moved

The interface that let an extension inspect a network request and decide, in code, whether to block it has been replaced in Chromium-based browsers. In its place is a declarative interface: an extension registers rules in advance, the browser applies them, and there is a ceiling on how many rules it may register.

That sounds like an implementation detail. It is not. It removes the ability to make a blocking decision from page context at request time, and it constrains cosmetic filtering, per-site dynamic rules and scriptlet injection, which are the capabilities that a serious content blocker depends on.

![[module_2_lesson_4_manifest_v3.svg]]

*Figure 1. The same extension, three different outcomes, depending only on which browser you run.*

### The Timeline, Precisely

Chrome's own documentation sets it out. The store stopped accepting new Manifest V2 submissions in January 2022. Disabling began in the stable channel in October 2024. By 31 March 2025 the extensions were disabled by default for all users, though they could still be re-enabled temporarily. On 24 July 2025, Chrome 138 disabled them with no option to re-enable, and Chrome 138 is named as the final version to support them at all. The enterprise exemption ended in Chrome 139. Remaining Manifest V2 listings were removed from the store on 31 August 2026. (Reference 1)

**Where that leaves each browser:**

- **Chrome and Edge.** The full uBlock Origin is unavailable. It was delisted from the store in late 2024. What remains is the Lite build, written for the new interface, which blocks meaningfully less. (Reference 2)
- **Brave.** Manifest V2 extensions are hosted by Brave on its own infrastructure, so the full extension still works. Brave also runs its own blocker below the extension layer, which the platform change does not touch at all.
- **Firefox.** A different engine and a different decision. The blocking request interface was retained, so the full extension works with dynamic filtering, cosmetic rules and per-site control intact. (Reference 2)

### What This Means for the Order of Your Decisions

It used to be reasonable to choose a browser, then choose extensions. That order no longer works.

Which content blocker you can run is now a **property of the browser**. If effective content blocking matters to your work, it belongs in the Lesson 2.3 browser decision, not in this lesson. That is a genuine argument for Firefox that did not exist a few years ago, and it is worth revisiting your Lesson 2.3 conclusion in light of it.

**One caution before you treat this as settled.** Brave's position depends on Brave continuing to fund hosting and patching for a platform its upstream has abandoned. That is a maintenance commitment, not a technical guarantee. Check it periodically rather than assuming it.

---

## 2. The Two Costs

Every extension guide states the paradox: extensions protect you and also expose you. Almost none of them put numbers on it.

![[module_2_lesson_4_two_costs.svg]]

*Figure 2. The benefit is real. So are both costs.*

### The Benefit Is Large, and Should Be Said Plainly

A content blocker removes more tracking than every preference in Lesson 2.3 combined. Requests that never leave your machine cannot be logged, correlated or sold. If you do one thing from this module, it is this one.

### Cost 1: Entropy, Measured

Lesson 2.2 established that a fingerprint is a budget of bits. Extensions add to it, and this has been measured rather than assumed.

Oleksii Starov and Nick Nikiforakis published XHOUND at the IEEE Symposium on Security and Privacy in 2017. They found that between about 9 percent and 23 percent of evaluated extensions could be fingerprinted automatically, the range depending on extension popularity and on the threat model assumed. Detection works from the traces an extension leaves in a page's structure, from files it exposes, and from the pattern of what it blocks. Collecting extension profiles from 854 real users, they found many carried distinctive sets. (Reference 3)

**Note what that does and does not say.** It is not "every extension is detectable." Most were not detectable by their method. But the ones that modify pages, which includes most privacy extensions, are exactly the detectable kind, and combinations become distinctive quickly.

This is also where popularity flips from irrelevant to useful. Lesson 2.2's crowd argument applies: a very widely used extension puts you in a large group, while an obscure one puts you in a small one. For extensions specifically, common is safer.

### Cost 2: Trust, and It Is Not Divisible

To block content on every page, an extension must be able to read and alter every page. There is no narrower permission that does the job.

So the same grant that lets it remove a tracker lets it read what you type, record where you go, and change what you see. You are not trusting a product. You are trusting whoever ships the next update.

Three documented cases show what that means:

**Web of Trust, 2016.** A site reputation extension, found to be selling detailed browsing histories. Removed from the stores.

**Stylish, 2018.** A page styling extension with roughly two million users. It changed hands, was acquired by an analytics firm, and was updated to report every site its users visited back to that firm's servers. Researcher Robert Heaton documented the traffic, and both Mozilla and Google pulled it. The community forked the earlier code into Stylus, which is the version that survives. (Reference 5, Reference 6)

**The Chrome store, 2020.** Researcher Jamila Kaya, working with Duo Security, found a coordinated campaign of extensions injecting ads and exfiltrating browsing data. An initial 70 extensions with over 1.7 million installations led Google to identify and deactivate around 430 more. (Reference 7, Reference 8)

**The pattern in two of those three is a change of ownership.** The code was fine when you installed it. Extensions update themselves silently, so "I reviewed it before installing" is a statement about the past.

---

## 3. Four Questions Before You Install Anything

![[module_2_lesson_4_install_test.svg]]

*Figure 3. Question 1 eliminates most candidates on its own.*

**Question 1: Does a browser setting already do this?**

A setting costs no entropy, requires no trust, and cannot be sold to anybody. When a setting and an extension do the same job, the setting wins, every time.

This retires a lot of standard recommendations. Forced encrypted connections is a browser feature now. Storage isolation between sites is a browser feature now, through Total Cookie Protection. (Reference 9) Canvas control is a browser mode. Each of those used to need an extension.

**Question 2: Can you name who maintains it now?**

Not who wrote it. Who ships the updates today. This is the question that would have caught Stylish.

**Question 3: Do the permissions match the job?**

A content blocker genuinely needs to read every page. A unit converter does not. A mismatch between what an extension asks for and what it visibly does is the clearest signal you will ever get, and it costs thirty seconds to check.

**Question 4: Will you notice if it changes?**

If you will never look at this extension again, you are trusting it permanently, in advance, including trusting whoever buys it later. Put a review date in your calendar or do not install it.

Four yes answers, or it does not go on.

---

## 4. Recommendations That Have Expired

Three that appear in nearly every list, including this course's earlier version.

### HTTPS Everywhere

Correctly retired. The function is built into browsers now, and the extension itself was wound down by its authors. One less extension is one less of both costs. This one the older guidance already got right.

### Decentraleyes and Local Library Extensions

These intercepted requests for common script libraries and served local copies instead, so that content delivery networks could not observe you across sites.

Two things ended the case for them. The platform change broke the redirection method they rely on under Manifest V3. And modern browsers partition the cache per site, which already removes much of the cross-site observation these were built to prevent. The bundled library sets have also aged, so you may be serving old code to sites that expect current code.

LocalCDN, the maintained fork, is in better health than the original. (Reference 10) Neither belongs in a default recommendation any more, which is a change from where the earlier version of this lesson placed them.

### Privacy Badger, Described Correctly

Lesson 2.3 covered this and it belongs here too, because this is the lesson that used to say "let it learn."

Privacy Badger's local learning has been **off by default since October 2020**. The Electronic Frontier Foundation turned it off after Google's security team demonstrated the flaw: a tracker could deliberately shape what your individual copy learned, and your resulting block list would then be unique to you. The learning mechanism had become a fingerprinting vector. It now ships a shared pre-trained list. (Reference 4)

That is the entropy argument in its cleanest possible form, and it is worth sitting with. A privacy tool that adapted to each user made each user identifiable. Any tool that behaves differently for you than for everybody else has this problem in some measure.

Privacy Badger remains reasonable to run. Just do not run it expecting it to learn, and do not turn learning back on without understanding what you are accepting.

---

## 5. Your Stacks

The right-hand panel of Figure 3 has the summary. In prose, with the reasoning:

**Firefox.** The full content blocker, which still has its complete capability here. Containers for separation between identities. Possibly a third extension for stripping tracking parameters out of addresses. That is two, or three.

**Brave.** Its blocking already runs below the extension layer, so most of what you would add duplicates something you already have. Zero extensions is the correct default, and it is the strongest argument for Brave as a low-effort choice.

**Chrome and Edge.** Only the reduced blocker is available, and no extension choice recovers what the platform removed. Install it, and understand that it does less than the equivalent elsewhere. If content blocking is important to this browser's role, the honest answer is to change browsers rather than to keep shopping for extensions.

**Tor Browser.** None. Not one. Lesson 2.3 gave the reason and it has not changed: the protection is being identical to every other user, so anything you add is a change away from safety. Tor Browser ships what it needs.

### Applied to the Four Layers

From Lesson 1.2, with each layer getting the smallest stack that serves it:

Your personal browser gets whatever makes it pleasant, plus a blocker. Your professional browser gets the blocker and containers. Your research browser gets the blocker and nothing else, because a distinctive extension set is exactly the linkage you are trying to avoid. Your anonymous browser gets nothing.

**Note the shape of that.** The stack gets *smaller* as the privacy requirement gets *higher*. That is counterintuitive and it is correct, and it is the single most useful idea in this lesson.

---

## 6. Summary

- The extension platform changed. Chromium replaced the blocking request interface with a declarative one, and Chrome disabled Manifest V2 for all users in Chrome 138 in July 2025.
- The full uBlock Origin runs in Firefox and Brave. Chrome and Edge get the reduced build.
- Which blocker you can run is now a property of your browser, so the decision belongs in Lesson 2.3.
- Every extension carries two costs: measurable entropy, and undivided trust in whoever ships the next update.
- Between about 9 and 23 percent of evaluated extensions were automatically fingerprintable, and many real users carry distinctive sets.
- Two of the three worst documented cases began with a change of ownership, so check who maintains a tool now.
- Ask four questions. The first, whether a browser setting already does it, eliminates most candidates.
- Privacy Badger stopped learning by default because the learning made its users identifiable.
- The higher your privacy requirement, the smaller your stack. Tor Browser gets none.

**Action item.** Open your extensions page right now and count them. For each one, answer question 1 from Figure 3. Remove anything a setting already covers. That is fifteen minutes and it is most of this lesson's value.

The next lesson is the last in Module 2, and it goes deep on compartmentalization: profiles, containers, and the workflow that keeps the separations from quietly collapsing.

---

## Knowledge Check

**Question 1.** What did Manifest V3 change for content blockers?

- A) It removed extension support from browsers entirely
- B) It replaced the interface that let an extension decide in code whether to block a request with a declarative one that has fixed rule limits and no dynamic filtering
- C) It made extensions faster with no functional change
- D) It required all extensions to be paid

**Answer: B.** The lost capabilities are cosmetic filtering, per-site dynamic rules and scriptlet injection.

**Question 2.** What is the current position of the full uBlock Origin in Chrome?

- A) It works normally
- B) It works but runs more slowly
- C) It is unavailable. Manifest V2 was disabled for all users in Chrome 138 in July 2025, and the extension was delisted from the store
- D) It requires an enterprise licence

**Answer: C.** Chrome 138 is named in Google's own documentation as the final version to support Manifest V2.

**Question 3.** Which browsers still run the full uBlock Origin?

- A) Chrome and Edge
- B) Edge only
- C) Chrome and Brave
- D) Firefox and Brave, because Firefox kept the blocking request interface and Brave hosts Manifest V2 extensions on its own infrastructure

**Answer: D.** Two different reasons for the same outcome, and Brave's is a maintenance commitment rather than a technical one.

**Question 4.** Research measuring extension fingerprintability found:

- A) Between about 9 and 23 percent of evaluated extensions could be fingerprinted automatically, and across 854 real users many carried a distinctive set
- B) No extension can be detected by a web page
- C) Every extension is detectable
- D) Only content blockers are detectable

**Answer: A.** The detectable ones are those that modify pages, which is most privacy extensions.

**Question 5.** Why does popularity count in an extension's favour, when it usually does not?

- A) Popular extensions are always better written
- B) Popular extensions receive official approval
- C) Popularity is irrelevant here
- D) A widely used extension puts you in a large group and attracts far more scrutiny

**Answer: D.** The same crowd argument from Lesson 2.2, applied to extensions.

**Question 6.** What do Stylish and Web of Trust have in common?

- A) Neither was removed from any store
- B) Both were trusted tools that began reporting users' browsing, and in Stylish's case the change followed a sale to a new owner
- C) Both were open source and independently audited
- D) Both were browser features rather than extensions

**Answer: B.** This is why question 2 in Figure 3 asks who maintains it now, not who wrote it.

**Question 7.** Privacy Badger's local learning is:

- A) Off by default, because a tracker could shape what your copy learned and turn your block list into a fingerprint
- B) On by default, and you should let it learn
- C) Removed from the extension entirely
- D) Available only to paying users

**Answer: A.** Option B is the advice in most guides, including this course's earlier version. Learning remains available as a choice, and it remains a fingerprinting risk.

**Question 8.** Why is Decentraleyes no longer a standard recommendation?

- A) It was found to be malicious
- B) It was never useful
- C) It does not function under Manifest V3, and cache partitioning already addresses much of the cross-site observation it was built to prevent
- D) It was merged into uBlock Origin

**Answer: C.** Its bundled library set has also aged, which is its own problem.

**Question 9.** What is the first question to ask before installing any extension?

- A) Is it free?
- B) How many users does it have?
- C) Is the source available?
- D) Does a browser setting already do this, since a setting costs no entropy and requires no trust

**Answer: D.** The other three matter, and they matter second.

**Question 10.** How many extensions belong in Tor Browser?

- A) Three to five
- B) One, a content blocker
- C) None, because anything you add moves you out of the group of identical users that is the entire protection
- D) As many as you need, since the network hides everything anyway

**Answer: C.** Tor Browser ships what it needs. This is design, not preference.

---

## Exercise: Audit, Then Decide

**Time: 75 minutes, plus 20 minutes to write up.**

### Objective

Reduce your extension set to what survives Figure 3, and verify what each survivor actually does.

---

### Part 1: The Question 1 Pass (20 minutes)

Open the extensions page in every browser you use. List everything, including the ones you forgot were there.

For each, answer only question 1: **does a browser setting already do this?**

| Extension | Browser | What it does | Setting that covers it | Keep or remove |
|---|---|---|---|---|

Remove everything that a setting covers, now, before you go further. Record how many you removed.

**Deliverable.** The table and the count.

---

### Part 2: The Remaining Three Questions (20 minutes)

For each survivor:

1. **Who ships the updates today?** Find the current maintainer, not the original author. Check whether the project has changed hands.
2. **Do the permissions match the job?** Read what it requests. Compare against what it visibly does.
3. **When will you review it again?** Put an actual date somewhere you will see it.

| Extension | Current maintainer | Permissions match? | Next review date |
|---|---|---|---|

Anything that fails 2 or 3 comes off.

**Deliverable.** The completed table, plus one sentence on any extension whose maintainer surprised you.

---

### Part 3: Confirm What Your Browser Can Actually Run (15 minutes)

This is the part the platform change made necessary.

For each browser you use, determine which content blocker build you are actually running. The blocker's own interface will tell you, and its name will differ between the full and reduced builds.

| Browser | Which build is installed | Full capability? | If reduced, what are you losing |
|---|---|---|---|

Then load one advertisement-heavy page in two different browsers and compare the blocked-request counts. Record both numbers.

**If they differ significantly, that difference is the platform change, measured on your own machine.**

**Deliverable.** The table and the two counts.

---

### Part 4: Measure the Entropy Cost (10 minutes)

In one browser, run a fingerprint test from Lesson 2.1 with your extensions enabled. Then disable them all and run it again.

| State | Result | Notable attributes |
|---|---|---|
| Extensions enabled | | |
| Extensions disabled | | |

Two honest questions afterwards:

1. Did the result change measurably? Frequently it does not, because these tests do not probe for extensions directly, and that is worth knowing rather than assuming.
2. Given what Section 2 said about detection working through page modification, what would a purpose-built extension detector find that a generic fingerprint test does not?

**Deliverable.** The comparison and your answers.

---

### Part 5: Stacks by Layer (10 minutes)

Write your final stack for each of your four layers from Lesson 1.2.

| Layer | Browser | Extensions | Why this few |
|---|---|---|---|
| Personal | | | |
| Professional | | | |
| Research | | | |
| Anonymous | | | |

Then check the shape: **does your stack get smaller as the privacy requirement rises?** If it gets larger, explain why, because that is the opposite of what Section 5 argues and you should be able to defend it.

**Deliverable.** The table and, if applicable, your defence.

---

### Submission

1. **The audit**, Parts 1 and 2, with removal count.
2. **Platform check**, Part 3, with the two blocked-request counts.
3. **Entropy measurement**, Part 4, with both answers.
4. **Final stacks**, Part 5.
5. **Reflection**, half a page: which extension did you keep out of habit rather than reasoning, and what did question 2 turn up that you did not know?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Question 1 pass | 25 | Complete inventory, settings correctly identified as replacements |
| Remaining questions | 25 | Current maintainer actually researched, permissions genuinely compared |
| Platform check | 20 | Correct build identified per browser, counts recorded |
| Entropy measurement | 15 | Both states measured, and the limits of the test understood |
| Stacks by layer | 15 | Smallest workable set per layer, with the shape defended |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources

- Chrome for Developers, Manifest V2 deprecation timeline: https://developer.chrome.com/docs/extensions/develop/migrate/mv2-deprecation-timeline
- uBlock Origin, which build runs where: https://ublockorigin.com/

Go to these rather than to comparison articles. This topic changed on specific dates, and the primary sources give the dates.

### Reading

- Electronic Frontier Foundation on the Privacy Badger change: https://www.eff.org/deeplinks/2020/10/privacy-badger-changing-protect-you-better

### Discussion Questions

1. Your Lesson 2.3 browser choice was made before you knew about the platform change. Does it still hold?
2. Which extension would you notice being compromised, and how would you notice? If the answer is that you would not, what does that tell you?
3. Section 5 argues your stack should shrink as your privacy requirement grows. Does your own setup follow that shape, and if not, why not?

---

## References

1. [Chrome for Developers, Manifest V2 deprecation timeline](https://developer.chrome.com/docs/extensions/develop/migrate/mv2-deprecation-timeline)
2. [uBlock Origin, official project site and browser availability](https://ublockorigin.com/)
3. [Starov and Nikiforakis, XHOUND: Quantifying the Fingerprintability of Browser Extensions, IEEE Symposium on Security and Privacy 2017](https://www.cyberphilosopher.org/wp-content/uploads/2019/05/xhound-oakland17.pdf)
4. [Electronic Frontier Foundation, Privacy Badger Is Changing to Protect You Better, October 2020](https://www.eff.org/deeplinks/2020/10/privacy-badger-changing-protect-you-better)
5. [Robert Heaton, Stylish is back, and you still should not use it, August 2018](https://robertheaton.com/2018/08/16/stylish-is-back-and-you-still-shouldnt-use-it/)
6. [BleepingComputer, Chrome and Firefox pull Stylish add-on after report it logged browser history, July 2018](https://www.bleepingcomputer.com/news/software/chrome-and-firefox-pull-stylish-add-on-after-report-it-logged-browser-history/)
7. [The Hacker News, 500 Chrome extensions caught stealing private data of 1.7 million users, February 2020](https://thehackernews.com/2020/02/chrome-extension-malware.html)
8. [Sophos, Google pulls 500 malicious Chrome extensions after researcher tip-off](https://www.sophos.com/en-us/blog/google-pulls-500-malicious-chrome-extensions-after-researcher-tip-off)
9. [Mozilla Support, Total Cookie Protection and website breakage FAQ](https://support.mozilla.org/en-US/kb/total-cookie-protection-and-website-breakage-faq)
10. [Decentraleyes, project overview and history](https://en.wikipedia.org/wiki/Decentraleyes)
