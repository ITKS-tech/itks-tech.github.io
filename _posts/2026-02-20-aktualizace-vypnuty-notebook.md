---
layout: single
title: "Jak nastavit vypnuté zařízení, aby nainstalovalo aktualizace v noci"
categories: [navody]
classes: it-style
gallery: true
toc: false
---

### 1) Instalace modulu PSWindowsUpdate (PowerShell, jako admin)
Otevři PowerShell jako správce a spusť postupně:

```
Set-ExecutionPolicy RemoteSigned -Scope Process
Install-PackageProvider -Name NuGet -Force
Install-Module -Name PSWindowsUpdate -Force
Import-Module PSWindowsUpdate
```

### 2) Nastavit BIOS (RTC alarm)
V BIOSu nastavíš:
- Power On / RTC Wake / Resume by RTC
- čas: např. 02:00
- opakování: Daily nebo Weekly
To je to, co probudí notebook z úplného vypnutí.

### 3) Nastavit úlohu UPDATE
Plánovač úloh → Vytvořit úlohu
Akce:
```
-NoProfile -ExecutionPolicy Bypass -Command "Import-Module PSWindowsUpdate; Install-WindowsUpdate -AcceptAll -AutoReboot"
```

### 4) Nastavit úlohu SHUTDOWN
Spouštěč:
- Při spuštění systému
- Zdržení úlohy: 15 minut
Podmínky:
- Spustit úlohu po době nečinnosti: 15 minut
- Čekání na nečinnost: 1 minuta
```
shutdown.exe /s /f /t 0
```

### 5) Hotovo - Jak to celé proběhne v reálu

02:00
BIOS probudí notebook (i z úplného vypnutí)
02:05
Úloha UPDATE spustí PSWindowsUpdate
02:05–??
Aktualizace → restart → konfigurace
Po restartu
Úloha SHUTDOWN se aktivuje
→ počká 15 minut
→ čeká na 15 minut nečinnosti
→ vypne notebook
Pokud někdo pracuje
→ notebook není nečinný
→ úloha se nespustí
→ NIC se nevypne

# Nastavení pokud nejde nastavit RTC zapnutí

## PŘESNÝ NÁVOD – krok za krokem
### 1) Vypněte hibernaci
Ať notebook používá jen spánek (S0ix).
- Otevřete Start
- napište cmd
- klik pravým → Spustit jako správce
- vložte:
```
powercfg /hibernate off
```
### 2) Zapněte „Wake timers“ (časovače probuzení)
To je klíčové — Windows se díky tomu probudí sám.
- Start → Nastavení
- Systém → Napájení → Další nastavení napájení
- U vašeho plánu klikněte Změnit nastavení plánu
- Změnit pokročilé nastavení napájení
- Najděte Sleep / Spánek
- Otevřete Allow wake timers / Povolit časovače probuzení
- Nastavte:
- Na baterii: Povolit
- Při napájení: Povolit

### 3) Zapněte „Automatic Maintenance“ (automatická údržba)
To je funkce, která v noci probudí notebook, nainstaluje aktualizace a zase ho uspí.
- Otevřete Ovládací panely
- Systém a zabezpečení
- Zabezpečení a údržba
- Rozbalte Údržba
- Klikněte Změnit nastavení údržby
- Nastavte čas, např. 03:00
- Zaškrtněte:
✔️ Povolit probuzení počítače pro provedení naplánované údržb

### 4) Nastavte Windows Update, aby instaloval v noci
- Start → Nastavení
- Windows Update
- Upřesnit možnosti
- Zapněte:
✔️ Download updates automatically
✔️ Notify me when restart is required
✔️ Active hours → Automaticky
Windows pak restart provede mimo aktivní hodiny — tedy v noci.




 
 
