# Verification and contribution backlog

- [x] Publish the extracted GMAC3/PHY5 DTS diff (`patches/ap315-gmac3-phy5.patch`; published October 8, 2026).
- [ ] Independently verify that the published DTS patch applies cleanly to the documented clean source revision and contains no unrelated changes; publication alone does not establish this test.
- [x] Publish the tested OpenWrt build configuration (`build/openwrt.config`; published October 8, 2026).
- [ ] Independently verify completeness of package dependencies and review the published configuration for private paths or secrets.
- [ ] Recover and independently verify the exact original APBoot SPI flash transcript. Until then, no copy-paste destructive bootloader instructions.
- [ ] Confirm the second unit's permanent firmware hash from the actual backup/device.
- [ ] Test HAOS reboot recovery for USB/IP and recheck after HAOS updates.
- [ ] Investigate QCA9984 5 GHz firmware/board-data initialization separately.
- [ ] Evaluate a suitable upstream OpenWrt issue/patch submission with logs from **both** tested units.
- [ ] Check licensing and attribution before sharing firmware binaries, bootloader artifacts, or code from historical staging trees.

## What has been demonstrated

Two physical Aruba AP-315 units were deployed with the GMAC3/PHY5 configuration and bridge-only IoT Wi-Fi. One exports a Bluetooth USB dongle over USB/IP to HAOS. These are field results, not proof of broad model-wide compatibility.

## Public disclosure boundaries

No Wi-Fi passwords, tokens, SSH keys, private device inventories, full configuration exports, or proprietary Aruba binaries. Use placeholders for private addresses. Cite original upstream authors and source history.
