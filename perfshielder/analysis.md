
# PerfShielder – Analysis

Tato část dokumentuje chování MIUI služby `com.miui.perfshielder.IPerfShielder`, zjištěné black‑box metodou pomocí `service call`, analýzy Parcelů a SecurityException stacktrace.

---

## Co PerfShielder dělá

PerfShielder je interní MIUI služba zaměřená na:

- měření výkonu aplikací
- správu CPU a scheduleru
- optimalizaci herního režimu (Game Turbo)
- sběr metrik (monotonic timers, performance counters)
- řízení priorit procesů
- detekci lagů a frame dropů

Je to jedna z nejdůležitějších MIUI služeb pro výkon.

---

## Metody přístupné ze shellu

Některé transakce nevyžadují systémová oprávnění a vrací data.  
Tyto metody budeme postupně doplňovat podle získaných Parcel výpisů.

### CODE X – (čeká na výpis)
Sem doplníme první nalezenou metodu.

---

## Metody chráněné (Permission Denial)

Většina PerfShielder metod vyžaduje systémové UID.  
Typicky vrací:
