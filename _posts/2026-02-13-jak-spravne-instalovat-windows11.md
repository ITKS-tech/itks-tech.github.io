---
layout: single
title: "Jak správně nainstalovat Windows 11"
categories: [navody]
classes: it-style
gallery: true
toc: false
---

Tento postup je nejlepší cestou k čistému systému bez omezení od Microsoftu. Získáte stabilnější zavaděč a vyhnete se povinnému online účtu.

### Jak správně nainstalovat Windows 11
750MB EFI a lokální účet příkazem „ms-cxh“

### 1. Ruční příprava disku (750MB EFI)
Na úvodní obrazovce instalátoru (výběr jazyka) stiskněte 
```
Shift + F10
```
 pro otevření příkazového řádku:
Zadejte diskpart.
```
list disk (najděte svůj disk, např. 0).
select disk 0 (použijte číslo svého disku).
clean (Smaže všechna data!).
convert gpt.
create partition efi size=750 – Vytvoří dostatečně velký prostor pro budoucí aktualizace a zavaděče.
format quick fs=fat32 label="System".
create partition msr size=16.
exit (zavře diskpart) a znovu exit (zavře řádek).
```
V grafickém průvodci:
Zvolte Vlastní instalaci.
Klikněte na „Nepřiřazené místo“ a dejte Další.
Windows si zbytek místa rozdělí sám – vytvoří oddíl C: a na konec disku umístí Recovery oddíl.

### 2. Obejití Microsoft účtu (Příkaz ms-cxh)
Jakmile se po instalaci a restartu objeví obrazovka OOBE (výběr regionu nebo sítě):
Znovu stiskněte Shift + F10.
Zadejte tento specifický příkaz a potvrďte Enterem:
```
start ms-cxh:localonly (případně variantu start ms-cxh:localonlyuser)
```
Co se stane: Tento příkaz vyvolá skrytý protokol v systému, který okamžitě ukončí vynucování online účtu.
Průvodce vás nyní hodí přímo na obrazovku „Kdo bude tento počítač používat?“.
Zadejte jméno svého lokálního účtu, heslo a hotovo.

### 3. Kontrola po instalaci
Po naběhnutí na plochu doporučujeme prověřit dvě věci:
Správa disků: Pravým tlačítkem na Start -> Správa disků. Zde uvidíte svůj 750 MB EFI oddíl na začátku a Recovery na konci.
Stav obnovy: V Terminálu (jako správce) zadejte reagentc /info. Pokud uvidíte Windows RE status: Enabled, Recovery oddíl je správně propojen se systémem.
Tímto postupem jste vytvořili technicky nejčistší možnou instalaci Windows 11, která je odolná proti chybám při budoucích aktualizacích (jako byla známá chyba KB5034441 s malým oddílem).
Chcete pomoci i s vypnutím automatické instalace sponzorovaných aplikací (jako Candy Crush nebo TikTok), které se tam po prvním připojení k netu mohou objevit?

### Kompletní příkazy v Diskpartu:

Čisté naformátování jakéhokoliv disku přes Diskpart:
```
diskpart
list disk
sel disk 0
list partition
clean
convert gpt
create partition primary
format fs=ntfs quick
assign letter=d
```
Vytvoření 750MB EFI oddílu 
```
create partition efi size=750
format fs=fat32 label="System"
create partition msr size=16
```
Kompletní postup v Diskpartu jak manuálně zvětšit EFI partition:
Po příkazech pro EFI a MSR pokračujte takto:

Vytvoření hlavního oddílu (C:):
```
create partition primary shrink minimum=1000
(Tímto vytvoříte oddíl přes zbytek disku, ale na konci "utrhnete" 1000 MB pro Recovery).
format quick fs=ntfs label="Windows"
assign letter=W (dočasné písmeno pro instalátor).
```
Vytvoření oddílu pro obnovení (Recovery):
```
create partition primary (vyplní zbývajících 1000 MB).
format quick fs=ntfs label="Recovery".
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac" (označí oddíl jako Recovery pro GPT disk).
gpt attributes=0x8000000000000001 (skryje oddíl a nastaví jej jako povinný).
```
Tip: Po vytvoření EFI a MSR v příkazovém řádku už nemusíte vytvářet primární oddíl ručně. Stačí Diskpart ukončit a v grafickém instalátoru vybrat „Nepřidělené místo“. Windows si tam sám vytvoří oddíl pro systém a na konec disku automaticky přidá oddíl pro obnovení (Recovery)