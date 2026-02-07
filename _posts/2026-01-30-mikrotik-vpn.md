---
layout: single
title: "Jak nastavit Mikrotik WireGuard VPN"
categories: [navody]
classes: it-style
---

WireGuard je moderní, rychlá a jednoduchá VPN technologie, která je dostupná na zařízení MikroTik od systému RouterOS 7. Oproti klasickému IPsec je výrazně jednodušší na konfiguraci, stabilnější, má nižší latenci, lépe funguje s mobilními zařízeními a poskytuje vyšší výkon i na slabším hardwaru. Níže najdete kompletní návod na vytvoření WireGuard VPN pro vzdálený přístup do sítě.

1) Ověření verze RouterOS
WireGuard funguje pouze na RouterOS 7 a vyšším.
Verzi ověříte příkazem:
/system resource print

Pokud máte verzi 6, je nutná aktualizace.

2) Vytvoření WireGuard rozhraní
Vytvořte nové WireGuard rozhraní.
Ve WinBoxu: Interfaces → Add → WireGuard
Nebo přes terminál:
/interface wireguard add name=wg0 listen-port=51820

Port 51820/UDP je výchozí, ale lze změnit.
WireGuard automaticky vygeneruje páry klíčů (public/private).

3) Zobrazení klíčů rozhraní
Pro zobrazení vygenerovaných klíčů zadejte:
/interface wireguard print

Public Key budete posílat klientovi.
Private Key musí zůstat v routeru.

4) Přidání IP adresy pro WireGuard síť
Vytvoříte síť pro klienty, např. 10.10.10.0/24:
/ip address add address=10.10.10.1/24 interface=wg0

10.10.10.1 bude adresa routeru v rámci VPN.

5) Přidání klienta (peer)
Každý klient musí mít vlastní public key a vlastní IP adresu.
Příklad klienta s IP 10.10.10.2:
/interface wireguard peers add interface=wg0 \
public-key="PUBLIC_KEY_KLIENTA" \
allowed-address=10.10.10.2/32


6) Povolení WireGuard portu ve firewallu
Bez tohoto pravidla se nikdo nepřipojí:
/ip firewall filter add chain=input action=accept protocol=udp dst-port=51820 comment="Allow WireGuard"


7) NAT pro přístup klientů na internet přes VPN
Toto umožní, aby klienti využívali internet přes Mikrotik WAN:
/ip firewall nat add chain=srcnat action=masquerade src-address=10.10.10.0/24 out-interface=ether1

(ether1 = vaše WAN)

8) Konfigurace klienta (mobil, PC, notebook)
Na zařízení použijete oficiální WireGuard aplikaci.
Do klienta vložíte konfiguraci:
[Interface]
PrivateKey = privátní_klíč_klienta
Address = 10.10.10.2/32
DNS = 1.1.1.1

[Peer]
PublicKey = veřejný_klíč_routeru
AllowedIPs = 0.0.0.0/0
Endpoint = tvoje_verejna_ip.nebo.domena:51820
PersistentKeepalive = 25

AllowedIPs = 0.0.0.0/0 znamená, že veškerý provoz půjde přes VPN.

9) Otestování spojení
Z klienta zkuste:

připojit WireGuard
pingnout router: ping 10.10.10.1
otevřít interní zdroje

Pokud funguje, VPN je správně nastavena.

Hlavní výhody WireGuard oproti IPsec
1) Extrémně jednoduchá konfigurace
IPsec vyžaduje IKE profily, politiky, SA, encryption proposals, lifetime nastavení a další komplikace.
WireGuard má pouze:

klíče
IP adresy
jeden port
AllowedIPs

Konfigurace je tak 10× jednodušší.

2) Vysoká rychlost
WireGuard využívá moderní kryptografii (ChaCha20), která je rychlá i na slabém hardwaru.
MikroTik s IPsec může dávat 30–150 Mbps.
MikroTik s WireGuard často zvládne 200–600 Mbps a více.

3) Stabilita a perfektní mobilní použití
WireGuard umí:

měnit IP adresy za běhu
okamžitě obnovovat spojení
perfektně fungovat při přepínání mezi Wi‑Fi/LTE

IPsec často padá při změně sítě nebo NAT.

4) Modernější a bezpečnější architektura
WireGuard používá:

ChaCha20
Poly1305
Curve25519
velmi čistý, malý a auditovatelný kód

IPsec má obrovsky složitý stack a více historických problémů.

5) Nižší latence a vyšší propustnost
WireGuard má typicky:

nižší latenci (nižší overhead)
stabilnější rychlosti
téměř okamžitý „handshake“

![Schéma VPN](/assets/images/vpn-schema.png)
{% include figure image_path="/assets/images/vpn-schema.png" alt="Schéma VPN" caption="Schéma VPN WireGuard" %}