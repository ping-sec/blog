---
title: "Module 2 Lesson 3: Choosing and Configuring a Browser: Firefox, Brave, Tor"
course: Digital Privacy for Ethical Hackers
module: 2
lesson: 2.3
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - browser-privacy
  - firefox
  - brave
  - tor
  - configuration
  - opsec
---

# Choosing and Configuring a Browser: Firefox, Brave, Tor

## About This Lesson

Lesson 2.1 measured the problem. Lesson 2.2 explained the mechanism and gave you four defense strategies. This lesson picks the tools.

It is also the lesson where published guidance goes stale fastest. A tracking technique from 2016 is usually still a tracking technique. A browser preference from 2016 is frequently a preference that does nothing, or one that now causes the harm it was written to prevent. Several settings in this lesson's original form fell into that category, and this version says so where it matters.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Select a browser from what a task requires, rather than from how private you would like to feel.
- Configure Firefox in ordered tiers, and stop at the tier that is enough.
- Recognise preferences that older guides recommend and that you should now skip.
- Use Tor Browser correctly, which mostly means leaving it alone.
- Explain why Brave's Tor window is not a substitute for Tor Browser.

### Prerequisites

- Lessons 2.1 and 2.2, and your own measurements from both.
- Your threat model from Lesson 1.4.

---

## 1. Start With What the Task Requires

Most browser guidance is organised as a ladder of privacy, from least to most, and invites you to climb as far as you can stand. That framing produces two bad outcomes: people install the heaviest tool for light work and abandon it, or they pick something comfortable without checking whether it addresses their actual requirement.

Lesson 2.2 gave the better framing. Ask which of two problems you have.

![[module_2_lesson_3_selection.svg]]

*Figure 1. Four questions in order. Stop at the first one you answer yes to.*

**Question 1 comes first for a reason.** If the scope agreement requires attributable traffic, everything else in this lesson is a scope violation. Lesson 1.1 raised this and it keeps coming back because people keep skipping it. Read the rules before you choose the tool.

**Question 2 is unattributability.** A target cannot tell your session from anybody else's. Only uniformity delivers this, and uniformity means Tor Browser, unmodified.

**Question 3 is unlinkability.** Your sessions cannot be tied to each other or to your other identities. Separation delivers this, cheaply, and it is what most professional security work actually needs.

**The common error is answering question 2 when the task only asked question 3.** Unattributability is expensive: it costs speed, functionality, and the ability to customise anything. Unlinkability costs a profile and some discipline. If you cannot state which one your task needs, you are not ready to choose a browser.

---

## 2. The Three Browsers

![[module_2_lesson_3_browser_matrix.svg]]

*Figure 2. Not a ranking. They do not solve the same problem.*

Read the strategy column first, because everything else follows from it. Firefox is whichever strategy you configure it to be. Brave randomises, seeding the noise per site and per session. (Reference 6) Tor Browser enforces uniformity, and blocks or prompts on the rendering interfaces rather than adding noise to them. (Reference 11)

Two entries in Figure 2 correct claims that appear in most comparisons, including this course's earlier version.

**Brave's Strict fingerprinting mode no longer exists.** Brave removed it in version 1.64, in early 2024. Their stated reasons are worth reading, because one of them is a Lesson 2.2 argument in the vendor's own words: fewer than half a percent of users had Strict enabled, which made that small group **more** conspicuous rather than less. Strict also broke sites often enough to limit its usefulness. There is now one fingerprinting setting. (Reference 5)

So if a guide tells you to set Brave's fingerprint blocking to Strict, it predates 2024. The option is not there.

**Brave's Tor window is not Tor Browser.** It routes your traffic through the Tor network, which addresses the network observer from Lesson 2.1. It does not give you Tor Browser's uniform fingerprint, so it does not address observer 3, and therefore it does not deliver unattributability. It is useful for hiding your address from a site. It is not the answer to question 2 in Figure 1.

---

## 3. Firefox: Climb in Order

Firefox is the one you configure, and configuration is where the damage gets done. The failure mode is not under-configuring. It is pasting in thirty preferences from a list, breaking something three weeks later, and having no idea which line caused it.

![[module_2_lesson_3_firefox_ladder.svg]]

*Figure 3. Four tiers. Most of the benefit is in the first two.*

### Tier 1: The Settings Interface

Enhanced Tracking Protection set to Strict. Telemetry and studies off. HTTPS-Only Mode on. Encrypted name resolution on. Permissions for location, camera, microphone and notifications set to block by default.

**This tier is worth more than it looks.** Strict is what enables Total Cookie Protection, which gives every site its own isolated storage jar, so a tracker present on two sites cannot join what it stored on each. That is the single most valuable thing on this whole ladder, and it is a dropdown. (Reference 1)

Also turn off the built-in password manager and use a dedicated one, and decide whether you want history and cookies cleared on close.

### Tier 2: A Content Blocker and Containers

One well-maintained content blocker. One, not four. Lesson 2.2's entropy argument applies to extensions as much as anything else, and a distinctive extension set is a distinctive fingerprint.

Then Multi-Account Containers, which is the cheapest separation available. Each container gets its own cookie jar, so your professional identity and your research identity can run in one browser without sharing state.

**Tier 2 is where Figure 1, question 3 gets answered.** If unlinkability is your requirement, you are done here for most purposes.

### Tier 3: Targeted Fingerprinting Protection

Firefox has two fingerprinting mechanisms and they are not interchangeable.

**Fingerprinting Protection**, inside Enhanced Tracking Protection, is the lighter targeted one. It is what Mozilla recommends for most users. It can be relaxed for a single site without disabling it everywhere. (Reference 3)

### Tier 4: Resist Fingerprinting

**Resist Fingerprinting** is the Tor-derived uniformity mode, and it needs describing accurately because almost everybody gets one detail wrong.

It forces a single time zone. It rounds the window with letterbox bars so your dimensions fall into a common bucket. It limits font access. And when a page tries to read canvas pixels, **it prompts you for permission**, declining automatically when there was no user interaction. (Reference 2, Reference 4)

It does **not** randomise canvas. That claim appears in a great many guides, including the earlier version of this lesson, and it is wrong. Randomisation is Brave's strategy. Resist Fingerprinting is uniformity, and uniformity blocks or prompts rather than adding noise. Lesson 2.2 made this distinction and it matters here: if you believe this mode randomises, you will expect unlinkability from something that is trying to give you uniformity.

The catch is that it is global. On or off, every tab, with no per-site relief. Pages will break, and when they do you cannot make an exception.

**When to use it.** When Figure 1, question 2 said yes. And at that point, ask why you are not simply using Tor Browser, which does the same thing with a much larger group of identical users to hide among.

### Preference Files

Community preference collections such as arkenfox are genuinely good work, and they are not a shortcut. They are a starting point that assumes you will read the annotations and write your own overrides file for the parts you do not want.

Copying the file in and restarting is the way people end up with a browser they cannot debug. If you use one, use its override mechanism and its cleaner script as documented, and read what you are enabling. (Reference 10)

---

## 4. Five Preferences to Stop Copying

The right-hand panel of Figure 3 lists these. Each was reasonable advice once. Each survived past the change that retired it.

**`privacy.firstparty.isolate`** is superseded. Total Cookie Protection does the same job dynamically, is on with Strict, and breaks far less. Setting both is redundant and adds breakage for nothing. (Reference 1)

**`dom.battery.enabled`** does nothing useful in Firefox, because Firefox removed the battery interface years ago. As Lesson 2.2 noted, it is Chrome that still exposes it. Setting this preference in Firefox protects you from an interface that is not there.

**`webgl.disabled`** is heavy-handed. It breaks maps and charts, and a browser with no graphics support at all is an unusual signal in its own right. Tiers 3 and 4 already limit what graphics reports.

**`network.http.referer.XOriginPolicy`** is mostly redundant. As Lesson 2.1 covered, the browser default already sends only the origin across sites. Tightening it further mainly breaks sign-in flows that legitimately need the referrer.

**`media.peerconnection.enabled = false`** disables video calling outright. The local address leak it was written for is largely closed, as Lesson 2.1 covered. If you want the narrower protection, the address-policy preferences do it without removing the feature.

**The pattern is the point.** A preference name in a guide is advice with an expiry date that nobody printed on it. Before you set one, find out what it does now and what your browser already does without it.

---

## 5. Extensions: Fewer Than You Think

One content blocker. Possibly a link cleaner. Containers if you use Firefox. That is a complete list for most people.

Two currency notes, because both of these appear in every recommendation list.

**Privacy Badger no longer learns by default, and the reason is instructive.** It originally built its block list by observing tracking behaviour as you browsed. The Electronic Frontier Foundation turned that off by default in October 2020 after Google's security team pointed out the flaw: a tracker could deliberately manipulate what your copy learned, and your resulting block list would then be unique to you. The learning mechanism had become a fingerprinting vector. It now ships a pre-trained list that every installation shares. (Reference 9)

Read that alongside Lesson 2.2. A privacy tool whose individualised behaviour made its users identifiable is the entropy argument in its purest form. Local learning remains available as an option, and it is still a fingerprinting risk.

**Decentraleyes and similar local-library extensions have largely been overtaken.** They were built to stop content delivery networks tracking you across sites. Total Cookie Protection and cache partitioning now address most of that, and the local library sets have aged.

**And the rule that outranks all of this:** every extension you add is entropy. Three deliberate extensions beat nine hopeful ones.

---

## 6. Tor Browser: Correct Use Is Mostly Restraint

Everything Tor Browser does depends on you being identical to every other user. That single fact generates all the rules.

**Do not customise it.** Not the window size, not an add-on, not a preference, not the theme. Each change moves you out of the group that was protecting you. This is why Tor Browser deliberately makes customisation awkward.

**Do not sign in to anything tied to your identity.** The network cannot help you once you have told the site who you are.

**Use the security levels.** Standard, Safer and Safest. Safer disables scripting on insecure connections and gates media. Safest disables scripting everywhere. Pick the level the task needs and expect breakage to increase with it. (Reference 8)

**Do not download and immediately open files.** A document can fetch a remote resource when opened, outside the browser and outside Tor, which discloses your real address. Open it on a machine that has no network path, or wait.

**Remember the time zone.** Tor Browser reports one time zone for everyone. If you then mention your local time in a message, you have undone it by hand.

### One Correction Worth Noting

Tor Browser used to ship HTTPS Everywhere. It does not any more. From Tor Browser 11.5, released in July 2022, HTTPS-Only Mode is built in and on by default, and HTTPS Everywhere is no longer bundled on desktop. (Reference 7) NoScript is still included.

This matters beyond the trivia. If a guide tells you to check that HTTPS Everywhere is enabled in Tor Browser, that guide is at least four years old, and you should assume the rest of its advice is the same vintage.

### Onion Services

Sites reachable only inside the Tor network. The connection never passes an exit relay, so there is no exit node to observe or interfere with it, and both ends stay anonymous.

**Find them the safe way.** When a site you already trust offers an onion service, your browser will show an offer to switch, driven by a header the site sends. Use that. Do not collect onion addresses from directories or from chat, because an address is a long random string that nobody can eyeball for correctness, and substituting a lookalike is the whole attack.

This lesson deliberately lists no onion addresses. Published lists go stale, services get retired, and a stale address in a course document is a phishing opportunity with an author's name on it.

### What Tor Does Not Do

It does not protect you from signing in to your own accounts. It does not protect you from writing in a recognisable style, which Lesson 1.3 covered. It does not protect you from an adversary who can observe both ends of the connection. And it does not protect you from a browser exploit, which is exactly why the security levels exist.

---

## 7. Putting It Together

A workable set for a security professional, mapped to Lesson 1.2's four layers:

**Personal.** Whatever you enjoy using, with a content blocker. Accept the tracking. Never used for work or research.

**Professional.** Firefox at tiers 1 and 2, with containers separating client contexts. Work accounts only.

**Research.** A separate profile or a separate machine. Brave or a configured Firefox inside it. Never any personal or work account.

**Anonymous.** Tor Browser, unmodified, at the level the task needs. No accounts, ever.

**The failure mode is not configuration. It is the wrong window.** Every one of these separations dies the same way, which is one task done in the wrong browser at the end of a long day. Lesson 1.2 made that point and it is still the most likely way your setup fails.

So make the browsers visually distinct. Different themes, different profile icons, different positions on screen. It is a trivial intervention and it prevents the actual failure far better than another preference does.

---

## 8. Summary

- Choose from what the task requires. Unattributability and unlinkability are different requirements with very different costs, and most work needs the cheaper one.
- If the scope agreement requires attributable traffic, none of this applies.
- Firefox tiers 1 and 2 carry most of the benefit. Enhanced Tracking Protection Strict brings Total Cookie Protection, which is the best single change on the ladder.
- Resist Fingerprinting is uniformity, not randomisation. It prompts on canvas rather than adding noise, and it is global with no per-site relief.
- Brave's Strict fingerprinting mode was removed in 1.64. Brave's Tor window is not Tor Browser.
- Tor Browser stopped bundling HTTPS Everywhere in 11.5, in 2022. Correct use of Tor Browser is mostly restraint.
- Privacy Badger stopped learning by default because the learning was itself a fingerprinting vector.
- Five commonly copied preferences should now be skipped. Check any preference before you set it.

**Action item.** Set up tier 1 and tier 2 today, in one browser. That is twenty minutes and it is most of the value. Everything above it can wait until you have a specific reason.

The next lesson covers extensions in detail: which ones earn their entropy, how to evaluate one before installing it, and what an extension can see about your browsing.

---

## Knowledge Check

**Question 1.** What does Firefox's Resist Fingerprinting mode do when a page tries to read canvas pixels?

- A) It returns randomised pixel data
- B) It returns the real data and logs a warning
- C) It removes the canvas element from the page
- D) It prompts for permission, and declines automatically when there was no user interaction

**Answer: D.** Option A is the common misconception. Randomisation is Brave's strategy. Resist Fingerprinting pursues uniformity, and uniformity blocks or prompts.

**Question 2.** Why is `privacy.firstparty.isolate` no longer the recommendation?

- A) Total Cookie Protection already isolates storage per site, is enabled with Enhanced Tracking Protection Strict, and breaks far less
- B) It was removed from Firefox
- C) It only works in Tor Browser
- D) It weakens security

**Answer: A.** Setting both is redundant and adds breakage for no gain.

**Question 3.** Brave's Strict fingerprinting mode:

- A) Is the recommended setting for security professionals
- B) Should be combined with Standard
- C) Was removed in version 1.64, partly because so few people used it that those users became more conspicuous
- D) Exists only on Android

**Answer: C.** A guide that tells you to enable it predates 2024.

**Question 4.** Tor Browser no longer bundles HTTPS Everywhere. What replaced it?

- A) A different extension from the same project
- B) HTTPS-Only Mode, built in and on by default since Tor Browser 11.5
- C) Nothing, so connections are now unprotected
- D) NoScript absorbed the function

**Answer: B.** NoScript is still bundled. HTTPS Everywhere is not.

**Question 5.** Privacy Badger's local learning is off by default. Why?

- A) It slowed the browser down
- B) It blocked too many sites
- C) A tracker could manipulate what your copy learned, so your resulting block list became a fingerprint
- D) It was replaced by a paid service

**Answer: C.** A privacy tool whose individualised behaviour identified its users. It now ships a shared pre-trained list.

**Question 6.** You must not resize the Tor Browser window because:

- A) It crashes the browser
- B) It breaches Tor network policy
- C) It slows the circuit
- D) The protection is that every user looks identical, so any change moves you out of that group

**Answer: D.** Every Tor Browser rule comes from this one fact.

**Question 7.** Brave's Tor window and Tor Browser differ mainly in that:

- A) Brave's version is faster and otherwise equivalent
- B) Brave routes the traffic but does not give you the uniform fingerprint, so it does not deliver unattributability
- C) Brave's version does not encrypt the connection
- D) There is no meaningful difference

**Answer: B.** It addresses the network observer and not the measurement observer. Useful, but not an answer to Figure 1 question 2.

**Question 8.** A task requires that your sessions cannot be linked to each other, but not that you be indistinguishable from other users. What is proportionate?

- A) A separate profile or machine per identity, with a randomising or configured browser inside it
- B) Tor Browser at Safest for everything
- C) Resist Fingerprinting enabled globally
- D) Disabling scripting

**Answer: A.** This is question 3, not question 2. Answering question 2 when the task asked question 3 is the common and expensive error.

**Question 9.** Which Firefox tier delivers Total Cookie Protection?

- A) Installing a content blocker
- B) Setting Enhanced Tracking Protection to Strict in the settings interface
- C) Setting `privacy.resistFingerprinting` to true
- D) Applying a community preference file

**Answer: B.** The most valuable change on the ladder is a dropdown, not a preference edit.

**Question 10.** Your engagement rules require a registered source address and an identifying header. What do you do?

- A) Use Tor Browser and register the exit relay address
- B) Use Brave's Tor window as a compromise
- C) Use an ordinary identifiable browser and profile, because anonymity here is a scope violation
- D) Use a VPN with a static address

**Answer: C.** Figure 1, question 1. The scope agreement outranks the general principle, and option A is not even technically coherent, since exit relays change.

---

## Exercise: Build and Verify Your Set

**Time: 2.5 hours, plus 30 minutes to write up.**

### Objective

Configure one browser properly, verify separation between identities, and prove that your setup is one you will still be running next month.

### Safety Note

Do this on a personal machine. Do not reconfigure an employer device or anything covered by an engagement.

---

### Part 1: Answer Figure 1 First (15 minutes)

Before installing anything, take three tasks you actually do and run each through Figure 1.

| Task | Which question did you stop at? | Browser it implies |
|---|---|---|

Be honest on the second column. If all three stop at question 2, re-read Section 1, because that is unlikely to be true.

**Deliverable.** The table, with one sentence on any task where your current habit does not match the answer.

---

### Part 2: Firefox, One Tier at a Time (50 minutes)

Create a fresh profile so you can throw it away: `about:profiles`, then create one.

Record a baseline on the fresh profile using the test sites from Lesson 2.1.

Then apply **one tier at a time**, and after each tier record: your uniqueness result, whether the third-party requests on a news site dropped, and whether anything broke.

| Tier | Uniqueness | Trackers blocked | What broke |
|---|---|---|---|
| Baseline | | | |
| Tier 1, settings only | | | |
| Tier 2, blocker and containers | | | |
| Tier 3, targeted protection | | | |
| Tier 4, Resist Fingerprinting | | | |

**The point of doing it in this order** is to see how much tier 1 alone achieves. Most people never measure that, because they apply everything at once and cannot attribute the result.

At tier 4, specifically check three things: does the window now have letterbox bars, what does a canvas test site do, and does your time zone read as one you are not in?

**Deliverable.** The completed table, plus which tier you are keeping and why.

---

### Part 3: Verify Separation (30 minutes)

Set up two containers or two profiles, and treat them as two identities.

1. Sign in to a test account in identity A only.
2. In identity B, load the same site. Are you signed in? You must not be.
3. Run a fingerprint test in both and compare the results.
4. Set a cookie in A through any site that will set one, then check whether B can see it.

Then the harder question: **could an observer link A and B anyway?** Consider your address, which both share unless you did something about it, and your timing, which Lesson 1.3 covered.

**Deliverable.** Evidence that storage is separated, plus an honest note on what still links the two.

---

### Part 4: Tor Browser, Used Correctly (25 minutes)

Install from the project's own site. Connect.

1. Confirm you are actually using Tor, using the project's own check page.
2. Run a fingerprint test. Compare with your Firefox results. Note that being non-unique here is the goal, and that it is a different goal from your Firefox result.
3. Move the security level from Standard to Safer to Safest, loading one non-trivial site at each level. Record what breaks at each step.
4. Request a new identity and confirm your apparent address changed.
5. **Deliberately break a rule and observe it.** Resize the window, then re-run the fingerprint test. Record what changed. Then put it back.

Step 5 is the one that teaches. Everything else you could have read.

**Deliverable.** Results for each security level, and what changed when you resized the window.

---

### Part 5: The Guide Audit (20 minutes)

Find a browser hardening guide published more than two years ago. There are many.

Pick five preferences or settings it recommends, and for each one determine: does it still exist, does it still do what the guide claims, and does the browser now do it by default?

| Setting | Still exists? | Still does what was claimed? | Now default? | Verdict |
|---|---|---|---|---|

**Deliverable.** The table. At least one entry should be stale, and if none are, say which guide you used, because that is worth knowing.

---

### Part 6: The Month Test (10 minutes)

Write down, in one paragraph:

- Which configuration you are actually keeping.
- What it costs you daily.
- Which site broke that you decided to live with.
- What you will do the first time it blocks something urgent.

That last question is the one that decides whether this survives. Lesson 1.4 said a control you abandon protected nothing. Decide the exception process now, while you are calm, rather than at the moment you are frustrated and about to turn everything off.

**Deliverable.** The paragraph, and your exception process in one sentence.

---

### Submission

1. **Figure 1 mapping**, from Part 1.
2. **Tier table**, from Part 2, with your chosen tier.
3. **Separation evidence**, from Part 3, including what still links the identities.
4. **Tor results**, from Part 4, including the window resize observation.
5. **Guide audit**, from Part 5.
6. **The month test**, from Part 6.

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Requirement mapping | 15 | Tasks correctly matched to Figure 1 questions, honestly |
| Tiered configuration | 25 | Applied and measured one tier at a time, not all at once |
| Separation verification | 20 | Storage separation proven, and residual linkage identified |
| Tor Browser | 20 | Security levels tested, and the rule-breaking observation recorded |
| Guide audit | 10 | Stale settings correctly identified with reasons |
| The month test | 10 | Realistic, with a stated exception process |
| **Total** | **100** | |

---

## Additional Resources

### Vendor Documentation, Which Outranks Third-Party Comparisons

- Mozilla, Total Cookie Protection and website breakage: https://support.mozilla.org/en-US/kb/total-cookie-protection-and-website-breakage-faq
- Mozilla, protection against fingerprinting: https://support.mozilla.org/en-US/kb/firefox-protection-against-fingerprinting
- Brave, privacy updates, which is where changes like the Strict removal get announced: https://brave.com/privacy-updates/
- Tor Browser manual: https://tb-manual.torproject.org

Read the vendors' own pages. Third-party comparisons are where the Tor and Brave strategies get reported the wrong way round, and where removed settings live on for years.

### Configuration

- arkenfox user.js, with its annotations and override mechanism: https://github.com/arkenfox/user.js

Read the annotations. The file is a reference, not a drop-in.

### Discussion Questions

1. Which Figure 1 question does your current daily browser actually answer, and is that the one you needed?
2. You audited a stale guide in Part 5. What is the oldest piece of security advice you personally still repeat without checking?
3. Your research browser blocks something you urgently need during an engagement. What is your process, and did you decide it in advance?

---

## References

1. [Mozilla Support, Total Cookie Protection and website breakage FAQ](https://support.mozilla.org/en-US/kb/total-cookie-protection-and-website-breakage-faq)
2. [Mozilla Support, Resist Fingerprinting](https://support.mozilla.org/en-US/kb/resist-fingerprinting)
3. [Mozilla Support, Firefox protection against fingerprinting](https://support.mozilla.org/en-US/kb/firefox-protection-against-fingerprinting)
4. [Mozilla Bugzilla 967895, prompt with site permission before allowing content to extract canvas data](https://bugzilla.mozilla.org/show_bug.cgi?id=967895)
5. [Brave, Sunsetting Strict fingerprinting mode, January 2024](https://brave.com/privacy-updates/28-sunsetting-strict-fingerprinting-mode/)
6. [Brave, Fingerprint randomization, the farbling approach](https://brave.com/privacy-updates/3-fingerprint-randomization/)
7. [The Tor Project, New Release: Tor Browser 11.5, HTTPS-Only Mode by default](https://blog.torproject.org/new-release-tor-browser-115/)
8. [Tor Browser Manual, security levels and features](https://tb-manual.torproject.org)
9. [Electronic Frontier Foundation, Privacy Badger Is Changing to Protect You Better, October 2020](https://www.eff.org/deeplinks/2020/10/privacy-badger-changing-protect-you-better)
10. [arkenfox user.js, community Firefox preference reference](https://github.com/arkenfox/user.js)
11. [Tor Browser support, fingerprinting protections](https://support.torproject.org/tor-browser/features/fingerprinting-protections/)
