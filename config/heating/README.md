# Pakiet ogrzewania

Aktualny podział plików:

```text
../packages/heating.yaml           punkt wejścia pakietu HA
helpers/                           przełączniki, suwaki, przyciski i timer
templates/00_profile.yaml          wybór profilu i wyliczenia niezależne od sprzętu
templates/10_demand.yaml           zapotrzebowanie stref w trybie obserwacji
templates/90_adapter_current_house.yaml
                                   mapowanie fizycznych czujników na wejścia logiczne
```

Moduł nie zawiera adaptera wyjściowego ani automatyzacji wykonawczych, więc
działa wyłącznie jako bezpieczny tryb obserwacji. Kolejny krok doda minimalny
czas 3 minut, rekonsyliację co 5 minut oraz logiczne polecenia wyjściowe.
