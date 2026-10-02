---
title: "Module 2 Lesson 5: Compartmentalization: Five Layers, Five Paths, and a Failure You Cannot See"
course: Digital Privacy for Ethical Hackers
module: 2
lesson: 2.5
format: article
reading_time: 28 minutes
tags:
  - digital-privacy-course
  - privacy
  - browser-privacy
  - compartmentalization
  - containers
  - profiles
  - opsec
  - module-2-capstone
---

# Compartmentalization: Five Layers, Five Paths, and a Failure You Cannot See

## About This Lesson

This closes Module 2, and it is the lesson that decides whether the previous four were worth anything.

Lesson 1.2 gave you four layers as a model. This lesson makes them real, and it does three things that most treatments of the subject skip: it states precisely what each isolation mechanism separates and what it does not, it corrects the linkage story that everybody teaches, and it takes seriously the fact that this control fails without telling you.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Say exactly what containers, profiles, separate browsers and separate machines each isolate.
- Explain what containers add on top of protection your browser already provides.
- Name the linkage paths that remain open after cookies are dealt with.
- Design a setup whose failure mode is prevention rather than detection.
- Respond correctly to a boundary break, which is not what most people assume.

### Prerequisites

- All of Module 2, and your threat model from Lesson 1.4.

---

## 1. Five Layers, and They Are Not Interchangeable

Compartmentalization is usually presented as one idea with several implementations, ranked from convenient to thorough. That framing hides the thing you actually need to know, which is that these mechanisms separate **different things**.

![[module_2_lesson_5_isolation_matrix.svg]]

*Figure 1. Read it as a matrix, not a ranking.*

### What Containers Actually Do

This is where most guidance, including this course's earlier version, is imprecise.

Firefox Multi-Account Containers separates **cookies and site storage**. Bookmarks, browsing history and extensions are **shared across every container**. (Reference 1, Reference 2)

That has a direct consequence for how you test your setup, and Section 5 comes back to it: a test that checks whether your browsing history is separated between two containers will report a failure, and that failure is expected behaviour rather than a misconfiguration.

### What Containers Add, Given What You Already Have

Since Lesson 2.3 you have had Enhanced Tracking Protection set to Strict, which brings Total Cookie Protection. That already gives every site its own cookie jar, so a tracker present on two sites cannot join what it stored on each.

So why containers at all?

Mozilla's own explanation is precise about this. Total Cookie Protection separates cookies **between** sites, and it does not isolate cookies from different tabs of the **same** site. Containers do exactly that: they let you hold several accounts on one service at the same time, in one browser, with each unaware of the others. (Reference 3, Reference 4)

**So the two do different jobs.** Total Cookie Protection is the anti-tracking control and it is automatic. Containers are the multiple-identities control and they are deliberate. If your reason for wanting containers was to stop cross-site tracking, your browser is already doing that, and you may not need them. If your reason is to run a personal and a work account on the same service without either seeing the other, containers are the tool and nothing else does it as cheaply.

### One Correction About Profiles

The earlier version of this lesson lists, as a disadvantage of profiles, that you cannot usually run more than one at a time and must close the browser to switch.

That is not correct, and the error matters because it makes profiles look far less practical than they are. Firefox will run several profiles simultaneously, in separate windows, using the `-no-remote` switch alongside a named profile. Chromium browsers run profiles concurrently as a matter of course. (Reference 5)

Profiles are therefore a much stronger everyday option than the older framing suggests. Look at Figure 1: profiles separate everything containers separate, **plus** history, bookmarks, extensions and settings. And they cost you one command-line switch.

### The Column That Reads No All the Way Down

Look at the network address column in Figure 1. Every row says no.

Nothing you do inside your machine changes the address you connect from. Containers do not. Profiles do not. Separate browsers do not. A virtual machine does not, unless you deliberately route it differently.

If two of your identities must not share an address, compartmentalization is not the control. Routing is, and that belongs to a later module. Any setup that treats browser separation as sufficient has left this open.

---

## 2. How Linkage Actually Happens

Here is the story every compartmentalization guide tells. You check a social platform in the morning. That platform's tracking pixel sits on your target's website. In the afternoon you research the target from the same browser, the pixel reads your cookie, and the platform now knows the person with that account is researching that company.

It is a good story. It is also, in a privacy browser, largely describing a path that is already closed.

![[module_2_lesson_5_linkage_paths.svg]]

*Figure 2. One of these is the path everybody teaches. Four others stay open.*

Lessons 2.1 and 2.3 established that Safari, Firefox and Brave block third-party cookies by default, and that Total Cookie Protection isolates storage per site. In those browsers the pixel cannot read the cookie the story depends on. In Chrome, which kept third-party cookies, it still can.

**This matters more than a correction to one example.** If you learned compartmentalization as "keep cookies apart", and your browser was already keeping cookies apart, then you have built a defence against the one path that needed the least defending and left the other four alone.

### The Four That Remain

**The device fingerprint.** Same machine, same graphics stack, same fonts. Lesson 2.2 measured this. Containers do nothing about it and profiles do very little, because the identifying attributes come from hardware you did not change.

**The network address.** One connection, both identities, as Section 1 established.

**Timing.** Your personal activity stops and your research activity begins, minutes apart, every day. Lesson 1.3 covered timing as an identifier and it applies here without modification.

**You sign in to something.** One account, once, in the wrong window. No technical control survives this, and in practice it is how compartmentalization usually fails.

**The honest ranking.** Order those by how likely each is to be what actually links you, and the sign-in path is first by a wide margin. It is also the only one that no configuration prevents. That should tell you where to spend your effort, and it is not on cookies.

---

## 3. Choosing Your Layers

Work from Figure 1 and from what your threat model actually asks.

**If you need multiple accounts on one service**, you need containers or profiles. Nothing else does it.

**If you need separate history, bookmarks and extension sets**, you need profiles or separate browsers. Containers share all three.

**If you need a different fingerprint**, you need a different machine. Separate browsers get you partway, because a different engine reports differently, but hardware-derived attributes are common to all of them.

**If you need a different address**, none of this helps. Route it.

A workable default for a security professional, mapped to Lesson 1.2's layers:

| Layer | Mechanism | Why this one |
|---|---|---|
| Personal | Its own browser | Nothing about it should touch the others, and it is the one you use least carefully |
| Professional | Profile, with containers inside it for per-client separation | Clients get separated cheaply, and the profile keeps work history out of everything else |
| Research | Its own profile, or its own browser | Separate extensions matter here, and Lesson 2.4 said this layer should carry the fewest |
| Anonymous | Tor Browser, unmodified | Uniformity, and it is the only layer where the address is handled too |

**Note what is deliberately absent.** No virtual machines in the default. They are the right answer when you genuinely need a different fingerprint or a different route, and they are overhead you will resent otherwise. Lesson 1.4's rule stands: a control you abandon protected nothing.

---

## 4. The Failure Is Silent

This is the part that deserves more weight than it usually gets.

Every other control in this module reports its own failure. A blocked script leaves a gap on the page. A broken setting produces an error. A misconfigured browser breaks a site you were trying to use, loudly.

Compartmentalization does none of that.

![[module_2_lesson_5_silent_failure.svg]]

*Figure 3. You cannot detect this after the fact, so the control has to work before it.*

When you open the wrong window and sign in, nothing errors. The page loads correctly. You were already signed in, so it felt like convenience. The link is made in the first request, so there is no interval in which to catch it. And closing the tab does not unmake it, because the record belongs to somebody else now.

**Every design decision follows from this.** If you cannot detect the failure, then detection controls are worthless and only prevention controls count.

### The Three That Work

**Make it visible.** A different theme, colour and window position for every identity. This sounds trivial next to preference files and isolation mechanisms. It is the highest-value control in this lesson, because it acts at the only moment that matters, which is the second before you click.

**Remove the choice.** Pin each site to its container or profile so the assignment happens without you deciding. A decision you make forty times a day will eventually be made wrong. A decision you made once will not.

In Firefox this is the "always open in this container" assignment, applied per site.

**A note on that.** The earlier version of this lesson describes a container rules interface with wildcard patterns, and separately presents a `firefox -P "Personal" -no-remote` command as a container feature. Those are two different mechanisms: the command launches a **profile**, not a container. Assignment in Multi-Account Containers is done per site. Check what your own version offers before you design a system around a syntax you read somewhere.

**Write it down.** One line per slip: what crossed, which two identities, what you were doing. The log is not for guilt. Its value is that it shows you **which boundary keeps failing**, and a boundary that fails repeatedly is a design fault rather than a discipline fault. Redesign that one instead of resolving to try harder.

### After a Break

The instinct is to clear the cookie and move on, as though that undoes it. It does not. The other party has its record.

Treat a break as **disclosed, not fixed**:

1. Assume the link was made.
2. Decide what follows, which is a Lesson 1.4 threat modeling question rather than a browser question.
3. If the identity was disposable, retire it and build a new one. Reusing a burned persona is worse than not having had one.
4. If it was not disposable, work out what the other party can now infer, and whether anyone has to be told. A client contract may make that answer not yours alone.
5. Fix the boundary that failed.

---

## 5. Testing It, Correctly

The point of testing is to find out what your setup actually does, not to confirm what you hoped.

**Cookie and session isolation.** Sign in to one account in identity A. Open the same site in identity B. You must not be signed in. This is the test that matters, and it works for containers, profiles and separate browsers alike.

**Multiple accounts on one service.** Sign in to two different accounts on the same service in two containers, at the same time. Both should stay signed in independently. This is what containers add over Total Cookie Protection, so it is worth confirming.

**History separation, with the right expectation.** Between profiles, history is separate. **Between containers it is not, and a test that expects it to be is testing for something that was never designed.** The earlier version of this lesson includes exactly that test and marks a shared history as a failure. It is not a failure. It is the documented behaviour. (Reference 1)

**Fingerprint comparison.** Run a fingerprint test in each identity. Expect containers and profiles on one machine to look similar or identical. That is correct, and Figure 1 predicted it. If it surprises you, re-read Lesson 2.2.

**Address comparison.** Check your apparent address in each identity. Expect it to be the same everywhere except Tor Browser. Also correct, also predicted.

**The point of the last two.** You are not looking for a pass. You are confirming that the limits are where the model says they are, so that you do not later assume protection you never had.

---

## 6. Summary

- Compartmentalization is five layers, and they separate different things. Read Figure 1 as a matrix, not a ranking.
- Containers isolate cookies and storage. History, bookmarks and extensions are shared across all of them.
- Total Cookie Protection already separates cookies between sites. What containers add is separation **within** one site, so several accounts can run at once.
- Profiles do run simultaneously, and they separate more than containers do. The claim that they cannot is wrong and it makes a good option look bad.
- Nothing inside your machine changes the address you connect from.
- The classic pixel-and-cookie linkage story describes the path most likely to be closed already. Fingerprint, address, timing and sign-in remain open.
- The sign-in path is the likeliest failure and no configuration prevents it.
- This control fails silently, so only prevention counts: visible windows, pinned assignments, and a log that shows you which boundary keeps failing.
- After a break, treat it as disclosed rather than fixed.

**Action item.** Give each of your browsers or profiles a visibly different theme today. Five minutes, and it addresses the failure mode that all the configuration in this module does not.

---

## Module 2 Is Complete

Four lessons of mechanism and one of practice, and the argument runs in a line.

Lesson 2.1 split tracking into what is stored and what is measured, and showed that restricting the first moved the work to the second. Lesson 2.2 opened the second up, gave you the entropy budget, and separated two questions that get confused: being unique within a session, and being linkable across sessions. Lesson 2.3 chose tools against those two questions rather than against a vague privacy scale. Lesson 2.4 found that the extension platform had moved underneath the topic, and that your stack should shrink as your requirement grows. This lesson built the separations and was honest about which of them are real.

**One idea carried all five.** Ask what the specific requirement is, then apply the control that addresses it, and know precisely what that control does not do. Every correction in this module was a case of somebody skipping the second half of that sentence.

**In Module 3** the subject becomes encrypted communication. Lesson 1.1's third myth is waiting there: encryption protects content and not metadata, and messaging is where that distinction gets expensive.

---

## Knowledge Check

**Question 1.** Firefox Multi-Account Containers isolate which of the following across containers?

- A) Cookies and site storage only. History, bookmarks and extensions are shared across every container.
- B) Everything, including history and bookmarks
- C) The browser fingerprint
- D) The network address

**Answer: A.** This is documented behaviour, and it changes how you test.

**Question 2.** Total Cookie Protection is already on with Enhanced Tracking Protection Strict. What do containers add?

- A) Nothing, they are redundant
- B) Isolation of cookies within a single site, so several accounts can be held on the same service at once
- C) A different fingerprint per container
- D) A different network address per container

**Answer: B.** Total Cookie Protection separates cookies between sites. It does not separate them within one site, and that gap is what containers fill.

**Question 3.** The earlier version of this lesson said you usually cannot run two browser profiles at once. What is correct?

- A) It is correct, profiles run strictly one at a time
- B) Only Chromium browsers can do it
- C) It requires a virtual machine
- D) Firefox runs several profiles simultaneously, which makes profiles far more practical than that claim implies

**Answer: D.** Profiles separate everything containers do, plus history, bookmarks, extensions and settings.

**Question 4.** A test that checks whether browsing history is separated between two Firefox containers will:

- A) Pass, confirming containers work
- B) Fail, showing containers are broken
- C) Report a failure that is expected behaviour, because containers share history by design
- D) Not run at all

**Answer: C.** Testing for something that was never designed produces a false alarm and teaches the wrong lesson.

**Question 5.** Of the five linkage paths, which one does no technical control survive?

- A) The third-party cookie
- B) Signing in to an account in the wrong window
- C) The device fingerprint
- D) Timing

**Answer: B.** It is also the most likely of the five to be what actually happens.

**Question 6.** Why is the classic pixel-reads-your-cookie story a weak opening example now?

- A) Tracking pixels no longer exist
- B) The platforms stopped tracking
- C) Cookies were prohibited
- D) In browsers that block third-party cookies by default, that path is largely closed, while the fingerprint, address, timing and sign-in paths stay open

**Answer: D.** It still works in Chrome. The risk of teaching it as the main path is that people defend it and leave the other four.

**Question 7.** Which column of the isolation matrix reads no for every layer?

- A) The network address, because nothing inside your machine changes the address you connect from
- B) Cookies between sites
- C) History and bookmarks
- D) Extensions and settings

**Answer: A.** If two identities must not share an address, routing is the control, not compartmentalization.

**Question 8.** Compartmentalization fails differently from the other controls in this module because:

- A) It fails more often
- B) It fails only on mobile devices
- C) It fails silently. Nothing errors, the page loads normally, and the link is made in the first request
- D) It cannot fail

**Answer: C.** Which is why detection controls are worthless here and only prevention counts.

**Question 9.** What should follow a boundary break?

- A) Clear the cookie and carry on, which undoes it
- B) Treat it as disclosed. Assume the link was made, decide what follows from your threat model, and fix the boundary that failed
- C) Nothing, breaks are unavoidable
- D) Reinstall the browser

**Answer: B.** Option A is the instinct and it is wrong, because the record belongs to somebody else now.

**Question 10.** Given that you cannot detect a break, which control best prevents one?

- A) Reviewing your history each week
- B) Adding more extensions
- C) Using a single browser to keep things simple
- D) Making the windows visibly different and pinning sites to a container, so the routine decision is removed

**Answer: D.** It acts at the only moment that matters, which is the second before you click.

---

## Capstone Exercise: Build It, Then Try to Break It

**Time: 2.5 hours, plus 30 minutes to write up.**

### Objective

Build the separations your threat model requires, confirm what they actually do, and design against the failure mode that no configuration prevents.

---

### Part 1: Requirements Before Mechanisms (20 minutes)

Do not start by choosing containers or profiles. Start by writing what must not link.

For each pair of your identities, state the one thing that must never cross and what happens if it does.

| Identity pair | What must not cross | Consequence if it does | Which row of Figure 1 covers it |
|---|---|---|---|

Then check the last column honestly. **If any requirement lands on the fingerprint or the address, note that no browser mechanism delivers it** and record what you are actually going to do about that, including the option of accepting it.

**Deliverable.** The table, with at least one requirement that browser compartmentalization cannot meet, identified as such.

---

### Part 2: Build the Minimum That Meets Part 1 (40 minutes)

Implement only what your requirements need. Resist building the full four-layer arrangement if your requirements do not call for it.

For each identity record: the mechanism, what it separates, what it does not, and which accounts are permitted in it.

| Identity | Mechanism | Separates | Does not separate | Accounts permitted |
|---|---|---|---|---|

**The last column is the important one.** Write "none" where none are permitted, and mean it.

**Deliverable.** The table, plus a one-line justification for any layer you built that Part 1 did not require.

---

### Part 3: Test What It Does, Not What You Hoped (35 minutes)

Run each of these and record the result you got, then whether that result was the one Figure 1 predicts.

| Test | Result | Predicted by Figure 1? |
|---|---|---|
| Sign in to identity A, open the same site in identity B | | |
| Two accounts on one service, in two containers, at once | | |
| History visible across containers | | |
| History visible across profiles | | |
| Fingerprint compared across containers | | |
| Fingerprint compared across separate browsers | | |
| Apparent address compared across all identities | | |

**Three of these should come back showing no separation.** If you recorded those as failures rather than as expected limits, re-read Section 1 before continuing, because that misreading is the point of the exercise.

**Deliverable.** The completed table, with the expected-limit rows correctly identified.

---

### Part 4: Attack Your Own Setup (25 minutes)

Take the five paths from Figure 2 and work through them against what you built.

| Path | Open against my setup? | What would close it | Am I closing it? |
|---|---|---|---|
| Third-party cookie | | | |
| Device fingerprint | | | |
| Network address | | | |
| Timing | | | |
| Sign-in in the wrong window | | | |

Then answer directly: **which of these is realistically how you would be linked?** Not which is most sophisticated. Which is most likely, given how you actually work.

**Deliverable.** The table and your one-sentence answer.

---

### Part 5: Design Against the Silent Failure (20 minutes)

1. **Make them visibly different.** Give each identity a distinct theme and a fixed window position. Do it now, in the browser, not on paper.
2. **Pin your routine sites.** List the ten sites you open most and assign each to its container or profile so the choice is made for you.
3. **Start the log.** Create the file. One line per slip: date, what crossed, which two identities, what you were doing.
4. **Write the break procedure.** Four or five lines, following Section 4. Decide it now, while nothing has gone wrong.

**Deliverable.** A screenshot showing two visibly distinct identities, your pinned site list, and your written break procedure.

---

### Part 6: The Honest Review (10 minutes)

Answer in a short paragraph each:

1. Which layer did you build because your requirements needed it, and which because it felt thorough?
2. Which boundary have you already crossed at least once in the past, before this lesson?
3. What is your setup's weakest path from Part 4, and are you accepting it deliberately or by default?
4. Will this survive a busy week? If not, which part goes first, and can you simplify that part now rather than losing it later?

Question 4 decides whether any of this lasts.

---

### Submission

1. **Requirements**, Part 1, including at least one that compartmentalization cannot meet.
2. **Build**, Part 2, with the accounts column completed.
3. **Test results**, Part 3, with expected limits correctly labelled.
4. **Self-attack**, Part 4, with your likeliest linkage path named.
5. **Prevention design**, Part 5, with evidence.
6. **Review**, Part 6.

### Assessment

| Area | Points | What is measured |
|---|---|---|
| Requirements first | 20 | Written before mechanisms, and at least one requirement correctly identified as out of reach |
| Proportionate build | 15 | Only what the requirements called for, with extras justified |
| Correct testing | 25 | Expected limits recognised as limits rather than recorded as failures |
| Self-attack | 20 | All five paths assessed, likeliest one named honestly |
| Prevention design | 15 | Actually implemented, not described. Break procedure written in advance. |
| Honest review | 5 | Names something that will not survive, and simplifies it |
| **Total** | **100** | |

Note the weighting. Correct testing and honest self-attack carry more than building does, because the building is the easy part and almost everybody gets the other two wrong.

---

## Additional Resources

### Primary Documentation

- Mozilla, Multi-Account Containers: https://support.mozilla.org/en-US/kb/containers
- Mozilla, how Total Cookie Protection and container extensions work together: https://blog.mozilla.org/en/firefox/how-firefoxs-total-cookie-protection-and-container-extensions-work-together/

That second one is worth reading in full. It is the clearest statement anywhere of what containers add over what your browser already does, and most third-party guidance gets the distinction wrong.

### Discussion Questions

1. Which of your separations exists because you needed it, and which because it felt like good practice?
2. You cannot detect a boundary break. Does anything else in your security setup share that property, and have you designed for it there?
3. Your likeliest linkage path from Part 4 is probably the sign-in path. What would actually stop you, at eleven at night, at the end of a long engagement?

---

## References

1. [Mozilla, Multi-Account Containers project, cookies separated by container while bookmarks, history and add-ons are shared](https://github.com/mozilla/multi-account-containers/)
2. [Mozilla Support, Multi-Account Containers](https://support.mozilla.org/en-US/kb/containers)
3. [The Mozilla Blog, How Firefox Total Cookie Protection and container extensions work together](https://blog.mozilla.org/en/firefox/how-firefoxs-total-cookie-protection-and-container-extensions-work-together/)
4. [Mozilla Support, Total Cookie Protection and website breakage FAQ](https://support.mozilla.org/en-US/kb/total-cookie-protection-and-website-breakage-faq)
5. [Mozilla Support, running multiple Firefox profiles at the same time](https://support.mozilla.org/en-US/questions/1231994)
