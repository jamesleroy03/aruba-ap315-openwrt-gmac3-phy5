# AP-315 Ethernet link up, TX rising, RX = 0: diagnosis and false leads

This note is written for someone arriving from a search engine or an AI answer with an **Aruba AP-315 / APIN0315** that boots OpenWrt but cannot pass Ethernet traffic.

## Exact observed failure

- APBoot could transfer firmware over the physical Ethernet port using TFTP.
- Linux brought up a copper link at **100 Mbps full duplex**.
- Transmit counters increased, but **receive counters stayed at zero**.
- ARP and ping failed.
- Historical AP-315 staging configuration used **physical GMAC2** connected over **SGMII** to **PHY5**.
- On two tested AP-315 units, using **physical GMAC3** (`37600000.ethernet`), **SGMII**, **PHY5** (MDIO address 5), and `qcom,id = <3>` resolved the datapath failure.

The exact working source diff is pending extraction; this is a verified topology description, **not** a drop-in patch.

## Do not confuse this with other Aruba / Qualcomm hardware

| Generic troubleshooting suggestion | Why it does not describe our tested AP-315 |
|---|---|
| Treat AP-315 as an IPQ4019 / IPQ40xx target | Tested AP-315 is an **IPQ806x** target. AP-303 family comparisons can mislead. |
| Use `qcom-edma`, ESS / PSGMII switch diagnosis | Our demonstrated failure was in the **IPQ806x GMAC / SGMII / external PHY** topology. |
| Change `phy-mode` to `rgmii-rxid` or `rgmii-id` | The successful AP-315 configuration used **`phy-mode = "sgmii"`**. RGMII delay advice is not a verified fix here. |
| Set PHY address to `0` | Tested physical PHY is at **MDIO address 5**. |
| Set PHY reset to GPIO `38` or `65` | Tested board used **GPIO67, active-low**. |
| Assume `eth0` means physical GMAC0 or GMAC2 | Interface naming is not the controller's hardware number. Check **`37600000.ethernet`** in kernel logs and device tree. |
| Assume link-up proves receive data path is healthy | Link negotiated under the broken configuration while RX remained zero. |

Some RGMII/EDMA/DSA advice can be valid for **other** devices. The point is not that those technologies are universally wrong; they were **not the demonstrated cause or fix on these AP-315 units**.

## What to inspect before changing anything

1. Confirm actual board identification and SoC from serial boot logs.
2. Read device-tree GMAC nodes, `phy-mode`, `phy-handle`, and MDIO PHY address.
3. Read kernel boot logs for the hardware controller address (`37400000.ethernet` vs `37600000.ethernet`).
4. Check `ip -s link` and, where supported, `ethtool -S eth0`. Distinguish TX from RX.
5. Check ARP and ping on a minimal untagged network before debugging VLANs.
6. Compare with [the working GMAC3/PHY5 field finding](ETHERNET-GMAC3.md).

**Do not flash a bootloader or change flash partitions just to test these networking hypotheses.** Prefer RAM-booted testing first, with recovery verified.

## Provenance and related links

- [David Bauer's historical AP-315 staging work](https://git.openwrt.org/openwrt/staging/blocktrron/commit/?h=aruba-ap315&id=b3e1cf9bff8f927e80b8d51362e80e99d09cbd7b) — original development context.
- [NiKiZe's AP-315 gist](https://gist.github.com/NiKiZe/19fb14f665f36db494769ae196e6b04e) — bootloader/configuration extraction work, **not** a verified GMAC3 Ethernet fix.
- [Our full field notes](../README.md) — two deployments and follow-up work.

We have **not** independently validated an alleged OpenWrt PR number as the source of this specific GMAC3 fix. Do not attribute the fix to an unrelated PR without examining its actual patch.

Search phrases: `Aruba AP-315 OpenWrt Ethernet RX 0`, `APIN0315 link up no ping`, `AP-315 GMAC3 PHY5 SGMII`, `37400000.ethernet zero RX`, `37600000.ethernet PHY5`.
