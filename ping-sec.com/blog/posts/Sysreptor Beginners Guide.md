---
title: "SysReptor for Beginners: Stop Writing Pentest Reports in Word"
description: "A hands-on introduction to SysReptor — what it is, how to self-host it, the four concepts that make the UI click, and how to build a finding library that cuts your reporting time in half."
tags:
  - sysreptor
  - reporting
  - pentesting
  - tools
  - beginner
draft: false
date: 2026-07-31
---

## What it is

SysReptor is a pentest reporting platform. You design the report layout once in HTML/CSS, write your actual findings in Markdown, and click a button to render a PDF. The whole point is to stop fighting Word every time you finish an engagement.

It's built by Syslifters (an Austrian shop) and comes in two flavors: a free Community Edition and a paid Professional tier. You can self-host either one, or pay for their cloud.

> [!info] Source-available, not open source
> Despite what a lot of write-ups say, SysReptor ships under the custom "SysReptor Community License" — not GPL or MIT. You can read the code and run it internally for free, but you don't get the same rights to fork, modify, and redistribute. Worth knowing if you're making this call for an employer.

## Why bother?

If you've written a report in Word, you already know the answer. But specifically:

- **Finding templates.** Build a library of your common findings once (reflected XSS, missing HSTS, weak TLS, whatever) and pull them into any report. This is the single biggest time-saver.
- **Consistent formatting.** The design is separate from the content, so a junior's report looks identical to a principal's.
- **Markdown.** No more clicking "Keep Source Formatting" 40 times when you paste a terminal dump.
- **Tool imports.** Nmap, Nessus, Burp, OpenVAS, ZAP, Qualys, and SSLyze output can be pulled in via CLI.
- **Notes.** There's a built-in note-taking section per project, so you can keep your engagement notes and your report in the same place.

It's also very popular for OSCP/CPTS/PNPT exam reports, which is how most people first run into it.

---

## Step 0: Don't install anything yet

Go play with the hosted demo first: **https://sysreptor.com/demo**

Seriously. Spend fifteen minutes clicking around before you spin up infrastructure. You'll know within that window whether the workflow fits how you think. The playground has demo reports loaded so you can see what a finished product looks like.

If you decide you like it, then pick your deployment.

## Choosing a deployment

| Option | Good for | Notes |
|---|---|---|
| **Cloud (paid)** | Teams who don't want to run servers | Syslifters hosts it; client data lives on their infra |
| **Self-hosted Community** | Solo pentesters, students, homelabs | Free, 3-user limit |
| **Self-hosted Professional** | Consultancies | Adds SSO, version history, granular permissions, spell check |

For a first-timer: self-hosted Community. It's free and there's no meaningful trial friction.

---

## Installing (self-hosted)

### Requirements

- **Ubuntu** server or VM — this is what's officially supported
- **8 GB RAM** (their stated requirement; it runs on less but rendering is memory-hungry)
- Docker with the Compose v2 plugin
- A modern desktop browser to access it (Chrome, Firefox, Edge, or Safari)

It *can* run on Kali, macOS, RHEL, and others, but you're on your own for dependencies. The install and update scripts assume Ubuntu. If you're doing this for an exam, running it on your host rather than inside your attack VM is usually smarter — that way it survives VM snapshots and reverts.

### The install

Install prerequisites:

```bash
sudo apt update
sudo apt install -y sed curl openssl uuid-runtime coreutils
```

Install Docker:

```bash
curl -fsSL https://get.docker.com | sudo bash
```

Add yourself to the docker group so you're not sudo-ing everything:

```bash
sudo groupadd docker 2>/dev/null
sudo usermod -aG docker $USER
newgrp docker
```

Run the installer:

```bash
bash <(curl -s https://docs.sysreptor.com/install.sh)
```

That script creates a `sysreptor/` directory with the source, generates your secrets and encryption keys, creates Docker volumes, pulls images, and brings the stack up. It'll prompt you to create your first admin user.

When it finishes, hit **http://127.0.0.1:8000/**.

> [!warning] You're piping a script from the internet into bash
> If that bothers you — and it should at least register — read the script before you run it, or use the manual Docker Compose install documented on the same page. Syslifters signs their images with cosign and documents the verification steps; do that if this is going anywhere near client data.

### Immediately after install

> [!danger] Put a real webserver in front of it
> The install script can spin up Caddy for you, which handles TLS automatically. Don't leave port 8000 exposed unauthenticated on anything routable. Syslifters maintains a public security advisories page on GitHub, and running the bare app server without a reverse proxy is explicitly not recommended.

Config lives in `sysreptor/deploy/app.env` if you need to change things later.

To stop it: `cd sysreptor/deploy && docker compose stop`

---

## The mental model

Four concepts. Get these and the UI makes sense:

**Design** — the template. HTML + CSS + Vue.js that defines what the PDF looks like: cover page, headers, fonts, table styles, how findings get laid out. You pick a design when you create a project. Most people set up one company design and never touch it again.

**Project** — one engagement. Contains report sections (scope, methodology, exec summary), findings, notes, and uploaded images.

**Finding** — one vulnerability. Title, severity, description, impact, recommendation, references, evidence. Written in Markdown.

**Finding Template** — a reusable finding. Your library. Instead of rewriting the SQLi description for the 30th time, you insert the template and edit the specifics.

The relationship: a **Design** styles a **Project**, which contains **Findings**, which are often created from **Finding Templates**.

---

## Your first report

1. **Import a demo design** so you're not starting from a blank page. The docs have demo reports and designs available for import (see docs.sysreptor.com/demo-reports). If you're doing HTB certs, they publish HTB-branded designs too.

2. **Create a project.** Pick your design, name the engagement, set the dates and client.

3. **Fill in the report sections.** These are the non-finding parts — scope, methodology, executive summary. What fields exist here is determined by the design, which is why designs matter.

4. **Add findings.** Either from scratch or from a template. Set severity (CVSS scoring is built in), write the description, drag in screenshots.

5. **Preview.** There's a live PDF preview pane. Use it constantly — it's the fastest way to catch a broken table or an image that's blowing out a page.

6. **Publish.** Renders the final PDF.

That's genuinely the whole loop.

---

## Markdown tips that'll save you pain

- **Images:** drag and drop into the editor. They get uploaded to the project and referenced automatically. Don't hotlink external images — they won't render reliably in the PDF pipeline.
- **Code blocks:** use fenced blocks with a language hint for syntax highlighting. Long terminal output will overflow the page — trim it to the relevant lines. Nobody wants your full nmap output in the finding body.
- **Tables:** standard Markdown tables work, but wide tables break page layout. If you need a wide table, that's usually a sign it belongs in an appendix.
- **Page breaks and layout control:** handled in the design, not the Markdown. If your finding is splitting awkwardly across pages, fix the CSS, not the content.
- **References:** there's a cross-referencing system so you can link between findings and sections and have them resolve properly in the PDF.

The renderer uses Chromium under the hood, so "how would this look as a web page" is a reasonable intuition for how it'll paginate.

---

## Building your template library

> [!tip] This is the part beginners skip
> Everything else in SysReptor is convenience. The finding library is where the actual return on investment lives. Start building it from your very first report.

After every engagement, take any finding you wrote that wasn't hyper-specific to that client and turn it into a template. Write the description and recommendation generically, leave placeholders for the client-specific details.

Within a handful of engagements you'll have a library that turns a four-hour report into a one-hour report. Templates also support multiple languages if you report in more than one.

If you're migrating from another tool, there are importers for Ghostwriter and DefectDojo finding templates.

---

## Automation: the `reptor` CLI

Once you're comfortable in the UI, there's a Python CLI called `reptor` (and a matching Python library) that automates the boring parts.

```bash
pip3 install reptor
```

What it's good for:

- Piping tool output straight into findings or notes — `nmap`, `Nessus`, `Burp`, `ZAP`, `SSLyze`, `OpenVAS`, `Qualys`
- Bulk-uploading evidence files
- Creating and pushing projects programmatically
- Exporting findings as a summary or checklist (handy for retest tracking)
- Translating reports via DeepL

Typical use: dump your nmap scan into a note automatically rather than copy-pasting. The Python library is the better choice if you're building anything more involved than one-off commands.

Don't reach for this on day one. Get comfortable with the manual workflow first, then automate what actually annoys you.

---

## Community vs Professional

Community gets you the full reporting workflow — unlimited projects, designs, templates, tool imports, notes, archiving. The main limits:

- **3 users max**
- No SSO (Keycloak, Entra ID, Google, ADFS)
- No version history on projects/findings/notes/designs
- No granular user permissions
- Spell check (LanguageTool) is Pro-only
- The AI Agent feature is read-only in Community; write access is Pro

Also worth knowing regardless of tier: **PDF is the only output format**. There's no CSV, JSON, or DOCX export. If a client demands a Word deliverable, SysReptor is not going to solve that for you.

For a solo tester or a student, Community is entirely sufficient. The 3-user cap is what pushes small teams to Pro, usually.

---

## Common gotchas

**Rendering breaks after an update.** The PDF renderer depends on Chromium, and Chromium updates have broken rendering in the past. If reports suddenly stop generating, update SysReptor before you debug anything else.

**Out of memory during render.** Rendering runs Chromium in the app container. Under-provisioned VMs fail here first. 8 GB is the stated requirement for a reason.

**Design edits blowing up the report.** The designer is real HTML/CSS/Vue — you can absolutely break it. Copy an existing design before customizing rather than editing in place, and there's a debugging page in the docs for when the preview goes blank.

**Following an outdated tutorial.** There are a lot of 2023–2024 SysReptor walkthroughs with install steps that no longer match. The official docs are actively maintained; prefer them.

**Treating it as a security boundary.** Findings data is client-confidential. Encryption at rest is supported, but you still need to think about TLS, backups, access control, and where the box lives on your network. Read the security considerations page in the docs before this holds real client data.

---

## Where to go next

- **Docs:** https://docs.sysreptor.com — genuinely good, better than most projects this size
- **Playground:** https://sysreptor.com/demo
- **GitHub:** https://github.com/Syslifters/sysreptor — issues and the Q&A discussions board are active
- **Backups:** read the backups page *before* you need it, not after

## The 20-minute version

1. Play with the demo
2. Install on Ubuntu with the script, put Caddy in front
3. Import a demo design so you have something to look at
4. Create a project, write one finding, render the PDF
5. Turn that finding into a template
6. Repeat until your template library does most of the work for you
