---
layout: single
title: "Jak efektivně zabezpečit management síť proti neoprávněnému přístupu na fyzické vrstvě pomoci MikroTik switche"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


Při správě síťové infrastruktury na prvcích MikroTik je kritickým bodem fyzické zabezpečení portů. Pokud útočník odpojí váš autorizovaný switch a do stejného portu zapojí svůj notebook, může získat přístup přímo do vaší management sítě.
Tento článek popisuje, jak využít MikroTik Firewall Filter k tomu, aby netagovaný management provoz fungoval pouze pro konkrétní MAC adresu, zatímco běžné klientské VLANy zůstaly plně průchozí.
Princip: Inteligentní filtr na fyzickém portu
Využíváme vlastnosti MikroTiku, kde firewall na fyzickém rozhraní (např. `ether21`) vidí pouze pakety bez VLAN tagu.
### 1. Autorizovaný switch: 
Má povolený přístup na základě své unikátní MAC adresy.
### 2. Cizí zařízení: 
Jakýkoliv jiný notebook nebo switch, který pošle netagovaný paket, je firewallem okamžitě zahozen.
### 3. VLAN provoz (Trunk): 
Data označená tagy (např. klientská internetová data) procházejí skrze virtuální rozhraní a tento filtr je ignoruje – fungují tedy bez omezení.

## Nastavení ve Winboxu (Krok za krokem)
Pravidla definujeme v menu IP -> Firewall na záložce Filter Rules.

### 1. Povolení důvěryhodné MAC adresy
Klikněte na modré "+" (Add New):
Tab General: 
Chain: `forward`
In. Interface: `ether21` (port, kam vede kabel ze switche) 
Src. MAC Address: `xx:xx:xx:xx:xx:xx` (fyzická adresa vašeho switche)
Tab Action: 
Action: `accept`

### 2. Zákaz neoprávněného přístupu (Drop)
Klikněte znovu na modré "+": 
Tab General: 
Chain: `forward` 
In. Interface: `ether21` 
Tab Action: 
Action: `drop` 
Pořadí je klíčové: Pravidlo Accept musí být nad pravidlem Drop!

### Nutná podmínka❗: Vypnutí Hardware Offloadu
Aby MikroTik dokázal tyto pakety filtrovat ve Firewallu, nesmí je odbavovat přímo switch-chip. Pokud je zapnutý HW Offload, paket "obteče" procesor a pravidla se neaplikují.
Postup deaktivace:
Otevřete menu Bridge -> záložka Ports.
Rozklikněte port `ether21`.
Na záložce General zrušte zaškrtnutí u volby Hw. Offload.
Tímto krokem donutíte port posílat netagovaný provoz skrze CPU, kde ho MikroTik Firewall zachytí. U VLAN provozu se díky moderním verzím RouterOS (v7+) stále může uplatnit částečná HW akcelerace, ale netagovaný management bude pod přísnou kontrolou procesoru.
### Závěr
Pokud nyní na `drop` pravidle vidíte narůstající bajty, váš MikroTik úspěšně blokuje "šum" ze sítě nebo pokusy o neoprávněné připojení. Vaše management síť je nyní svázána s konkrétním hardwarem a fyzický přístup k portu útočníkovi k ničemu nepomůže.
