---
layout: single
title: "Jak opravit ikony Wordu Office 365"
categories: [navody]
classes: it-style
gallery: true
toc: false
---


Nainstalovali jste Office 365, ale místo klasických modrých ikon vidíte jen bílé papíry s linkami? Navíc vám po kliknutí pravým tlačítkem na plochu v nabídce „Nový“ úplně chybí Word?
Pokud selhala rychlá i online oprava, problém vězí hluboko v registrech Windows. Tady je rychlý návod, jak to opravit u 32-bitové instalace, kde instalátor „zapomněl“ správně propojit přípony.
Proč se to stalo?
Windows ztratily cestu k souboru wordicon.exe a WINWORD.EXE. Systém pak neví, jak má soubor vypadat, a v kontextovém menu ho pro jistotu nenabídne vůbec.
Prověřené řešení přes Registry
Tento postup natvrdo přepíše cesty v systému tak, aby ukazovaly na správné soubory Office ve složce Program Files (x86).
### Postup krok za krokem:
Spusťte Poznámkový blok.
Vložte do něj tento kód:
```
registry
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\.docx]
@="Word.Document.12"
"Content Type"="application/vnd.openxmlformats-officedocument.wordprocessingml.document"
"PerceivedType"="document"

[HKEY_CLASSES_ROOT\.docx\ShellNew]
"NullFile"=""
"FileName"="C:\\Program Files (x86)\\Microsoft Office\\root\\VFS\\Windows\\ShellNew\\word.docx"

[HKEY_CLASSES_ROOT\Word.Document.12\DefaultIcon]
@="C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\wordicon.exe,13"

[HKEY_CLASSES_ROOT\Word.Document.12\shell\Open\command]
@="\"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE\" /n \"%1\""

[HKEY_CLASSES_ROOT\.doc]
@="Word.Document.8"
"Content Type"="application/msword"

[HKEY_CLASSES_ROOT\Word.Document.8\DefaultIcon]
@="C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\wordicon.exe,1"

[HKEY_CLASSES_ROOT\Word.Document.8\shell\Open\command]
@="\"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE\" /n \"%1\""
Při použití kódu buďte obezřetní.
```

Soubor uložte jako oprava.reg (pozor na koncovku .reg, ne .txt).
Soubor spusťte, potvrďte varování systému a nechte změny zapsat.
Restartujte počítač.
Co tím získáte?
Správné ikony: Soubory .doc a .docx budou mít opět své modré logo.
Menu Nový: Word se vrátí do nabídky pravého tlačítka na ploše.
Funkční otevírání: Poklepání na soubor konečně spustí Word bez chybových hlášek.
