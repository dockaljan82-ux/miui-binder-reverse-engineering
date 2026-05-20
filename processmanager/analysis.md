# ProcessManager – Analysis

Tato část dokumentuje chování MIUI služby `miui.process.IProcessManager`, zjištěné čistě black‑box metodou pomocí `service call`, analýzy Parcelů a SecurityException stacktrace.

## Co ProcessManager dělá
 je proprietární MIUI vrstva nad Android ActivityManagerem. Slouží k:

- řízení běžících procesů
- ochraně vybraných aplikací před ukončením
- správě AppLocku
- optimalizaci výkonu (Game Turbo)
- sledování popředí/pozadí aplikací
- správě whitelistů a konfigurací

## Metody přístupné ze shellu
Některé transakce nevyžadují systémová oprávnění a vrací data:

### **CODE 4 – seznam chráněných balíčků**
Vrací pole UTF‑16 stringů, např.:

- `com.xiaomi.bsgamecenter`
- `com.jeejen.family.miui`

Jde o interní whitelist aplikací, které MIUI nesmí ukončit.

### **CODE 12 – stav běžících procesů**
Vrací strukturu obsahující:

- název balíčku (`com.termux`, `com.miui.home`)
- dvě hex hodnoty (pravděpodobně PID a UID nebo tokeny)

Jde o MIUI optimalizovanou alternativu k `ps`.

## Metody chráněné (vyžadují systémové UID)
Tyto metody vrací `Permission Denial`:

- `updateApplicationLockedState()`
- `protectCurrentProcess()`
- `updateConfig()`
- `addMiuiApplicationThread()`
- `getForegroundApplicationThread()`
- `getRunningProcessInfo()`
- `getActiveUidInfo()`
- `registerActivityChangeListener()`
- `registerForegroundWindowListener()`

MIUI je používá pro interní správu výkonu a bezpečnosti.

## Zajímavé nálezy
- některé transakce vrací status kódy (`000001f4`, `fffffe18`)
- některé transakce crashují kvůli špatnému typu argumentu (unmarshalling error)
- některé transakce jsou oneway nebo očekávají jiný Parcel formát

## Viz také
- `transaction-map.md` – kompletní mapa transakcí
- `parcel-dumps/` – surové výpisy Parcelů
# ProcessManager – Analysis

Tato část dokumentuje chování MIUI služby `miui.process.IProcessManager`, zjištěné čistě black‑box metodou pomocí `service call`, analýzy Parcelů a SecurityException stacktrace.

---

## Co ProcessManager dělá

ProcessManager je proprietární MIUI vrstva nad Android ActivityManagerem. Slouží k:

- řízení běžících procesů
- ochraně vybraných aplikací před ukončením
- správě AppLocku
- optimalizaci výkonu (Game Turbo)
- sledování popředí/pozadí aplikací
- správě whitelistů a konfigurací

---

# CODE 4 — Whitelist chráněných aplikací

Parcel výpis:  
→ viz soubor [`pm_code4.txt`](ca://s?q=otevrit_pm_code4_txt)

Tato metoda vrací **seznam aplikací**, které MIUI chrání před:

- ukončením
- omezením na pozadí
- agresivním power managementem

### Výsledek z testu:

1. **com.shizuku.privileged.api**  
2. **com.termux**  
3. **com.y.c.gloryfit**  
4. **com.xiaomi.bsgamecenter**  
5. **com.jeejen.family.miui**

### Závěr:

- seznam je **dynamický**  
- reaguje na změny provedené přes shell / Shizuku  
- MIUI používá tento whitelist pro AppLock, Game Turbo a správu výkonu  

---

# CODE 12 — Seznam běžících procesů

Parcel výpis:  
→ viz soubor [`pm_code12.txt`](ca://s?q=otevrit_pm_code12_txt)

Tato metoda vrací strukturu obsahující:

- název balíčku (UTF‑16)
- PID (proces ID)
- UID (uživatelské ID)
- status / flag

### Příklad z testu:

**com.termux**  
- PID: `0x2881` → 10369  
- UID: `0x116A` → 4458  

**com.miui.home**  
- PID: `0x27B9` → 10169  
- UID: `0x1252` → 4690  

### Závěr:

- jde o MIUI optimalizovanou alternativu k `ps`  
- metoda je přístupná i ze shellu  
- MIUI ji používá pro sledování popředí, Game Turbo a správu výkonu  

---

# Další chráněné metody (Permission Denial)

Tyto metody vyžadují systémové UID:

- `updateApplicationLockedState()`
- `protectCurrentProcess()`
- `updateConfig()`
- `addMiuiApplicationThread()`
- `getForegroundApplicationThread()`
- `getRunningProcessInfo()`
- `getActiveUidInfo()`
- `registerActivityChangeListener()`
- `registerForegroundWindowListener()`

---

# Shrnutí

ProcessManager je klíčová MIUI služba, která:

- řídí procesy
- chrání vybrané aplikace
- poskytuje MIUI‑specifické informace o běhu systému
- má vlastní whitelist a vlastní scheduler logiku

Další detaily viz:  
- [`transaction-map.md`](ca://s?q=otevrit_transaction_map)  
- složka [`parcel-dumps`](ca://s?q=otevrit_parcel_dumps)
