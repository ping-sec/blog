> [!NOTE]
> Please use the [[Contact]] form or use these options:
> - PING on the [TCM Security](https://discord.gg/tcm) and [HackSmarter](https://discord.gg/PhF23AnyJm) Discords
> - LinkedIn: https://www.linkedin.com/in/casey-campbell-a63255264
> - Bluesky: https://bsky.app/profile/ping-sec.bsky.social

The guide will now solely focus on NON-SBCs as it's the best value for the money going forward. No more recommendations of the Raspberry PI or similar Single Board Computing devices moving ahead.

Choose a link to see the hardware suggestions reproduced here, from my good friends at [ServerBuilds.net](https://serverbuilds.net)

- Cases
- Motherboards
- CPUs
- Hard Drives
- RAM
- Power Supply
- NIC
- GPU
- OSs
- Firewall Hardware - OPNsense/PFSense/Protectli
- Wireless Access Points (WAPs)
- Network Switches

### Cases
Tower Case Suggestions

| Brand          | Case + Link                                                                                                                 | Fits          | 3.5" Drive Space | Estimated Pricing |
| -------------- | --------------------------------------------------------------------------------------------------------------------------- | ------------- | ---------------- | ----------------- |
| Cooler Master  | [N400](https://www.ebay.com/sch/i.html?_nkw=Cooler+master+n400&_sacat=0)                                                    | <- ATX        | 8 + 2            | ~$75              |
| Cooler Master  | [Elite 350 + PSU](https://www.ebay.com/sch/i.html?_nkw=Cooler+master+Elite+350&_sacat=0&_odkw=Cooler+master+n400&_osacat=0) | <- ATX        | 6 + 4            | ~$90              |
| Fractal Design | [Node 804](https://www.ebay.com/sch/i.html?_nkw=fractal+design+node+804&_sacat=0&_odkw=Cooler+master+Elite+350&_osacat=0)   | <- mATX       | 10               | ~$115             |
| SilverStone    | [DS380B](https://www.ebay.com/sch/i.html?_nkw=Silverstone+DS380B&_sacat=0&_odkw=Silverstone+DS280B&_osacat=0)               | DTX, Mini-ITX | 8 (Hotswappable) | ~$200             |

Rackmount Chassis Suggestions

| Brand    | Case + Link                                                                                                                     | Fits | 3.5" Drive Space  | Estimated Pricing |
| -------- | ------------------------------------------------------------------------------------------------------------------------------- | ---- | ----------------- | ----------------- |
| Rosewill | [RSV-L4500](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-L4500&_sacat=0&_odkw=Silverstone+DS380B&_osacat=0)                | All  | 15                | ~$150             |
| Rosewill | [RSV-R4100](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-R4100&_sacat=0&_odkw=Rosewill+RSV-L4500&_osacat=0)                | All  | 6 + 2             | ~$110             |
| Rosewill | [RSV-R4000](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-R4100&_sacat=0&_odkw=Rosewill+RSV-L4500&_osacat=0)                | All  | 8 + 3             | ~$100             |
| Rosewill | [RSV-L4000](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-L4000&_sacat=0&_odkw=Rosewill+RSV-R4100&_osacat=0)                | All  | 8 + 3             | ~$120             |
| Rosewill | [RSV-L4412](https://www.ebay.com/sch/i.html?_nkw=Rosewill+RSV-L4412&_sacat=0&LH_TitleDesc=0&_odkw=Rosewill+RSV-L4000&_osacat=0) | All  | 12 (Hotswappable) | ~$275             |

## Motherboard Comparison Table

| Brand (Specs) | Model (Link) | CPU | RAM | Form Factor | PCIe | SATA | NIC | IPMI | Other | Est. Price |
|---|---|---|---|---|---|---|---|---|---|---|
| **ASUS** H310 Chipset | [PRIME H310M-C](https://ebay.us/BLdV13) | LGA1151 8th/9th Gen, up to 95W | 2x DDR4-2666, max 32GB, Non-ECC | Micro-ATX | 1x x16, 2x x1, 1x PCI (legacy) | 4x SATA3 | 1x Realtek RTL8111H GbE | ❌ | 1x M.2 NVMe (2280) | ~$50 🔵 |
| **Gigabyte** B360 Chipset | [B360M DS3H](https://ebay.us/qP0Off) | LGA1151 8th/9th Gen, up to 95W | 4x DDR4-2666, max 32–64GB, Non-ECC | Micro-ATX | 1x x16, 2x x1 | 6x SATA3 | 1x Realtek 8118 GbE | ❌ | 1x M.2 NVMe (2280), RGB | ~$60 🔵 |
| **ASRock** Z370 Chipset | [Z370 OEM ATX](https://ebay.us/coUqed) | LGA1151 8th Gen (Z370), up to 95W | 4x DDR4-4000+(OC), max 64GB, Non-ECC | ATX | 4x x16 physical (3x electrical x4), 2x x1 | 6x SATA3 | 1x Intel I219V GbE | ❌ | 2x M.2 NVMe (2280) | ~$65 🔵 |
| **ASRock** H310 Chipset | [H310CM Mini-DTX](https://ebay.us/2AyPKX) | LGA1151 8th/9th Gen, up to 95W | 2x DDR4-2666, max 32GB, Non-ECC | Mini-DTX (fits most Mini-ITX cases) | 1x x16, 1x x1 | 4x SATA3 | 1x Realtek RTL8111H GbE | ❌ | No M.2 | ~$45 🔵 |
| **ASRock** Z370 Chipset | [Z370M-ITX/ac](https://ebay.us/lObGsN) | LGA1151 8th Gen (Z370), up to 95W | 2x DDR4-4000+(OC), max 32GB, Non-ECC | Mini-ITX | 1x x16 | 6x SATA3 | 2x Intel GbE (I219V + I211AT), 802.11ac WiFi + BT 4.2 | ❌ | 1x M.2 NVMe (2280), Dual NIC, WiFi | ~$145 🔵 |
| **Supermicro** Q370 Chipset | [X11SCV-Q](https://ebay.us/v1tqrX) | LGA1151 8th Gen, up to 95W | 2x DDR4-2666, max 32GB SODIMM, Non-ECC | Mini-ITX | 1x x16, 2x x4 | 5x SATA3 (+1 SATADOM) | 2x Intel GbE (I210-AT + I219LM) | ❌ | 1x M.2 NVMe (2280), Dual NIC | ~$100 🔵 |
| **Supermicro** Q370 Chipset | [X11SCQ](https://ebay.us/D1exBf) | LGA1151 8th/9th Gen, up to 95W | 4x DDR4-2666, max 64GB UDIMM, Non-ECC | Micro-ATX | 1x x16, 2x x4 (open-ended), 1x x1 | 6x SATA3 (+1 SATADOM) | 2x Intel GbE (I210-AT + I219LM) | ❌ | 1x M.2 NVMe (2280/22110), Dual NIC | ~$75 🟢 |
| **Supermicro** C246 Chipset | [X11SCA-F](https://ebay.us/vLcc56) | LGA1151 8th
