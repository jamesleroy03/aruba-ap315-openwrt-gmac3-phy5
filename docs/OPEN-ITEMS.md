# Verification and contribution backlog

- [ ] Extract the **exact** GMAC3/PHY5 DTS diff from the working OpenWrt source tree, compare against clean source commit, remove unrelated changes, and test applying it.
- [ ] Publish the precise build configuration and package dependencies, after screening for secrets and private paths.
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
