# Zadnie 1.
Raport zamieszczony w pliku trivy_local.txt

# Zadanie 2.
Raport zamieszczony w pliku semgrep.txt

# Zadanie 3.
link do udanego zadania: https://github.com/j-jodczyk/task4/actions/runs/12699010479/job/35398713086

# Zadanie 4.
Raport zamieszczony w pliku zap_report.html

ZAP jest narzędziem typu DAST, które służy do dynamicznej analizy aplikacji. Skupia się na wykrywaniu problemów bezpieczeństwa w warunkach runtime, takich jak błędy konfiguracji serwera czy podatności. Nie identyfikuje problemów w kodzie źródłowym.

Samgrep i Trivy służą do statycznej analizy kodu, bez jego uruchomienia. Mają za zadanie wykryć błędy w implementacji, niebezpieczne konstrukcje czy luki w logice oraz przenanalizować zewnętrzne bibliotki pod kątem znanych podatności.

Dlatego podatności wykryte przez ZAP, Samgrep i Trivy się różnią. Niektóre problemy da się wykryć jedynie poprzez interakcję z aplikacją, a niektóre statycznie. Dynamiczna analiza kodu staje się bardziej przydatna w dalszych etapach dewelopmentu, podczas gdy statyczna pozwala na wczesną detekcję błędów w kodzie.
