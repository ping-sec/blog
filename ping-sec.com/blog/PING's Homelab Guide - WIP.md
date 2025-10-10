Hey There! Thanks for checking out the guide, I hope it really helps you. There are lots of links to other vendors, NONE of the links are affiliate links where I make any sort of money. Please feel free to reach out to [me](https://bsky.app/profile/ping-sec.bsky.social) on Bluesky with any questions or for clarifications. You'll find me lurking on the [TCM Security Discord](https://discord.gg/tcm) as PING. You can help make the guide a community-driven effort and resource. It always feels good to help one another!

> [!NOTE]
> NOTE: This guide will be very photo heavy, so it may take longer to load for some users. It is also pointed more towards first-time builders and the experienced friends out there may be a little bored with the guide quickly. If you'd like some *other* reading material on the more complex, visit my good friend __0xBEN's__ site: https://benheater.com/

> [!ANOTHER NOTE]
> I am trying to build this machine using parts nearly entirely from eBay or select Amazon sellers. If I find a good deal on Facebook, I may snag that too. I'm always hunting for more gear!

# Homelab Ideas:
- You can find a [Raspberry Pi kit](https://www.pishop.us/product-category/raspberry-pi/raspberry-pi-5/raspberry-pi-5-kits/) for just about $120 USD, containing the Raspberry Pi 5, USB-C Power Supply (They will NOT run off a cell phone charger) and a microSD Card.
- One could find mini-pc's or Small Form Factor machines with better processors and more memory for likely cheaper via: eBay. **The bigger your budget, the better the performance**. On that note, you can get something a bit more expensive like the [Minisforum MS-01](https://store.minisforum.com/products/minisforum-ms-01?utm_medium=cpcg&gad_source=1&gad_campaignid=20895639069&gclid=Cj0KCQjw3aLHBhDTARIsAIRij5_VQRK7O6Br0H7lsYgGiYo2GImoqURDc9y7jobUJHkSe9e6JVsttU8aAljpEALw_wcB) workstation.
	- I am a frequent Facebook marketplace, eBay and local computer re-seller's/repair shop hunter. If I see something that's reasonable I grab it for my homelab.
- "Retired Gaming PC" or that *old* PC you just upgraded recently. I have added retired machines to my home lab with absolute joy.

> [!Current Build]
> My Current, Working build:
> I am running [TrueNAS Scale CE](https://www.truenas.com/truenas-community-edition/) as my base OS. I'm using a Supermicro 4u case with room for 20 drives inserted into the front bays of the server. It uses 2x Intel(R) Xeon(R) CPU E5-2650 v2 @ 2.60GHz and gives me 16 cores and 32 threads, often abbreviated to just: 16c/32t in other places. It has 128 GB of DDR3 ECC RAM. Most of the drive bays are filled with Western Digital 8TB SATA Drives. There is also a 256GB SSD for Truenas to live on. This arrangement gives me 72.56 Terabytes for storage with some disks reserved for ZFS replication (we'll go over this in a later update to the guide).
> 
> Build Tasks: VMs, Media Streaming, among AI and other personal workflows.

> [!WIP Build]
> Work In Progress (WIP) Proxmox build:
> - Case: [Rosewill RSV-R4100U](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-R4100U&_sacat=0&_from=R40&_trksid=p4624852.m570.l1313)
> - Motherboard: [Huananzhi X99-F8D Plus](https://www.aliexpress.us/item/3256807414795351.html?spm=a2g0o.productlist.main.4.499azR1UzR1UEB&aem_p4p_detail=2025100916571710792524128859310000657189&algo_pvid=90ee19b6-411f-43bb-849f-58f653a98892&algo_exp_id=90ee19b6-411f-43bb-849f-58f653a98892-3&pdp_ext_f=%7B%22order%22%3A%22207%22%2C%22eval%22%3A%221%22%2C%22fromPage%22%3A%22search%22%7D&pdp_npi=6%40dis%21USD%21462.19%21171.01%21%21%213280.29%211213.71%21%402101e7f617600542378558278ee185%2112000041460603001%21sea%21US%21891770572%21X%211%210%21n_tag%3A-29919%3Bd%3Aafaafc1d%3Bm03_new_user%3A-29895&curPageLogUid=fYjs2sdJy6ix&utparam-url=scene%3Asearch%7Cquery_from%3A%7Cx_object_id%3A1005007601110103%7C_p_origin_prod%3A&search_p4p_id=2025100916571710792524128859310000657189_1)
> - Proposed yet necessary hardware:
> - Hard Drives:
> - CPU Coolers:
> - CPUs
> - HBA Card(s):
> - RAM:
> - PSU:
> - GPU?
> - Upgraded 10Gbe NIC card
> - Fan upgrades?

# Thinking about the build
This is the most important part of the entire process. Weight out your budget and your goals. Do you want a VM or Virtualization server, A backup server, a 4K media streaming powerhouse, or a docker container paradise? **Use what you have and upgrade, when, and if, you are able**. Start with something simple like I did, with a Raspberry Pi, a freebie from the E-Waste recycler in your area, a cheapie you found on eBay or from the pile at work the IT Guy was giving away.

When I started buying equipment, I started cheap and eased my way into the larger, overspecced server that I have now. My original plan was to only buy 2 Raspberry Pi 4's to run a network ad-blocker using a utility called [Pi-Hole](https://pi-hole.net). I wanted 2 of them for each side of my house, sometimes when one goes down for an update or upgrade, it's nice to have a backup ready to continue feet away. I quickly ran into memory issues and had to plan my next move.

Next, I switched to purchasing a couple of [mini or small form factor](https://www.ebay.com/itm/295020790427?_skw=mini+home+server+pc&itmmeta=01K75PJSMDGD23RVNHQF8EE7A7&hash=item44b09c029b:g:NSsAAeSwFWZo2Egz&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1dGJ6FRavo2V%2FUWe8e1pDzSiLutTQ4eI7LyAT%2FcmdT1wT7ev5SJt%2BNlNUAYPu3xbYh7gBeRUCpd30%2F59S%2BV6JZYLOHbYsDnEi0bmKlD6B4v08yfG6SwXPd1eYOUx0egPmwhZwuKTYHv41Wt1EEX20f355UcDKiMY17ioJ6V%2BJlf5zaeWSYlmGXPHRzn36%2FyI5FZbGeabFv4ys9pV5JXbZRHYmLLQtRsF8%2FLMnw7SLtEMlh%2B5hpmrZiRrnXyX1CpvGo%3D%7Ctkp%3ABlBMULCay7a5Zg) PC's. This started the main hobby that's become my homelab today. I ran so many docker containers on those little guys, I had an absolute blast learning how to configure Docker Compose files to bring up an entire stack of applications that I used often up with one command and little wait time.

Recently, I found a marketplace post nearby, in which the poster was giving away their homelab. It was 3 nice yet older Dell servers, fully upgraded, with some Cisco switches. Even though I could never get in touch with the poster, I began contemplating building another machine for my personal homelab. I've chosen to document that journey here. Further on in the guide, I will be posting links, resources, and buying tips as I spec out and build the new machine.

I mostly follow the guides from [ServerBuilds.net](https://serverbuilds.net), they specifically recommend older equipment which still has lots of life remaining but that was retired on a planned decommission program by their respective owners. As I'm building a new big rig for the homelab, it felt more honest to just buy what I always wanted, instead of what I needed. Especially considering the parts are rather reasonable at auction and if you look hard enough.

# Needs
Here are some "must-have's" that every build requires. We'll break them down into individual sections.

# Case
Not only do you want something with good airflow as mechanical hard drives like to be cool but you want plenty of room for as many as you can afford. This is why I went with the Rosewill case. It had space for 7 mechanical hard drives. You also want to consider if you need a server rack or cabinet. I have a 42u metal server rack which lives in the closet and holds my current 4u build with plenty of room to spare. Other options include the following: updating in a later edition of this guide.

# Motherboard
The motherboard is an important part though I'd argue the single most important piece is actually a quality power supply. If you're spending a good amount of cash on the rest of it and it fails due to some no name Chinese special, you're not going to be a happy camper. I chose to stick with the "Use what you have" mindset. Below you'll find pictures of the start of my build.

# Hard Drives
One could in theory go all the way with SSD's but that's rather expensive and low density of storage unless you've got a credit card with plenty of room. I suggest buying Western Digital external drives and "shucking" them like an oyster to get at the drive inside. It sounds complicated and definitely voids your warranty but anyone with a screwdriver can handle this task. I'll go into more detail on the shucking process in a later edition of the guide.

# CPU Coolers
Intel Xeon's, at least in the choice I'm making can run rather hot stuck in a case surrounded by mechanical drives. You want to increase the air flow as much as possible. Depending on the configuration of the motherboard and what you want to spend, a single CPU model may make more sense than the raw power of a dual CPU build. These are typically available for pretty reasonable on eBay and Amazon.

# CPU(s)
I based my decision on this topic on raw cores and threads. I have my eye on a pair of Intel Xeon CPUs that ship with a 20c/40 t count. They're usually around $42 for the ones I'm looking into. More options to come in a future edition of the guide.

# HBA Card(s)
These cards are great for adding more drive density to a machine, provided it can hold the drives in the first place. HBA stands for Host Bus Adapter card and they're usually flashed into IT mode and pretty widely available.

# RAM
This is where it can get expensive, depending on the proposed workload, and compatibility with your motherboard...It might be expensive. My motherboard seems to only take DDR4 and RAM prices seem to rollercoaster up then down fairly regularly for no real reason.

# Power Supply - PSU
Get a quality power supply, full stop. There are many different efficiency ratings but overall, remember this isn't meant to truly be a gaming build. You want enough juice flowing as efficiently as possible, while maintaining that you want a stable supply of power to your drives all the time. Cheap power supplies are known to surge sometimes and fry whatever is connected to them. That's a bad thing when you're spending this amount of money and have important data to protect.

# Graphics Card - GPU
Remember, we aren't going for a gaming build so we don't require a Nvidia GTX 5090 card or anything like that. We can get a purpose built card that will do a hardware conversion on media, known as transcoding. As this can be necessary since the CPUs may not have onboard video, you can remove it after you're done configuring the system. Proxmox, my chosen OS, allows one to setup headless or without a monitor through a web GUI.

# Network Interface Card - NIC
For my builds, I am typically transferring files that are large back and forth. Think media that's streaming to my smart home devices or mainly ISO's. I love trying out new flavors of Linux but I've been known to build labs like [[GOAD]] (link is to the notes portion of the site), an intentionally vulnerable Active Directory lab based on the ever popular Game of Thrones series. You'll want the fastest transfer speed you can get and one can find 1 or dual slot 10Gbe (instead of 1Gbe) cards for very reasonable. It helps to have a network switch which supports this otherwise, there's no point. To be clear, you can get by with onboard NICs in the majority of cases but note it could impact your workflow. Time is money as is often said!

# OS
This is an important consideration also. With TrueNAS and zfs, in general, you need all disks to be the same size or the array chooses the lowest TB storage and applies that capacity to all drives. Note that's an oversimplification but it's very close to the truth.

I am choosing Proxmox for the build this time. It's always been a bit of a mystery to me and I finally have the ability to learn it by tinkering with it in the comfort of my home. If I tried this all in a cloud lab, I'm looking at hundreds of dollars per month for all the VMs and such I tinker with on a daily basis.

# Miscellaneous Supplies
You'll want to have some thermal paste on hand and it doesn't hurt to have some spare ethernet patch cables and SATA cables. I've known SATA cables to go bad and completely confuse my troubleshooting efforts. I have thought whole drives were bad before and it was a simple SATA cable change that fixed me right up. 

Again, this isn't a gaming build so some artic 5 silver thermal paste is probably good. One shouldn't need something fancy like liquid metal. You'll only need a healthy dot, like the size of a BB for installing the CPU Cooler(s).
# Misc Pictures of my build unboxing:

For starters, I ordered a 4u rack mounted case, the Rosewill RSV-R4100U case from eBay. Why a 4u case? I found a good deal on it and I already have a rack that lives in my closet that will fit the new machine nicely. There happens to be space for either 7x 3.5" hard drives or 14x 2.5" SSD slots. 

Here are my Australian Shepherds helping me open the box:
![[Doggo-opening-party.jpg]]

WHAT'S IN THE BOX?!
![[4u-front-view.jpg]]
Front view of the case, bonus shot of my feet. It does have a locking panel for the power and reset buttons, 2 USB 3.0 ports, and blanks for 2 5.25" size peripherals. There is also a front mounted 120mm fan for cooling, on the left side.

Honestly, I will likely remove the panel since I don't mind having these exposed to the world. Although it does make it look a bit cleaner, so I am on the fence still.

Following this sentence, there are more photos showing off the different parts of the case:

![[4u-internal.jpg]]
Above is the case with the side panel removed. Do your research on cases, this one I don't so much like internally as I have to remove the drive bays to mount the motherboard or other PCIe cards.
![[4u-rear-view.jpg]]
Here is the rear of the case, note the cutouts meant for the power supply and the motherboard I/O shield to fit. You can also see the 2 80mm exhaust fans and the expansion slots.

Next, I bought a motherboard from a seller on eBay. This was actually something I had in storage for a PC I was considering building for a family member but it didn't work out. So, back to what I said earlier: "Use what you have." It was advertised as a gaming motherboard rather than a server motherboard. It's also from an odd Chinese brand I've never heard of before, so reliability testing will also be in the works. 

Here is the unopened box, prior to the dogs helping open it too:
![[mobo-box.jpg]]

This is a dual CPU board from Huananzhi that takes Intel Xeon Processors. This is the X99-F8D Plus.

![[mobo-full.jpg]]

Here, we have a close-up of a single CPU slot:
![[mobo-single-cpu-close-up.jpg]]

Here is a photo of the dual CPU's with protective covers still installed from the factory:
![[mobo-dual-cpu-close-up.jpg]]

and finally, here it is zoomed out somewhat to show the entire board outside of the anti-static bag:

![[mobo-full.jpg]]

Next, I will purchase a Power Supply Unit or PSU, for short. I will need DDR4 for RAM, hard drives for storage, 2x CPU's and likely a very cheap video card for hardware transcoding. I am rather annoyed that there are few expansion slots, as I would have liked to have space for a 10GBe Network Interface Card or NIC, for short.