# Działanie DNS i punkty ataku

Sprawozdanie krótko tłumaczy działanie protokołu `DNS` **(Domain Name System)** i wskazuje na punkty w których może on zostać zaatakowany.

## 1. Lista dokumentów RFC definiujących działanie DNS

Dokumenty `RFC` **(Request for Comments)** to seria ponumerowanych dokumentów publikowanych przez `IETF` **(Internet Engineering Task Force)**. Dokumenty te opisują standardy, protokoły (np. `HTTP`, `TCP/IP`), formaty danych i technologie sieciowe.<br>
Dokumenty dotyczące standardów `DNS`:
- `RFC` **1034**: Ten Dokument wprowadza koncepcje i architekturę systemu nazw domenowych.
- `RFC` **1035**: Zawiera specyfikację techniczną formatu komunikatów DNS oraz zasad przesyłania danych przez UDP i TCP.
- `RFC` **1122**: Definiuje wymagania dotyczące hostów internetowych, w warstwach komunikacyjnych (Łącza danych, IP i Transportowa).
- `RFC` **1123**: Definiuje wymagania dotyczące hostów internetowych, w warstwie aplikacji.
- `RFC` **2181**: Wprowadza poprawki do oryginalnej specyfikacji, eliminując niejasności, które mogły powstać na podstawie wcześniejszych dokumentów.

## 2. Schemat typowego przebiegu procesu rozwiązywania nazw

1. **Klient** -> **Resolver**: "Chcę wejść na stronę `example.com`" (Zapytanie Rekurencyjne).

2. **Resolver** -> **Serwer Root**: "Kto obsługuje końcówkę `.com`?" (Zapytanie Iteracyjne).

3. **Resolver** <- **Serwer Root**: "Oto adresy serwerów TLD dla `.com`".

4. **Resolver** -> **Serwer TLD**: "Kto obsługuje domenę example.com?" (Zapytanie Iteracyjne).

5. **Resolver** <- **Serwer TLD**: "Oto adresy serwerów Autorytatywnych dla `example.com`".

6. **Resolver** -> **Serwer Autorytatywny**: "Daj mi adres IP dla `www.example.com`" (Zapytanie Iteracyjne).

7. **Resolver** <- **Serwer Autorytatywny**: "To jest IP: `93.184.216.34`".

8. **Resolver** -> **Klient**: "Oto Twój adres IP: `93.184.216.34`" (Odpowiedź Końcowa).

**Schemat:** <br>
 ![alt text](https://github.com/Mchsmo/Cyberbezpieczenstwo_Zadania/blob/zad_DNS/images/DiagramDNS.png)

 ## 3. Punkty w których można ingerować w rozwiązywanie nazw

1. **Plik systemowy** `hosts` - Jest to pierwszy punkt, który jest sprawdzany przed wysłaniem zapytania o adres.<br> Plik, który zależnie od systemu operacyjnego, może się znajdować w `/etc/hosts` (Linux/macOS) lub `C:\Windows\System32\drivers\etc\hosts` (Windows). <br>
 zawiera on w sobie pary **[adres IP - adres strony]** np. `127.0.0.1` google.com.

2. **Pamięć Podręczna Resolvera** - Jeśli resolver otrzyma fałszywą odpowiedź, z miejsca innego niż serwer autorytatywny, to będzie on ją podawał dalej klientom przez czas trwania `TTL` (Time To Live).

3. **Zmiana Adresu Resolvera** - Zmiana adresu DNS w ustawieniach karty sieciowej lub routera na własny, kontrolowany serwer. Zamiast np. do Google (`8.8.8.8`), zapytania trafiają do serwera, który dla każdej domeny może zwrócić dowolny adres IP.

4. **Przejęcie Serwera Autorytatywnego** - Podmiot kontrolujący serwer autoratywny ma możliwość kierowania ruchu i zmiany adresu IP w rekordach.

 ## 4. Jak można zapobiegać ingerencji

 1. **Ochrona Pliku** `hosts`:
 - Ograniczenie dostępu do konta z uprawnieniami administratora.
 - Oprogramowanie antywirusowe, które kontroluje zmiany wykonane w pliku.
 - Rutynowa weryfikacja, czy nie doszło do zmian w pliku.
 2. **Zapobieganie "zatruwaniu" cache Resolvera** (`DNSSEC`): 
 - **Serwer autorytatywny** podpisuje swoje rekordy cyfrowym kluczem. **Resolver**, otrzymując odpowiedź, sprawdza **podpis**. Jeśli dane zostały zmienione w locie **(zatrute)**, podpis będzie nieważny, a **resolver** odrzuci taką odpowiedź.
 3. **Zabezpieczenie routera i sieci lokalnej**:
 - Zabezpieczenie urządzeń sieci bezpiecznymi hasłami.
 - Manualne ustawienie adresów zaufanych adresów `DNS`.
 4. **Zabezpieczenie serwera autorytatywnego**:
 - Wdrożenie `DNSSEC` - Nawet jeśli zapytanie zostanie przechwycone nie będzie można podrobić odpowiedzi bez zdobycia klucza prywatnego.

 ## 5. Ataki na protokół DNS

 - **Cache Poisoning** - Atak polega na wprowadzeniu fałszywych informacji do pamięci podręcznej serwera `DNS`. Kiedy do serwera wysłane jest zapytanie o adres danej strony, wysyłany jest fałszywy adres z pamięci cache. Głównym sposobem obrony jest wykorzystanie `DNSSEC`.

- **Snooping** - Domyślnie zapytania `DNS` przesyłane są w formie niezaszyfrowanego tekstu, co oznacza, że każda osoba podłączona do sieci jest w stanie zobaczyć z jakimi stronami internetowymi chcesz się połączyć. Głównym rozwiązaniem jest wdrożenie `DoH` **(DNS over HTTPS)** lub `DoT` **(DNS over TLS)**, które szyfrują ten konkretny odcinek drogi.

 - **Ataki DDoS** - Ataki `Distrbuted Denial of Service` skierowane w serwery `DNS` opierają się na wysyłaniu dużej ilości zapytań, co prowadzi do przeciążania serwera i odcięcia użytkowników od zasobów.

 - **DNS hijacking** - Atak polega na przejęciu kontroli nad ustawieniami `DNS` domeny, co pozwala na przekierowanie ruchu. Atak taki jest często spowodowany przez włamanie na konto rejestratora strony. Głównym sposobem zapobiegania jest `Registry Lock` czyli blokada zmian u rejestratora bez wykonania wieloetapowej procedury uwierzytelniającej.

 - **DNS tunneling** - technika polegająca na wysyłaniu danych przez protokół `DNS`. Ataki takie stosuje w celu obejścia zabezpieczeń i zapór sieciowych. Dane które są zakodowane w zapytaniach i odpowiedziach `DNS` są łatwe do przeoczenia i mogą zostać wykorzystane np. do zdalnego sterowania złośliwym oprogramowaniem.

### Źródła
- https://nflo.pl/slownik/atak-na-dns/
- https://www.cloudflare.com/pl-pl/learning/dns/what-is-dns/
- https://controld.com/blog/dns-internet-weakest-link/
- https://learn.microsoft.com/pl-pl/windows/win32/dns/dns-standards-documents
- https://www.ssl.com/pl/FAQ/co-to-jest-dns-ponad-https-doh/
- http://dns.pl/pl_registry_lock