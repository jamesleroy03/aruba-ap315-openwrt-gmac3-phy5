# Aruba AP-315 / APIN0315: OpenWrt GMAC3–PHY5 field notes

**Experimental community documentation — two physical AP-315 units successfully deployed (October 2026).** Not an official OpenWrt-supported installation guide. Flashing a bootloader can permanently brick a device.

## The useful discovery

The [historical AP-315 staging commit](https://git.openwrt.org/openwrt/staging/blocktrron/commit/?h=aruba-ap315&id=b3e1cf9bff8f927e80b8d51362e80e99d09cbd7b) described a physical `gmac2` → PHY5 SGMII path. On the tested units with a modern Linux/OpenWrt build, that configuration negotiated copper Ethernet at 100/full **but received zero Ethernet frames**. Linux TX counters increased; RX remained zero. APBoot TFTP worked, so the physical jack and PHY were not simply dead.

The breakthrough was enabling physical **GMAC3 (`37600000.ethernet`) with PHY5**, SGMII, and `qcom,id = <3>`, while disabling GMAC2. RX frames appeared (one test counted 3,548), bidirectional ping worked, and permanent OpenWrt installations succeeded on **two** AP-315 devices.

**Critical gotcha:** Linux called the working GMAC3-backed interface `eth0`. An `eth0` label does **not** mean physical GMAC2. Verify the device-tree/controller address.

## Tested result

- Hardware: Aruba IAP-315-US / APIN0315, Qualcomm IPQ806x; PHY5 AR8031/AR8033.
- OpenWrt: experimental SNAPSHOT `r0-f795077` / source `f795077`, custom device-tree/build.
- Both units: patched APBoot 1.5.7.2 and permanent OpenWrt; 2.4 GHz IoT Wi-Fi, 20 MHz channel, VLAN 20 tagged, VLAN 1 untagged management.
- Second unit also hosts a **SENA UD100 USB Bluetooth adapter exported over USB/IP** to a separate Home Assistant OS machine.
- The QCA9984 5 GHz radio was **not** made operational; ath10k firmware/board-data initialization remains unresolved.

## If Ethernet links up but RX stays at zero

**Start here:** [AP-315 Ethernet RX-zero troubleshooting and misleading generic fixes](docs/ETHERNET-RX-ZERO-TROUBLESHOOTING.md). This covers the exact symptoms, distinguishes AP-315/IPQ806x from AP-303/IPQ40xx, and explains why RGMII delay and incorrect PHY-reset suggestions are not the tested solution.

## Documentation

- [Exact working GMAC3/PHY5 DTS patch](patches/ap315-gmac3-phy5.patch)
- [Tested OpenWrt build configuration](build/openwrt.config)
- [Wi-Fi MAC hotplug helper](scripts/10_fix_aruba_ap32x_wifi_mac)
- [Ethernet debugging and DTS findings](docs/ETHERNET-GMAC3.md)
- [Safe reproduction workflow and flash checkpoints](docs/FLASHING.md)
- [Network and VLAN deployment](docs/NETWORK-VLAN.md)
- [USB/IP Bluetooth and Home Assistant OS](docs/USBIP-HAOS.md)
- [Firmware checksums and provenance](SHA256SUMS.md)
- [Unresolved work and verification gaps](docs/OPEN-ITEMS.md)

**Not included:** proprietary Aruba firmware/bootloader binaries, private network credentials, personal SSH keys, or unverified bootloader flashing commands. The exact working DTS diff has now been extracted and published; the original bootloader-flash transcript still needs independent verification. Do not infer destructive SPI commands from prose.

## Related upstream history

David Bauer / blocktrron's [2020 Aruba AP-315 support commit](https://git.openwrt.org/openwrt/staging/blocktrron/commit/?h=aruba-ap315&id=b3e1cf9bff8f927e80b8d51362e80e99d09cbd7b) was invaluable for PHY address, MDIO and hardware archaeology. The GMAC3 result reported here is a later field finding, **not** a claim that the original commit was universally incorrect.

Search terms: Aruba AP-315 OpenWrt no Ethernet RX; APIN0315; Glenfarclas; GMAC2 GMAC3; PHY5; AR8031; `37400000.ethernet`; `37600000.ethernet`.

This repository records a working experiment, not a promise that every AP-315 hardware revision behaves identically.
