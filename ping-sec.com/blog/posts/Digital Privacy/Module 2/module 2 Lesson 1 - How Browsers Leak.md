---
title: "Module 2 Lesson 1: How Browsers Leak: What a Single Page Load Gives Away"
course: Digital Privacy for Ethical Hackers
module: 2
lesson: 2.1
format: article
reading_time: 28 minutes
tags:
  - digital-privacy-course
  - privacy
  - browser-privacy
  - fingerprinting
  - tracking
  - cookies
  - opsec
  - attribution
---

# How Browsers Leak: What a Single Page Load Gives Away

## About This Lesson

Module 1 was about understanding. This module is about a specific tool, and it starts with the one you use most.

Before you continue, get your threat model out. Lesson 1.4 said not to begin Module 2 without it, and the reason is now immediate: this lesson lists a great many ways that a browser discloses information, and most of them will not matter to you. The model is what tells you which ones do.

This lesson does not fix anything. It measures the problem. Lessons 2.2 and 2.3 do the fixing.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Tell stateful tracking from stateless tracking, and say why the difference decides your defenses.
- Describe what each of three separate observers learns from one page load.
- State honestly how unique a browser is, and explain why the popular figure is too high.
- Identify which classic browser privacy claims are now out of date, and what replaced them.
- Measure your own browser and describe your attribution exposure.

### Prerequisites

- All of Module 1, and your completed threat model.
- Basic familiarity with HTTP, cookies, and JavaScript.

---

## 1. Two Families, and Only One of Them Can Be Deleted

Almost every browser privacy article organizes itself as a list of tracking techniques. That list is long, it is boring, and it hides the one distinction that actually drives your decisions.

There are two families.

![[module_2_lesson_1_stateful_stateless.svg]]

*Figure 1. Stateful tracking stores something on your device. Stateless tracking measures something about it.*

**Stateful tracking** puts a value on your machine and reads it back later. Cookies, local storage, indexed databases, cache entries. The identifier exists as data, in a place, and you can delete it. The browser can also partition it, so that a value stored by one site cannot be read in the context of another.

**Stateless tracking** stores nothing. It measures your machine and derives an identifier from the measurements. How your graphics stack renders a shape. Which fonts you have. Your screen geometry, your time zone, your core count. There is no file to remove, because there is no file.

**This is the distinction that matters, and here is why.** Every piece of advice you have ever read about clearing cookies, using private browsing, or blocking third-party storage applies to the first family only. None of it touches the second family. Private browsing gives you a clean storage jar. It gives you the same hardware, the same fonts, and the same screen.

### The Trend, and It Is Measurable

The two families have moved in opposite directions, and the numbers are documented.

On the stateful side, browsers restricted things. Safari and Firefox block third-party cookies by default, and Brave does too.

On the stateless side, use increased. In 2016 Steven Englehardt and Arvind Narayanan crawled the top one million sites and found canvas fingerprinting on about 1.6 percent of them. In October 2025 a team from the University of California San Diego and Mozilla published a follow-up measurement and found canvas fingerprinting on 12.7 percent of the top twenty thousand sites. (Reference 4, Reference 5)

Different site populations, so the two figures are not a clean like-for-like comparison. The direction is not in doubt.

**Read that as cause and effect.** Constraining one family did not reduce tracking. It moved tracking to the family that you cannot clear.

---

## 2. What Actually Happened to Third-Party Cookies

If you learned browser privacy from material written between 2020 and 2024, you learned that third-party cookies were going away. That did not happen, and a lesson that repeats it gives bad advice.

The sequence:

- Google announced a plan to remove third-party cookies from Chrome, and built the Privacy Sandbox as the replacement.
- In July 2024 Google reversed the removal. Third-party cookies would stay, with a user choice prompt instead.
- In April 2025 Google dropped the standalone choice prompt as well, and kept the existing controls in Chrome settings. (Reference 7)
- In October 2025 Google announced the wind-down of most Privacy Sandbox technologies, with deprecation starting in Chrome 144 and removal targeted at Chrome 150. (Reference 6)

So the position in 2026 is:

| Browser | Third-party cookies by default |
|---|---|
| Safari | Blocked |
| Firefox | Blocked, and storage is partitioned per site |
| Brave | Blocked |
| Chrome | Present, subject to the user's own settings |

**What this means for you.** Two things, and they point in opposite directions.

First, your browser choice now determines your stateful exposure more than your settings do. That is a Lesson 2.3 topic and it is a real decision.

Second, do not celebrate. Cookie restriction was always the easier half of the problem, and the harder half grew while the industry argued about the easier one. A reader who blocks third-party cookies and stops there has addressed the family that was already in retreat.

### The Ecosystem Is More Concentrated Than It Looks

The Princeton measurement of one million sites found more than 81,000 distinct third parties. That number sounds hopeless until you read the next one: only 123 of those were present on more than one percent of sites. Only Google, Facebook and Twitter appeared on more than ten percent. All of the top five third parties, and twelve of the top twenty, were Google-owned domains. (Reference 4)

That is a 2016 measurement and the specific names have shifted since. The shape has not. There is an enormous tail of trackers that you will almost never meet, and a very small head that you meet constantly.

The practical consequence is encouraging. You do not have to block 81,000 things. A list that covers the head covers most of your exposure.

---

## 3. Three Observers, One Page Load

A page load is not watched by one party. It is watched by three, and they see different things.

![[module_2_lesson_1_three_observers.svg]]

*Figure 2. The same request, from three vantage points. A control that helps against one does nothing about the others.*

**Observer 1, the network path.** Your provider and anyone else carrying your traffic. They see your address, the domain name lookup unless it is encrypted, the hostname in the connection handshake unless that is encrypted too, and the size and timing of everything.

**Observer 2, the server and every third party it loads.** Request headers, cookies for that domain, the origin you arrived from, and any tracking parameter carried in the address itself.

**Observer 3, script running on the page.** This is the one that measures your device, and it is the one that produces a stateless identifier.

Look at Figure 2 next to Myth 3 from Lesson 1.1. That myth said encryption protects content and leaves metadata exposed. Figure 2 is the same statement with the field names filled in. Transport encryption protects the content that observer 2 exchanges with you. It protects nothing in observer 1, and nothing at all in observer 3, because observer 3 is running inside your own browser on the far side of the encryption.

**Why three matters operationally.** A VPN moves observer 1 and leaves observers 2 and 3 exactly as they were. Clearing cookies affects part of observer 2 and nothing else. Only a browser that resists measurement does anything about observer 3. When you evaluate any privacy tool in the rest of this module, ask which observer it addresses. Most tools address one.

---

## 4. How Unique Is Your Browser, Honestly

Here is the number you have seen: 84 percent of browsers have a unique fingerprint. Sometimes it is quoted as 87, or 90, or 94.

It comes from real research. Peter Eckersley collected roughly half a million browser fingerprints through the Electronic Frontier Foundation Panopticlick project and reported in 2010 that about 84 percent were unique, rising above 90 percent among browsers running Flash or Java. (Reference 1, Reference 2)

That study is sound. The way it gets quoted is not.

![[module_2_lesson_1_uniqueness.svg]]

*Figure 3. The widely quoted figure and the general-population figure are not the same figure.*

### The Sample Problem

Panopticlick measured people who chose to visit a fingerprinting test site. Those people are not a random sample of web users. They are unusually technical, they run unusual configurations, they install unusual extensions, and several of the things they do to protect their privacy make them more distinctive rather than less.

In 2018 Alejandro Gómez-Boix, Pierre Laperdrix and Benoit Baudry did the obvious follow-up. They collected 2,067,942 fingerprints from ordinary visitors to one of the fifteen most visited websites in France. The results were very different. (Reference 3)

- **33.6 percent** of all fingerprints were unique, against the 80-plus percent reported by earlier work.
- **35.7 percent** of desktop fingerprints were unique.
- **18.5 percent** of mobile fingerprints were unique. Earlier work on a self-selected sample had put mobile at 81 percent.
- With scripting disabled, desktop uniqueness collapsed from 35.7 percent to **0.7 percent**.

### What To Take From This

**Fingerprinting works.** A one in three chance of being unique from a single page load is a serious tracking capability, and a non-unique fingerprint still narrows you to a small group that other signals can then split.

**It works less well than the popular figure claims.** If you have been telling people that they are 84 percent certain to be unique, you have been overstating it for a general audience by a factor of more than two.

**Phones are a much smaller crowd problem than desktops.** Mobile hardware and software are far more homogeneous. A phone is roughly half as likely to be unique as a desktop. This is the opposite of most people's intuition and it has a direct operational consequence.

**Almost the entire fingerprint arrives through script.** That last bar in Figure 3 is the most actionable finding in this lesson. Disabling scripting is not a realistic default for general browsing, but it is entirely realistic for a narrow operational task where you only need to read a page.

**And the general lesson.** When a privacy statistic seems dramatic, look at who was measured. This is the same discipline that Lesson 1.2 applied to data broker lists and that Lesson 1.3 applied to the stylometry accuracy claim. A number from a self-selected sample describes that sample.

---

## 5. Three Claims That Are Now Out of Date

Browser privacy material ages badly. These three appear in almost every guide, including older versions of this course, and all three need correcting.

### "WebRTC leaks your real IP address, even behind a VPN"

**What was true.** WebRTC enumerates your network interfaces to establish peer connections, and early implementations exposed your private local address to any script that asked. This was measured in the wild: the Princeton crawl found WebRTC local address discovery on 715 sites. (Reference 4)

**What changed.** Browsers now obfuscate it. Instead of a real local address, the browser generates a random hostname ending in `.local` and registers it on the local network. The random value changes per session, so it is useless as an identifier. (Reference 11)

**What remains true, and this is the part to keep.** The obfuscation covers the local address only. Your public address can still be exposed through the connectivity server that WebRTC uses to discover its route. And the obfuscation stops applying to a site once you grant it camera or microphone permission, because at that point the site has a legitimate need for the real interface.

**How to state it accurately.** WebRTC no longer hands out your local network address to any page that asks. It can still expose your public address, and permission grants re-open the local exposure. Test it rather than assuming, which the exercise does.

### "The Referer header leaks the full page you came from"

**What was true.** The default policy used to send the complete address, including path and query string. A page at `internal.example.com/reports/client-acme-findings` would hand that entire string to any external resource it loaded.

**What changed.** Chrome moved the default to `strict-origin-when-cross-origin` in version 85, in August 2020. Firefox made the same change in version 87, in March 2021. (Reference 9, Reference 10) Under that default, a cross-site request sends only the origin. The path and the query string stay behind.

**What remains true.** The origin still goes. A site can still choose a more permissive policy for its own requests. Same-origin requests still carry the full address. And nothing about this touches tracking parameters, which are in the destination address rather than in a header.

### "Sites can read your history through visited link styling"

**What was true.** For roughly twenty years, a page could style links and then measure the result to work out which of those sites you had visited. Direct colour reading was blocked long ago, but a long series of side-channel variants kept the attack alive.

**What changed.** Chrome 136, released in April 2025, partitioned visited link state by three keys: the link target, the top-level site, and the frame origin. A link now shows as visited only on the same site and in the same frame where you actually clicked it. That makes the whole family of side-channel attacks against visited styling obsolete. (Reference 8)

**Why this one is worth noting.** It is a twenty-year-old privacy defect that was actually fixed. Keep a note of which claims in your own material have expiry dates, and re-check them.

**Also expired: Flash cookies.** Flash reached end of life in December 2020. Local shared objects are a historical example now, not a live threat. The concept that replaced them, storing the same identifier in several places so that clearing one restores it from another, is very much alive, and that is the part to teach.

---

## 6. What Is Not Out of Date

Balance requires the other list.

**Cookie syncing and identifier reconstruction.** Storing one identifier across cookies, local storage, indexed databases and cache entries, then rebuilding whichever one you delete. Clearing cookies alone has never defeated this, and still does not.

**Link decoration.** Tracking identifiers carried in the address itself, such as click identifiers appended by social and advertising platforms. No header policy touches these, because they are part of the destination address. When you share a link that carries one, you may be sharing an identifier that was issued to you.

**Cache-based identification.** Whether a resource is already cached is observable through timing. Browsers have partitioned the cache to make this harder, but it is not a solved category.

**Timing and behavioural patterns.** When you browse is identifying, as Lesson 1.3 established for publication timing. Interaction patterns such as typing rhythm and pointer movement are also used, mostly for fraud detection.

**A caution on the behavioural numbers.** You will find claims that keystroke dynamics identify a person with 95 percent accuracy or better. Treat those the way Lesson 1.3 treated the stylometry claim. High accuracy figures generally come from controlled conditions with a fixed phrase and a small candidate pool. Free-text identification against a large population is a much harder problem and performs much worse. The mechanism is real, the risk is real for a targeted user, and the headline percentage is not a number to repeat without knowing the study behind it.

---

## 7. Why This Is Operational Security, Not Preference

For a security professional the browser is not just a privacy concern. It is an attribution surface.

Consider a worked example. It is constructed rather than a documented case, and it is worth walking through because the arithmetic is the point.

A researcher does reconnaissance carefully. Traffic goes through a VPN. Cookies are cleared between sessions. The user agent string is changed.

The VPN addressed observer 1. Clearing cookies addressed part of observer 2. Nothing addressed observer 3, so every session still presents the same measured device: an unusual screen geometry, a distinctive graphics renderer string, and a font list carrying entries that arrived with security tooling. Changing the user agent does not help, and can hurt, because a browser claiming to be one thing while measuring like another is itself distinctive.

The result is a stable identifier across sessions that the VPN never touched. Separate engagements can be linked to one operator.

**Two honest qualifications**, because Figure 3 has just told us not to overstate this:

The single most identifying attribute in that example is unusual hardware. A researcher on a mainstream laptop with a default font set is a much smaller problem than this example suggests. And a defender only links those sessions if they are actually collecting and correlating fingerprints, which most organizations do not do, though bot detection and fraud vendors do it as a service.

So the risk is real, it is conditional, and its size depends on your hardware, your tooling, and who your target is. That is a threat modeling question, which is why Module 1 came first.

**The reliable conclusion.** Compartmentalization by browser profile, or better by machine, is the control that addresses observer 3, because it changes the thing being measured instead of trying to hide it. That is the four-layer model from Lesson 1.2, applied to the browser.

---

## 8. Summary

- Tracking comes in two families. Stateful is stored and can be deleted. Stateless is measured and cannot.
- Restricting the stateful family moved the work to the stateless family. Canvas fingerprinting went from about 1.6 percent of the top million sites in 2016 to 12.7 percent of the top twenty thousand in 2025.
- Third-party cookies were not removed from Chrome. The plan was reversed, and Privacy Sandbox is being wound down. Browser choice now decides your stateful exposure.
- One page load has three observers. Most privacy tools address exactly one of them. Ask which, every time.
- Browsers are less unique than the popular figure says: about 34 percent in a general population, 35.7 percent on desktop and 18.5 percent on mobile, against 84 percent in the self-selected sample everyone quotes.
- Scripting carries almost the whole fingerprint. Desktop uniqueness falls from 35.7 percent to 0.7 percent without it.
- Three classic claims have expired: the WebRTC local address leak, full-path referrer leakage, and visited-link history sniffing. Know what replaced each one.

**Action item.** Run the exercise below before Lesson 2.2. That lesson goes deeper into fingerprinting technique, and it is far more useful once you have your own measurements to compare against.

The next lesson takes observer 3 apart in detail: exactly which attributes carry the most entropy, how the measurements are taken, and what actually reduces them.

---

## Knowledge Check

**Question 1.** Stateful and stateless tracking differ in that:

- A) Stateful tracking stores something on your device, and stateless tracking measures something about your device
- B) Stateful tracking is lawful and stateless tracking is not
- C) Stateful tracking works only on desktop machines
- D) Stateless tracking requires you to sign in

**Answer: A.** Stored against measured. That difference decides which of your defenses can possibly work.

**Question 2.** Why is stateless tracking the harder problem?

- A) It collects a larger number of data fields
- B) It is a newer technique
- C) It runs only over encrypted connections
- D) There is nothing stored to delete, so clearing cookies and using private browsing do not change it

**Answer: D.** Private browsing gives you empty storage and the same hardware.

**Question 3.** A 2010 study of about 500,000 browsers found 84 percent of fingerprints unique. A 2018 study of 2.07 million fingerprints from an ordinary website found 33.6 percent. What explains most of that gap?

- A) Fingerprinting technique became less effective
- B) The 2010 sample was self-selected, because those visitors chose to go to a fingerprinting test site
- C) The 2018 study measured fewer attributes
- D) Browsers stopped supporting scripting

**Answer: B.** Both studies are sound. They measured different populations, and only one of them was a general population.

**Question 4.** In the 2018 study, uniqueness on mobile devices was:

- A) Higher than desktop, at about 81 percent
- B) The same as desktop
- C) Much lower than desktop, at about 18.5 percent
- D) Not measured

**Answer: C.** Mobile hardware and software are far more homogeneous, so a phone hides in a much larger crowd than a desktop does.

**Question 5.** In that same study, desktop uniqueness fell from 35.7 percent to 0.7 percent when one thing changed. What was it?

- A) Scripting was disabled
- B) A VPN was used
- C) Cookies were cleared
- D) The user switched to private browsing

**Answer: A.** Almost the entire fingerprint is collected by script. Options C and D change nothing about a fingerprint.

**Question 6.** What happened to the plan to remove third-party cookies from Chrome?

- A) It completed on schedule in 2024
- B) It was postponed to 2027
- C) It completed, and Privacy Sandbox replaced cookies
- D) It was abandoned. The removal was reversed, the replacement choice prompt was then dropped, and most Privacy Sandbox technologies are being wound down

**Answer: D.** Safari, Firefox and Brave still block third-party cookies by default. Chrome kept them.

**Question 7.** Canvas fingerprinting measured across the web:

- A) Has disappeared since 2016
- B) Has stayed flat at about 1.6 percent of sites
- C) Has grown, from about 1.6 percent of the top million sites in 2016 to 12.7 percent of the top twenty thousand in 2025
- D) Has never been measured

**Answer: C.** Different site populations, so not a clean comparison, but the direction is not in doubt.

**Question 8.** Older guidance says that WebRTC leaks your real local address even behind a VPN. What is accurate now?

- A) The claim was never true
- B) Browsers replace the local address with a random `.local` hostname, so that leak is largely closed, but the public address can still be exposed through the connectivity server, and granting camera or microphone permission re-opens the local exposure
- C) WebRTC has been removed from browsers
- D) The leak affects mobile devices only

**Answer: B.** The claim was true, it was fixed in part, and the remaining exposure is narrower and conditional.

**Question 9.** Under the current default referrer policy, following a link to another site normally gives the destination:

- A) The full address, including path and query string
- B) Nothing at all
- C) Your account name on the previous site
- D) Only the origin of the page you came from, without the path or the query string

**Answer: D.** Chrome changed the default in version 85 and Firefox in version 87. Tracking parameters are unaffected, because they live in the destination address rather than in this header.

**Question 10.** Chrome 136 partitioned visited link styling by link target, top-level site and frame origin. What did that fix?

- A) History sniffing, where a site styled links to work out which other sites you had visited
- B) Canvas fingerprinting
- C) Third-party cookie tracking
- D) Domain name lookup exposure

**Answer: A.** A twenty-year-old defect, and the partitioning makes the whole family of side-channel variants obsolete.

---

## Exercise: Measure Your Own Browser

**Time: 90 minutes, plus 30 minutes to write it up.**

### Objective

Produce real measurements of your own browsers, and turn them into an attribution assessment that feeds Lesson 2.2.

### Safety Notes

- Your results contain your address, your location and your hardware details. This output is sensitive. Do not post it publicly and redact addresses in anything you share.
- Run the tests on a personal device. Corporate networks often block the test sites and will distort the results.

---

### Part 1: Baseline (25 minutes)

Use your normal everyday browser first, in its normal configuration. Do not clean it up before testing, because a cleaned-up browser is not the one you actually use.

**Test 1.** Cover Your Tracks, at https://coveryourtracks.eff.org. Record whether it reports your browser as having a unique or a near-unique fingerprint, the "one in N browsers" figure, and the three or four attributes carrying the most bits.

**Test 2.** AmIUnique, at https://www.amiunique.org. Record your overall result, and the attributes with the lowest similarity percentage.

**Test 3.** BrowserLeaks, at https://browserleaks.com. Work through the canvas, WebGL, fonts and WebRTC pages. Record your canvas hash, your graphics vendor and renderer strings, your font count, and what the WebRTC page reports.

**Interpretation note, and this matters.** These sites measure a self-selected population, which is exactly the bias that Section 4 described. Their "one in N" figures compare you against other people who chose to visit a fingerprinting test site. Treat the result as a relative measure that lets you compare your own configurations, not as your probability of being unique on the open web.

**Deliverable.** A baseline record with the three or four attributes that carry the most weight for you.

---

### Part 2: The Comparisons That Teach Something (30 minutes)

Skip the exhaustive browser matrix. Four comparisons carry almost all the value.

**Comparison A: normal window against private window.** Same browser, same tests. Compare the canvas hash and the WebGL strings specifically.

Expected result: they do not change. Write down that they did not change. This is the single most useful thing in the exercise, because private browsing is what most people believe protects them.

**Comparison B: your normal browser against a fingerprint-resistant browser.** Install Tor Browser, or Brave with its fingerprint protection enabled, and run the same tests. Record what changed and what broke.

**Comparison C: desktop against phone.** Run Cover Your Tracks on your phone and compare with your desktop result.

Section 4 predicts that your phone hides in a bigger crowd. Check whether your own two measurements agree with the published finding. If they do not, work out why, because that is a more interesting write-up than a confirmation.

**Comparison D: scripting on against scripting off.** Disable JavaScript, either in browser settings or with an extension, then reload the fingerprint test.

Most of the measurement should disappear. Note what still gets through, because those are the header-level and network-level attributes that no script blocker can help with.

**Deliverable.** Four comparisons, each with a one-line statement of what changed.

---

### Part 3: Watch the Three Observers (20 minutes)

**Observer 2, in the developer tools.** Open the developer tools on a major news site and look at the storage and network panels. Count the distinct third-party domains that receive a request. Note how many of them belong to the same handful of organizations, which is the concentration finding from Section 2.

**Observer 2, with a content blocker.** Install uBlock Origin temporarily if you do not run it. Load the same site and record how many requests it blocks, and which domains.

**Link decoration.** Follow a link from a social platform or from a marketing message and read the resulting address carefully. Identify each tracking parameter. Then strip them and confirm the page still loads correctly, which it almost always will.

**Observer 1.** Run a domain-name-lookup leak test and record which resolvers answer for you, and whether they belong to your provider. If you use a VPN, run this test with it connected and disconnected and compare.

**Deliverable.** A count of third parties on one page, a list of the tracking parameters you found, and your resolver result.

---

### Part 4: Your Attribution Assessment (20 minutes)

This is the part that connects to your threat model rather than to the tools.

1. **Your three most identifying attributes.** From Part 1, name them and say why each one is unusual.
2. **Which observer is your weakest?** Given how you actually work, is your exposure mostly network, mostly server-side, or mostly device measurement?
3. **The linking question.** If a target organization collected fingerprints, could they link your reconnaissance sessions to each other? Give your reasoning, and be specific about which attribute would do the linking.
4. **The honest likelihood.** Would your realistic adversaries actually do that? Section 7 says this is conditional. Answer it from your Lesson 1.4 threat model, not from the worst case.
5. **Your one change.** Name the single change that would most reduce your attribution exposure, and say what it would cost you in convenience.

**Deliverable.** A one-page assessment. Question 4 is the one that stops this becoming a shopping list.

---

### Submission

1. **Measurements**, from Parts 1 to 3, with addresses redacted.
2. **Comparison table**, the four comparisons from Part 2 with the outcome of each.
3. **Attribution assessment**, one page, from Part 4.
4. **Reflection**, half a page: which result surprised you, and which belief of yours did the private-window comparison change?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Measurement | 25 | Tests completed on the browser you actually use, results recorded accurately |
| Comparisons | 25 | All four run, with a clear statement of what each one showed |
| Observer analysis | 20 | Correct attribution of each finding to the right observer |
| Attribution assessment | 20 | Specific, tied to your own threat model, and honest about likelihood |
| Reflection | 10 | Demonstrates a changed belief rather than a restated one |
| **Total** | **100** | |

---

## Additional Resources

### Test Sites

- Cover Your Tracks, Electronic Frontier Foundation: https://coveryourtracks.eff.org
- AmIUnique: https://www.amiunique.org
- BrowserLeaks: https://browserleaks.com

Remember what Section 4 said about all three: they measure a self-selected population. Use them to compare your own configurations against each other, which is what they are genuinely good for.

### Reading

- Princeton Web Census, the one-million-site tracking measurement: https://webtransparency.cs.princeton.edu/webcensus/
- Privacy Sandbox, the current official position on third-party cookies: https://privacysandbox.google.com/cookies

### Discussion Questions

1. Which observer does your current setup defend best, and which does it ignore completely?
2. Your phone is roughly half as likely to be unique as your desktop. Does that change which device you would choose for a given task?
3. Which claim in your own security knowledge has an expiry date that you have not checked recently?

---

## References

1. [Eckersley, How Unique Is Your Web Browser?, Privacy Enhancing Technologies Symposium 2010](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1)
2. [Electronic Frontier Foundation, Is Every Browser Unique? Results from the Panopticlick Experiment](https://www.eff.org/deeplinks/2010/05/every-browser-unique-results-fom-panopticlick)
3. [Gómez-Boix, Laperdrix and Baudry, Hiding in the Crowd: an Analysis of the Effectiveness of Browser Fingerprinting at Large Scale, The Web Conference 2018](https://inria.hal.science/hal-01718234v2)
4. [Englehardt and Narayanan, Online Tracking: A 1-million-site Measurement and Analysis, ACM CCS 2016, Princeton Web Census](https://webtransparency.cs.princeton.edu/webcensus/)
5. [Canvassing the Fingerprinters: Characterizing Canvas Fingerprinting Use Across the Web, ACM Internet Measurement Conference 2025](https://www.sysnet.ucsd.edu/~voelker/pubs/canvas-imc25.pdf)
6. [Privacy Sandbox, third-party cookies, official current position](https://privacysandbox.google.com/cookies)
7. [OneTrust, Google Drops Plans for Third-Party Cookie Choice Prompt in Chrome, April 2025](https://www.onetrust.com/blog/google-drops-plans-for-third-party-cookie-choice-prompt-in-chrome/)
8. [Chrome for Developers, Making :visited more private, partitioned visited links in Chrome 136](https://developer.chrome.com/blog/visited-links)
9. [Chrome for Developers, A new default Referrer-Policy for Chrome, strict-origin-when-cross-origin](https://developer.chrome.com/blog/referrer-policy-new-chrome-default/)
10. [Mozilla Security Blog, Firefox 87 trims HTTP Referrers by default to protect user privacy, March 2021](https://blog.mozilla.org/security/2021/03/22/firefox-87-trims-http-referrers-by-default-to-protect-user-privacy/)
11. [WebRTC project announcement, private IP addresses exposed by WebRTC changing to mDNS hostnames](https://groups.google.com/g/discuss-webrtc/c/6stQXi72BEU)
