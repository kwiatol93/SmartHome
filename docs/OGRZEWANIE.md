# Założenia nowego systemu ogrzewania

## Cel

Sterowanie ogrzewaniem ma być niezależne od konkretnego sprzętu, zapisane w YAML i wersjonowane w prywatnym GitHubie. Po wymianie urządzenia zmieniamy tylko adapter sprzętowy.

```text
sprzęt wejściowy → adapter wejściowy → logiczne wejścia
→ brain (profile, setpointy, decyzja) → logiczne wyjścia
→ adapter wyjściowy → sprzęt wykonawczy
```

Brain nie może używać `device_id`, nazw Zigbee/ZHA/Tuya ani konkretnych encji sprzętowych.

## Strefy

| Strefa | Typ | Uwagi |
|---|---|---|
| Strefa dzienna | Podłogówka | Salon + kuchnia + jadalnia, jeden setpoint; dwa obecne kanały wykonawcze: salon i kuchnia |
| Łazienka | Podłogówka | Wieczorne dogrzanie przed kąpielą |
| Korytarz | Podłogówka | Osobna strefa |
| WC | Podłogówka | Powiązane komfortowo ze strefą dzienną |
| Wiatrołap | Podłogówka | Powiązany komfortowo ze strefą dzienną |
| Biuro | Grzejnik | Priorytet w dzień roboczy przy pracy z domu |
| Pokój Hani | Grzejnik | Wyższy komfort w dzień wolny |
| Sypialnia | Grzejnik | Osobna strefa |
| Poddasze | Grzejnik | Osobna strefa |

## Profile i tryby

Ręczny tryb domu: `Dom` albo `Wyjazd`.

| Warunek | Profil |
|---|---|
| Wyjazd | `wyjazd` |
| Godziny nocne | `noc` |
| Godziny dzienne + wymuszenie dnia wolnego | `dzien_wolny` |
| Dzień roboczy | `dzien_praca` |
| Weekend / dzień wolny | `dzien_wolny` |

Priorytet: `Wyjazd` → `Noc` → ręczne wymuszenie dnia wolnego → kalendarz tygodnia.

## Setpointy i dashboard

Każda strefa ma:

- aktualny setpoint edytowalny suwakiem;
- domyślny setpoint aktywnego profilu w YAML;
- status zapotrzebowania na ciepło;
- logiczne polecenie wyjściowe.

Zmiana profilu albo `Dom ↔ Wyjazd` wpisuje domyślne setpointy nowego profilu. Ręczna zmiana suwakiem działa do kolejnej zmiany profilu lub użycia resetu. Dashboard ma przycisk resetu pojedynczej strefy i resetu wszystkich stref.

## Podłogówka i grzejniki

Podłogówka ma stabilne temperatury: różnice dzień/noc są małe, a wyjazd i powrót następują łagodnie. Brain użyje histerezy i minimalnych czasów pracy/postoju.

Grzejniki mogą mieć większe różnice między profilami oraz szybszą reakcję, nadal z histerezą.

## Reguły komfortu do zachowania

- W dzień roboczy biuro jest główną ogrzewaną strefą dzienną.
- W dzień wolny biuro grzeje słabiej, a pokój Hani mocniej.
- Łazienka rozpoczyna dogrzanie około 18:30, by osiągnąć komfort podłogi około 19:30–20:00.
- Gdy grzeje strefa dzienna, WC i wiatrołap mogą być dogrzewane do ograniczonych wartości komfortowych, z zachowaniem własnych temperatur minimalnych.

## Migracja

1. Obecna automatyzacja pozostaje punktem odniesienia.
2. Nowy brain najpierw działa obserwacyjnie i nie steruje sprzętem.
3. Porównujemy jego decyzje z obecnym systemem.
4. Przełączamy strefy pojedynczo.
5. Stare automatyzacje usuwamy po potwierdzeniu działania wszystkich stref.
