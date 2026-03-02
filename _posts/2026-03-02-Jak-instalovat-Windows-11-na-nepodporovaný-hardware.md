---
layout: single
title: "Jak instalovat Windows 11 na nepodporovaný hardware"
categories: [navody]
classes: it-style
gallery: true
toc: false
---

Pokud se pokoušíte o upgrade na Windows 11 u staršího stroje, pravděpodobně narážíte na striktní požadavky na TPM 2.0 a generaci procesoru. Existuje však elegantní řešení, které nevyžaduje modifikaci registru ani nástroje třetích stran. Klíčem je skrytý parametr instalačního procesu.
Mechanismus obcházení kontrol
Metoda využívá spouštěcí parametr /product server. Tento přepínač instruuje instalátor, aby se identifikoval jako verze pro Windows Server. Serverové edice mají odlišné (benevolentnější) rutiny pro kontrolu hardwarové kompatibility. Přestože instalátor běží v tomto režimu, cílová verze systému zůstává věrná licenci původního hostitele (např. Windows 11 Pro).
### Technický postup (Step-by-Step)
Získání obrazu disku: Stáhněte si aktuální ISO Windows 11 z oficiálního webu Microsoftu.
[Stáhnout Windows 11 ISO z webu Microsoftu](https://www.microsoft.com/cs-cz/software-download/windows11)
Soubor uložte na lokální disk.
Připojení ISO: Klikněte na soubor pravým tlačítkem a zvolte Připojit (Mount). Poznamenejte si písmeno jednotky, které bylo systému přiděleno (např. E:).
Spuštění s parametry:
Otevřete Příkazový řádek (CMD) s oprávněním správce.
Přepněte se na písmeno jednotky zadáním E: (nahraďte vaším písmenem) a stiskněte Enter.
Spusťte instalaci příkazem:
```
e:
setup.exe /product server
```
Průběh upgradu: Instalátor se spustí s titulkem „Windows Server Setup“. To je v pořádku. Pokračujte v instalaci se zachováním souborů a aplikací. Po restartu vás přivítá standardní Windows 11 Home/Pro.
Rizika a provozní omezení
Instalace Windows 11 na zařízení, která nesplňují minimální požadavky, nese určitá rizika. Microsoft upozorňuje, že takové systémy mohou vykazovat nestabilitu (tzv. BSOD) a v budoucnu mohou být odstřihnuty od bezpečnostních aktualizací.
V praxi zatím kumulativní aktualizace na nepodporovaných PC fungují, nicméně u velkých výročních aktualizací (např. verze 25H2) může být nutné tento proces s parametrem /product server opakovat.