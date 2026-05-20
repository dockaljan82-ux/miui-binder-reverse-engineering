# PerfShielder – Transaction Map

Mapa transakčních kódů MIUI PerfShielder služby (`com.miui.perfshielder.IPerfShielder`), získaná black‑box metodou pomocí `service call`, analýzy Parcelů a SecurityException stacktrace.

---

## Struktura mapy

| CODE | Metoda (podle stacktrace / chování) | Stav | Poznámka |
|------|--------------------------------------|-------|----------|
| 1    | neznámá                              | čeká na výpis | základní init / status |
| 2    | neznámá                              | čeká na výpis | pravděpodobně performance counter |
| 3    | `reportPerceptibleJank()`            | Permission Denial | hlášení lagů / frame dropů |
| 4    | `reportGameScene()`                  | Permission Denial | Game Turbo – scény / režimy |
| 5    | neznámá                              | čeká na výpis | může být monotonic time |
| 6    | `registerPerformanceListener()`      | Permission Denial | MIUI performance callback |
| 7    | `enforceNoDataAvail()`               | Permission Denial | kontrola Parcelu |
| 8    | neznámá                              | čeká na výpis | možná CPU scheduler info |
| 9    | neznámá                              | čeká na výpis | může vracet performance counters |
| 10   | neznámá                              | čeká na výpis | oneway transakce |
| 11   | neznámá                              | čeká na výpis | další performance metrika |

---

## Poznámky k mapování

- PerfShielder má **více chráněných metod** než ProcessManager  
- většina transakcí vrací `SecurityException`  
- některé transakce jsou **oneway** (bez návratové hodnoty)  
- některé transakce očekávají specifický Parcel formát  
- některé transakce vrací **monotonic time** nebo **performance counters**

---

## Viz také

- [`analysis.md`](ca://s?q=otevrit_PerfShielder_analysis) – detailní popis chování služby  
- [`parcel-dumps`](ca://s?q=otevrit_PerfShielder_parcel_dumps) – surové výpisy Parcelů
