# Raspberry Pi 4 based home router

This repo describes how to set up RPi 4 as a wireless and wired home router. In my case it works with a fiber optic line from Deutsche Telekom.

## Why

The router from my previous ISP was too expensive to keep, the one I wanted to have was not yet on sale, and I had a Pi 4 lying around. It appeared to be fast and very stable. So I decided to stay with it. I then extended it with a managed switch to have wired LAN.

## Current state

### Internet access performance

The connection is throtteld by the ISP to 275/60 Mb/s (down/up).

- 265/60 Mb/s directly from the RPi
- 260/60 Mb/s for the clients connected over ethernert
- 90/60 Mb/s for 5GHz WiFi clients (there is a 100 Mb/s limit of the RPi 4 SDIO interface)

### Availability
- working for months without a reboot

### Security
- SSH with public key only login for external access
- IKEv2 VPN
- no restrictive firewall yet

## Things done
On top of the vanilla Raspbian distribution:
1. Public key only SSH login
2. PPPOE connection to Telekom ISP
3. Routed AP
4. Resolved TCP MSS issue over PPPOE (clients could not open some web pages, in particular duckduckgo.com)
5. Adequate 5 GHz and 2.4 GHz WiFi throughput
6. Wired LAN via managed switch with VLANs (bridge with WLAN)
7. Selfhost.de DynDNS with automatic updating (upon ppp link establishment and periodically)
8. IKEv2 VPN server for roadwarrior scenario
9. Fail2Ban intrusion prevention for SSH
10. Landline on a softphone client on laptop/smartphone (also works over VPN connection)
11. Pruned unused services and network configuraiton
12. Building and running a 64-bit kernel definitely improves routing performance (from 100% to 50% ksoftirq thread CPU use at 250 Mb/s client download)

## Things to solve

### Network basic
- sometimes not more that 220 Mb/s routing performance over Ethernet. Reason: ksoftirqd kernel thread maxes out one CPU core. A kernel regression or a feature?
- simpify network setup:
  - kick out resolvconf.conf
  - do all setup via systemd-netowrkd; remaining challenge - ppp link setup
- DONE prevent allocation of auto private IPv4 and IPv6 addresses to stub interfaces
- MAYBE enable IPv6 networking (does not bring a lot currently)
- make managed switch get (or have) IP address in LAN subnet

### Security
- dnsmasq opens port 53 on the WAN side (but not listens to it) - seems it has to stay like this 
- tighten down iptables rules to drop all but necessary stuff from WAN

### WLAN 
- Low 802.11ac throughput on Macbook Air 2017: only about 50Mb/s which is far below the SDIO 100 Mb/s limit. iOS and Win10 fine, reach 80 Mb/s.
- WPA3 (SAE) or WPA2-PMF do not work for iOS and mac OS clients. They work for Win10, but throughput becomes somewhat slower compared to WPA2.

### Telephony
- softphone directly on RPi, or find a softpone iOS/Android app that keeps online status while in background (Linphone does not)
