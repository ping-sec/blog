---
title: "Module 4 Lesson 4: Account Recovery: The Second Authentication System Nobody Designed"
course: Digital Privacy for Ethical Hackers
module: 4
lesson: 4.4
format: article
reading_time: 30 minutes
tags:
  - digital-privacy-course
  - privacy
  - password-security
  - authentication
  - account-recovery
  - incident-response
  - opsec
---

# Account Recovery: The Second Authentication System Nobody Designed

## About This Lesson

Three lessons went into the front door. A vault, a hardware key, the weak methods removed. This lesson is about the other door, the one that came with the building.

**Account recovery is not a safety net attached to your authentication. It is a second, complete authentication system**, usually designed by different people, to a different standard, for a different user: somebody distressed who has genuinely lost everything. It opens the same account. So your account is exactly as strong as the weaker of the two, and almost nobody has read the weaker one.

This is also where attacker effort went once sign in got harder, which is not a prediction. Both of the attacks in Section 2 are documented, current, and aimed squarely at this door.

### Learning Objectives

After you read this lesson, you can do these tasks:

- Explain why the usual security against recoverability framing is wrong, and state the real trade.
- Draw your own recovery dependency graph and find the loop in it.
- Describe two current attacks aimed at the recovery path rather than at authentication.
- Rank recovery methods, and say why one commonly recommended option no longer exists.
- Respond to a compromised account in the right order, and know what a password change does not do.
- Build a monitoring routine you will actually follow.

### Prerequisites

- All three previous lessons in Module 4. This one assembles them.
- The threat modeling method from Lesson 1.4.

---

## 1. Two Doors

![[module_4_lesson_4_two_doors.svg]]

*Figure 1. Both open the same room. You built one of them.*

**The door you built** has a unique password from a vault you chose, a hardware key or a passkey, and the weaker methods removed. You designed it deliberately, over three lessons, against a threat model you wrote down. It is optimised for you on an ordinary day.

**The door that came with it** is frequently a link to an email address, a code by text message, a date of birth, or a person on a telephone. A product team designed it, balancing support costs against fraud, to a standard you were not consulted on. It is optimised for a distressed person who has lost everything.

Everything in Lessons 4.1 to 4.3 raised the first door. This lesson exists because raising one door does nothing while the other is still there, which is the same argument as Lesson 4.3 arriving from a different direction.

### The Framing That Makes This Look Unsolvable

You will read that recovery is a trade between security and recoverability. Lock it down and risk losing everything permanently, or keep it easy and let attackers walk in. Presented that way the problem has no answer and people stop thinking about it.

**That is not the actual trade. The trade is between fast recovery and safe recovery.**

A recovery path can be completely reliable and still be hostile to an attacker, if it has three properties:

- **Slow.** A delay of hours costs you an afternoon, once, perhaps twice in a decade. It costs an attacker the whole operation, because the delay is time in which you are told what is happening.
- **Noisy.** Every step announces itself on a channel the attacker does not control. Recovery *starting* is the single most important alert your accounts can send you.
- **Arranged in advance.** It uses something you set up while calm, not something you can persuade a stranger to accept while distressed. Which also means not something a stranger can persuade somebody else to accept while pretending to be you.

This is not theoretical. Apple's Stolen Device Protection, introduced in January 2024, imposes an hour of delay plus a second biometric check before the account password, the recovery key, or a recovery contact can be changed while away from familiar locations. (Reference 1) The reasoning is exactly the above: a thief who has your phone and watched you type your passcode must not be able to lock you out before you can react.

**Slow, noisy, arranged in advance.** That is the design goal for every recovery path you control.

---

## 2. Where the Attacker Effort Went

![[module_4_lesson_4_where_the_effort_moved.svg]]

*Figure 2. Neither of these touches your password, your key, or your vault.*

### The Downgrade

A phishing kit sold as a service, credited with roughly 62 percent of the phishing volume one large provider blocked by the middle of 2025, carries code written for this purpose. **It detects that the real site is about to ask for a passkey, and steers the target to a different method instead.** (Reference 2)

Nothing about the passkey is defeated. It is simply never used, because a plausible page said the fingerprint reader was unavailable and offered a code.

**This is the whole module's argument, observed in the wild.** Every fallback you leave enabled is a menu item the attacker gets to choose from, and the attacker will always choose the worst one you left on.

### The Help Desk

A criminal group documented in a joint national advisory, published in 2023 and updated in July 2025, uses this as a primary route in. **It telephones support, presents itself as an employee, and has the password reset and the second factor moved to a device it controls.** (Reference 3)

No malware, no phishing page, and no interaction with the target at all. The target finds out afterwards.

What the advisory recommends for defenders is instructive, because it describes what a well designed recovery path looks like from the inside: scripted identity checks, two independent verifications with at least one resistant to voice impersonation, and resets correlated against ticket evidence. (Reference 4) When you evaluate a service's recovery process, that is the standard to hold it to.

### The Conclusion Both Point At

Stated plainly at a standards conference in 2025: adding passkeys as an option does not make a system phishing resistant, if a text message recovery route or a password reset path is still available. (Reference 2)

**Which is why this lesson comes last, and why it is not optional.** Lessons 4.1 to 4.3 are incomplete without it in a specific and measurable way.

---

## 3. Draw the Graph

Standard advice on this topic gives instructions one at a time, and each of them is individually correct. Nobody draws them together.

![[module_4_lesson_4_recovery_graph.svg]]

*Figure 3. The most useful ten minutes in this lesson, and it needs paper.*

Here are three pieces of advice you will find in almost every guide, including the outline this lesson is built from:

1. Store your recovery codes in your password manager.
2. Secure your password manager account, whose recovery runs through your email address.
3. Secure your email account, and keep its recovery codes somewhere safe.

Follow all three and you get a circle. **The email is recovered by its codes, the codes live in the vault, and the vault is recovered through the email.** Three nodes, three arrows, no way out. Lose any one of them and you have lost all three, and nothing was attacked.

**The rule that catches this.** Every recovery graph needs at least one **root**: a node that depends on nothing else. If no node in your diagram qualifies, you do not have a recovery plan, you have a loop.

**And the root has to be physical.** Anything online is recoverable by somebody, which means it has an incoming arrow you did not draw and did not choose. A second hardware key, the codes on paper, and a copy of the vault, in a safe or a deposit box or a locked drawer somewhere that is not your house. That node has no outgoing arrow, and it cannot be phished, reset by a support agent, or lost along with a device.

**A local vault with no online recovery does not escape this.** It makes the vault itself the root, which is a legitimate design, and it means the paper copy in the safe is the only thing standing behind every account you own. Lesson 4.1 called the vault a single point of failure. This is what that looks like drawn out.

---

## 4. Recovery Methods, Ranked

### Backup Codes

One time codes issued by the service, usually eight to ten of them, offered once during setup.

These are the best general purpose recovery method available and they are treated far too casually. **They are credentials.** They grant access to the account without any other factor, which is the entire point of them, and that means they deserve the storage discipline you would give a password.

- Store them where you can reach them **without the account they unlock**. Read that twice against Section 3.
- Print a copy and put it with your backup key. That copy is often the root node in Figure 3.
- Regenerate the set if you use one, if you suspect exposure, and after any incident.
- Never in a plain text file, an email to yourself, or a photograph in a camera roll that synchronises.

### A Second Hardware Key

Covered fully in Lesson 4.2. Registered on the same accounts, in the same sitting, and tested on its own. This is the only recovery method that is itself above the phishing resistance line.

### A Recovery Email Address

Workable, with conditions, and the conditions are where people fail.

A dedicated address, used for nothing else, secured at least as strongly as the account it recovers, **and not recovered by that account in turn.** The last clause is the one that produces circles. If your work email recovers your personal email and your personal email recovers your work email, compromising either gives both.

Ask the question directly for each pair: if somebody has this address, what else do they now have?

### Recovery Phone Number

Lesson 4.3 covered why: SIM swapping requires no access to you, your accounts, or your device, only to a support agent at a carrier, and current NIST guidance treats text message codes as a restricted authenticator. (Reference 5)

Remove it from anything that matters. Where a service will not let you remove it entirely, set a carrier PIN and a port freeze and treat the account as weaker than it looks.

**One piece of dated advice to drop.** Older guidance suggests using a landline instead of a mobile. Voice calls are divertible too, call forwarding is trivially social engineered, and this substitutes one telephone network problem for another.

### Trusted Contacts

**Facebook's Trusted Contacts feature no longer exists.** It was discontinued around 2022, and guidance still listing it is out of date. (Reference 6)

Apple's Account Recovery Contact does exist and works as described: a person you nominate can supply a code that helps you back in. It is a reasonable option, and it makes another human part of your threat model. Choose somebody whose judgement holds up under a convincing telephone call, tell them what the role is, and remember that relationships change over a decade.

### Security Questions

Not a second factor, as Lesson 4.3 established, and a poor recovery method because the answers are frequently things you have published.

If a service forces them, **invent the answers and store them in the vault as passwords**, because that is what they are. Never the real answer. Your mother's maiden name is in a public record and your first school is on your own profile.

### The Ranking

| Method | Fit for a root node | Notes |
|---|---|---|
| Printed codes and a spare key, offline | Yes, this is the root | No incoming arrows, cannot be reset by anybody |
| Second hardware key, registered | Yes | The only option above the phishing resistance line |
| Backup codes in the vault | No, it has an incoming arrow | Fine as a middle node, never as the root |
| Dedicated recovery email | No | Workable if it is not recovered by what it recovers |
| Recovery contact | No | Adds a person to your threat model, deliberately |
| Phone number | Avoid | Restricted in current guidance, defeated without touching you |
| Security questions | Avoid, and lie | Treat as passwords if forced |

---

## 5. Configuration, Corrected

The settings worth checking, with two entries removed because they no longer exist.

### The Account That Resets The Others

Your primary email first, always, because every other account's recovery probably terminates here.

- Above the line for sign in, with a second key registered.
- **Recovery phone removed**, or reduced to the minimum the service permits.
- Recovery email is a dedicated address, not recovered by this one.
- Alerts on for sign in, password change, recovery information change, and second factor added or removed.
- Third party application access reviewed, with unused grants removed.
- **Passkeys reviewed.** After three lessons on passkeys, check which devices hold one for this account, and remove entries for devices you no longer have.

**One correction.** Guidance for this account commonly includes disabling "less secure app access." That setting is gone. It was removed for consumer accounts in 2022 and fully disabled across Workspace by 2025, with application passwords and OAuth replacing it. (Reference 7) If a checklist you are following still contains it, the checklist has not been revised in three years, which tells you something about the rest of it.

### Source Repositories

Second factor above the line, recovery codes stored and reachable without the account. Review SSH keys and remove old ones. Review OAuth grants, which accumulate silently and often hold write access. Check the security log.

### The Vault Itself

- A long passphrase, not a password.
- **Argon2id**, and note that the memory parameter is the primary defence rather than the iteration count, which Lesson 4.1 covered.
- Automatic lock on a short timer, and clipboard clearing.
- Backups: one local, one offsite, one offline, and a restore actually tested rather than assumed.

**One correction here too.** Older guidance suggests a memorised master password plus a "secure hint." Do not use a hint. A hint is a permanently attached clue that an attacker reads and you do not need. If you cannot remember the passphrase, the answer is the paper copy in the safe, which is the root node from Figure 3.

### Financial and Cryptocurrency Accounts

Second factor above the line where supported, and never text messages. Transaction and sign in alerts on. Withdrawal address allow lists where offered, which convert a full compromise into a smaller one.

And the point Lesson 4.1 made from the 2022 vault breach: **anything that cannot be rotated does not belong in the vault.** A recovery phrase is the clearest example. Rotating a password does not recall a stolen copy of your vault, and a phrase you cannot change is exposed permanently.

---

## 6. When It Has Already Happened

### The Signs

Your password stops working. A second factor you did not add appears, or one you did add disappears. Sign in alerts you cannot account for. Messages you did not send. Recovery information changed. Settings you did not change.

**The recovery information changing is the highest signal item on that list**, because it is what an attacker does to keep the account after you notice.

### The Order, and Why It Is Not the Obvious One

The instinctive first move is to change the password. Do that, and understand what it does not do.

**A password change does not, on most services:**

- End sessions that are already signed in.
- Remove a mail forwarding rule.
- Revoke an OAuth grant to a third party application.
- Remove a second factor or a recovery method the attacker added.

So an attacker who has established any of those keeps access through a password change, and may be reading the notification about it.

**Minute 0 to 5.** Get in, using a backup key or a recovery code if the password is already changed. Change the password. Then, in the same sitting rather than later:

**Minute 5 to 20, persistence, in this order:**

1. **Authentication and recovery methods.** Remove any second factor or recovery route you did not add. This is first because it is how they come back.
2. **Sessions.** Revoke every active session except the one you are using.
3. **Forwarding and filters.** Mail forwarding, auto-forward rules, and filters that delete or archive. A rule that silently forwards and deletes is the classic quiet persistence, and it survives everything else on this list.
4. **Third party access.** Revoke OAuth grants you do not recognise, and any you do not currently need.
5. **Application passwords**, where the service issues them.

**Minute 20 to 60, scope.** What did they reach? What was downloaded or exported? Who was messaged? Screenshot the account activity log before it ages out, because most services keep a short window.

**Hours 1 to 24, blast radius.** Which accounts list this address for recovery? Those are now suspect and should be checked in the order of Lesson 4.3. Notify anybody who received messages. If it is a work or client account, notify immediately rather than after you have tidied up, because the disclosure clock is usually contractual.

**Afterwards, cause.** Was it a reused credential, a relayed code, a session cookie, or the recovery path? The answer determines what you change, and "be more careful" is not one of the available answers.

### When You Cannot Get Back In

Follow the official process, provide what identity evidence you have, and expect it to take time. Support is a real route and it is slow by design, which Section 1 argued is correct.

**Be realistic about the ceiling.** For most consumer services there is no human to appeal to beyond that process, and legal escalation is not a practical option for an individual. This is why the paper in the safe matters more than any part of the response plan: **prevention here is not merely cheaper than recovery, it is frequently the only thing that works.**

---

## 7. Monitoring You Will Actually Do

The usual version of this section has five schedules: daily, weekly, monthly, quarterly and annually. Nobody keeps five schedules. A plan that is not followed is worse than a smaller one that is, because it produces the feeling of having a plan.

**Let the alerts do the daily work.** You configured them in Section 5. They watch continuously, they cost nothing, and they fire on the events that matter. If an alert arrives that you cannot account for, that is the event.

**One calendar entry, once a quarter, thirty minutes:**

1. Sign in to your two most important accounts using the **backup** method only.
2. Confirm you can reach the recovery codes without the account they unlock.
3. Review active sessions and third party grants on those accounts, and remove what has accumulated.
4. Check breach notifications from your vault or a breach notification service.
5. Add any account created since last time.

**One entry a year, an hour:** redraw the graph from Section 3, because it drifts as you add accounts, and confirm the root node is still where you left it and still physically accessible.

That is two calendar entries and a set of alerts. It is small enough to survive contact with a busy quarter, which is the only property that matters.

---

## 8. What Module 4 Built

**Lesson 4.1** established that a vault solves password reuse completely and creates a single point of failure, and that a real breach cost people years because of settings nobody revisited.

**Lesson 4.2** established that a hardware key makes phishing structurally impossible rather than merely unlikely, and that the second key is not optional.

**Lesson 4.3** established that there is one line worth crossing, that everything below it fails the same way, and that adding a strong method raises nothing while a weak one is still enabled.

**This lesson** established that all of that sits beside a second door, that attacker effort has already moved to it, and that the fix is a recovery graph with a physical root.

Read together they make one argument: **name what a control closes, then go and look at what is still open.** That is the method from Lesson 1.4, and it is the only part of this module that will still be correct in ten years.

**Next module:** the endpoint, which is where the last three attacks in Lesson 4.3 Figure 2 all ended up.

---

## Knowledge Check

**Question 1.** Why is "security against recoverability" the wrong way to frame account recovery?

- A) Recovery is always less secure, so there is no trade
- B) Recoverability does not affect security at all
- C) The two are the same thing measured differently
- D) The real trade is between fast recovery and safe recovery, and a slow, noisy, pre-arranged path can be both reliable and hostile to an attacker

**Answer: D.** Apple's Stolen Device Protection is a working example: an hour of delay plus a second biometric check before security settings can be changed away from familiar locations.

**Question 2.** You store your email recovery codes in your password vault, and your vault account is recovered through your email. What have you built?

- A) A correctly layered recovery plan
- B) A loop, in which losing any one node loses all three, with nothing having been attacked
- C) A secure arrangement, because both are encrypted
- D) A problem only if the vault is cloud hosted

**Answer: B.** Each instruction is individually correct. The loop only appears when you draw them together.

**Question 3.** What makes a node suitable as the root of a recovery graph?

- A) It depends on nothing else, and it is physical rather than online
- B) It is protected by the strongest available encryption
- C) It is stored in at least three places
- D) It is the account you use most often

**Answer: A.** Anything online is recoverable by somebody, which means an incoming arrow you did not draw and did not choose.

**Question 4.** A phishing kit detects that a site is about to request a passkey and offers the target a different method instead. What has been defeated?

- A) The WebAuthn protocol
- B) The origin binding in the browser
- C) Nothing about the passkey, which was simply never used, because a weaker method was still enabled
- D) The hardware key's private key storage

**Answer: C.** Every fallback left enabled is a menu item the attacker chooses from, and they will choose the worst one you left on.

**Question 5.** How does the criminal group described in the joint national advisory typically get in?

- A) By deploying malware through email attachments
- B) By exploiting unpatched public services
- C) By purchasing credentials on criminal markets
- D) By telephoning support, impersonating an employee, and having the password reset and the second factor moved to a device they control

**Answer: D.** No malware, no phishing page, and no interaction with the target, who finds out afterwards.

**Question 6.** Which of these does a password change reliably do on most services?

- A) None of these reliably, which is why the persistence checklist matters more than the password change
- B) End all existing signed in sessions
- C) Remove mail forwarding rules the attacker created
- D) Revoke third party application access

**Answer: A.** An attacker with a forwarding rule, an OAuth grant, or an added recovery method keeps access through a password change, and may be reading the notification about it.

**Question 7.** In the first twenty minutes of responding to a compromise, what should be removed first?

- A) Mail forwarding rules
- B) Any authentication or recovery method you did not add
- C) Third party application grants
- D) Active sessions

**Answer: B.** All four need doing. Added methods come first because they are how the attacker returns after everything else is cleaned up.

**Question 8.** What is the current status of Facebook's Trusted Contacts recovery feature?

- A) It is the recommended recovery option for that platform
- B) It was renamed and expanded
- C) It was discontinued around 2022, and guidance still listing it is out of date
- D) It is available only to business accounts

**Answer: C.** Apple's Account Recovery Contact does exist and works similarly, and it puts another person into your threat model deliberately.

**Question 9.** What is wrong with setting a hint for your vault master password?

- A) A hint is a permanently attached clue that an attacker reads and you do not need
- B) Hints are encrypted separately and slow down unlocking
- C) Nothing, a hint is good practice for a memorised passphrase
- D) Hints expire after a fixed period

**Answer: A.** If you cannot remember the passphrase, the answer is the paper copy in the safe, which is the root node of your graph.

**Question 10.** Why is a monitoring plan with daily, weekly, monthly, quarterly and annual tasks a poor plan?

- A) Frequent checking desensitises you to real alerts
- B) Most services rate limit security reviews
- C) The tasks conflict with each other
- D) Nobody keeps five schedules, and a plan that is not followed is worse than a smaller one that is

**Answer: D.** Configured alerts do the continuous work. Two calendar entries, one quarterly and one annual, is small enough to survive a busy quarter.

---

## Capstone Lab: The Graph, the Audit, and the Rehearsal

**Time: 2 to 3 hours.**

This is the capstone for Module 4, so it audits what the previous three labs built rather than adding anything new. The centre of it is Part 1, and Part 1 needs paper.

**A safety note before you start.** Part 4 deliberately does **not** ask you to initiate a real password reset on an account you care about. Recovery flows cannot always be cancelled once started, several services impose a waiting period or invalidate sessions when one begins, and a rehearsal that locks you out of your own email for five days is a poor lesson. The tests below are chosen to be reversible.

---

### Part 1: Draw the Graph (40 minutes)

On one sheet of paper. Not in a document.

1. Draw a box for each of: your primary email, your vault, your recovery email if you have one, your phone number, your hardware keys, and your stored recovery codes.
2. Draw an arrow from each box to whatever recovers it. Label each arrow with the actual method, checked in the service's settings rather than recalled.
3. **Find every cycle.** Trace each arrow forward until you either reach a node with no outgoing arrow, or return somewhere you have been.
4. Mark your root nodes. If there are none, write "no root" in large letters, because that is the finding.

**Deliverable.** A photograph or scan of the sheet, a list of every cycle found, and your root nodes or the absence of them.

---

### Part 2: Break the Loops (35 minutes)

For each cycle from Part 1, do one of these and record which:

- Create a physical root: print the codes, put them with a spare key, and place them somewhere that is not your home office.
- Remove an arrow: delete a recovery route that creates the dependency.
- Redirect an arrow: point a recovery method at the physical root instead of at another online account.

| Cycle | How it was broken | New root |
|---|---|---|

**Deliverable.** The table, and a redrawn graph with no cycles.

---

### Part 3: The Menu Audit (30 minutes)

Figure 2 said every enabled fallback is a menu item the attacker chooses from. Go and read the menu.

For your six most important accounts, list **every** route in, including ones you did not set up and had forgotten: sign in methods, recovery methods, third party grants, and application passwords.

| Account | Routes in | Which is the weakest | Removed? |
|---|---|---|---|

**Deliverable.** The table, plus a count of routes you found that you did not know were enabled. That count is the interesting number.

---

### Part 4: Rehearsal, Safely (30 minutes)

Three tests, all reversible.

1. **Backup method sign in.** On two accounts, sign out and sign back in using only the backup key or backup method. Do not use the primary.
2. **Reach the root.** Physically go to wherever the root node lives and confirm it is there, current, and readable. Codes regenerated six months ago and never reprinted are not current.
3. **Read the process, do not run it.** For your two most important accounts, find the provider's documented account recovery procedure and write down what it actually requires. Compare it against the standard in Section 2: scripted checks, two independent verifications, one resistant to voice impersonation.

**Deliverable.** Results of tests one and two, and for test three, the requirements each provider states plus your judgement on whether somebody could talk their way through it.

---

### Part 5: Persistence Drill (20 minutes)

Without a compromise having occurred, walk the persistence checklist from Section 6 on your primary email as though one had.

| Item | Where the setting lives | Anything unexpected found? |
|---|---|---|
| Authentication and recovery methods | | |
| Active sessions | | |
| Forwarding rules and filters | | |
| Third party access | | |
| Application passwords | | |

**The point is finding the settings while calm**, so that during an incident you are executing rather than searching. Note how long it took you to locate each one.

**Deliverable.** The table, including anything unexpected. People routinely find an OAuth grant from years ago.

---

### Part 6: Alerts and Two Calendar Entries (15 minutes)

- Enable alerts on your five most important accounts for: sign in, password change, recovery information change, and second factor added or removed. Record which of the four each service actually supports, because the gaps are the finding.
- Create the two calendar entries from Section 7, quarterly and annual, with the checklist in the body of the entry rather than in a separate document you will not open.

**Deliverable.** The alert support table and confirmation both entries exist.

---

### Submission

1. **The graph**, Part 1, photographed, with cycles and roots.
2. **Loops broken**, Part 2, with the redrawn graph.
3. **Menu audit**, Part 3, with the count of forgotten routes.
4. **Rehearsal**, Part 4, including your judgement on the two recovery processes.
5. **Persistence drill**, Part 5.
6. **Alerts and calendar**, Part 6, with the gaps.
7. **Module reflection**, one page: across all four lessons of Module 4, what was the single largest gap between what you believed your setup was and what you found it to be?

### Assessment

| Area | Points | What is measured |
|---|---|---|
| The graph | 25 | Drawn from checked settings, cycles genuinely traced, roots identified or their absence stated |
| Loops broken | 25 | At least one physical root created, and the graph is acyclic afterwards |
| Menu audit | 15 | Six accounts, every route listed, forgotten routes reported honestly |
| Rehearsal | 15 | Both reversible tests performed, recovery processes read and judged |
| Persistence drill | 10 | All five settings located, timings noted |
| Alerts and calendar | 5 | Enabled where available, gaps recorded |
| Module reflection | 5 | Names a real gap rather than restating the lessons |
| **Total** | **100** | |

**Note the weighting.** Half the marks are the graph and breaking its loops, because that is the part no other exercise in this course covers, and it is the part that prevents the failure where nothing was attacked and you simply cannot get in.

---

## Additional Resources

### Primary Sources

- Apple, About Stolen Device Protection for iPhone: https://support.apple.com/en-us/120340
- CISA and partners, Scattered Spider advisory AA23-320A, updated July 2025: https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a
- NIST SP 800-63B-4, Digital Identity Guidelines: https://pages.nist.gov/800-63-4/sp800-63b.html

### On the Downgrade

- Analysis of the fallback downgrade problem, including the passkey detection behaviour: https://workos.com/blog/passkeys-stop-ai-phishing-mfa-fallbacks

Read this one even if you have adopted passkeys everywhere. Especially then.

### Discussion Questions

1. Part 1 asked you to draw the graph on paper. What did you find that you would not have found by reading a checklist?
2. Section 1 argued that good recovery is slow, noisy and arranged in advance. Which of your accounts fails that test most badly, and would you accept an hour of delay on it?
3. The help desk attack in Figure 2 does not touch the target at all. If you were scoping an engagement, how would you test a client's recovery process without impersonating a real employee?

---

## References

1. [Apple, About Stolen Device Protection for iPhone](https://support.apple.com/en-us/120340)
2. [WorkOS, Passkeys stop phishing, your MFA fallbacks undo it, on the passkey downgrade behaviour in phishing kits](https://workos.com/blog/passkeys-stop-ai-phishing-mfa-fallbacks)
3. [CISA, Scattered Spider advisory AA23-320A, updated July 2025](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a)
4. [MITRE ATT&CK, Scattered Spider group profile G1015](https://attack.mitre.org/groups/G1015/)
5. [NIST SP 800-63B-4, on restricted authenticators](https://pages.nist.gov/800-63-4/sp800-63b.html)
6. [Ask Leo, Facebook Trusted Contacts is going away](https://askleo.com/facebook-trusted-contacts-is-going-away-prepare-now/)
7. [Google Workspace Updates, winding down Google Sync and less secure apps support](https://workspaceupdates.googleblog.com/2023/09/winding-down-google-sync-and-less-secure-apps-support.html)
