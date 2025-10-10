Hey There! Thanks for checking out the guide, I hope it really helps you. There are lots of links to other vendors, NONE of the links are affiliate links where I make any sort of money. Please feel free to reach out to [me](https://bsky.app/profile/ping-sec.bsky.social) on Bluesky with any questions or for clarifications. You'll find me lurking on the TCM Security Discord as PING. You can help make the guide a community-driven effort to lift one another up with your own projects and advice.

> [!NOTE]
> NOTE: This guide will be very photo heavy, so it may take longer to load for some users. It is also pointed more towards first-time builders and the experienced friends out there may be a little bored with the guide quickly. If you'd like some *other* reading material on the more complex, visit my good friend __0xBEN's__ site: https://benheater.com/

> [!ANOTHER NOTE]
> I am trying to build this machine using parts nearly entirely from eBay or select Amazon sellers. If I find a good deal on Facebook, I may snag that too. I'm always hunting for more gear!

# Homelab Ideas:
- You can find a [Raspberry Pi kit](https://www.pishop.us/product-category/raspberry-pi/raspberry-pi-5/raspberry-pi-5-kits/) for just about $120 USD, containing the Raspberry Pi 5, USB-C Power Supply (They will NOT run off a cell phone charger) and a microSD Card.
- One could find mini-pc's or Small Form Factor machines with better processors and more memory for likely cheaper via: eBay. The bigger your budget, the better the performance.
	- I am a frequent Facebook marketplace, eBay or local computer re-seller's/repair shop hunter. If I see something that's reasonable I grab it for my homelab.
- "Retired Gaming PC" or that *old* PC you just upgraded recently.

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
> - HBA Card(s):
> - RAM:
> - PSU:
> - GPU?
> - Upgraded 10Gbe NIC card
> - Fan upgrades?

# Thinking about the build
This is the most important part of the entire process. Weight out your budget and your goals. Do you want a VM or Virtualization server, A backup server, a 4K media streaming powerhouse, or a docker container paradise? **Use what you have and upgrade, when, and if, you are able**. Start with something simple like I did, with a Raspberry Pi, a freebie from the E-Waste recycler in your area, a cheapie you found on eBay or from the pile at work the IT Guy was giving away.

When I started buying equipment, I started cheap and eased my way into the larger, overspecced server that I have now. My original plan was to only buy 2 Raspberry Pi 4's to run a network ad-blocker using a utility called [Pi-Hole](https://pi-hole.net). I wanted 2 of them for each side of my house, sometimes when one goes down for an update or upgrade, it's nice to have a backup ready to continue feet away.

Next, I switched to purchasing a couple of [mini or small form factor](https://www.ebay.com/itm/295020790427?_skw=mini+home+server+pc&itmmeta=01K75PJSMDGD23RVNHQF8EE7A7&hash=item44b09c029b:g:NSsAAeSwFWZo2Egz&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1dGJ6FRavo2V%2FUWe8e1pDzSiLutTQ4eI7LyAT%2FcmdT1wT7ev5SJt%2BNlNUAYPu3xbYh7gBeRUCpd30%2F59S%2BV6JZYLOHbYsDnEi0bmKlD6B4v08yfG6SwXPd1eYOUx0egPmwhZwuKTYHv41Wt1EEX20f355UcDKiMY17ioJ6V%2BJlf5zaeWSYlmGXPHRzn36%2FyI5FZbGeabFv4ys9pV5JXbZRHYmLLQtRsF8%2FLMnw7SLtEMlh%2B5hpmrZiRrnXyX1CpvGo%3D%7Ctkp%3ABlBMULCay7a5Zg) PC's. This started the main hobby that's become my homelab today. I ran so many docker containers on those little guys, I had an absolute blast learning how to configure Docker Compose files to bring up an entire stack of applications that I used often up with one command and little wait time.

Recently, I found a marketplace post nearby, in which the poster was giving away their homelab. It was 3 nice yet older Dell servers, fully upgraded, with some Cisco switches. Even though I could never get in touch with the poster, I began contemplating building another machine for my personal homelab. I've chosen to document that journey here. Further on in the guide, I will be posting links, resources, and buying tips as I spec out and build the new machine.

I mostly follow the guides from [ServerBuilds.net](https://serverbuilds.net), they specifically recommend older equipment which still has lots of life remaining but that was retired on a planned decommission program by their respective owners. As I'm building a new big rig for the homelab, it felt more honest to just buy what I wanted, instead of what I needed. Especially considering the parts are rather reasonable at auction and if you look hard enough.

Misc Pictures:

For starters, I ordered a 4u rack mounted case, the Rosewill RSV-R4100U case from eBay. Why a 4u case? I found a good deal on it and I already have a rack that lives in my closet that will fit the new machine nicely. There happens to be space for either 7x 3.5" hard drives or 14x 2.5" SSD slots. 

Here are my Australian Shepherds helping me open the box:
![[Doggo-opening-party.jpg]]

WHAT'S IN THE BOX?!
![[4u-front-view.jpg]]
Front view of the case, bonus shot of my feet. It does have a locking panel for the power and reset buttons, 2 USB 3.0 ports, and blanks for 2 5.25" size peripherals. There is also a front mounted 120mm fan for cooling, on the left side.

Honestly, I will likely remove the panel since I don't mind having these exposed to the world. Although it does make it look a bit cleaner, so I am on the fence still.

Following this sentence, there are more photos showing off the different parts of the case:

![[4u-internal.jpg]]
Above is the case with the front panel removed. Do you research on cases, this one I don't so much like internally as I have to remove the drive bays to mount the motherboard or other PCIe cards.
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

Next, I will purchase a Power Supply Unit or PSU, for short. I will need DDR4 for RAM, hard drives for storage, 2x CPU's and likely a very cheap video card for graphics. I am rather annoyed that there are few expansion slots, as I would have liked to have space for a 10GBe Network Interface Card or NIC, for short.