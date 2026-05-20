
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
Typicky vrací: java.lang.SecurityException: Permission Denial: ...

Mezi chráněné metody patří například:

- enforceNoDataAvail()
- reportPerceptibleJank()
- reportGameScene()
- registerPerformanceListener()
- getPerformanceInfo()

---

## Zajímavé nálezy (bude se doplňovat)

- některé transakce vrací monotonic time v nanosekundách  
- některé transakce vrací performance counters  
- některé transakce crashují kvůli špatnému typu Parcelu  
- některé transakce jsou oneway (bez návratové hodnoty)

---

## Viz také

- `transaction-map.md` – mapa transakcí PerfShielderu  
- `parcel-dumps/` – surové výpisy Parcelů

## Kompletní chování PerfShielder (kódy 1–15)

Na základě testů pomocí `service call perfshielder X` se ukazuje, že PerfShielder má pouze jedinou aktivní metodu vracející živá data. Ostatní transakce jsou buď prázdné, chráněné, nebo placeholdery.

### Mapa kódů

| Kód | Odpověď | Význam |
|-----|---------|--------|
| 1–3 | Parcel(NULL) | metoda neexistuje nebo je přísně chráněná |
| 4 | Parcel(00000000) | existuje, vrací prázdný parcel (void) |
| 5–11 | Parcel(00000000 00000000) | existuje, vrací dvě nulové hodnoty |
| 12 | Parcel(e89bf000 00000001) | **jediná živá metoda – monotonic time / performance counter** |
| 13 | Parcel(NULL) | chráněná / neimplementovaná |
| 14–15 | Parcel(00000000 00000000) | stejné jako 5–11 |

---

## Co z toho plyne

### 1) Kód 12 je unikátní – jediný živý monitor
Hodnota `e89bf000` se mění lineárně s časem → jde o monotonic timer nebo performance counter.

### 2) PerfShielder není hlavní služba
Na rozdíl od ProcessManageru má PerfShielder jen minimum aktivních metod.  
Většina je prázdná nebo chráněná.

### 3) Kód 12 = systémový časovač
První hodnota = čas/čítač  
Druhá hodnota = stav (pravděpodobně vždy 1)

---

## Praktické použití

Kód 12 lze použít jako vysoce přesné stopky:

start=$(service call perfshielder 12 | grep -oE '[0-9a-f]{8}' | head -1)
sleep 1
end=$(service call perfshielder 12 | grep -oE '[0-9a-f]{8}' | head -1)
echo "Rozdíl: $((0x$end - 0x$start)) jednotek"

---

## Porovnání s ProcessManager

| Vlastnost | PerfShielder | ProcessManager |
|----------|--------------|----------------|
| Aktivní metody | ~1 | 30+ |
| Živá data | jen CODE 12 | mnoho (4, 12, 21, 22…) |
| Užitečnost | měření času | procesy, whitelist, foreground, AppLock |

---

## Závěr

PerfShielder je úzká, specializovaná služba.  
Jediný skutečně užitečný kód je 12, který vrací monotonic time.  
Pro hlubší analýzu MIUI je mnohem cennější ProcessManager.
