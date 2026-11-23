# Laboratorium 1 — pierwsze uruchomienie projektu z agentem

**Repozytorium GitHub** — [Murzynator04/agents-ml-course-2026](https://github.com/Murzynator04/agents-ml-course-2026).  
**Aplikacja i ustawienia** — Codex Desktop · `gpt-6.2-sol` · `ultra` (identyfikatory z metadanych sesji).

## 1. Zakres zlecenia

Agent miał uruchomić `describe_data.py` na `data/exercise.csv` i zapisać podsumowanie w `results/exercise-summary.json`. W pierwszym etapie zakres obejmował uruchomienie skryptu, odczyt raportu i wykonanie gotowego testu. Kod, dane i testy miały pozostać bez zmian. W drugim etapie dopuszczona była zmiana jednej etykiety z `ham` na `spam`, a po sprawdzeniu tej zmiany ponowne wygenerowanie raportu.

Do odbioru pracy potrzebne były ścieżki wejścia i wyjścia, rzeczywiste polecenie i kod zakończenia procesu, treść raportu, wynik testu oraz informacja o ewentualnych niewykonanych krokach.

## 2. Uruchomienie i odbiór raportu

Polecenia wykonało narzędzie terminalowe Codexa (`exec_command`, PowerShell) na lokalnym komputerze z systemem Windows. Katalog roboczy to `work/lab1/project`. Polecenie `py -3 --version` zwróciło `Python 3.14.7`. Model wybrał polecenie, aplikacja przekazała je do terminala, a interpreter Pythona odczytał CSV i zapisał JSON. Wynik procesu wrócił przez narzędzie do agenta.

```powershell
py -3 describe_data.py --data data/exercise.csv --out results/exercise-summary.json
```

Przed pierwszym uruchomieniem plik wynikowy jeszcze nie istniał. Skrypt zakończył się kodem `0` i podał miejsce zapisu. Następnie odczytano JSON, porównano jego `input_file` z pełną ścieżką pliku wejściowego i sprawdzono `input_sha256` względem sumy obliczonej dla CSV. Pierwszy raport zawierał 8 wiadomości, w tym 5 `ham` i 3 `spam`. Końcowy raport po drugim uruchomieniu zawiera 8 wiadomości, w tym 4 `ham` i 4 `spam`. Liczby oznaczają rekordy z poszczególnymi etykietami przypisanymi w danych. [Końcowy raport JSON](project/results/exercise-summary.json)

## 3. Rekord CSV a wiersze treści wiadomości

Wybrany fragment dotyczy struktury rekordów CSV. W `exercise.csv` sprawdzono rekord `exercise-001`. Pole `message_id` identyfikuje wiadomość i pozwala powiązać ją z oryginałem `data/raw/exercise-001.eml`. Pole `text` zawiera temat i treść, które w tym rekordzie zajmują łącznie 22 wiersze. W polu `label` pierwotnie zapisano `ham`. [Polska instrukcja projektu](project/README.md#pliki-projektu) · [Oryginalny fragment odczytany w sesji](https://github.com/Murzynator04/agents-ml-course-2026/blob/d108784e3bfe944fb1d1b7772a74719327e13cd6/lab1/project/README.md#files)

**Własne wyjaśnienie i przewidywanie.** Jeden rekord CSV odpowiada jednej wiadomości e-mail. Gdy plik ma dwa rekordy, a pierwsza wiadomość zawiera trzy wiersze tekstu, skrypt powinien policzyć dwie wiadomości, a nie cztery.

Po zapisaniu przewidywania przygotowano osobny plik CSV z danymi syntetycznymi poza katalogiem `project/`. Pierwszy rekord zawierał trzy wiersze tekstu i etykietę `ham`, a drugi jeden wiersz i etykietę `spam`. Polecenie wykonano z katalogu `project/`.

```powershell
py -3 describe_data.py --data ../proba-dwa-rekordy/wiadomosci.csv --out ../proba-dwa-rekordy/podsumowanie.json
```

Proces zwrócił kod `0`, a odczyt raportu próby potwierdził 2 wiadomości, w tym 1 `ham` i 1 `spam`. Parser zachował trzy wiersze wewnątrz jednego pola `text` ujętego w cudzysłów. Próba korzystała z własnego wejścia i wyjścia.

## 4. Gotowy test

```powershell
py -3 -m unittest discover -s tests -v
```

Test `test_cli_writes_expected_counts` zakończył się kodem `0`. Komunikaty `Ran 1 test` i `OK` oznaczają, że wykonano jeden test i przeszedł on pomyślnie. Test sprawdził kod zakończenia skryptu, zapis i odczyt JSON, ścieżkę wejścia oraz ustalone wcześniej liczebności. Dane w `tests/fixtures/messages.csv` obejmują trzy syntetyczne wiadomości o wielowierszowej treści, w tym 2 `ham` i 1 `spam`. Wynik dotyczy tej próbki i raportu zapisanego w katalogu tymczasowym. Raport z `exercise.csv` zweryfikowano osobno na podstawie danych wejściowych i treści zapisanego pliku.

## 5. Zmiana etykiety i drugi raport

W `data/exercise.csv` zmieniono `exercise-001` z `ham` na `spam` na potrzeby ćwiczenia. Porównanie rekordów potwierdziło, że była to jedyna zmiana pola. Po samej edycji JSON był bajtowo identyczny z pierwszym raportem, a suma SHA-256 aktualnego CSV już się różniła. Zapisany raport opisywał więc poprzednią wersję danych.

Przed drugim uruchomieniem agent przewidział spadek liczby `ham` o 1, wzrost liczby `spam` o 1 i tę samą liczbę wiadomości ogółem. Ponownie wykonano polecenie z punktu 2, aby przeliczyć bieżące dane. Następnie odczytano JSON i porównano jego ścieżkę wejściową, sumę SHA-256 oraz liczebności z aktualnym CSV. Proces zakończył się kodem `0`. Nowy raport zawierał sumę SHA-256 aktualnego pliku i potwierdził przewidywanie.

| Liczebność | Przed zmianą | Przewidywanie | Po ponownym uruchomieniu |
| --- | ---: | ---: | ---: |
| `ham` | 5 | 4 | 4 |
| `spam` | 3 | 4 | 4 |
| Ogółem | 8 | 8 | 8 |

## Zapisy sesji

Treść raportów z obu etapów, wywołania narzędzi, wyniki procesów i sprawdzenie własnego przewidywania są w [głównej sesji Codexa](sesje/sesja-1.jsonl). Sesje pomocnicze dokumentują [lekturę wprowadzenia](sesje/sesja-2.jsonl), [sprawdzenie wymagań i wyników](sesje/sesja-3.jsonl), [końcowe porównanie z wymaganiami](sesje/sesja-4.jsonl) i [przegląd poufności](sesje/sesja-5.jsonl).

W kopiach sesji zamaskowano dane prywatne i metadane techniczne Codexa. Zachowano chronologię, treść pracy i pary wywołań z wynikami. Instrukcje i materiały źródłowe są dostępne na [stronie zadania Lab 1](https://agents-ml-course-2026.czuba-przemyslaw.chatgpt.site/lab-1.html).
