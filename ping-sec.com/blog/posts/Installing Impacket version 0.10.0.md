---
title: Installing an Older Version of Impacket (0.10.0)
description: 'description: "Install Impacket 0.10.0 on modern Kali, Ubuntu, or Debian without the usual errors — venv, pipx, and source methods, plus a reproducible requirements.txt."'
tags:
  - pentesting
  - impacket
  - python
  - tools
  - active-directory
date: 2026-07-28
draft: false
---

Impacket 0.10.0 came out on **May 4, 2022**, and it's the release that **dropped Python 2.7 support**. Since it's a couple of years old now, the main thing that trips people up isn't Impacket itself — it's modern systems (Kali, Ubuntu 24.04, Debian 12) blocking global `pip` installs and shipping a newer Python than 0.10.0 was built against. The fix for both is the same: install it inside a **virtual environment**. That also keeps it from clobbering the Impacket that Kali already ships system-wide.

Here's the beginner-friendly path.

> [!info] Why pin an old version?
> Some tools, courses, or lab guides depend on behavior from a specific Impacket release, and a newer version can subtly break them. Pinning to `0.10.0` guarantees you get exactly that release instead of whatever is latest.

## Method 1: Virtual environment + pip (recommended)

A virtual environment ("venv") is just an isolated folder with its own Python and packages, so nothing you install leaks into the rest of your system.

**1. Make a folder and create the venv:**

```bash
mkdir ~/impacket-0.10 && cd ~/impacket-0.10
python3 -m venv venv
```

**2. Activate it** (you'll see `(venv)` appear in your prompt):

```bash
source venv/bin/activate
```

**3. Install the exact version:**

```bash
pip install impacket==0.10.0
```

The `==0.10.0` is what pins it to that specific release instead of grabbing the latest.

**4. Verify it worked:**

```bash
pip show impacket
```

You should see `Version: 0.10.0`. The example tools (`secretsdump.py`, `GetUserSPNs.py`, `wmiexec.py`, etc.) are now on your PATH *while the venv is active* — running any of them prints a banner like `Impacket v0.10.0` at the top.

When you're done, type `deactivate` to leave the venv. To use it again later, just `cd ~/impacket-0.10 && source venv/bin/activate`.

## Method 2: From GitHub source

Use this if you want to inspect or modify the code.

```bash
git clone https://github.com/fortra/impacket.git
cd impacket
git checkout impacket_0_10_0
python3 -m venv venv && source venv/bin/activate
pip install .
```

`git checkout impacket_0_10_0` rewinds the repo to the exact 0.10.0 tag before installing.

## Method 3: pipx (cleanest if you just want the tools globally)

If you don't want to manually activate a venv every time but still want isolation, [pipx](https://pipx.pypa.io) handles the venv for you and puts the commands on your PATH permanently:

```bash
sudo apt install pipx    # if you don't have it
pipx install impacket==0.10.0
```

## Common problems

> [!warning] `error: externally-managed-environment`
> This means you tried to `pip install` into the system Python (outside a venv). Modern Debian-based distros block that on purpose. Just use one of the venv/pipx methods above and the error goes away.

> [!warning] Build errors on Python 3.12+
> If you see errors mentioning `distutils` or `setuptools`, it's because Python 3.12 removed some modules that older packages expect. Two easy fixes:
> - Upgrade the build tools inside your venv first: `pip install --upgrade pip setuptools wheel`, then retry.
> - Or create the venv with an older interpreter if you have one installed: `python3.10 -m venv venv`.

> [!tip] `command not found` for the scripts
> The example tools only work while the venv is activated (Methods 1–2). If your prompt doesn't show `(venv)`, re-run `source venv/bin/activate`. With pipx (Method 3) they work anywhere.

## Pinning it in a `requirements.txt` (reproducible labs)

If you're building a lab, sharing a setup with students or teammates, or just want to be able to rebuild the exact same environment later, a `requirements.txt` is the cleanest way to do it. It's a plain text file listing the packages (and versions) you want, so anyone can recreate your environment with a single command.

**1. Create the file:**

```bash
echo "impacket==0.10.0" > requirements.txt
```

Or open it in an editor and add one package per line:

```text
impacket==0.10.0
```

**2. Install from it (inside an activated venv):**

```bash
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

Now anyone who has your `requirements.txt` gets the identical Impacket version, no guesswork.

> [!tip] Capture the *whole* environment
> Once everything works, you can freeze every installed package — including Impacket's own dependencies — so the environment is byte-for-byte reproducible:
> ```bash
> pip freeze > requirements.txt
> ```
> This overwrites the file with the full pinned dependency tree (e.g. `impacket==0.10.0`, `pyasn1==...`, `pycryptodomex==...`, and so on). Commit it alongside your lab notes so future-you can rebuild the exact same setup.

> [!example] Bonus: hash-pinned installs
> For maximum reproducibility (and to defend against a dependency being tampered with upstream), you can pin cryptographic hashes and require them at install time:
> ```bash
> pip install --require-hashes -r requirements.txt
> ```
> This only works if every line in the file includes a `--hash=sha256:...` entry. Tools like `pip-compile` (from [pip-tools](https://github.com/jazzband/pip-tools)) can generate those for you.

---

That's it — `0.10.0` installed cleanly in an isolated environment, plus a `requirements.txt` you can hand to anyone to reproduce it exactly.
