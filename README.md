# rpi-home-router
Setting up a Raspberry Pi 4 based home router

## Current state

### Internet access performance

#### over wired network
- 265/60 Mb/s directly from RPI (hitting the line limit) 
- 220/60 Mb/s throughput from devices connected over Ethernet
- the cap set by the ISP is 275/60 Mb/s

#### over 5 GHz WiFi
- 70-80 Mb/s iOS and Win10 clients
- 40-50 Mb/s mac OS
- the cap induced by the RPi 4 SDIO interface is 100 Mb/s

### Availability
- working for months without a reboot

### Security
- SSH with public key only login for external access
- IKEv2 VPN
- no restrictive firewall

## Things done
1. Public key only SSH login
2. PPPOE connection to Telekom ISP (over fiber)
3. Routed AP
4. Resolved TCP MSS issue over PPPOE (clients could not open some web pages, in particular duckduckgo.com)
5. Adequate throughput for 802.11ac and 802.11n
6. Wired LAN via managed switch with VLANs (bridge with WLAN)
7. Selfhost.de DynDNS automatic updating
8. IKEv2 VPN server for roadwarrior scenario
9. Fail2Ban intrusion prevention for SSH
10. Landline on a softphone client on laptop/smartphone (also works over VPN connection)

## Things to solve

### Network basic
- not more that 220 Mb/s routing performance over Ethernet. Reason: ksoftirqd kernel thread maxes out one CPU core. A kernel regression or a feature?
- configuration now a mix of /etc/network/interfaces and /etc/systemd/network/ scripts. Because bridge does not work when set up in /network/interfaces. Stick to one.
- routing table: pppoe default route setting - both with replace and witout replace have potential issues
- prevent allocation of auto private IPv4 addresses to stub interfaces
- enable IPv6 networking
- make managed switch get (or have) IP address in LAN subnet

### Security
- dnsmasq listens and responds not only in LAN also on the WAN side
- limit exposure on WAN side by tighten down iptables rules

### Network extra
- run selfhost.de DynDNS updater script on pppoe  link set up and re-establishment

### WLAN 
- Low 802.11ac throughput on Macbook Air 2017. About 50Mb/s which is far below the SDIO 100 Mb/s limit. iOS and Win10 fine, reach 70-80 Mb/s.
- WPA3 (SAE) or WPA2-PMF do not work with iOS and mac OS. They works with Win10, though throughput becomes noticeably slower compared to WPA2.

### Telephony
- softphone directly on RPi, or find a softpone iOS/Android app that keeps online status while in background (linphone does not)
