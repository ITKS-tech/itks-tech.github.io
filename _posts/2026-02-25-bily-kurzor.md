---
layout: single
title: "Jak opravit neviditelný nebo bílý kurzor (nejen) u procesorů AMD Ryzen"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


Pokud se vám stává, že ve Wordu, Outlooku nebo prohlížeči Chrome mizí kurzor (stává se bílým na bílém pozadí), je to nejčastěji způsobeno chybou v komunikaci mezi grafickým ovladačem a systémem Windows. Tento problém je typický pro procesory AMD Ryzen 3 3200G a podobné modely s grafikou Radeon Vega.
Zde jsou tři kroky k trvalé opravě:

### 1. Automatická oprava pomocí registru (.reg soubor)
Toto je nejúčinnější metoda, která opravuje chybu vykreslování vrstev (tzv. Overlay), kvůli které se kurzor schovává pod textové pole.
Postup:
Otevřete Poznámkový blok (Notepad).
Zkopírujte do něj text níže:
reg
```
Windows Registry Editor Version 5.00

; Vypnuti Hardware-Accelerated GPU Scheduling (HAGS)
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GraphicsDrivers]
"HwSchMode"=dword:00000001

; Fix pro mizejici kurzor u AMD (Overlay Mode)
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Dwm]
"OverlayTestMode"=dword:00000005
```

Při použití kódu buďte obezřetní.

Soubor uložte jako oprava.reg (ujistěte se, že koncovka není .txt).
Na uložený soubor poklepejte, potvrďte přidání do registru a restartujte počítač.

### 2. Vypnutí hardwarové akcelerace v systému (GUI)
Pokud problém přetrvává, vypněte funkci plánování GPU, která u starších integrovaných grafik způsobuje nestabilitu kurzoru.
Klikněte na Start a vyberte Nastavení (ikona ozubeného kola).
Přejděte do Systém > Obrazovka.
Sjeďte úplně dolů a klikněte na odkaz Grafika (u starších verzí Windows 10 "Nastavení grafiky").
Klikněte na Změnit výchozí nastavení grafiky.
Přepněte vypínač u položky Hardwarem akcelerované plánování GPU do polohy Vypnuto.
Restartujte PC.

### 3. Instalace nejnovějších ovladačů (Důležité)
Ovladače z roku 2022 a starší obsahují chyby, které Microsoft v novějších verzích Office a Windows opravil až v kombinaci s novým softwarem od AMD.
Odkaz ke stažení: Oficiální ovladače AMD Ryzen 3 3200G
Tip: Při instalaci v sekci Additional Options zvolte Factory Reset. Tím se kompletně odstraní starý ovladač z roku 2022 a nainstaluje se čistá verze.
Tip pro rychlou pomoc: Pokud potřebujete kurzor vidět hned bez restartu, můžete v Ovládacích panelech > Myš změnit schéma ukazatele na "Windows - černý (systémové schéma)". Černý kurzor bude vidět i tam, kde ten bílý "zmizel".