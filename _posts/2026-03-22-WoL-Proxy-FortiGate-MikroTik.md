---
layout: single
title: "Jak udělat WoL Proxy v segmentované síti (FortiGate + MikroTik)"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


### 1. Fyzické zapojení a Switch
Kabel: Propoj libovolný port MikroTiku (např. ether1) s Trunk portem na tvém switchi.
Switch: Na tomto portu povol (Tag) všech 4–5 VLAN ID, které ve škole máš.

### 2. MikroTik: Rozhraní a IP
Vytvoř virtuální cesty do každé sítě, aby MikroTik v každé z nich „viděl“ a „slyšel“.
VLANs: Interfaces -> VLAN -> +. Vytvoř VLAN10, VLAN20 atd. (Interface: ether1).
Adresy: IP -> Addresses. Každé VLAN dej IP (např. 192.168.10.250/24), která bude sloužit jako WoL brána.

### 3. MikroTik: „Magický“ ARP a NAT (Klíč k úspěchu)
Tímto zajistíš, že paket nezmizí v prázdnu, ale rozletí se ke všem PC.
Statický ARP: V IP -> ARP vytvoř pro každou VLAN záznam:
IP: 192.168.10.254 (falešná broadcastová IP)
MAC: FF:FF:FF:FF:FF:FF
NAT (Injekce): V IP -> Firewall -> NAT nastav:
Chain: dstnat, Protocol: udp, Dst. Port: 7,9.
Action: dst-nat, To Addresses: 192.168.10.254.
(Tento krok zajistí, že cokoli přijde na MikroTik jako WoL, on přemění na celosíťový výkřik).

### 4. MikroTik: Bezpečnostní zámek (Zákaz routování)
Aby ti žáci nechodili přes MikroTik do učitelské sítě (protože MikroTik standardně vše propojuje).
Firewall: IP -> Firewall -> Filter Rules:
Chain: input, Src. Address: [TVOJE_ADMIN_IP], Action: accept (aby ses nevyhodil z administrace).
Chain: forward, Protocol: udp, Dst. Port: 7,9, Action: accept (povolení samotného WoL).
Chain: forward, Action: drop (KLÍČOVÉ: vypne routování mezi VLANami).
Chain: input, Action: drop (zakáže útoky přímo na MikroTik).

### 5. FortiGate: Propustka
FortiGate musí dovolit ESETu (nebo tobě), aby na ty „falešné“ IP adresy v MikroTiku mohl mluvit.
Policy: Admin_VLAN -> Cílová_VLAN.
Destination: Objekt s IP MikroTiku (např. 192.168.10.250).
Service: UDP 7 a 9.

### 6. Finální test (ESET Protect / WakeMeOnLan)
Při buzení už neposílej paket „naslepo“ na IP adresu daného PC (které je vypnuté a FortiGate ho nevidí), ale pošli ho na IP adresu MikroTiku v dané VLAN.
Cíl: 192.168.10.250 (IP MikroTiku v daném segmentu).
MAC: MAC adresa PC, které chceš zapnout.

## Výsledek: 
MikroTik paket zachytí, díky ARP ho „rozstřelí“ po celé VLANě a PC se probudí. FortiGate přitom zůstává pánem situace, protože MikroTik má zakázáno cokoli jiného přeposílat.