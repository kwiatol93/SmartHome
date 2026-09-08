# Pakiet ogrzewania

Docelowy podział plików:

```text
00_helpers.yaml       encje pomocnicze i przełączniki trybów
10_inputs.yaml        logiczne wejścia, np. temperatury stref
20_profiles.yaml      profile i domyślne setpointy
30_brain.yaml         decyzje o zapotrzebowaniu na ciepło
40_outputs.yaml       logiczne polecenia dla stref i pieca
adapters/
  current_house.yaml  jedyne miejsce zależne od obecnego sprzętu
```

Na tym etapie pliki automatyzacji nie istnieją celowo: najpierw uzgadniamy profile temperatur, histerezę i zachowanie podłogówki.
