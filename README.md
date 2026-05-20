
# MIUI Binder Reverse Engineering  
Komunitní projekt zaměřený na dokumentaci interních MIUI Binder služeb pomocí čistě black‑box metod:  
`service call`, analýza Parcelů, SecurityException stacktrace a runtime chování.

Cílem projektu je vytvořit první veřejnou, strukturovanou a ověřenou dokumentaci MIUI interních služeb.

---

# Struktura repozitáře

processmanager/
    analysis.md
    transaction-map.md
    parcel-dumps/

perfshielder/
    analysis.md
    transaction-map.md
    parcel-dumps/

Každá služba má:

- analysis.md – detailní popis chování služby  
- transaction-map.md – mapu transakcí (CODE → metoda)  
- parcel-dumps/ – surové výpisy Parcelů získané pomocí service call  

---

## ProcessManager

MIUI služba miui.process.IProcessManager.

Co umí:
- správa běžících procesů  
- whitelist chráněných aplikací  
- AppLock  
- Game Turbo integrace  
- foreground/background tracking  
- MIUI‑specifické optimalizace  

Klíčové nálezy:
- CODE 4 → whitelist chráněných aplikací  
- CODE 12 → seznam běžících procesů (PID, UID, package)  
- mnoho dalších metod vrací strukturovaná data  

Detailní analýza:  
→ processmanager/analysis.md  
Mapa transakcí:  
→ processmanager/transaction-map.md

---

## PerfShielder

MIUI služba com.miui.perfshielder.IPerfShielder.

Co umí:
- měření výkonu  
- monotonic timers  
- performance counters  
- Game Turbo telemetry  
- detekce lagů  

Klíčové nálezy:
- CODE 12 → jediná živá metoda, vrací monotonic time / performance counter  
- ostatní kódy jsou prázdné, chráněné nebo placeholdery  

Detailní analýza:  
→ perfshielder/analysis.md  
Mapa transakcí:  
→ perfshielder/transaction-map.md

---

## Metodika

Projekt používá čistě black‑box přístup:

- service call <service> <code>  
- analýza Parcel výpisů  
- dekódování UTF‑16 řetězců  
- interpretace hex hodnot (PID, UID, flagy, časovače)  
- porovnání s runtime chováním systému  

---

## Cíl projektu

- vytvořit první veřejnou dokumentaci MIUI Binder služeb  
- umožnit komunitě pochopit interní chování MIUI  
- poskytnout nástroje pro analýzu výkonu a procesů  
- otevřít cestu k dalšímu reverse‑engineeringu MIUI frameworku  

---

## Přispívání

Každý může přispět:

- novými výpisy  
- doplněním map transakcí  
- analýzou dalších MIUI služeb  
- opravami a vylepšeními dokumentace  

---

## Stav projektu

- ProcessManager → fáze 1 kompletní / fáze 2 (rozpracované)  
- PerfShielder → fáze 1 kompletní / fáze 2 (rozpracované) 
- další služby → plánováno  

---

## Licence
MIT License

Projekt je otevřený a komunitní.  
Všechny výpisy pochází z uživatelských zařízení a jsou legální k analýze.


## Autor
Jan Dočkal a Copilot (Česká republika) – výzkum MIUI internals na zařízení Xiaomi 14 (houji)
