---
title: "Module 2 Lesson 2: Fingerprinting Up Close: Entropy, Canvas, and What Actually Defends"
course: Digital Privacy for Ethical Hackers
module: 2
lesson: 2.2
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - browser-privacy
  - fingerprinting
  - canvas
  - entropy
  - opsec
  - attribution
---

# Fingerprinting Up Close: Entropy, Canvas, and What Actually Defends

## About This Lesson

Lesson 2.1 established that stateless tracking exists, that it cannot be deleted, and that it is growing. This lesson opens it up.

**A note on scope.** This lesson covers the mechanism and the four defense strategies. It does not tell you which browser to install or which settings to change, because that is Lesson 2.3 and it deserves its own treatment. What you get here is the understanding that makes those choices sensible instead of copied.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Explain a fingerprint as a budget of bits, and say which attributes carry the most.
- Describe how a canvas fingerprint is produced and why it is stable.
- Identify which classic fingerprinting attributes have since been neutralised.
- Detect fingerprinting on a live page using your own instrumentation.
- Tell the four defense strategies apart, and say which of two different problems each one solves.

### Prerequisites

- Lesson 2.1, and your own measurements from its exercise.
- Working knowledge of JavaScript and the browser developer tools.

---

## 1. A Fingerprint Is a Budget of Bits

A fingerprint is not one measurement. It is many small measurements combined, and the useful way to think about it is information: how much does each attribute narrow the field?

The unit is a bit. One bit halves the population. Ten bits divides it by about a thousand. Twenty bits divides it by about a million.

![[module_2_lesson_2_entropy_budget.svg]]

*Figure 1. What each attribute was worth in the classic study, and what has happened to it since.*

Peter Eckersley measured this across 470,161 browsers and reported the per-attribute values in 2010. The plugin list carried 15.4 bits. The font list carried 13.9. The user agent string carried 10.0. Screen size grouped with colour depth carried 4.83, and time zone about 3.04. The whole fingerprint carried at least 18.1 bits, which is one browser in roughly 287,000. (Reference 1)

Note that the whole is much less than the sum of its parts. That is because the attributes are correlated: a browser with a particular plugin list very often has a particular user agent too. Adding a highly correlated attribute buys almost nothing.

**This has a direct consequence for defense.** You do not need to hide everything. You need to remove enough correlated bits that the remainder does not identify you. That is a much smaller job, and it is why targeted protections work better than they sound like they should.

### The Budget Moved

Two of the top three entries in Figure 1 no longer work as they did.

**The plugin list is gone as an identifier.** It was the single most identifying attribute in the study, at 15.4 bits. Browsers now return a fixed, specification-mandated list. If inline PDF viewing is supported, the browser reports the same five standard entries regardless of what is installed. (Reference 3) Every browser reports the same thing, so the attribute carries no information about which browser you are.

**The user agent string was reduced.** Chrome froze the desktop string in version 107 in October 2022 and the mobile string in version 110 in February 2023, completing the change in May 2023. The version number reports only the major version, the platform is pinned, and the device model is replaced. (Reference 2)

But read that second one carefully, because it is not a straight win. The device detail did not disappear. It moved to client hints, an interface a site can ask for explicitly. The entropy was relocated behind a request rather than removed, and a site that wants it still gets it.

**Meanwhile the gap filled in.** Canvas and graphics rendering do not appear in Figure 1 at all, because they were not part of that study's attribute set. They are now among the strongest signals available, and Lesson 2.1 gave the measurement: canvas fingerprinting went from about 1.6 percent of the top million sites in 2016 to 12.7 percent of the top twenty thousand in 2025.

**The rule to carry forward.** Any attribute list you read has a date on it, whether or not the date is printed. Check which entries still work before you plan a defense around them.

---

## 2. Canvas Fingerprinting, Step by Step

Canvas is the technique worth understanding in full, because the others follow the same shape.

![[module_2_lesson_2_canvas_pipeline.svg]]

*Figure 2. Four steps, a few milliseconds, and nothing visible on the page.*

Here is the whole thing, reduced to what matters:

```javascript
// 1. An off-screen canvas. It is never added to the page.
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');

// 2. Draw content chosen to expose rendering differences.
ctx.textBaseline = 'alphabetic';
ctx.fillStyle = '#f60';
ctx.fillRect(125, 1, 62, 20);
ctx.fillStyle = '#069';
ctx.font = "11pt 'no-such-font-12345'";   // forces a fallback
ctx.fillText('Cwm fjordbank glyphs vext quiz', 2, 15);
ctx.font = '18pt Arial';
ctx.fillText('\u{1F680}', 4, 45);          // an emoji

// 3. Read the pixels back out.
const data = canvas.toDataURL();

// 4. Reduce to a short, stable value.
const id = hash(data);
```

Every choice in step 2 is deliberate. The named font that does not exist forces the system to pick a fallback, and different systems pick differently. The emoji comes from a system font set that varies by platform and version. The overlapping coloured shapes expose blending and colour management. A plain string in a common font would produce far fewer differences.

### Why the Result Is Stable

Six things drive the pixel differences, and you do not control any of them from inside the browser:

Graphics hardware and model. Graphics driver version. Font rasterisation, hinting and anti-aliasing. Colour management and gamma handling. The operating system text engine and the system fonts it draws from. Browser compositing and its own canvas implementation.

Every one of those is a property of your machine, not of your session. That is exactly why clearing storage does nothing, why private browsing does nothing, and why a new profile does nothing. You brought the same computer.

**And it tells you where a defense has to act.** Look at step 3 in Figure 2. Reading the pixels back is the only point in the sequence where anything can be intercepted. Every meaningful canvas defense operates there, or else it changes the machine.

---

## 3. The Rest of the Rendering Surface

### Graphics

The graphics interface exposes two things. The first is a set of strings naming your graphics vendor and renderer, which frequently identifies the hardware model directly. The second is the same read-back trick as canvas, applied to a rendered three-dimensional scene, plus a long list of capability limits that vary by hardware and driver.

This is generally more identifying than canvas, because the vendor and renderer strings are close to a hardware serial number in information terms.

### Audio

An oscillator signal is generated, passed through a processing graph, and the output is hashed. Small differences in the audio stack produce a stable value.

**Keep this one in proportion.** The Princeton crawl found audio fingerprinting in only three scripts across 67 sites, against canvas fingerprinting on thousands. It is real, it is used, and it is nowhere near the threat that canvas is. A guide that presents them as equals is padding its list.

### Fonts

Font enumeration does not read your font directory. It measures text.

The technique renders a test string in a generic family, measures the width, then renders it again asking for a specific font with the generic as fallback. If the measured width changes, the requested font resolved and is therefore installed. Repeat across a list of a few hundred candidates.

Figure 1 makes this the attribute to watch. At 13.9 bits it was already second, and now that the plugin list contributes nothing, **fonts are the largest single contributor left**. Your installed fonts reveal your operating system, and often your profession: design software, office suites and programming fonts all leave distinctive sets.

---

## 4. Attributes That Expired, and One That Did Not

Guides accumulate stale entries. Three examples, and the corrections matter differently.

**The battery interface.** It reported charge level, charging state and time estimates, which combined into a short-lived but highly distinctive value. Firefox removed it in version 52. Safari never shipped it. **Chrome still supports it**, restricted to secure contexts since Chrome 103. (Reference 10) So "the browsers removed it" is wrong. Two removed it, one kept it, and that difference is itself a browser-selection input.

**Plugin enumeration.** Covered in Section 1. It was the top attribute and it is now worth nothing.

**Motion sensors, and this one holds up.** The claim that accelerometer imperfections identify a device is real and it is sourced. Dey and colleagues published AccelPrint at NDSS in 2014, testing 80 standalone accelerometer chips, 25 Android phones and two tablets, and reported precision and recall above 96 percent. Manufacturing tolerances make each sensor respond slightly differently to the same motion. (Reference 11)

Take the caveat with the finding. That is a laboratory study across a small device pool, not a measurement of what a random website achieves against the general public, and browsers now put motion sensors behind a permission prompt and a secure context. The mechanism is genuine. The 96 percent is a controlled-conditions number, in the same way that the 84 percent uniqueness figure in Lesson 2.1 was a self-selected-sample number.

**The habit worth building.** When you meet a striking percentage, ask what was measured, how many, and under what conditions, before you repeat it.

---

## 5. Watching It Happen

You do not have to take any of this on trust. Instrument the interfaces and load a real page.

Paste this into the developer console **before** navigating to the page you want to observe:

```javascript
(() => {
  const log = (what) => { console.warn(`[fp] ${what}`); console.trace(); };

  const el  = HTMLCanvasElement.prototype;
  const c2d = CanvasRenderingContext2D.prototype;

  // Canvas read-back: step 3 in Figure 2.
  for (const [obj, name] of [[el, 'toDataURL'], [el, 'toBlob'], [c2d, 'getImageData']]) {
    const original = obj[name];
    obj[name] = function (...args) {
      log(`canvas ${name}`);
      return original.apply(this, args);
    };
  }

  // Graphics contexts, including webgl2 and webgpu.
  const getContext = el.getContext;
  el.getContext = function (type, ...rest) {
    if (/webgl|webgpu/i.test(type)) log(`graphics context: ${type}`);
    return getContext.call(this, type, ...rest);
  };

  // Font enumeration shows up as a burst of width measurements.
  const measureText = c2d.measureText;
  let measures = 0;
  c2d.measureText = function (...args) {
    if (++measures === 50) log('measureText called 50 times: likely font enumeration');
    return measureText.apply(this, args);
  };

  // Audio graphs. A Proxy keeps the class usable, so the page still works.
  for (const name of ['AudioContext', 'OfflineAudioContext', 'webkitAudioContext']) {
    const Original = window[name];
    if (!Original) continue;
    window[name] = new Proxy(Original, {
      construct(target, args, newTarget) {
        log(`new ${name}`);
        return Reflect.construct(target, args, newTarget);
      }
    });
  }

  console.log('[fp] monitors installed');
})();
```

Three notes on why it is written this way.

The graphics check matches `webgl2` and `webgpu`, not only `webgl`. A monitor that tests for equality with the string `webgl` misses most current usage.

The audio wrapper uses a proxy rather than replacing the constructor with a plain function. Replacing it with a function breaks construction semantics and can break the page you are trying to observe, which then does not fingerprint you and you conclude wrongly that it was clean.

The `measureText` counter exists because Figure 1 says fonts are now the largest contributor. Most published monitors watch canvas and audio and ignore font enumeration entirely, which means they miss the biggest thing on the page.

**Reading the output.** The stack trace on each warning names the script responsible. That is what turns "this page fingerprints" into "this specific third-party script fingerprints", which is the finding worth writing down.

---

## 6. Four Strategies, and the Two Questions

Here is where most guidance goes wrong, including older versions of this course. It treats fingerprint defense as one problem with a ranked list of solutions. It is two problems, and the strategies answer them differently.

**Question one: are you unique within a single session?**
**Question two: can two of your sessions be linked to each other?**

![[module_2_lesson_2_defense_strategies.svg]]

*Figure 3. The four strategies, scored against both questions.*

### Blocking

Refuse the call, or prompt before allowing it. The attribute contributes nothing.

The catch is that refusal is itself observable. A browser that returns nothing for canvas is unusual, so you have traded a specific value for the distinctive fact of having no value. The cost is breakage: maps, charts, games and video tools all need these interfaces.

### Uniformity

Force every user to report the same values, so that no member of the group can be told apart from any other member.

**This is Tor Browser's design goal, and getting this right matters.** Tor Browser is frequently described as randomising its fingerprint. It does not. It pursues uniformity: all users report the same screen dimensions, the same bundled font set, the same time zone, the same language and the same headers, and it blocks the canvas and graphics interfaces rather than adding noise to them. (Reference 4, Reference 5)

The cost follows directly from the mechanism. **You must not customise anything.** Resize the window, install an add-on, or change a setting, and you leave the group you were hiding in. The protection comes from being identical, so any deviation is the whole problem.

### Randomisation

Add per-session noise, so that today's reading does not match tomorrow's.

**This is Brave's approach, which it calls farbling.** The implementation is more careful than "add noise" suggests: the values are generated deterministically from a seed that is per-session and per-site. A site gets a consistent value throughout one session, different sites get different values, and the next session produces different values again. It is applied to canvas and audio, and later to language and font reporting. (Reference 6, Reference 7)

Now look at the two questions. Randomisation leaves you **unique within a session**, because a random value is still a unique value. It makes you **unlinkable across sessions**, which is precisely what it is built for. Those are opposite answers to the two questions, and a comparison that collapses them into one score cannot express that.

The cost is low breakage, but the noise can be detected, which marks you as someone running randomisation.

### Separation

Change the thing being measured instead of hiding it. One profile, virtual machine or device per identity, with no crossover of accounts, files or habits.

Each identity remains unique on its own, and that is fine, because they do not share a machine to be linked by. This is the four-layer model from Lesson 1.2, applied to the browser.

The cost is discipline, and the failure mode is silent. One task done in the wrong window links two identities permanently, and nothing tells you it happened.

### How To Choose

Match the strategy to the question your threat model actually asks.

If you need to be **unattributable**, so that a target cannot tell your sessions apart from anyone else's, uniformity is the only strategy that delivers it, and you must accept its constraints in full.

If you need to be **unlinkable**, so that your sessions cannot be tied to each other, randomisation or separation both work, and both cost far less.

If you need to be **compliant**, because a program requires attributable traffic, then as Lesson 1.1 warned, none of this applies and the scope agreement wins.

Most working security professionals need unlinkability, not unattributability, and reach for the heaviest tool anyway.

---

## 7. What Firefox Now Recommends

One configuration note belongs here rather than in Lesson 2.3, because it is a correction rather than a setting.

Older guidance, including the earlier version of this course, says to set `privacy.resistFingerprinting` to true and stop. Mozilla's current position is different.

There are two mechanisms. **Resist Fingerprinting** is the Tor-derived uniformity approach. It is global: on or off across every window and tab, with no way to relax it for one site. It rounds window dimensions, forces a single time zone, and limits fonts, and it breaks pages. **Fingerprinting Protection**, in the Enhanced Tracking Protection settings, is the lighter targeted mechanism, and it is what Mozilla recommends for most users. (Reference 8, Reference 9)

Both can be enabled together, in which case the stricter one applies.

**The point is not which to pick.** It is that a preference name copied from a four-year-old guide is not advice. Lesson 2.3 covers the configuration properly.

---

## 8. Summary

- A fingerprint is a budget of bits. The whole is far less than the sum of its parts, because attributes correlate, which is why targeted defenses work better than they sound like they should.
- The budget shifted. The plugin list went from the top attribute at 15.4 bits to nothing. The user agent was frozen, with its detail relocated to client hints rather than removed. Fonts are now the largest single contributor.
- Canvas produces a stable value because it measures your hardware, drivers and text rendering. Reading the pixels back is the only interceptable step.
- Audio fingerprinting is real and rare. Do not present it as canvas's equal.
- Tor Browser pursues **uniformity** and blocks canvas. Brave uses **randomisation**, called farbling, seeded per session and per site. These are opposite strategies and they are commonly reported the wrong way round.
- Ask two questions, not one. Unique within a session, and linkable across sessions, are different problems with different answers.
- Most professionals need unlinkability and reach for the heaviest tool anyway.

**Action item.** Run the monitor from Section 5 on three sites you use daily, before Lesson 2.3. Knowing which of your own sites fingerprint you makes the configuration decisions in that lesson concrete instead of abstract.

The next lesson turns all of this into specific browser choices and configurations, with the breakage costs stated honestly.

---

## Knowledge Check

**Question 1.** In the 2010 study, which attribute carried the most entropy?

- A) Canvas rendering
- B) Time zone
- C) The plugin list, at 15.4 bits
- D) Screen resolution

**Answer: C.** Canvas was not part of that study's attribute set at all, which is worth noting on its own.

**Question 2.** That attribute contributes almost nothing today. Why?

- A) Browsers removed script access to it
- B) Browsers now return a fixed specification-mandated list, so every browser reports the same entries
- C) Plugins were prohibited by regulation
- D) It was merged into the user agent string

**Answer: B.** An attribute where everyone reports the same value carries no information about which person you are.

**Question 3.** Chrome froze the user agent string in 2023. What happened to the device detail it used to carry?

- A) It moved to client hints, which a site has to request
- B) It was permanently deleted
- C) It moved into the canvas fingerprint
- D) Nothing changed

**Answer: A.** The entropy was relocated behind a request, not removed. A site that wants it still gets it.

**Question 4.** A canvas fingerprint stays the same across sessions because:

- A) It is stored in a cookie
- B) The page keeps it in local storage
- C) The server assigns it to you
- D) It is derived from your hardware, drivers and text rendering, none of which change between sessions

**Answer: D.** This is why clearing storage, private browsing and new profiles all fail against it. You brought the same computer.

**Question 5.** Tor Browser's fingerprinting strategy is best described as:

- A) Randomising every reading so no two sessions match
- B) Uniformity, making every user report the same values so that no member of the group can be told apart
- C) Routing traffic through three relays and nothing more
- D) Disabling scripting entirely

**Answer: B.** It also blocks the canvas and graphics interfaces rather than adding noise to them. This is commonly reported the wrong way round.

**Question 6.** Brave's approach, which it calls farbling, is best described as:

- A) Randomisation, with values seeded per session and per site, so the same site sees one value all session and a different one next session
- B) Uniformity
- C) Refusing all rendering interfaces
- D) Disabling the graphics interface

**Answer: A.** Deterministic per seed rather than noisy per call, which is what keeps sites working.

**Question 7.** Randomisation and uniformity give opposite answers to which question?

- A) Whether an extension is required
- B) Whether they work on mobile
- C) Whether they cost money
- D) Whether you are unique within a single session. Randomisation leaves you unique per session but unlinkable across them. Uniformity leaves you not unique at all.

**Answer: D.** A single ranked list of browsers cannot express this, which is why the comparison in this lesson has two separate columns.

**Question 8.** Mozilla's current guidance for most Firefox users is:

- A) Set `privacy.resistFingerprinting` to true
- B) Install a canvas blocking extension rather than using built-in settings
- C) Use Fingerprinting Protection in the Enhanced Tracking Protection settings, because Resist Fingerprinting is global, cannot be relaxed per site, and breaks pages
- D) Do nothing, because Firefox has no fingerprinting protection

**Answer: C.** Option A is the advice in most older guides, including the earlier version of this course.

**Question 9.** You enable a strong resistance mode and your test result becomes less unique. What have you not yet verified?

- A) Whether the sites you actually need still work, and whether the resistance itself is detectable
- B) Whether your address changed
- C) Whether your cookies cleared
- D) Whether your password manager still works

**Answer: A.** A lower uniqueness score with unusable browsing is not a win, and a defense that announces itself has traded one distinctive signal for another.

**Question 10.** Research on accelerometer fingerprinting reported precision and recall above 96 percent. What is the correct caveat?

- A) The research was retracted
- B) It was a laboratory study across a small device pool, and browsers now gate motion sensors behind permission and a secure context, so field results differ
- C) It applies only to desktop machines
- D) Accelerometers cannot be read from a browser at all

**Answer: B.** The mechanism is genuine. The percentage describes controlled conditions, in the same way the 84 percent uniqueness figure described a self-selected sample.

---

## Exercise: Observe, Then Test the Strategies

**Time: 2 hours, plus 30 minutes to write up.**

### Objective

Catch fingerprinting on live pages, then test the four strategies against your own baseline and score each on both questions from Section 6.

### Safety Notes

- Instrument pages you already use. Do not attach instrumentation to a client system or to anything covered by an engagement scope.
- Your results include hardware details. Treat the write-up as sensitive.

---

### Part 1: Catch It in the Act (30 minutes)

Load the monitor from Section 5 into the console, then navigate to a page and watch.

Do this for three sites: one major news site, one retail site, and one site you personally use daily.

For each, record:

- How many canvas read-backs occurred.
- Whether a graphics context was created.
- Whether the font enumeration warning fired.
- Whether an audio graph was constructed.
- **The script responsible**, taken from the stack trace. This is the part that matters.

**Expected pattern.** Retail and financial sites fingerprint heavily, usually for fraud detection rather than advertising. Note where the fingerprinting comes from a fraud vendor rather than a tracker, because that changes the ethics and the likely defensive response.

**Deliverable.** A table of three sites against the four signals, with the responsible script named.

---

### Part 2: Read a Real Payload (25 minutes)

Pick one site from Part 1 that fingerprinted you. In the network panel, find the request that carries the result back.

1. Locate the request. It usually follows the fingerprinting script closely and posts a body.
2. Decode it if it is encoded.
3. List which attributes from this lesson you can identify in it.
4. Note anything present that this lesson did **not** cover.

Point 4 is the valuable one. Fingerprinting moves, and finding one attribute this article missed is a better result than confirming the ones it listed.

**Optional, 15 minutes.** Write a minimal fingerprinter of your own: collect five attributes, concatenate them, hash them. Run it in two different browsers on the same machine and compare. Then run it in the same browser on two machines. Explain which comparison changed the hash and why, in terms of Figure 2.

**Deliverable.** An annotated payload, plus one attribute you found that is not in this lesson.

---

### Part 3: Test the Four Strategies (40 minutes)

Establish your baseline first, using your normal browser and the test sites from Lesson 2.1.

Then test each strategy. Use whatever is available to you; the point is the comparison, not the specific product.

| Strategy | How to test it |
|---|---|
| Blocking | A canvas-blocking extension, or a browser setting that refuses the interfaces |
| Uniformity | Tor Browser at default settings, changing nothing |
| Randomisation | Brave with its fingerprinting protection enabled |
| Separation | Two separate profiles, or a virtual machine |

For each, record your uniqueness result, your canvas hash, and what broke.

**Then run the test twice for each, in two separate sessions**, and compare the canvas hash between them. This is the step that distinguishes the strategies and the one most people skip.

**Deliverable.** A table with a session-one hash and a session-two hash for each strategy.

---

### Part 4: Score Both Questions (20 minutes)

Using your Part 3 results, fill this in from your own measurements rather than from Figure 3:

| Strategy | Unique within one session? | Same hash across two sessions? | What broke |
|---|---|---|---|
| Baseline | | | |
| Blocking | | | |
| Uniformity | | | |
| Randomisation | | | |
| Separation | | | |

Then answer:

1. Which strategies gave **different** hashes across your two sessions, and what does that tell you about linkability?
2. Which left you unique within a single session, and does that matter for your threat model?
3. Did any of them make you distinctive in a **new** way, by refusing a call or by producing detectably noisy output?

**Deliverable.** The completed table plus three answers.

---

### Part 5: The Breakage Budget (15 minutes)

Pick the six sites you genuinely cannot do your work without. Load each one under your preferred strategy from Part 4.

| Site | Works? | What breaks | Acceptable? | Workaround |
|---|---|---|---|---|

Then state the honest conclusion: **which strategy will you still be using in a month?** A configuration abandoned after three weeks protected nothing and cost you three weeks, which is the point Lesson 1.4 made about controls you do not use.

**Deliverable.** The table and a one-sentence commitment.

---

### Submission

1. **Detection results**, Parts 1 and 2, with the responsible scripts named.
2. **Strategy comparison**, Parts 3 and 4, including both session hashes.
3. **Breakage budget**, Part 5, with your commitment.
4. **Reflection**, half a page: which strategy did you expect to choose before this lesson, and did the two-question test change it?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Detection | 25 | Monitor used correctly, responsible scripts identified from the traces |
| Payload analysis | 15 | Attributes correctly identified, including one not covered in the lesson |
| Strategy testing | 30 | All four tested, and both sessions run so linkability can be assessed |
| Two-question scoring | 20 | Correctly distinguishes within-session uniqueness from cross-session linkability |
| Breakage budget | 10 | Honest, and the commitment is realistic |
| **Total** | **100** | |

---

## Additional Resources

### Primary Sources on Browser Design

- Tor Project, fingerprinting protections: https://support.torproject.org/tor-browser/features/fingerprinting-protections/
- Brave, fingerprint randomisation: https://brave.com/privacy-updates/3-fingerprint-randomization/
- Mozilla, protection against fingerprinting: https://support.mozilla.org/en-US/kb/firefox-protection-against-fingerprinting

Read the browser vendors' own descriptions rather than third-party comparisons. The Tor and Brave strategies are frequently reported the wrong way round, and both projects document their own approach clearly.

### Testing

- Cover Your Tracks: https://coveryourtracks.eff.org
- AmIUnique: https://www.amiunique.org
- BrowserLeaks: https://browserleaks.com

Lesson 2.1's caution still applies: these measure a self-selected population, so use them to compare your own configurations against each other.

### Discussion Questions

1. Does your threat model need unattributability or unlinkability? Be honest, and check whether your current setup matches the answer.
2. Which attribute from Figure 1 do you have the most control over, and have you actually changed it?
3. You found a fingerprinting script on a site you use for banking. Is that a privacy violation, a fraud control, or both? What would you advise a client to do about it?

---

## References

1. [Eckersley, How Unique Is Your Web Browser?, Privacy Enhancing Technologies Symposium 2010](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1)
2. [The Chromium Projects, User-Agent Reduction](https://www.chromium.org/updates/ua-reduction/)
3. [MDN Web Docs, Navigator.plugins, and the specification-mandated fixed list](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/plugins)
4. [The Tor Project, Browser Fingerprinting: An Introduction and the Challenges Ahead](https://blog.torproject.org/browser-fingerprinting-introduction-and-challenges-ahead/)
5. [Tor Browser support, fingerprinting protections](https://support.torproject.org/tor-browser/features/fingerprinting-protections/)
6. [Brave, Fingerprint randomization](https://brave.com/privacy-updates/3-fingerprint-randomization/)
7. [Brave, Fingerprinting defenses 2.0](https://brave.com/privacy-updates/4-fingerprinting-defenses-2.0/)
8. [Mozilla Support, Resist Fingerprinting](https://support.mozilla.org/en-US/kb/resist-fingerprinting)
9. [Mozilla Support, Firefox protection against fingerprinting](https://support.mozilla.org/en-US/kb/firefox-protection-against-fingerprinting)
10. [MDN Web Docs, Battery Status API, and its browser support](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API)
11. [Dey, Roy, Xu, Roy Choudhury and Nelakuditi, AccelPrint: Imperfections of Accelerometers Make Smartphones Trackable, NDSS 2014](https://www.ndss-symposium.org/ndss2014/ndss-2014-programme/accelprint-imperfections-accelerometers-make-smartphones-trackable/)
