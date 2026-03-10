---
layout: single
title: "Jak opravit chybu aktualizace Windows (0x800f0922) rozšířením EFI partition"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


Narazili jste při aktualizaci Windows nebo přechodu na novou verzi (např. 23H2) na chybu 0x800f0922? Pokud spravujete firemní notebooky přes WSUS nebo ESET, pravděpodobně bojujete s nedostatkem místa v systémovém oddílu EFI (ESP). Moderní Windows si už se standardními 100 MB nevystačí a aktualizace pak končí selháním v "SafeOS fázi".
Běžné nástroje ve Windows neumí EFI partition „nafouknout“, pokud za ní hned následuje disk C:. Existuje ale elegantní a bezpečné řešení: vytvořit novou, větší EFI partition a nasměrovat na ni zavaděč.
Proč k chybě dochází?
EFI partition obsahuje spouštěcí soubory. Postupem času se zaplní logy, fonty nebo zbytky po aktualizacích. Jakmile volné místo klesne pod kritickou hranici (cca 15 MB), Windows Update odmítne zápis nových verzí souborů a instalace havaruje.
Ověřený postup: Vytvoření nové 500MB EFI partition
Tento postup vytvoří nový oddíl na konci disku (nebo v uvolněném místě) a natvrdo tam přesune "mozek" bootování.

### 1. KROK: Příprava místa a nové partition (Diskpart)
Nejprve musíme vzít kousek místa z disku C: a vytvořit tam nový systémový oddíl. Spusťte CMD jako správce:
```
cmd
diskpart
select disk 0
list partition
select partition 3  :: Vyberte oddíl s Windows (C:)
shrink desired=500
create partition efi
format quick fs=fat32 label="System_New"
assign letter=S
exit
```

### 2. KROK: Kopírování bootovacích souborů
Nyní na nový disk S: nakopírujeme soubory potřebné pro start systému.
```
cmd
bcdboot C:\Windows /s S: /f UEFI
```
Výsledek musí být: „Boot files successfully created.“

### 3. KROK: Nasměrování zavaděče (Zlatý hřeb)
Samotný bcdboot někdy nestačí, protože BIOS si drží starou cestu. Musíme mu v konfiguraci (BCD) natvrdo říct, že nový domov je na S:.
```
cmd
bcdedit /set {bootmgr} device partition=S:
bcdedit /set {memdiag} device partition=S:
Při použití kódu buďte obezřetní.
```

### 4. KROK: Úklid a aktivace
Odebereme dočasné písmeno a restartujeme.
```
cmd
diskpart
select volume S
remove letter=S
exit
```

### Finální krok: Restart a Boot Menu
Při restartu vyvolejte Boot Menu (typicky klávesa `F9`, `F12` nebo `F11` podle výrobce). V seznamu vyberte Windows Boot Manager. Díky našemu nastavení se systém načte z nové 500MB partition.
Jakmile Windows naběhnou, zkuste aktualizaci spustit znovu – nyní už projde hladce, protože má pro svůj život královských 500 MB prostoru. Původní 100MB partition můžete na disku nechat jako „mrtvou“ zálohu, ničemu nepřekáží.

### tip na závěr
Pokud máte notebook HP máte v EFI partition i ovladače výrobce, třeba BIOS nebo Dock. V tom případě překopírujte i celou složku HP.

Připojte si písmenka starý oddíl Z: nový oddíl S: a použijte příkaz `Robocopy`

robocopy Z:\EFI\HP S:\EFI\HP /E /R:3 /W:5

Přeji Wám, ať okna šlapou, jak mají.