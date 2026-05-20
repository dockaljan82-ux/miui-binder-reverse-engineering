# MIUI / HyperOS Binder Reverse Engineering  
Reverse‑engineering interních Binder služeb Xiaomi (ProcessManager, PerfShielder) pomocí black‑box analýzy, service call brute‑force a dekódování Parcel struktur.

Tento projekt dokumentuje:
- transakční kódy (Transaction Codes)
- názvy interních Java metod (získané přes SecurityException stacktrace)
- struktury Parcelů
- chování MIUI scheduleru, AppLocku a Game Turbo
- rozdíly mezi AOSP a MIUI implementací Binder služeb

## Obsah
- **ProcessManager**
  - updateApplicationLockedState
  - protectCurrentProcess
  - getRunningProcessInfo
  - getActiveUidInfo
  - getForegroundApplicationThread
  - a další

- **PerfShielder**
  - performance counters
  - monotonic timers
  - enforceNoDataAvail behavior

## Metodologie
- brute‑force Binder transakcí (`service call`)
- analýza SecurityException stacktrace
- dekódování Parcel formátu (UTF‑16 strings, int arrays, flags)
- porovnání s chováním systému (PID/UID změny, foreground app, whitelisty)

## Struktura
- `/processmanager` – mapa transakcí, raw Parcel výstupy, analýza
- `/perfshielder` – mapa transakcí, raw Parcel výstupy, analýza
- `/scripts` – automatizované brute‑force skripty

## Licence
MIT License

## Autor
Jan Dočkal (Česká republika) – výzkum MIUI internals na zařízení Xiaomi 14 (houji)
