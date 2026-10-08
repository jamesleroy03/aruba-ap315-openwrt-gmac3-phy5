# Conversion workflow and safety gates

**Warning:** Experimental AP-315 conversion. Bootloader writes are destructive and can brick the AP. This document intentionally does **not** publish unverified erase/write commands.

1. **Inventory the exact device.** Confirm APIN0315 board, MAC, bootloader, flash chip/partitions, console access, and current image compatibility. Back up original boot partitions if feasible.
2. **Connect serial.** CP2102 3.3 V TTL: cross TX/RX, connect ground, **never connect adapter power to the AP**. Original APBoot uses **9600 8N1**; patched APBoot uses **115200 8N1**. Use one program to own the serial port.
3. **Prepare TFTP and wired build host.** In the tested setup, TFTP served images over a wired connection. Patched APBoot TFTP did not work reliably via the host's Wi-Fi route. Verify actual IP addresses and hashes; don't copy private IPs from another deployment.
4. **Patch APBoot only after independent verification.** The two tested APs used patched APBoot **1.5.7.2**, build `d36bb727`. Check its SHA256 against [checksums](../SHA256SUMS.md). The exact SPI flash transcript is not yet publicly verified, so this guide deliberately stops short of a destructive bootloader command sequence.
5. **Switch serial to 115200 after patched bootloader reset.** Garbage at 9600 can simply mean the baud rate changed.
6. **RAM boot the verified OpenWrt initramfs** from the wired TFTP host. On the tested patched APBoot, the command format was `netget 44000000 <verified-initramfs-filename>`; set `serverip` to the real TFTP host. Verify that the image loads and boots.
7. **Prove wired data works before writing NAND.** Check controller address, PHY, RX/TX counters, bidirectional ping, ARP, and bridge configuration. Link-up alone is insufficient.
8. **Validate the intended sysupgrade image** with `sysupgrade -T <verified-image>` on the running compatible OpenWrt build. Check partitions, build, and hash. Only then consider a permanent sysupgrade, with a tested recovery path.
9. **Deploy bridge-only networking.** Keep management on native VLAN1, IoT clients on tagged VLAN20, with upstream router handling DHCP/routing.
10. **Verify after reboot:** SSH/LuCI, management VLAN, client VLAN, unique BSSID, radio, and any USB/IP services.

## Serial workflow

A persistent GNU `screen` session can let a human and a remote agent share one serial console. Example (adjust device):

```sh
screen -dmS aruba-console /dev/ttyUSB0 9600
screen -r aruba-console
```

After the verified patched bootloader is active:

```sh
screen -dmS aruba115200 /dev/ttyUSB0 115200
screen -r aruba115200
```

Never open the same UART in two separate terminal programs simultaneously.

## Historical dead ends

Modern Linux GMAC2 link-up with zero RX; switching logical qcom ID alone; changing lane1 analog register alone; forcing PHY PLL_ON; and rebuilding BusyBox with a custom configuration that accidentally produced a nonbooting image. The useful breakthrough was physical GMAC3 + PHY5.

**Still needed for a truly reproducible public flashing guide:** original verified APBoot erase/write transcript, exact DTS patch, and build configuration. Do not guess these steps.
