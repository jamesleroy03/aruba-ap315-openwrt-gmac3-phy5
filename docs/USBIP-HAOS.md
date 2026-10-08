# USB/IP: Aruba-hosted Bluetooth dongle to Home Assistant OS

**Tested:** A SENA UD100 Bluetooth USB dongle physically connected to the second AP-315/OpenWrt unit was exported over Ethernet and attached to a separate Home Assistant Yellow HAOS host. USB ID: `0a12:0001`. Home Assistant's Bluetooth integration detected the adapter and BLE devices.

## OpenWrt AP (USB/IP server)

The tested AP runs `usbipd --ipv4 --ipv6` and binds USB bus ID `1-1`. Its `/etc/rc.local` includes:

```sh
/usr/sbin/usbip bind -b 1-1 >/tmp/usbip-sena-bind.log 2>&1 || true
exit 0
```

The USB/IP daemon must also be installed and started; binding alone does not start it. Confirm the real bus ID with `usbip list -l` and confirm export from the client with `usbip list -r <AP-address>`. USB IDs and bus IDs can change between devices.

## HAOS host (USB/IP client)

The tested HAOS host has a persistent `/etc/modules-load.d/usbip-sena.conf` containing:

```text
vhci-hcd
```

Its `/etc/modprobe.d/usbip-sena.conf` contains an `install vhci-hcd` hook that loads the kernel module and starts a systemd watcher. The watcher checks `usbip port` every 10 seconds and attempts `usbip attach -r <AP-address> -b 1-1` if USB ID `0a12:0001` is not present. It uses `systemd-run` with restart enabled.

This is **HAOS host-level customization**, not an ordinary Home Assistant Core integration or supported add-on. It may need revalidation after HAOS updates. Do not paste another site's addresses into the watcher.

## Validation

1. On OpenWrt, verify `usbipd`, USB device and export.
2. On HAOS host, verify `lsmod` contains `vhci_hcd` and `usbip_core`.
3. Check `usbip port` and `lsusb` for `0a12:0001`.
4. Confirm Home Assistant Bluetooth sees the adapter and receives BLE data.
5. Test recovery after restarting the AP and separately after rebooting the HAOS host.

**Test status:** AP reboot recovery succeeded. **HAOS reboot recovery has not yet been tested.** Retain existing Bluetooth proxy coverage until persistence and range are confirmed.

## Security note

USB/IP generally lacks strong transport authentication/encryption. Use it only on a trusted/isolated network, restrict exposure, and do not forward its service to the public internet.
