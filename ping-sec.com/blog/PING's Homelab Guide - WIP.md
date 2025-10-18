Hey There! Thanks for checking out the guide, I hope it really helps you. There are lots of links to other vendors, NONE of the links are affiliate links where I make any sort of money. 

> [!NOTE]
> There's also the [[Contact]] form.
> Discords: [TCM Security](https://discord.gg/tcm) and [HackSmarter](https://discord.gg/PhF23AnyJm)
> LinkedIn: https://www.linkedin.com/in/casey-campbell-a63255264
> Bluesky: https://bsky.app/profile/ping-sec.bsky.social

The guide is meant to be free information, and occupies a lot of time finding this information and searching and building, etc. If you want to support the guide, I keep 95% of what one sends. Please consider [Buying me a Coffee](https://buymeacoffee.com/pingsec)! Obviously not required, I'll continue posting regardless.

> [!NOTE]
> NOTE: This guide will be very photo and link heavy, so it may take longer to load for some users. It is also pointed more towards first-time builders and the experienced friends out there may be a little bored with the guide quickly. If you'd like some *other* reading material on the more complex, visit my good friend __0xBEN's__ site: https://benheater.com/

> [!ANOTHER_NOTE]
> This guide is from the perspective of an American buyer with ready access to platforms selling equipment, concerns like affordable HVAC, internet and reliable stable utilities. Your mileage may vary.
> 
> I am trying to build this machine using parts nearly entirely from eBay or select Amazon sellers. If I find a good deal on Facebook, I may snag that too. I'm always hunting for more gear!

# Homelab Ideas:
- You can find a [Raspberry Pi kit](https://www.pishop.us/product-category/raspberry-pi/raspberry-pi-5/raspberry-pi-5-kits/) for just about $120 USD, containing the Raspberry Pi 5, USB-C Power Supply (They will NOT run off a cell phone charger, they require more wattage than a normal everyday one can output) and a microSD Card. Pi's are usually considered overpriced for being underpowered but if you want to start small, this is it but I think your money would be better spent on a more capable box.


> [!NOTE] NOTE
> Raspberry Pi's are overpriced for being underpowered. Put that cash towards a more capable machine.

- One could find Intel NUCs, mini-pc's or Small Form Factor (SFF) machines with better processors and more memory for likely cheaper via eBay. **The bigger your budget, the better the performance**. On that note, you can get something a bit more expensive like the [Minisforum MS-01](https://store.minisforum.com/products/minisforum-ms-01?utm_medium=cpcg&gad_source=1&gad_campaignid=20895639069&gclid=Cj0KCQjw3aLHBhDTARIsAIRij5_VQRK7O6Br0H7lsYgGiYo2GImoqURDc9y7jobUJHkSe9e6JVsttU8aAljpEALw_wcB) workstation.
	- I am a frequent Facebook marketplace, eBay and local computer reseller's/repair shop hunter. If I see something that's priced reasonable, I grab it for my homelab.
- "Retired Gaming PC" or that *old* PC you just upgraded recently. I have added retired machines to my home lab with absolute joy or alternatively, harvested their drives for other machines.

> [!Current Build]
> My Current, Working build:
> - OS: TrueNAS Scale CE
> - Case: Supermicro 4U w/ 20 hard drive bays
> - Motherboard: Will need to check
> - Hard Drives: 8TB Western Digital + 1x 256 GB Samsung SSD for boot drive
> - CPU Coolers: 2x Noctua DH14s
> - CPUs: 2x Intel Xeon(R) CPU- E5-2650 V2 - 16c/23t
> - HBA Cards: Will have to check
> - RAM: 128 GB DDR-3 ECC
> - PSU: Evga 1,000W
> - GPU: Nvidia 1080
> - NIC Card: Onboard and 2 10Gbe ports via PCIe card
> 
> Build Tasks: VMs, Media Streaming, among AI and other personal workflows.

> [!WIP Build]
> Work In Progress (WIP) Proxmox build:
> - Case: [Rosewill RSV-R4100U](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-R4100U&_sacat=0&_from=R40&_trksid=p4624852.m570.l1313)
> - Motherboard: [Huananzhi X99-F8D Plus](https://www.aliexpress.us/item/3256807414795351.html?spm=a2g0o.productlist.main.4.499azR1UzR1UEB&aem_p4p_detail=2025100916571710792524128859310000657189&algo_pvid=90ee19b6-411f-43bb-849f-58f653a98892&algo_exp_id=90ee19b6-411f-43bb-849f-58f653a98892-3&pdp_ext_f=%7B%22order%22%3A%22207%22%2C%22eval%22%3A%221%22%2C%22fromPage%22%3A%22search%22%7D&pdp_npi=6%40dis%21USD%21462.19%21171.01%21%21%213280.29%211213.71%21%402101e7f617600542378558278ee185%2112000041460603001%21sea%21US%21891770572%21X%211%210%21n_tag%3A-29919%3Bd%3Aafaafc1d%3Bm03_new_user%3A-29895&curPageLogUid=fYjs2sdJy6ix&utparam-url=scene%3Asearch%7Cquery_from%3A%7Cx_object_id%3A1005007601110103%7C_p_origin_prod%3A&search_p4p_id=2025100916571710792524128859310000657189_1)
> - Proposed yet necessary hardware:
> - Hard Drives: Likely to be SAS Drives
> - CPU Coolers: Likely Noctua DH-14s
> - CPUs: High core/High Thread Intel Xeons
> - HBA Card(s): TBD
> - RAM: As much as the board can hold
> - PSU: ~600-800W
> - GPU: ?
> - Upgraded 10Gbe NIC card
> - Fan upgrades: ?

# Thinking about the build
This is the most important part of the entire process. Weight out your budget and your goals. Do you want a VM or Virtualization server, A low power backup server, a 4K media streaming powerhouse, or a docker container paradise? Do you want a rack mounted server or a tower server? Do you want to use an unused laptop? **Use what you have and upgrade, when, and if, you are able**. Start with something simple like I did, with a Raspberry Pi, a freebie from the E-Waste recycler in your area, a cheapie you found on eBay or from the pile at work the IT Guy was giving away.

When I started buying equipment, I started cheap and eased my way into the larger, overspecced server that I have now. My original plan was to only buy 2 Raspberry Pi 4's to run a network ad-blocker using a utility called [Pi-Hole](https://pi-hole.net). I wanted 2 of them for each side of my house, sometimes when one goes down for an update or a botched upgrade, it's nice to have a backup ready to continue feet away. I quickly ran into memory issues and had to plan my next move.

Next, I switched to purchasing a couple of [mini or small form factor](https://www.ebay.com/itm/295020790427?_skw=mini+home+server+pc&itmmeta=01K75PJSMDGD23RVNHQF8EE7A7&hash=item44b09c029b:g:NSsAAeSwFWZo2Egz&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1dGJ6FRavo2V%2FUWe8e1pDzSiLutTQ4eI7LyAT%2FcmdT1wT7ev5SJt%2BNlNUAYPu3xbYh7gBeRUCpd30%2F59S%2BV6JZYLOHbYsDnEi0bmKlD6B4v08yfG6SwXPd1eYOUx0egPmwhZwuKTYHv41Wt1EEX20f355UcDKiMY17ioJ6V%2BJlf5zaeWSYlmGXPHRzn36%2FyI5FZbGeabFv4ys9pV5JXbZRHYmLLQtRsF8%2FLMnw7SLtEMlh%2B5hpmrZiRrnXyX1CpvGo%3D%7Ctkp%3ABlBMULCay7a5Zg) PC's. This started the main hobby that's become my homelab today. I ran so many docker containers on those little guys, I had an absolute blast learning how to configure Docker Compose files to bring up an entire stack of applications that I used often up with one command and little wait time. Eventually, I needed more horsepower and came across my current build, case and all minus drives for ~$600 USD.

Recently, I found a marketplace post nearby, in which the poster was giving away their homelab. It was 3 nice yet older Dell rack mountable servers, fully upgraded, with some Cisco switches. Even though I could never get in touch with the poster, I began contemplating building another machine for my personal homelab. I've chosen to document that journey here. Further on in the guide, I will be posting links, resources, and buying tips as I spec out and build the new machine.

I mostly follow the guides from [ServerBuilds.net](https://serverbuilds.net), they specifically recommend older equipment which still has lots of life remaining but that was retired on a planned decommission program by their respective owners. As I'm building a new big rig for the homelab, it felt more honest to just buy what I always wanted, instead of what I needed. Especially considering the parts are rather reasonable at auction and if you look hard enough.

# Needs
Here are some "must-have's" that every build requires. We'll break them down into individual sections. Primary concerns not listed below are networking, do you have enough switches and ethernet drops? Do you have power to the area you want to keep the home lab and all the various bits and pieces? They're going to run hot, in some parts of the world air conditioning is not an affordable luxury, consistent and reliable power and internet are not always guaranteed. The access to parts, HVAC, power, and networking are a standard in the US and I apologize for not including these statements earlier. Not only do servers and switches run hot, but they can run noisy when fully engaged. Keep that in mind for the location of the server and equipment too. Will this drive my significant other crazy? Valid concern for sure!

# Cases
Not only do you want something with good airflow as mechanical hard drives like to be cool but you want plenty of room for as many as you can afford. This is why I went with the Rosewill case. It had space for 7x mechanical hard drives. You also want to consider if you need a server rack or cabinet. I have a 42u metal server rack which lives in the closet and holds my current 4u build with plenty of room to spare. Other options include the following: Do you want a tower server, this is basically a larger version of the standard PC case. You also have the choice of a rackmountable server. Which flows into another potential need...A server rack of some sort.

Here are some recommendations from the Serverbuilds.net community, unfortunately, many are no longer available at a *reasonable* price:

## Tower Cases

| Brand          | Case + Link                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Fits          | 3.5" Drive Space | Estimated Price     |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- | ---------------- | ------------------- |
| Cooler Master  | [N400](https://www.amazon.com/Cooler-Master-NSE-400-KKN2-Mid-Tower-Computer/dp/B00DKXXBU0/ref=sr_1_1?crid=T558P08O05CX&dib=eyJ2IjoiMSJ9.IWoSVkAv4yXjYWHKL9FjNpd32vDUAYZSYR121NU6WVZ9FFgkPVR5tkX8RvO5Nct0SI3TDJ6JGWGv-f3NiK5bd9Q6kliUkMcFjqMeDlVKbboiV8J8SQB_giCOJlCAOHXlcERp0wktngVVMGX0avWdCEKTMaAsOam7g3IlRjiU0kqUh1X9FdlI8Nv8wotKzpMucq3L08BRiaM4fwrVt91Km8gTBzhzOyvMK3_LXrFzzlw.VnuXG8idlsyLXybsWjrAVRGzXjMJ6NwnkLYBfDi_ILE&dib_tag=se&keywords=Cooler%2BMaster%2Bn400&qid=1760238420&sprefix=cooler%2Bmaster%2Bn400%2Caps%2C152&sr=8-1&th=1) | <= ATX        | 8 + 2            | ~$75                |
| Cooler Master  | Elite 350 + PSU                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <= ATX        | 6 + 4            | ~$90 - Unavailable  |
| Fractal Design | Node 804                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | <= mATX       | 10               | ~$115 - Unavailable |
| Fractal Design | Node 304                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Mini-ITX      | 6                | ~$90 - Unavailable  |
| Silverstone    | DS380B                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | DTX, Mini-ITX | 8 hotswap        | ~$200 -             |
*Note:* I'm researching more readily available options
## Rackmount Chassis

| Brand    | Case + Link | Fits | 3.5" Drive Space | Estimated Price |
| -------- | ----------- | ---- | ---------------- | --------------- |
| Rosewill | RSV-L4500   | All  | 15               | ~$150           |
| Rosewill | RSV-R4100   | All  | 6 + 2            | ~$110           |
| Rosewill | RSV-R4000   | All  | 8 + 3            | ~$100           |
| Rosewill | RSV-L4000   | All  | 8 + 3            | ~$120           |
| Rosewill | RSV-L4412   | All  | 12 Hotswap       | ~$275           |
*Note:* More research required
# Motherboards
Motherboards are usually pretty reasonably priced. You want one with enough SATA ports AND with enough expansion slots for your needs. As I already have a case, I'm pretty locked in with options. Spend money here and on a quality PSU.

Bear in mind also, that server motherboards are often larger and use a different form factor than desktop builds in the majority of situations. Consider this carefully when purchasing.

## Consumer Motherboards

| Brand - Specs | Model + Link                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | CPU           | RAM                | Form Factor | PCIe | SATA            | NIC | IPMI | Other  | Estimated Price |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- | ------------------ | ----------- | ---- | --------------- | --- | ---- | ------ | --------------- |
| ASRock        | [B85 PRO4](https://www.amazon.com/ASRock-CrossFireX-Motherboard-B85M-PRO4/dp/B00D3IKM1S/ref=sr_1_1?crid=2JLV7118BGGXY&dib=eyJ2IjoiMSJ9.zbWyijYd1jVL_FdplrfA2n8-JJVX5rC_azKGYf2ElAWrQc1NPfKnA9mEJ7fhFnWX_RIYCRg69rwcYN_8wzjsxPoBGaEzfNV7K9OmJLOFOtguGjUAiaMxLL9MTJEJzgtFz00l52SzBXY450oTCsF80RPTqzqplNDpw7SCH5vcbFXj-muKTMyR659jtpfOn6mZ4oPbyHpQFfzt5Cie6oW2Q1sqMthrG8OOdTzvpZV1KynvukLEDqU9Mf0L0TaiTshRvA-7RfA4HoCYQx-emKk26NvDHPUsTbqTYqPsNGc45zg.CEjcNwCWK-9cJ8OIQld1fdBZEbXe3KH9rZnjTaAlOP0&dib_tag=se&keywords=B85+PRO4&qid=1760666500&s=electronics&sprefix=b85+pro4%2Celectronics%2C97&sr=1-1) | Core i3/i5/i7 | Non-ECC UDIMM ONLY | ATX         | 4    | 2xSATA3 4xSATA4 | 1   | No   | -      | ~$55            |
| ASUS          | [H81I-PLUS](https://www.amazon.com/ASUS-H81I-PLUS-Mini-ITX-DDR3-Motherboards/dp/B00ESETQN6/ref=sr_1_1?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Core i3/i5/i7 | Non-ECC UDIMM ONLY | Mini-ITX    | 1    | 2xSATA3         | 1   | No   | -      | ~$45            |
| ASUS          | [H81M-A](https://www.amazon.com/ASUS-H81I-PLUS-Mini-ITX-DDR3-Motherboards/dp/B00ESETQN6/ref=sr_1_1?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Core i3/i5/i7 | Non-ECC UDIMM ONLY | Micro-ATX   | 3    | 2xSATA3 2xSATA3 | 1   | No   | -      | ~$35            |
| ASUS          | [H81M-C](https://www.amazon.com/Asus-FBA_90MB0GT0-M0EAY0-H81M-C-Mainboard-Mikro-ATX/dp/B00EL42XNI/ref=sr_1_1?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Core i3/i5/i7 | Non-ECC UDIMM ONLY | Micro-ATX   | 3    | 2xSATA3 2xSATA3 | 1   | No   | 1x PCI | ~$50            |
| Gigabyte      | [GA-H81M-S1](https://www.amazon.com/Motherboard-GIGABYTE-GA-H81M-S1-Intel-Shield/dp/B0F83FK5G7/ref=sr_1_1?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Core i3/i5/i7 | Non-ECC UDIMM ONLY | Micro-ATX   | 3    | -               | 1   | No   | -      | ~$50            |

## Server/Workstation Motherboards


| Brand + Specs | Model + Link                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | CPU                 | RAM                    | Form Factor | PCIe | SATA            | NIC | IPMI | Other    | Estimated Price |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ---------------------- | ----------- | ---- | --------------- | --- | ---- | -------- | --------------- |
| Supermicro    | [X10SLM-F](https://www.amazon.com/SUPERMICRO-X10SLM-F-motherboard-LGA1150-MBD-X10SLM-F/dp/B0DG6CDZPC/ref=sr_1_2?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                  | Xeon, Core i3       | ECC UDIMM ONLY         | Micro-ATX   | 3    | 4xSATA3 2xSATA2 | 2   | Yes  | SATA DOM | ~$90            |
| Supermicro    | [X10SLM±F](https://www.amazon.com/SUPERMICRO-X10SLM-F-motherboard-LGA1150-MBD-X10SLM-F/dp/B0DG6CDZPC/ref=sr_1_2?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                  | Xeon, Core i3/i5/i7 | ECC UDIMM ONLY         | Micro-ATX   | 3    | 4xSATA3 2xSATA2 | 2   | Yes  | SATA DOM | ~$100           |
| Supermicro    | [X10SLM±LN4F](https://www.amazon.com/Supermicro-Micro-Motherboards-X10SLM-LN4F/dp/B00D65IE2M/ref=sr_1_2?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                          | Xeon, Core i3       | ECC UDIMM ONLY         | Micro-ATX   | 2    | 4xSATA3 2xSATA2 | 2   | Yes  | SATA DOM | ~$130           |
| Supermicro    | [X10SLV](https://www.amazon.com/Supermicro-Motherboard-Mini-DDR3-X10SLV-Q/dp/B00GIZHLD0/ref=sr_1_1?s=electronics)                                                                                                                                                                                                                                                                                                                                                                                               | Core i3/i5/i7       | Non-ECC UDIMM ONLY     | Mini-ITX    | 1    | 2xSATA3 2xSATA2 | 2   | No   | SODIMM   | ~$120           |
| ASRock Rack   | [C226M WS](https://www.ebay.com/itm/297577662504?_skw=Asrock+rack+C226M+WS&itmmeta=01K7QZB9QX4J7N40W2G5QYCV2P&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1dVC4occFqYuDq5QoszIp5uLEFTVG6dejWNYBzwS8OrM9XQ3fYQ2LvSBrKeH9ROZ%2BlZ79gY1ZY94w4rYsRWseW1kxkQbs8aMhbVlSuAtWsvH%2BCUOi65XY21c2fpNNwc6oUM8BFE6E72F5B9%2BaiVZfjzdckh2zEY9eSM9VXxdb0Aw3c1ZCKSpR8Lmg4hQP73pETbEIfRo7BsSyq2P%2FZH4czk--QzbreI4UFjQGw9DyBin2Yg2v7IrxDbvQKPZ6xcEPe3WY%2B11ex3rvoepVpQyYNj2IU4y1oQIZhNB%2FMoNSpuGA%3D%3D%7Ctkp%3ABk9SR5qcrf-9Zg) | Xeon, Core i3/i5/i7 | ECC/Non-ECC UDIMM ONLY | Micro-ATX   | 3    | 6xSATA3         | 2   | No   | --       | ~$170           |

# Hard Drives
One could in theory go all the way with SSD's but that's rather expensive and low density of storage unless you've got a credit card with plenty of room. I suggest buying Western Digital external drives and "shucking" them like an oyster to get at the drive inside. It sounds complicated and definitely voids the warranty but anyone with a screwdriver can handle this task. I'll go into more detail on the shucking process in a later edition of the guide.

Here you have a handful of choices, Shuck-able mechanical drives of the SATA variety, SAS drives, and SSD drives. Note the motherboard models I've outlined don't include any M.2 slots so you'll need to choose 2.5" SSDs if you want to go that route. 
# CPU Coolers
Intel Xeon's, at least in the choice I'm making can run rather hot stuck in a case surrounded by mechanical drives. You want to increase the air flow as much as possible. Depending on the configuration of the motherboard and what you want to spend, a single CPU model may make more sense than the raw power of a dual CPU build. These are typically available for pretty reasonable on eBay and Amazon.

# CPU(s)
CPU's are relatively cheap. Most consumer models (i3/i5/i7) have an integrated GPU on the chip itself, this saves one needing a graphics card and consuming extra power. It would also occupy a PCIe slot better used to maximize storage density for a NAS or backup server. Though for some builds, like a streaming server, it's probably best to have one that's supported by the OS for transcoding. Note that the newer builds of TrueNAS, Gold Eye and beyond, appear to remove support for older GPU's specific to this purpose. Below are a handful of CPU options, match it to your board and fit it to your wallet.

| Model                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Code  | Cores + Threads | Max Freq. | TDP | Passmark Score | Price (est.) |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----- | --------------- | --------- | --- | -------------- | ------------ |
| [Pentium G3240](https://www.ebay.com/itm/353902720272?_skw=Pentium+G3240+CPU&epid=622485440&itmmeta=01K7QZTBKXAMZR72RPH1H9QCC5&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1eDX2XzpKzqZ34%2FHGfmtryX69EJRRrEdLZawEAeH7K0UP3tekIM%2B5l%2BfMXbtlOtUEpz66rblzGC2Hr2YuGFvRzfZNezONttEqnwipT1g0%2BR0BNhGzQGilRWs7UNyb7RFscA58X8U6OZQBv6wiyRI71lntGyTcalw4jNC1TklWHq4XbGId56i5ounxDi36nlaAkwtEb0lAfJ8jVUTsQv3ljRufpiLyllVsRznvAOGdq1D3QgJM%2BVxmwJdkeVdawfKTnMn%2BIdgCoIyaTfRKY46qRgkh0soANW9RYbhAOWLIBwSw%3D%3D%7Ctkp%3ABk9SR4y66f-9Zg)           | SR1NB | 2C/2T           | 3.2GHz    | 53  | 3251           | ~$10         |
| [i3-4330TE](https://www.ebay.com/itm/205760435883?_skw=i3-4330TE&epid=8026995534&itmmeta=01K7QZV5V8H67S8PPMT23QQ2RW&itmprp=enc%3AAQAKAAAAwFkggFvd1GGDu0w3yXCmi1eqy%2Bp2q16IgH57lWvAIuNC6%2B%2F56t%2F5ifckLKtsp%2Fw6Wyu6mbZsAXT6ohtDqSVSKah3JEESFDl7ppp%2F96OpW%2F%2Bg8GK0%2BOBV5NGEQ5I2f3Xnz0ie40ecjxwOzyzxJ5HjQ%2B7XJReMC25hhE%2Fg5fyWcMvpE9AyvWr1MuUmNGLOz4gtMQWKEcLTHrnyUHeYHjnrvsF6WqBTJjR1KTnUSiuM4ygciewsfEeuzdoo6%2F2296aGmw%3D%3D%7Ctkp%3ABk9SR-Ld7P-9Zg)                                                                          | SR180 | 2C/4T           | 2.4GHz    | 34  | 3680           | ~$25         |
| [i7-5775C](https://www.ebay.com/itm/317054213805?_skw=i7-5775C&itmmeta=01K7QZWH9FKGPQRG3Y9WMWCYRP&itmprp=enc%3AAQAKAAAA4FkggFvd1GGDu0w3yXCmi1fGOa8kWjnhB9pB8iuQNK6FRRc0dkn9CR3W7pKzQyIgjkW98TFYQk8XvlO70KQHmXBP3UVJjSbOhNb6qYwg58dWwvJkfM0KrjAnzLMoBY%2BvcEeRQGMuBYRiVNSv53S7Xud9yrq4JGNyKSNS%2FolwOwy4aziwuFMIZiM12eNwYfUdJmNrXAz%2FyyynkvHD77Zpz7u2jEtEssRURQGV7I9vWdhzDO54J5aG3XKbRpGV3Ep9em5RWPnkqxomtN%2F4WlUY1JGGUppk%2BWWJrZHJ6Ju%2BJzjk%7Ctkp%3ABFBM9pTy_71m)                                                                      | SR2AG | 4C/8T           | 3.7GHz    | 65  | 10837          | ~$100        |
| [Xeon E3-1225 V3](https://www.ebay.com/itm/177424739358?_skw=Xeon+E3-1225+V3+CPU&epid=22029759027&itmmeta=01K7QZY3C212Y6ZGW62ZSSH7RG&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1ck6DZwRhZtnwn1jlfKTYEak1nGv%2B5orxdWhXqTIUxPdaztiTUDnYpMa0sV%2BYXlGYq18izGSr6fEPclzu%2FLYIJs0NwqMUp8KxtxL79qRMJrSKWVqvKWeBeWTCHRJNMcjPQw%2BBspX%2FcOI9KZU6oe6YhSMFtUQejf7DFRzL2NufMwmTTZVuXfkRw9JVi9YoQ4NGvlTuaU8r6A%2BI2Wnesy3Iek7ptLvTqUsrCSAgF0smTZqqbFNgwBt5r9VYrZ%2Fgj6Ntc%3D%7Ctkp%3ABk9SR5i2-P-9Zg)                                                 | SR1KX | 4C/4T           | 3.6GHz    | 84  | 7238           | ~$45         |
| [Xeon E3-1230L V3](https://www.ebay.com/itm/257040169487?_skw=Xeon+E3-1230L+V3+CPU&epid=165799373&itmmeta=01K7QZZA08FVN3GZ9BHT254RCK&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1e4BL82HwH3rvCiinLXPMnHPkNWGIFGfhSZJjhM1MrGZRm5v367AgG5mv9TqfyaxuPmtmx7oYRGyMNaegH%2Bv1sXEbS7gb63BB0%2BbMdus6tz%2B1zrUhqDpJjyzqBh20WY30ldFdx1l2yh%2FEY3nUDi4YotHo45pFGvSD87U5mjYmj%2FfDomhzPCqA7JmD6IMRI6kxN4SKnPpWSPKZ%2FeXEr09rUrS6AVSkibTDUmTZ2A6J%2B18CDpdrhbHsegnbLV9B1o4549cM89YLDAD8GPGfMtWPYyyh%2BbLZY42cB9mNiOUm59BQ%3D%3D%7Ctkp%3ABk9SR6ag_f-9Zg) | SR158 | 4C/8T           | 2.8GHz    | 25  | 7231           | ~$65         |
| [Xeon E3-1280 V3](https://www.ebay.com/itm/236376768496?_skw=Xeon+E3-1280+V3+CPU&itmmeta=01K7R00F3VCX7NC6A0GTX61EMT&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1elxz0dkGvhG47D3O7NTBXvn%2FtRNL6QsLlIiBFwI72PYVr9uyVWdh8qcJJ2YfmzJYnXMJ0c9lbSiLcOvsA9wx9u3ItKTqyjLCP4JitCbQOxHA8v2VGtEMwbCiZ5jICsZuwEYhf4lDgXDroZxrwDD%2BqZDhm872etQRMuLnF4n6paY2rooeDgnJ3AqKTELl2bmKcesZZsCq3Ma1p4o54BtofKRbgUOh7dAQc6pXz3v9txUrtiYOZpF3s3z7uRoYH1Y2c%3D%7Ctkp%3ABk9SR4jygYC-Zg)                                                                            | SR150 | 4C/8T           | 4.0GHz    | 82  | 9841           | ~$100        |

# HBA Card(s)
These cards are great for adding more drive density to a machine, provided it can hold the drives in the first place. HBA stands for Host Bus Adapter card and they're usually flashed into IT mode and pretty widely available.

# RAM
This is where it can get expensive, depending on the proposed workload, and compatibility with your motherboard...It might be costly. My motherboard seems to only take DDR4 and RAM prices seem to rollercoaster up then down fairly regularly for no real reason.

# Power Supply - PSU
Get a quality power supply, full stop. There are many different efficiency ratings but overall, remember this isn't meant to truly be a gaming or mining build. You want enough juice flowing as efficiently as possible, while maintaining that you want a stable supply of power to your drives all the time. Cheap power supplies are known to surge sometimes and fry whatever is connected to them. That's a bad thing when you're spending this amount of money and have important data to protect.

# Graphics Card - GPU
Remember, we aren't going for a gaming build so we don't require a Nvidia GTX 5090 card or anything like that. We can get a purpose built card that will do a hardware conversion on media, known as transcoding. As this can be necessary since the CPUs may not have onboard video, you can remove it after you're done configuring the system. Proxmox, my chosen OS, allows one to setup headless or without a monitor through a web GUI.

# Network Interface Card - NIC
For my builds, I am typically transferring files that are large back and forth. Think media that's streaming to my smart home devices or mainly ISO's. I love trying out new flavors of Linux but I've been known to build labs like [[GOAD]] (link is to the notes portion of the site), an intentionally vulnerable Active Directory lab based on the ever popular Game of Thrones series. You'll want the fastest transfer speed you can get and one can find 1 or dual slot 10Gbe (instead of 1Gbe) cards for very reasonable. It helps to have a network switch which supports this otherwise, there's no point. To be clear, you can get by with onboard NICs in the majority of cases but note it could impact your workflow. Time is money as is often said!

# OS
This is an important consideration also. With TrueNAS and zfs, in general, you need all disks to be the same size or the array chooses the lowest TB storage and applies that capacity to all drives. Note that's an oversimplification but it's very close to the truth.

I am choosing [Proxmox](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso) for the build this time. It's always been a bit of a mystery to me and I finally have the ability to learn it by tinkering with it in the comfort of my home. If I tried this all in a cloud lab, I'm looking at hundreds of dollars per month for all the VMs and storage and such I tinker with on a daily basis.

# Firewall - OPNsense, PFsense, and Protecli Firewalls
__Coming soon__

# Network Switches
__Coming Soon__

# Wireless Access Points
__Coming Soon__

# Miscellaneous Supplies
You'll want to have some thermal paste on hand and it doesn't hurt to have some spare ethernet patch cables and SATA cables. I've known SATA cables to go bad and completely confuse my troubleshooting efforts. I have thought whole drives were bad before and it was a simple SATA cable change that fixed me right up. 

Again, this isn't a gaming build so some artic 5 silver thermal paste is probably good. One shouldn't need something fancy like liquid metal. You'll only need a healthy dot, like the size of a BB for installing the CPU Cooler(s).

# Additional Considerations
A homelab doesn't have to be just a box with some drives in it. Once you have your goals in mind, you can expand or shrink your expectations from there. Some add in gear like network switches for learning Cisco's IOS or a firewall is a good way to start setting up advanced projects you can add to your resume or CV. It comes across really well that you're a self-starter and motivated towards continuous learning.
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

Next, I will purchase a Power Supply Unit or PSU, for short. I will need DDR4 for RAM, hard drives for storage, 2x CPU's and likely a very cheap video card for hardware transcoding. I am rather annoyed that there are three expansion slots, as I would have liked to have space for a 10GBe Network Interface Card or NIC, for short.