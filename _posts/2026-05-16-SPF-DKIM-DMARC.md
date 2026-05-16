---
layout: single
title: "Jak zabezpečit e-mailovou doménu: Kompletní průvodce SPF, DKIM a DMARC)"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


Nenechávejte doručitelnost svých firemních e-mailů na náhodě. Od roku 2024 zavedli globální hráči jako Google, Yahoo a český Seznam přísná pravidla pro ověřování odesílatelů. Pokud vaše doména nemá správně nastavenou trojici DNS záznamů – SPF, DKIM a DMARC – vaše e-maily (faktury, nabídky, objednávky) budou končit ve spamu, nebo je servery příjemců rovnou smažou.
V tomto průvodci si ukážeme, jak tyto technologie fungují, co jejich nasazením získáte a jak je správně nakonfigurovat a ověřit.
________________________________________
# 🛡️ Tři pilíře zabezpečení e-mailu
Představte si e-mailovou komunikaci jako klasickou papírovou poštu. Kdokoliv může na obálku napsat jakoukoliv zpáteční adresu. SPF, DKIM a DMARC fungují jako digitální podatelna, která ověřuje pravost odesílatele i obsahu.

> [ Odeslaný e-mail ] <br>
> 🔽 <br>
> **1. SPF** — *Odešel mail ze schválené IP adresy?* <br>
> 🔽 <br>
> **2. DKIM** — *Je digitální podpis mailu platný?* <br>
> 🔽 <br>
> **3. DMARC** — *Co dělat, když SPF nebo DKIM selže?* <br>
> 🔽 <br>
> [ Doručeno / Spam / Blokováno ]
   
________________________________________
## 1. SPF (Sender Policy Framework) – Seznam povolených odesílatelů

### Co to dělá
SPF je TXT záznam v DNS vaší domény, který obsahuje veřejný seznam IP adres a serverů, ze kterých smí odcházet e-maily s vaší koncovkou (např. @firma.cz).
### Co tím získáte
Server příjemce (např. Gmail) při přijetí e-mailu zkontroluje IP adresu odesílajícího serveru. Pokud tato IP adresa není na vašem SPF seznamu, e-mail je vyhodnocen jako podezřelý.
### Jak to nastavit
Každá doména smí mít striktně pouze jeden SPF záznam. Pokud používáte více služeb (poštu od registrátora, e-shop, účetní software), musíte je spojit do jednoho řádku pomocí operátoru include:.
•	Příklad pro poštu u Wedosu:
```
v=spf1 mx include:_spf.we.wedos.net -all
```
•	Příklad pro kombinaci Google Workspace, Seznamu a Fakturoidu:
```
v=spf1 mx include:_://google.com include:spf.seznam.cz include:_://fakturoid.com -all
```
Význam -all na konci: Říká, že jakýkoliv jiný server mimo tento seznam je nelegitímní a mail má být odmítnut.
________________________________________
## 2. DKIM (DomainKeys Identified Mail) – Digitální podpis 

### Co to dělá
DKIM podepisuje každý odchozí e-mail neviditelným kryptografickým klíčem. V DNS domény je umístěn veřejný klíč, kterým si server příjemce ověří, zda e-mail skutečně vytvořil majitel domény.
### Co tím získáte
Zajišťuje integritu e-mailu. Garantuje příjemci, že s obsahem zprávy ani s hlavičkou odesílatele nikdo během cesty po internetu nemanipuloval. Na rozdíl od SPF zůstává DKIM v e-mailu platný i při automatickém přeposílání zpráv.
### Jak to nastavit
1.	Přihlaste se do administrace svého e-mailového poskytovatele (Google Workspace, Microsoft 365, Seznam Profi, Wedos).
2.	V sekci nastavení e-mailu nechte vygenerovat nový DKIM klíč.
3.	Poskytovatel vám vygeneruje název (selektor, např. google._domainkey) a dlouhý textový řetězec (veřejný klíč).
4.	Tento klíč vložíte do DNS záznamů své domény jako typ TXT.
________________________________________  
## 3. DMARC (Domain-based Message Authentication) – Řídící střecha  

### Co to dělá  
DMARC určuje, jak má server příjemce naložit s e-mailem, který neprošel kontrolou SPF nebo DKIM. Zároveň umožňuje majiteli domény zasílat detailní reporty o tom, kdo se z jeho domény pokouší maily posílat.
### Co tím získáte  
Absolutní kontrolu nad svou identitou. Útočníci již nebudou moci falšovat vaše e-mailové adresy. Navíc díky reportům přesně vidíte všechny legitimní i nelegitímní servery využívající vaši doménu.
### Jak to nastavit 
DMARC se nastavuje jako TXT záznam s názvem _dmarc.vase-domena.cz. Má tři základní bezpečnostní režimy (p=):
1.	p=none (Monitoring): Maily se neblokují. Ideální na začátek (1–2 týdny) pro sběr dat do logů a odladění chyb.
2.	p=quarantine (Karanténa): Maily bez SPF/DKIM padají příjemcům rovnou do spamu.
3.	p=reject (Odmítnutí): Nevalidní maily jsou serverem příjemce okamžitě smazány a k adresátovi vůbec nedorazí.
•	Příklad bezpečného DMARC pro monitoring se zasíláním logů na váš IT e-mail:
o	Název: _dmarc
o	Typ: TXT
o	Data: v=DMARC1; p=none; rua=mailto:logs@itks.cz; ruf=mailto:logs@itks.cz; fo=1; pct=100;
Význam parametrů: rua definuje schránku pro denní statistiky, ruf schránku pro okamžitá hlášení o jednotlivých chybách, fo=1 spustí hlášení při jakémkoliv selhání.
⚠️ Pozor na externí domény: Pokud posíláte reporty na jinou doménu, než kterou monitorujete (např. z domény klienta na vaši doménu itks.cz), musíte na své přijímací doméně vytvořit autorizační DNS záznam typu TXT s názvem klient.cz._report._dmarc.itks.cz a hodnotou v=DMARC1;. Jinak servery reporty z bezpečnostních důvodů neodešlou.
________________________________________
## 🛠️ Ověření funkčnosti přes Windows PowerShell
Nemusíte čekat na doručení e-mailu, abyste zjistili, zda máte DNS záznamy správně zapsané. Můžete využít systémový nástroj Windows PowerShell a příkaz Resolve-DnsName.
### 1. Kontrola SPF záznamu
Pro ověření hlavního SPF záznamu domény zadejte:
powershell
```
Resolve-DnsName -Name firma.cz -Type TXT
```
Ve výsledku hledejte řádek začínající v=spf1....
### 2. Kontrola DMARC záznamu
Pro ověření politiky DMARC a správnosti e-mailu pro reporty zadejte:
powershell
```
Resolve-DnsName -Name _dmarc.firma.cz -Type TXT
```
Při použití kódu buďte obezřetní.
PowerShell musí vrátit váš řetězec s parametry p=none nebo p=quarantine.
### 3. Kontrola DKIM záznamu
U DKIM musíte znát tzv. selektor, který vám vygeneroval poskytovatel (např. szn1 pro Seznam nebo google pro Google):
powershell
```
Resolve-DnsName -Name selektor._domainkey.firma.cz -Type TXT
```
Pokud je záznam aktivní, PowerShell zobrazí dlouhý šifrovaný veřejný klíč.
________________________________________
## 📈 Doporučený postup nasazení v praxi
1.	Nasaďte SPF pro všechny známé servery.
2.	Aktivujte DKIM u vašeho poštovního poskytovatele.
3.	Spusťte DMARC v režimu p=none a nechte si posílat reporty po dobu 14 dní.
4.	Analyzujte logy (vhodné použít nástroje jako MXToolbox DMARC Delivery nebo DMARC Analyzer). Pokud objevíte zapomenutý e-shop nebo fakturační systém, přidejte ho do SPF.
5.	Jakmile jsou reporty čisté, přepněte DMARC na p=quarantine a následně na p=reject. Vaše doména je od té chvíle neprůstřelná.

