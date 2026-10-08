# The GMAC2 / GMAC3 / PHY5 Ethernet breakthrough

## Original baseline

Historical [OpenWrt staging AP-315 support](https://git.openwrt.org/openwrt/staging/blocktrron/commit/?h=aruba-ap315&id=b3e1cf9bff8f927e80b8d51362e80e99d09cbd7b) used physical `&gmac2`, `phy-mode = "sgmii"`, `phy-handle = <&phy5>`, and `qcom,id = <1>`.

The physical AR8031/AR8033 PHY responds at MDIO address 5. A GPIO-bitbanged MDIO implementation used GPIO1 and GPIO0, with active-low PHY reset on GPIO67.

## Symptoms under the experimental modern build

- AR8031 negotiated 100 Mbps/full-duplex; PHY ID `0x004dd074`.
- Linux `37400000.ethernet` reported link up.
- TX packet/byte counters rose, but RX packets/bytes/errors stayed at zero.
- ARP resolution failed and pings failed.
- The bootloader could download multi-megabyte TFTP images on the same port.

Changing only `qcom,id` between 1 and 2, experimenting with lane tuning, and toggling a PHY PLL bit did not fix the data path. The problem was not simply the wrong Linux interface name or dead copper PHY.

## Working correction

The successful device-tree topology used:

- **Disable `&gmac2`**.
- **Enable `&gmac3`** at `37600000.ethernet`.
- Set `phy-mode = "sgmii"`.
- Set `phy-handle = <&phy5>`.
- Set `qcom,id = <3>`.
- Point `ethernet0` and `label-mac-device` to GMAC3, preserving factory MAC/nvmem handling.
- Retain GPIO MDIO and PHY5 reset configuration.

With that configuration, receive traffic immediately appeared; one test saw RX reach **3,548**, followed by successful bidirectional IP connectivity. The second physical AP-315 was converted successfully using the known-good build.

**This is a topology description, not a complete copy-paste DTS patch.** The actual source diff needs to be extracted from the working tree and reviewed before publication.

## Lesson

The kernel interface may be named `eth0` even when the underlying controller is physical GMAC3. Check the device tree, boot log, and controller address, not just `eth0`/`eth1`.

Avoid interpreting link-up alone as proof of a functioning SGMII datapath. Inspect RX counters and ARP/ping both directions.
