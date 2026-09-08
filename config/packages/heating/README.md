# Pakiet ogrzewania

Aktualny podział plików:

```text
../heating.yaml                    punkt wejścia pakietu HA
helpers/                           przełączniki, suwaki, przyciski i timer
templates/00_profile.yaml          wybór profilu i wyliczenia niezależne od sprzętu
templates/90_adapter_current_house.yaml
                                   mapowanie fizycznych czujników na wejścia logiczne
```

Pierwsza wersja zawiera tylko encje i adapter wejściowy. Nie zawiera adaptera
wyjściowego ani automatyzacji wykonawczych, więc działa wyłącznie jako bezpieczny
fundament do trybu obserwacji. Kolejny krok doda brain, minimalny czas 3 minut,
rekonsyliację co 5 minut oraz logiczne polecenia wyjściowe.
