# Two deployed AP-315 units: bridge-only IoT access points

Both tested APs run 2.4 GHz OpenWrt Wi-Fi on a common IoT SSID, with upstream router and managed switches handling segmentation.

## Tested topology

- Management: Ethernet `eth0` bridged into `br-lan`; DHCP on native/untagged **VLAN 1**.
- IoT: tagged Ethernet subinterface `eth0.20` bridged to `br-iot`; OpenWrt `iot` interface is unmanaged (no AP-side DHCP, NAT, or routing).
- Wireless: 2.4 GHz SSID attached only to `iot`, WPA2-PSK/AES, US regulatory domain, channel 1, HT20; no password published.
- The working Linux `eth0` interface is backed by **physical GMAC3**.

The first AP's transmit power was reduced to **20 dBm** after it attracted distant clients. Do not assume that is an appropriate RF value for another site.

## A four-day lesson in PVIDs

One switch port had previously been configured with **PVID 20** for a different AP. When the second Aruba was plugged into that port, its untagged management DHCP landed on the IoT VLAN, yielding an unexpected IoT-subnet management address. The router was not at fault.

Moving the Aruba to a properly configured trunk (**native/PVID 1; tagged VLAN 20**) fixed management and client separation immediately.

## Verify rather than assume

- The AP management address comes from the VLAN1 DHCP scope.
- A wireless IoT client receives a VLAN20 address from the upstream DHCP server.
- The AP has no separate NAT/DHCP service for IoT.
- Two APs advertising the same SSID have **distinct BSSIDs**. A synthetic-looking BSSID was observed earlier in development; verify after final installation.
- Client roaming works on the same SSID/security; it is **not** a controller-managed mesh.

The QCA9984 5 GHz radio is unresolved due to ath10k firmware/board-data initialization, not proven physically dead.
