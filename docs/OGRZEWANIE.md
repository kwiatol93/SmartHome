# Specyfikacja modułu ogrzewania

## Cel i granice modułu

Moduł ogrzewania ma być niezależny od konkretnych urządzeń. Jest przechowywany jako YAML w tym repozytorium i może zostać odtworzony po wymianie czujników, przekaźników, termostatów albo pieca.

Logika używa wyłącznie stabilnych, logicznych encji. Nazwy urządzeń ZHA, Zigbee, Tuya, `device_id` oraz konkretne encje sprzętowe są dozwolone tylko w adapterach.

```text
sprzęt wejściowy → adapter wejściowy → logiczne encje wejściowe
→ brain: profile, setpointy i decyzje → logiczne encje wyjściowe
→ adapter wyjściowy → sprzęt wykonawczy
```

## Strefy

| Strefa logiczna | Typ | Uwaga |
|---|---|---|
| Strefa dzienna | Podłogówka | Salon + kuchnia + jadalnia; jeden czujnik, setpoint i decyzja |
| Łazienka | Podłogówka | Osobna strefa, powiązana komfortowo z korytarzem |
| Korytarz | Podłogówka | Osobna strefa piętra |
| WC | Podłogówka | Komfortowo powiązane ze strefą dzienną |
| Wiatrołap | Podłogówka | Komfortowo powiązany ze strefą dzienną |
| Biuro | Grzejnik | Priorytet w dzień roboczy przy pracy z domu |
| Pokój Hani | Grzejnik | Wyższy komfort w dzień wolny i po południu |
| Sypialnia | Grzejnik | Osobna strefa |
| Poddasze | Grzejnik | Osobna strefa |

Obecny adapter strefy dziennej steruje razem dwoma fizycznymi kanałami przekaźnika `CTRL-PARTER`: **Obwód Salon** i **Obwód Kuchnia**. Jadalnia jest częścią jednego z nich.

## Tryb domu i profile

Ręczny tryb domu ma wartości `Dom` i `Wyjazd`.

| Czas | Dni robocze | Weekend / wymuszony dzień wolny |
|---|---|---|
| 06:00–15:30 | `dzien_praca_praca` | `dzien_wolny` |
| 15:30–20:00 | `dzien_praca_popoludnie` | `dzien_wolny` |
| 20:00–06:00 | `noc` | `noc` |

Przy `Wyjeździe` aktywny jest stale profil `wyjazd`.

Priorytet wyboru profilu: `Wyjazd` → `Noc` → wymuszony dzień wolny → kalendarz tygodnia.

### Wymuszony dzień wolny

Wymuszenie jest nakładką na harmonogram dni roboczych. Działa w godzinach dziennych; noc i wyjazd mają wyższy priorytet.

Dashboard udostępnia formularz wyboru czasu: do końca dnia, 1–5 dni albo do ręcznego wyłączenia. Przycisk **Anuluj wymuszenie dnia wolnego** od razu wybiera profil właściwy dla dnia tygodnia i godziny oraz stosuje jego domyślne setpointy.

Na start nie ma automatycznej integracji z kalendarzem świąt.

## Setpointy

Każda strefa ma temperaturę logicznego wejścia, aktualny ręcznie edytowalny setpoint, domyślny setpoint aktywnego profilu, status zapotrzebowania, logiczne polecenie wyjściowe oraz informację o faktycznym stanie urządzenia.

Zmiana profilu albo trybu `Dom ↔ Wyjazd` od razu wpisuje domyślny setpoint nowego profilu do aktualnych setpointów. Ręczna zmiana suwakiem obowiązuje do następnej zmiany profilu albo użycia resetu.

Dashboard zawiera reset pojedynczej strefy oraz reset wszystkich setpointów do aktywnego profilu. Podłogówka dostaje nowy setpoint od razu; nie stosujemy sztucznego rampowania, ponieważ instalacja reaguje wolno sama z siebie.

## Setpointy profili

### Podłogówka

| Strefa | Praca | Popołudnie | Dzień wolny | Noc | Wyjazd |
|---|---:|---:|---:|---:|---:|
| Strefa dzienna | 22,9 | 22,9 | 22,9 | 22,65 | 20,0 |
| Łazienka — baza | 23,5 | 23,5 | 23,5 | 23,2 | 20,0 |
| Korytarz | 22,8 | 22,8 | 22,8 | 22,5 | 20,0 |
| WC — minimum | 22,1 | 22,1 | 22,1 | 22,0 | 19,0 |
| Wiatrołap — minimum | 19,5 | 19,5 | 19,5 | 19,5 | 17,0 |

### Grzejniki

| Strefa | Praca | Popołudnie | Dzień wolny | Noc | Wyjazd |
|---|---:|---:|---:|---:|---:|
| Biuro | 22,8 | 21,5 | 21,5 | 20,8 | 17,0 |
| Pokój Hani | 21,3 | 21,7 | 21,7 | 20,8 | 17,0 |
| Sypialnia | 21,3 | 21,8 | 21,8 | 20,8 | 17,0 |
| Poddasze | 19,9 | 19,9 | 19,9 | 19,5 | 16,0 |

## Nakładki i powiązania stref

### Łazienka

Automatyczne wieczorne dogrzanie działa codziennie w trybie `Dom`, od 18:30 do 20:00. Ustawia efektywny setpoint łazienki na `25,0°C`.

Dashboard udostępnia **Dogrzewanie łazienki**: ręczne wymuszenie `25,0°C` na 60, 90 albo 120 minut. Działa wyłącznie w trybie `Dom`; kolejne uruchomienie zastępuje poprzedni timer.

Po zakończeniu automatycznej albo ręcznej nakładki brain wraca do aktualnego setpointu bazowego, również jeśli profil zmienił się podczas działania timera.

Łazienka ma silne powiązanie z korytarzem:

```text
zapotrzebowanie łazienki = własne zapotrzebowanie łazienki
                          LUB zapotrzebowanie korytarza
```

Ma to utrzymywać komfort ciepłej podłogi na piętrze.

### WC i wiatrołap

W dzień, gdy strefa dzienna grzeje, WC może dogrzać się do `23,7°C`, a wiatrołap do `20,2°C`. Poza tym stosują własne minima z tabeli. W nocy WC zachowuje minimum `22,0°C`; wiatrołap zachowuje istniejącą zależność od strefy dziennej i minimum `19,5°C`.

## Algorytm stref

Nie stosujemy histerezy:

```text
temperatura < setpoint  → grzanie włączone
temperatura ≥ setpoint  → grzanie wyłączone
```

Po każdej zmianie stanu obowiązuje minimalny czas 3 minut: po włączeniu strefa grzeje co najmniej 3 minuty, a po wyłączeniu pozostaje wyłączona co najmniej 3 minuty. Awaryjne wyłączenie omija ten limit.

Brain reaguje natychmiast na zmianę temperatury, profilu, setpointu lub trybu domu. Dodatkowo co 5 minut wykonuje rekonsyliację: porównuje stan żądany z faktycznym stanem urządzenia i ponawia polecenie tylko w razie rozjazdu.

Po trzech nieskutecznych próbach, wykonywanych w odstępach pięciu minut, system tworzy trwały alert w HA i wysyła push na telefon. SMS nie jest na razie wdrażany.

Gdy czujnik strefy jest `unknown` lub `unavailable`, system przez 15 minut zachowuje ostatnią decyzję. Potem wyłącza tylko tę strefę i zgłasza problem.

## Piec, pompa i krzywa grzewcza

Piec pracuje w trybie `auto`, jeżeli co najmniej jedna strefa zgłasza zapotrzebowanie. Gdy żadna nie zgłasza, piec jest wyłączany.

Pompa obiegu podłogówki pozostaje regułą adaptera hydraulicznego:

```text
zapotrzebowanie obiegu → włącz siłownik/siłowniki
                       → odczekaj 3 minuty na przepływ
                       → włącz pompę
```

Zapobiega to pompowaniu wody przy zamkniętych pętlach. Dokładne przypisanie stref do obiegów pozostaje w adapterze obecnego domu.

Krzywa grzewcza jest osobnym modułem i zachowuje obecną regułę:

```text
temperatura zewnętrzna > 12°C → 38°C zasilania
w przeciwnym razie            → 38 + floor((14 - temperatura_zewnętrzna) / 2)
                                 maksimum 55°C
```

Jeżeli temperatura zewnętrzna nie jest dostępna, piec zachowuje ostatnią prawidłowo wyliczoną temperaturę zasilania, a system wysyła alert. Dashboard pokazuje temperaturę zewnętrzną, temperaturę zasilania oraz wykres krzywej grzewczej.

## Bezpieczniki, restart i dashboard

Przełącznik **Automatyka ogrzewania aktywna** blokuje przekazywanie nowych poleceń do urządzeń, ale nie zmienia aktualnych setpointów ani stanów urządzeń.

Osobny przycisk **Awaryjnie wyłącz ogrzewanie** natychmiast wyłącza wszystkie wyjścia, niezależnie od minimalnego czasu stanu.

Po restarcie HA system porównuje aktualny profil z ostatnio zastosowanym:

- ten sam profil: zachowuje ręcznie zmienione setpointy;
- inny profil: stosuje domyślne setpointy aktualnego profilu;
- następnie rekonsyliuje rzeczywiste wyjścia.

Powstaje osobny dashboard **Ogrzewanie**. Główny dashboard ma wyłącznie skrót: tryb domu, aktywny profil, globalny status i błędy.

## Git i wdrożenie

Repozytorium jest klonowane na HA jako osobny katalog:

```text
/config/
  configuration.yaml
  smarthome/
    config/packages/heating.yaml
    config/heating/
```

Główny `configuration.yaml` HA ładuje pakiety z repozytorium. Repo nie zastępuje całego `/config` i nie zawiera sekretów, `.storage`, bazy danych, backupów, logów, mediów ani certyfikatów.

## Migracja

1. Obecna automatyzacja pozostaje aktywna jako punkt odniesienia.
2. Nowy brain działa równolegle w trybie obserwacyjnym i nie steruje sprzętem.
3. Porównujemy decyzje nowego i starego systemu w pełnym cyklu dnia oraz nocy.
4. Przełączamy pojedyncze strefy na nowe adaptery wyjściowe.
5. Stare automatyzacje usuwamy dopiero po potwierdzeniu wszystkich stref.
