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
