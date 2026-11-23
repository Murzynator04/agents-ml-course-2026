# Dane do pierwszego uruchomienia

## Pola pliku CSV

| Pole | Znaczenie |
|---|---|
| `message_id` | Identyfikator wiadomości w tej paczce. Wskazuje plik `raw/<message_id>.eml`. |
| `text` | Zdekodowany temat (`Subject:`), pusty wiersz i pełna treść wiadomości. |
| `label` | Etykieta wiadomości. W paczce początkowej pochodzi ze zbioru źródłowego i przyjmuje wartość `spam` lub `ham` (zwykła wiadomość e-mail). |

Skrypt odczytuje etykietę zapisaną w danych. Ustalanie etykiety na podstawie pojedynczego słowa może prowadzić do błędów, ponieważ zwykła wiadomość e-mail może zawierać stopkę reklamową. Jeden rekord CSV może zajmować kilka wierszy, ponieważ w polu `text` zachowano podział treści na akapity.

## Źródła i przygotowanie danych

Dane przygotowano 2026-10-22 na podstawie archiwów [20030228_easy_ham.tar.bz2](https://spamassassin.apache.org/old/publiccorpus/20030228_easy_ham.tar.bz2) i [20030228_spam.tar.bz2](https://spamassassin.apache.org/old/publiccorpus/20030228_spam.tar.bz2). Oba archiwa pochodzą z [publicznego korpusu Apache SpamAssassin](https://spamassassin.apache.org/old/publiccorpus/). Przygotowane pliki dołączono do lokalnej paczki projektu.

Do ręcznego przeglądu wybrano krótkie, jednoczęściowe wiadomości typu `text/plain`. Próbka pochodzi z doboru celowego. Na potrzeby treningu lub oceny filtra należy ją traktować jako niereprezentatywną. Pliki `demo.csv` i `exercise.csv` zawierają różne wiadomości. Historyczny korpus odzwierciedla pocztę elektroniczną z okresu, w którym go zgromadzono.

### Przekształcenia danych

1. Wybrane pliki z archiwów skopiowano bajt w bajt do katalogu `raw/` i nadano im nazwy zgodne z polem `message_id`. Są to oryginały **z opublikowanego korpusu**. Jego wydawca wcześniej zmienił niektóre adresy i nazwy hostów.
2. Wiadomości odczytano za pomocą parsera `email` z biblioteki standardowej Pythona z ustawieniem `policy.default`. Zdekodowano tematy i treść wiadomości z uwzględnieniem kodowania transferowego treści i zadeklarowanego kodowania znaków. W razie braku deklaracji kodowania znaków przyjęto ASCII. Zakończenia wierszy ujednolicono do LF. Zachowano pełną treść, w tym cytaty i stopki.
3. Pole `text` zawiera wiersz `Subject: <subject>`, dwa znaki nowego wiersza i treść wiadomości. Pozostałe nagłówki są dostępne w oryginalnym pliku `.eml`.
4. Etykiety przypisano według katalogów w archiwach (`easy_ham` → `ham`, `spam` → `spam`). Wiadomościom nadano identyfikatory obowiązujące w tej paczce. Pliki CSV zapisano w kodowaniu UTF-8 z zakończeniami wierszy LF.

Plik `manifest.json` zawiera adresy URL i sumy kontrolne SHA-256 archiwów, listę wybranych plików z archiwów wraz ze ścieżkami do plików `.eml`, a także sumy kontrolne oryginalnych wiadomości i zestawów CSV. Manifest pozwala odtworzyć wybór wiadomości i zweryfikować go na podstawie tych samych archiwów. Przy określaniu cech wejściowych przyszłego modelu pomija się nazwy plików, identyfikatory i etykiety.

Suma kontrolna `exercise.csv` w manifeście dotyczy wersji z paczki początkowej. Sumę pliku po zmianie jednej etykiety zawiera [raport z laboratorium](../results/exercise-summary.json).

## Zasady korzystania

Prawa autorskie do treści wiadomości pozostają przy ich pierwotnych nadawcach. Licencja SpamAssassin dotyczy oprogramowania. Wydawca korpusu wyklucza przesyłanie tych wiadomości przez działające systemy pocztowe. Zob. [opis korpusu](https://spamassassin.apache.org/old/publiccorpus/readme.html).

W projekcie pliki odczytuje się jako tekst i pomija odnośniki zawarte w wiadomościach.
