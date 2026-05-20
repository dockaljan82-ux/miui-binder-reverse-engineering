# ProcessManager – Transaction Map

Mapa transakčních kódů MIUI ProcessManager služby (miui.process.IProcessManager) zjištěná black‑box analýzou přes `service call` a SecurityException stacktrace.

| CODE | Java metoda (podle stacktrace)           | Stav z pohledu shellu | Poznámka |
|------|------------------------------------------|------------------------|---------|
| 1    | neznámá                                  | Not a data message     | pravděpodobně jiný formát Parcelu / oneway |
| 2    | neznámá                                  | OK, vrací `00000000`   | nějaký status / init |
| 3    | `updateApplicationLockedState()`         | Permission Denial      | MIUI AppLock / zamčení aplikace v paměti |
| 4    | (getPackageList / whitelist)            | OK, vrací data         | seznam chráněných balíčků (např. `com.xiaomi.bsgamecenter`, `com.jeejen.family.miui`) |
| 5    | `updateConfig()`                         | Permission Denial      | změna konfigurace správce procesů |
| 6    | neznámá                                  | crash při unmarshalling| typový nesoulad argumentů |
| 7    | `protectCurrentProcess()`                | Permission Denial      | ochrana aktuálního procesu proti OOM killeru |
| 8    | neznámá                                  | crash (velikost pole)  | moje data interpretována jako velikost pole, překročení Binder limitu |
| 12   | (stav procesů – obdoba `ps`)            | OK, vrací data         | vrací např. `com.termux`, `com.miui.home` + PID/UID/tokeny |
| 13   | `addMiuiApplicationThread()`             | Permission Denial      | registrace vlákna do MIUI plánovače výkonu |
| 14   | `getForegroundApplicationThread()`       | Permission Denial      | Binder reference na app v popředí |
| 15   | `registerActivityChangeListener()`       | Permission Denial      | posluchač změn aktivit |
| 16   | `unregisterActivityChangeListener()`     | Permission Denial      | odregistrování posluchače |
| 17   | `getRunningProcessInfo()`                | Permission Denial      | detailní info o běžících procesech |
| 18   | `getActiveUidInfo()`                     | Permission Denial      | stav aktivních UID (foreground/background) |
| 19   | `registerForegroundWindowListener()`     | Permission Denial      | sledování oken v popředí (Game režim) |
| 20   | `unregisterForegroundWindowListener()`   | Permission Denial      | zrušení sledování oken |
| 21   | neznámá                                  | OK, vrací `000001f4`   | pravděpodobně error/status code (500) |
| 22   | neznámá (IPreloadCallback v textu)       | OK, vrací text         | souvislost s preload callbackem |
| 23   | neznámá                                  | OK, vrací `00000000`   | status / no-op |
| 25   | neznámá                                  | OK, vrací `fffffe18`   | negativní error code |
| 26   | neznámá                                  | OK, vrací `00000000`   | status |
| 28   | `reportGameScene`                        | OK, vrací data         | herní režim / Game Turbo |
| 29   | neznámá                                  | OK, vrací `00000000`   | status |
| 30   | varianta `updateConfig`                  | Permission Denial      | jiná větev konfigurace |

> Poznámka: mapování je získané čistě black‑box metodou (service call + analýza chyb), bez přístupu k AIDL.
