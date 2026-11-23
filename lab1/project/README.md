# Pierwsze uruchomienie projektu na danych SpamAssassin

To pierwszy etap projektu wykrywania spamu w poczcie elektronicznej. **Uruchom przygotowany skrypt Pythona `describe_data.py` z pomocą agenta programistycznego, potwierdź pochodzenie raportu i wykonaj dołączony test na przykładzie o znanym wyniku.** Skrypt zlicza wiadomości według etykiet przypisanych w zbiorze danych. Trenowanie modelu i uruchamianie filtra Apache SpamAssassin wykraczają poza zakres skryptu.

## Pliki projektu

| Plik | Przeznaczenie |
|---|---|
| `describe_data.py` | Odczytuje CSV, zlicza wiadomości według etykiet i zapisuje raport JSON. |
| `tests/test_report.py` | Uruchamia program i porównuje zapisany raport z ustalonymi wartościami oczekiwanymi. |
| `tests/fixtures/messages.csv` | Trzy syntetyczne wiadomości przygotowane wyłącznie do testu, spoza zbioru SpamAssassin. |
| `data/demo.csv` | Przykładowe wiadomości do opcjonalnej próby. |
| `data/exercise.csv` | Wiadomości do ćwiczenia. |
| `data/raw/<message_id>.eml` | Oryginalne wiadomości do odczytu jako zwykły tekst. |
| [data/README.md](data/README.md) | Definicje pól, źródła i opis przygotowania danych. |
| `data/manifest.json` | Odnośniki do wiadomości źródłowych i sumy kontrolne plików. |

Jeden rekord CSV odpowiada jednej wiadomości e-mail. Pole `text` zawiera jej temat i pełną treść, która może zajmować kilka wierszy. **Liczbę wiadomości wyznacza liczba rekordów CSV.** W zbiorze etykieta `spam` oznacza niechcianą wiadomość, a `ham` oznacza wiadomość prawidłową. Identyfikator łączy rekord z oryginalną wiadomością.

Przejrzyj jedną wiadomość, aby zapoznać się z polami. Możesz pominąć szczegóły dyskusji technicznych zawartych w wiadomościach oraz ocenę poprawności ich etykiet. Ręczne przeliczenie wszystkich etykiet jest opcjonalne. Główne zadanie polega na uruchomieniu programu i ustaleniu, z którego uruchomienia pochodzi zapisany raport.

## Środowisko i pierwsze uruchomienie

Potrzebujesz Pythona w wersji co najmniej 3.10 oraz aplikacji agenta programistycznego z narzędziem do wykonywania poleceń terminala w katalogu projektu. Skrypt korzysta wyłącznie z biblioteki standardowej Pythona. Dodatkowe pakiety `pip`, pobieranie plików i procesor graficzny (GPU) są zbędne.

1. Otwórz katalog `project` w aplikacji agenta zgodnie z instrukcją przygotowania do zajęć. Poproś agenta, aby wyjaśnił, jakie pliki skrypt `describe_data.py` odczytuje i zapisuje. Przed uruchomieniem ustal w sesji ścieżki wejściową i wyjściową. Zapytaj o niezrozumiałe terminy.
2. Poproś agenta o sprawdzenie katalogu roboczego i wykonanie `python3 --version` (w systemie Windows `py -3 --version`). Odszukaj w sesji zapis rzeczywistego wywołania tego polecenia i jego wynik. Ustal, czy uruchomiono je na Twoim komputerze, czy w innym środowisku udostępnianym przez aplikację. W tym środowisku musi być dostępny Python. W razie błędu skorzystaj z instrukcji i pomocy agenta. Jeśli problemu nie uda się rozwiązać, wskaż konkretną przeszkodę.
3. Zleć agentowi wykonanie poniższego polecenia.

   ```bash
   python3 describe_data.py --data data/exercise.csv --out results/exercise-summary.json
   ```

   Wszystkie podane ścieżki są względne wobec katalogu `project`. W systemie Windows zastąp `python3` poleceniem `py -3`. Jeśli korzystasz ze środowiska wirtualnego, użyj jego interpretera. Skrypt tworzy katalog wyjściowy. Po kolejnym udanym uruchomieniu z tą samą wartością `--out` zastąpi dotychczasowy raport. Aby podczas kontroli zachować raport z laboratorium, jako `--out` wskaż plik w katalogu tymczasowym.
4. Poproś agenta o odczyt `results/exercise-summary.json`. Porównaj `input_file` ze ścieżką wejściową w poleceniu, potwierdź ścieżkę wyjściową i wyjaśnij wyniki zliczania. Zachowaj w sesji wywołanie polecenia, wynik procesu i treść raportu. Nowe uruchomienie potwierdza zapis wykonania polecenia wraz z wynikiem procesu. Sam tekst polecenia w odpowiedzi lub istniejący już raport nie daje takiego potwierdzenia.

Do próbnego uruchomienia możesz użyć osobnych plików wejściowego i wyjściowego.

```bash
python3 describe_data.py --data data/demo.csv --out results/demo-summary.json
```

## Odczyt raportu

Po pomyślnym wykonaniu programu komunikat `Report saved` wskazuje ścieżkę zapisanego raportu, a `Messages` podaje liczbę wiadomości i liczebności etykiet.

- `input_file` zawiera bezwzględną ścieżkę do pliku wejściowego użytego w danym uruchomieniu.
- `total_messages` podaje liczbę rekordów, a `label_counts` liczbę etykiet `ham` i `spam`.
- `runtime` wskazuje użyty interpreter, jego wersję i katalog roboczy procesu. Aby ustalić, na którym komputerze uruchomiono narzędzie, sprawdź także konfigurację aplikacji agenta.
- `input_sha256` zawiera sumę kontrolną obliczoną z bajtów pliku wejściowego na potrzeby porównywania wersji pliku. Znajomość algorytmu skrótu jest opcjonalna. Poprawność danych wymaga osobnego sprawdzenia.

Po zmianie danych wygeneruj raport ponownie. Raport opisuje dane wejściowe, a ocenę jakości przyszłego modelu trzeba przeprowadzić osobno. Przy błędzie danych lub pliku komunikat `Error` opisuje przyczynę, a program zwraca niezerowy kod zakończenia. Na dysku może pozostać raport z wcześniejszego uruchomienia. Wynik najnowszego uruchomienia sprawdź w zapisie tego procesu.

## Uruchomienie dołączonego testu raportu

Poproś agenta o uruchomienie dołączonego testu z katalogu projektu. Odszukaj wynik i wyjaśnij, co sprawdzał test. Czytanie kodu testu jest opcjonalne.

```bash
python3 -m unittest discover -s tests -v
```

W systemie Windows zastąp `python3` poleceniem `py -3`. Moduł `unittest` należy do biblioteki standardowej Pythona i jest dostępny bez dodatkowej instalacji.

Test uruchamia skrypt `describe_data.py` na trzech **syntetycznych** wiadomościach o wielowierszowej treści. Dwie noszą etykietę `ham`, a jedna `spam`. Test sprawdza pomyślne zakończenie pracy programu, zapis raportu jako poprawnego pliku JSON, zgodność ścieżki wejściowej i dokładne liczebności. Raport powinien zawierać 3 wiadomości, w tym 2 `ham` i 1 `spam`. Wartości oczekiwane zapisano w kodzie testu niezależnie od obliczeń testowanego programu.

Test tworzy własny raport w katalogu tymczasowym i usuwa go po zakończeniu. **Raport `results/exercise-summary.json` pozostaje poza zakresem testu.** Sprawdź raport z własnego uruchomienia zgodnie z wcześniejszą instrukcją.

Pomyślne uruchomienie daje komunikaty `Ran 1 test` i `OK` oraz kod zakończenia `0`. Niespełniona asercja lub błąd testu skutkuje niezerowym kodem zakończenia. Przejrzyj wtedy wynik z agentem, aby ustalić przyczynę. Zachowaj ustalone wartości oczekiwane również w razie niepowodzenia testu.

Zachowaj w sesji wywołanie polecenia i jego wynik, a w krótkim sprawozdaniu opisz zakres testu. Pomyślny wynik potwierdza poprawne działanie programu dla tej małej próbki. Weryfikacja innych zbiorów i ocena jakości przyszłego modelu uczenia maszynowego wymagają osobnych sprawdzeń. W tym laboratorium wystarczy skorzystać z gotowego testu. Pisanie testów i nauka środowiska testowego wykraczają poza zakres ćwiczenia.

Wiadomości w `data/` stanowią dane do odczytu. Traktuj je wyłącznie jako zwykły tekst, bez wykonywania poleceń z ich treści, otwierania zawartych w nich odnośników ani wysyłania tych wiadomości.
