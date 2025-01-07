# Security By Design - Zadanie 4

## 1 Zadanie (opcjonalne): Trivy na lokalnie zbudowanym obrazie Dockera

**Cel:**
* Przeprowadzenie testu skanującego obraz kontenerowy przy użyciu Trivy
* Zweryfikowanie podstawowych raportów bezpieczeństwa uzyskiwanych z Trivy

### Wynik
Raport o podatnościach z obrazu Dockera i potwierdzenie, że test działa poprawnie znajduje się w pliku [results.json](results.json).

## 2 Zadanie (opcjonalne): SAST z wykorzystaniem Semgrep

**Cel:**
* Zapoznanie się z narzędziem Semgrep do statycznej analizy kodu (SAST)
* Weryfikacja, w jaki sposób Semgrep może wykrywać potencjalne błędy i podatności w kodzie źródłowym

### Wynik
Raport SAST z wykorzystaniem Semgrep i potwierdzenie, że reguły działają prawidłowo znajduje się w pliku [semgrep_results.json](semgrep_results.json).

## 3 Zadanie (obowiązkowe): Przygotowanie procesu CI/CD z wykorzystaniem Trivy i Semgrep

**Cel:**
* Zbudowanie kompletnego procesu CI/CD w GitHub Actions lub GitLab CI, który:
    * Będzie wykonywał skanowanie obrazu kontenerowego (lub skanowanie zależności kodu) za pomocą Trivy
    * Będzie przeprowadzał SAST z wykorzystaniem Semgrep
* Automatyzacja testów bezpieczeństwa w ramach procesu Continuous Integration

### Realizacja
Zgodnie z instrukcją, utworzono proces CI/CD dla narzędzi Trivy oraz Semgrep.
Zdecydowano się zmodyfikować przykładowy opis pipeline'u dostarczony przez prowadzącego w taki sposób, aby skorzystać z możliwości Github Actions oraz zakładki Security.

Zadanie rozdzielono na dwa osobne job'y, co pozwala na zrównoleglenie analizy statycznej.
Wyniki obu narzędzi zapisywane są do formatu *sarif*, a następnie importowane do Githuba - można je podejrzeć w dedykowanej [zakładce](https://github.com/bugajakmateusz/tbo-task4/security/code-scanning?query=is%3Aopen+pr%3A1+).

W ramach zadania zmodyfikowano polecenie uruchamiające narzędzie semgrep w taki sposób, aby zwracane były informacje o wszystkich podatnościach - konfiguracja **p/security-audit**, zgodnie z [archiwalną dokumentacją narzędzia](https://github.com/Shopify/semgrep-rules-1/blob/develop/README.md#how-do-i-use-these-rules), powinny być używane do testów manualnych.

### Wynik
Stworzono proces CI/CD, który realizuje testy z wykorzystaniem Trivy i Semgrep: [security-scan.yml](.github/workflows/security-scan.yml)
Stworzono Pull Requesta na GitHub zawierającego:
    * Zmiany w kodzie (dodanie pliku security-scan.yml).
    * Link do zadania w CI/CD, które się wykonało i pokazało wynik testów - link znajduje się w komentarzu ze względu na ponowne uruchomienie procesu CI/CD po zaktualizaniu pliku README.md.

## 4 Zadanie (obowiązkowe): Uruchomienie aplikacji lokalnie + DAST z wykorzystaniem ZAP

**Cel:**
* Zweryfikowanie dynamicznego bezpieczeństwa aplikacji uruchomionej w kontenerze
* Poznanie narzędzia OWASP ZAP w trybie automatycznego skanowania (ZAP auto scan)
* Porównanie wyników DAST (ZAP) z wynikami SAST (Semgrep) i SCA (Trivy)

### Realizacja
W ramach zadania zdecydowano się uruchomić narzędzie ZAP również w procesie CI/CD przy użyciu akcji *zaproxy/action-baseline*.
Pozwala to na pełniejszą integrację z Githubem, ale przede wszystkim ciągłą kontrolę nad potencjalnymi podatnościami.

### Wynik
* Uruchomiono narzędzie typu DAST (OWASP ZAP) - wynik jego działania znajduje się w sekcji [Issues](https://github.com/bugajakmateusz/tbo-task4/issues/2).

### Opis różnic w podatnościach

Narzędzia do statycznej analizy kodu (SAST), takie jak Trivy oraz Semgrep, koncentrują się na syntaktycznym sprawdzaniu kodu źródłowego przy użyciu wcześniej zdefiniowanych reguł. Dzięki temu możliwe jest wykrycie potencjalnych luk bezpieczeństwa na wczesnym etapie cyklu życia oprogramowania – jeszcze podczas jego tworzenia lub nawet podczas wyboru technologii.

W przeanalizowanej aplikacji narzędzia te zidentyfikowały 17 krytycznych podatności, które w większości związane były z błędami takimi jak przepełnienie bufora lub odczyt spoza dozwolonego zakresu pamięci. Tego rodzaju problemy mogą prowadzić do eskalacji uprawnień, wykonania nieautoryzowanego kodu lub niekontrolowanego dostępu do danych.

Z kolei narzędzie ZAP (Zed Attack Proxy), będące przykładem dynamicznej analizy bezpieczeństwa aplikacji (DAST), przyjmuje inne podejście do oceny ryzyka. Analizuje ono działanie aplikacji w czasie rzeczywistym, emulując interakcje użytkownika i testując jej odpowiedzi. Kluczowym aspektem DAST jest fakt, że bada ono aplikację jako całość – w środowisku zbliżonym do produkcyjnego, z uwzględnieniem dodatkowych warstw infrastruktury, takich jak serwer webowy, reverse proxy czy inne elementy sieciowe.

Podczas analizy dostarczonej aplikacji narzędzie ZAP nie wykryło żadnych podatności o krytycznym poziomie ryzyka. Na poziomie średnim zidentyfikowano jednak trzy istotne problemy:
- Brak tokenów Anti-CSRF (Cross-Site Request Forgery)
- Nieustawiony nagłówek Content Security Policy (CSP)
- Brak nagłówka Anti-clickjacking

Warto zauważyć, że tego typu podatności mają charakter bardziej strukturalny i koncentrują się na zabezpieczeniu aplikacji przed atakami skierowanymi na warstwę klienta oraz dostarczane treści. Problemy takie jak brak tokenów CSRF mogą umożliwić ataki manipulujące sesją użytkownika, podczas gdy brak odpowiednich nagłówków CSP i zabezpieczeń anty-clickjackingowych może prowadzić do przejęcia kontroli nad zawartością wyświetlaną użytkownikowi lub naruszenia integralności aplikacji.

Podsumowując, zastosowanie zarówno SAST, jak i DAST pozwala na wszechstronne podejście do bezpieczeństwa aplikacji – od analizy kodu źródłowego po ocenę działania aplikacji w środowisku runtime. Integracja obu metod w procesie CI/CD pozwala na szybkie wykrywanie i eliminowanie podatności, zwiększając tym samym poziom bezpieczeństwa i niezawodności oprogramowania.

## Credits
* Java application - [GitHub Repo](https://github.com/pedrohenriquelacombe/spring-thymeleaf-crud-example)
* Python application - [GitHub Repo](https://github.com/MohammadSatel/Flask_Book_Library)
* JWT application - [GitHub Repo](https://github.com/onsecru/jwt-hacking-challenges)
