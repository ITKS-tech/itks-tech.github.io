---
layout: single
title: "Kompletní balík příkazů pro zjištění licence Windows"
categories: [navody]
classes: it-style
gallery: true
toc: false
---

slmgr /dlv
# Detailní info o licenci – typ licence, aktivace, ID kanálu.

slmgr /xpr
# Informace, zda je Windows trvale aktivován (KMS = expirace).

slmgr /dli
# Základní info o licenci, posledních 5 znaků klíče.

wmic path softwarelicensingservice get OA3xOriginalProductKey
# Zobrazení OEM klíče uloženého v BIOS/UEFI.

ms-settings:activation
# Otevře okno Aktivace Windows.

slmgr /ato
# Pokus o aktivaci (znovu aktivuje klíčem nebo digitální licencí).

dism /online /get-currentedition
# Zobrazení aktuální edice Windows.

dism /online /get-targeteditions
# Zobrazení edic, na které lze provést upgrade/downgrade.

sfc /scannow
# Kontrola systémových souborů, pokud má aktivace problémy.

dism /online /cleanup-image /restorehealth
# Oprava systémového image – může vyřešit poškozené licenční soubory.

nslookup -type=srv _vlmcs._tcp
# Hledání KMS serveru v síti (KMS aktivace EDU/Enterprise).

slmgr /upk
# Odebrání nainstalovaného Product Key (POZOR – odstraní licenci).

slmgr /ipk XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
# Instalace nového produktového klíče.

slmgr /rearm
# Reset licenčního stavu (omezené použití).

powershell -command "(Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform').BackupProductKeyDefault"
# Získá uložený klíč v registrech (pokud existuje).

powershell "(Get-WmiObject -query 'select * from SoftwareLicensingService').OA3xOriginalProductKey"
# OEM klíč z BIOSu přes PowerShell.

powershell "slmgr /dlv; slmgr /xpr; (Get-WmiObject -query 'select * from SoftwareLicensingService').OA3xOriginalProductKey"
# Souhrn všech informací o licenci v jednom výstupu.