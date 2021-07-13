# rpi-home-router
Setting up a Raspberry Pi 4 based home router

## Current state

### Performance Internet access

### over wired network
- 265/60 Mb/s directly from RPI (hitting the line limit) 
- 220/60 Mb/s throughput from devices connected over Ethernet
- the cap set by the provider is 275/60

#### over 5 GHz WiFi
- 70-80 Mb/s iOS and Win10 clients
- 40-50 Mb/s mac OS
- the cap induced by the RPi 4 SDIO interface is 100 Mb/s

### Availability
- working for months without a reboot

### Security
- SSH with public key only login for external access
- no restrictive firewall

## Things done
1. Public key only SSH login
2. PPPOE connection to Telekom ISP (Magenta XL over fiber)
3. Routed AP [Raspberry Pi guide]
4. TCP MSS issue over PPPOE (clients could not open some web pages, in particular duckduckgo.com)
5. 802.11ac and 802.11n with in general adequate throughput
6. Wired LAN via managed switch with VLANs (bridging WiFi and Ethernet interfaces on RPi)
7. Selfhost.de DynDNS automatic updating
8. IKEv2 VPN server for roadwarriors
9. Fail2Ban for SSH
10. landline on a softphone client on laptop/smartphone (available anywhere in the world over VPN)

## Things to solve

### Network basic
- not more that 220 Mb/s routing performance over Ethernet. Reason: ksoftirqd kernel thread maxes out one CPU core. A kernel regression or a feature?
- configuration now a mix of /etc/network/interfaces and /etc/systemd/network/ scripts. Because bridge does not work when set up in /network/interfaces. Stick to one.
- routing table: pppoe default route setting - both with replace and witout replace have issues
- prevent allocation of auto private v4 IP to stub interfaces (they also pollute the routing table)
- enable IPv6 networking
- make managed switch to get (or have) IP address in LAN subnet

### Security
- dnsmasq listens on wan too
- limit exposure on WAN side by tighten down iptables rules

### Network extra
- run selfhost.de DynDNS updater script on pppoe  link set up and re-establishment

### WLAN:
 
- Low 802.11ac throughput on Macbook Air 2017. About 50Mb/s which is far below the SDIO 100 Mb/s limit. iOS and Win10 fine, reach 70-80 Mb/s. x
- WPA3 (SAE) or WPA2-PMF do not work with iOS and mac OS. They works with Win10, though throughput becomes noticeably slower compared to WPA2.

### Telephony:
- softphone directly on RPi, or find a softpone iOS/Android app that keeps online status while in background (linphone does not)
