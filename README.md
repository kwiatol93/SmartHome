# SmartHome

Wersjonowane źródło konfiguracji Home Assistanta: automatyzacji, dashboardów, pakietów, encji pomocniczych i dokumentacji.

Repozytorium ma obejmować różne obszary domu. Ogrzewanie jest pierwszym projektowanym modułem, a nie jedynym celem projektu. Konfiguracja produkcyjna Home Assistanta działa obecnie niezależnie; nowe moduły będą wdrażane etapami.

## Zasady

- Logika automatyzacji używa stałych, logicznych encji, a nie `device_id` ani nazw konkretnych urządzeń.
- Mapowanie czujników i urządzeń wykonawczych jest, gdy ma to sens, izolowane w adapterach.
- Sekrety, backupy, baza danych, pliki `.storage`, logi, certyfikaty i media nie trafiają do repozytorium.
- Zmiany wdrażamy etapami: najpierw obserwacja, potem pojedyncze strefy, na końcu przełączenie całości.

## Struktura

```text
docs/                         dokumentacja architektury i decyzji
config/
  configuration.yaml.example  minimalny punkt wejścia Home Assistant
  packages/
    heating/                  moduł ogrzewania
    .../                      kolejne moduły automatyzacji
  dashboards/                 dashboardy Home Assistant
```

## Status

Projekt jest w fazie projektowej. Pierwsze założenia modułu ogrzewania: [docs/OGRZEWANIE.md](docs/OGRZEWANIE.md).
