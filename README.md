# Raspberry Pi 4 based home router

This repo describes how to set up RPi 4 as a wireless and wired home router. In my case it works with an FTTH connection from Deutsche Telekom that provides Internet and IP telephony.

## Why?

The router from my previous ISP was overly expensive to keep, the one I wanted to have was not yet on sale, and I had a Pi 4 lying around. The improvised basic setup appeared to be fast and stable. So I decided to stay with RPi as the home router.

I then extended it with a managed switch as I needed wired LAN, and configured a number of software features. There is no extra network adapter - it is one-armed router on what concerns the wires.

I decided to stick to vanilla Raspbian, as OpenWRT for RPi seems not to be mature yet.

## Current state

### Internet access performance

The connection is throtteld by the ISP to 275/60 Mb/s (down/up).

- 265/60 Mb/s directly from the RPi
- 260/60 Mb/s for the clients connected over Ethernet
- 90/60 Mb/s for 5GHz WiFi clients (there is a 100 Mb/s limit of the RPi 4 SDIO interface)

### Availability
- working for months without a reboot

### Security
- SSH with public key only login for external access
- IKEv2 VPN
- no restrictive firewall yet

## Hardware
- Raspberry Pi 4 model B with 2 GB RAM with the official power supply
- Netgear GS308E managed switch
- fiber modem provided by Telekom.de
- some Cat 5e patch cables

## Things done
On top of the vanilla Raspbian distribution, in the order of implementation.
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
- DONE prevent allocation of auto private IPv4 and IPv6 addresses to stub interfaces -> cleaner routing table
- MAYBE enable IPv6 networking (does not bring a lot currently)
- make managed switch get (or have) IP address in LAN subnet

### Security
- dnsmasq opens port 53 on the WAN side (but not listens to it) - seems it has to stay like this 
- tighten down iptables rules to drop all but necessary stuff from WAN

### WLAN 
- Low 802.11ac throughput on a Macbook Air: only about 50/50 Mb/s which is far below the SDIO 100 Mb/s limit. iOS and Win10 fine, reaching the limit of 90/60 Mb/s.
- WPA3 (SAE) or WPA2-PMF do not work for iOS and mac OS clients. They work for Win10, but throughput is somewhat lower compared to WPA2.

### Telephony
- softphone directly on RPi, or find a softpone iOS/Android app that keeps online status while in background (Linphone does not)
