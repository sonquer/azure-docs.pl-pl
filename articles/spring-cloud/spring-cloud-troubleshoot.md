---
title: Przewodnik rozwiązywania problemów z chmurą wiosenną platformy Azure | Microsoft Docs
description: Przewodnik rozwiązywania problemów z chmurą wiosenną platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277584"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Rozwiązywanie typowych problemów z chmurą wiosenną platformy Azure

Ten artykuł zawiera instrukcje dotyczące rozwiązywania problemów z programowaniem w chmurze na platformie Azure. Aby uzyskać dodatkowe informacje, zobacz temat [często zadawane pytania dotyczące chmury Azure wiosennej](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemy z dostępnością, wydajnością i aplikacją

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Nie można uruchomić mojej aplikacji (na przykład punkt końcowy nie może być połączony lub zwraca 502 po kilku ponownych próbach)

Wyeksportuj dzienniki do Log Analytics platformy Azure. Tabela dla sprężynowych dzienników aplikacji nosi nazwę *AppPlatformLogsforSpring*. Aby dowiedzieć się więcej, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md).

W dziennikach może pojawić się następujący komunikat o błędzie:

> "org. springframework. Context. ApplicationContextException: nie można uruchomić serwera sieci Web"

Komunikat wskazuje jeden z dwóch możliwych problemów: 
* Brakuje jednego z ziaren lub jednej z jego zależności.
* Brakuje jednej z właściwości ziarna lub jest ona nieprawidłowa. W takim przypadku prawdopodobnie zostanie wyświetlony komunikat "Java. lang. IllegalArgumentException".

Powiązania usługi mogą również powodować błędy uruchomienia aplikacji. Aby wykonać zapytanie dotyczące dzienników, należy użyć słów kluczowych związanych z usługami powiązanymi. Załóżmy na przykład, że aplikacja ma powiązanie z wystąpieniem programu MySQL, które jest ustawione na czas system lokalny. Jeśli uruchomienie aplikacji nie powiedzie się, w dzienniku może pojawić się następujący komunikat o błędzie:

> "Java. SQL. SQLException: wartość strefy czasowej" Coordinated Universal Time "nie została rozpoznana lub reprezentuje więcej niż jedną strefę czasową".

Aby naprawić ten błąd, przejdź do `server parameters` wystąpienia MySQL i zmień wartość `time_zone` z *system* na *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Moja aplikacja ulega awarii lub zgłasza nieoczekiwany błąd

Gdy debugujesz awarie aplikacji, Zacznij od sprawdzenia stanu uruchomienia i stanu odnajdywania aplikacji. W tym celu przejdź do pozycji _Zarządzanie aplikacjami_ w Azure Portal, aby upewnić się, że wszystkie aplikacje są _uruchomione_ i w _górę_.

* Jeśli stan jest _uruchomiony_ , ale stan odnajdywania nie jest _ustawiony_, przejdź do sekcji ["Moje aplikacje nie można zarejestrować"](#my-application-cant-be-registered) .

* Jeśli stan odnajdywania jest _ustawiony_, przejdź do pozycji metryki, aby sprawdzić kondycję aplikacji. Sprawdź następujące metryki:


  - `TomcatErrorCount` (_tomcat. Global. Error_): w tym miejscu są zliczane wszystkie wyjątki aplikacji wiosennej. Jeśli ta liczba jest duża, przejdź do usługi Azure Log Analytics w celu sprawdzenia dzienników aplikacji.

  - `AppMemoryMax` (_JVM. Memory. Max_): Maksymalna ilość pamięci dostępnej dla aplikacji. Kwota może być niezdefiniowana lub może ulec zmianie w czasie, jeśli jest zdefiniowana. Jeśli jest zdefiniowana, ilość używanej i zadeklarowanej pamięci jest zawsze mniejsza lub równa max. Jednak alokacja pamięci może zakończyć się niepowodzeniem z komunikatem `OutOfMemoryError`, jeśli alokacja próbuje zwiększyć użytą pamięć, taką jak *użycie > zatwierdzone*, nawet jeśli jest *używana < = Max* jest nadal true. W takiej sytuacji spróbuj zwiększyć maksymalny rozmiar sterty przy użyciu parametru `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory._ Application): ilość pamięci w bajtach, która jest obecnie używana przez aplikację. W przypadku normalnej aplikacji Java do załadowania ta seria metryk tworzy wzorzec *powoduje piłokształtny* , w którym użycie pamięci stale rośnie i zmniejsza się w małych przyrostach i nagle porzuca dużo, a następnie wzorzec powtarza się. Ta seria metryk występuje ze względu na wyrzucanie elementów bezużytecznych wewnątrz maszyny wirtualnej Java, gdzie akcje kolekcji reprezentują porzucenia wzorca powoduje piłokształtny.
    
    Ta Metryka jest ważna, aby ułatwić identyfikowanie problemów z pamięcią, takich jak:
    * Rozłożenie pamięci na bardzo rozpoczęciu.
    * Alokacja pamięci przepięcia dla określonej ścieżki logicznej.
    * Stopniowe przecieki pamięci.

  Aby uzyskać więcej informacji, zobacz [metryki](spring-cloud-concept-metrics.md).

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz Wprowadzenie do [log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Moja aplikacja używa dużo mocy procesora lub pamięci

Jeśli aplikacja korzysta z dużego procesora lub pamięci, jedno z dwóch rzeczy jest prawdziwe:
* Wszystkie wystąpienia aplikacji napotykają duże użycie procesora CPU lub pamięci.
* Niektóre wystąpienia aplikacji napotykają duże użycie procesora CPU lub pamięci.

Aby ustalić, która sytuacja ma zastosowanie, wykonaj następujące czynności:

1. Przejdź do pozycji **metryki**, a następnie wybierz opcję **procent użycia procesora CPU** lub **użyta pamięć usługi**.
2. Dodaj **aplikację =** filtr, aby określić, która aplikacja ma być monitorowana.
3. Podziel metryki według **wystąpienia**.

Jeśli *wszystkie wystąpienia* mają duże użycie procesora CPU lub pamięci, należy albo skalować aplikację, albo skalować w górę użycie procesora lub pamięci. Aby uzyskać więcej informacji, zobacz [Samouczek: skalowanie aplikacji w chmurze Azure wiosennej](spring-cloud-tutorial-scale-manual.md).

Jeśli w *niektórych wystąpieniach* występuje wysokie użycie procesora CPU lub pamięci, sprawdź stan wystąpienia i stan odnajdywania.

Aby uzyskać więcej informacji, zobacz [metryki dla chmury wiosennej platformy Azure](spring-cloud-concept-metrics.md).

Jeśli wszystkie wystąpienia są uruchomione, przejdź do usługi Azure Log Analytics, aby wykonać zapytanie dotyczące dzienników aplikacji i przejrzeć logikę kodu. Ułatwi to sprawdzenie, czy którykolwiek z nich może mieć wpływ na partycjonowanie skalowania. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md).

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz Wprowadzenie do [log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Wykonaj zapytanie dotyczące dzienników przy użyciu [języka zapytań Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Lista kontrolna wdrażania aplikacji wiosennej w chmurze Azure wiosennej

Przed dołączeniem aplikacji upewnij się, że spełnia ona następujące kryteria:

* Aplikacja może działać lokalnie z określoną wersją środowiska uruchomieniowego języka Java.
* Konfiguracja środowiska (procesor/pamięć RAM/wystąpienia) spełnia wymagania minimalne ustawione przez dostawcę aplikacji.
* Elementy konfiguracji mają oczekiwane wartości. Aby uzyskać więcej informacji, zobacz [config Server](spring-cloud-tutorial-config-server.md).
* Zmienne środowiskowe mają oczekiwane wartości.
* Parametry JVM mają oczekiwane wartości.
* Zaleca się wyłączenie lub usunięcie z pakietu aplikacji usług rejestru Embedded _Server_ i _sprężyny_ usług.
* Jeśli jakiekolwiek zasoby platformy Azure mają być powiązane za pomocą _powiązania usługi_, upewnij się, że zasoby docelowe zostały uruchomione.

## <a name="configuration-and-management"></a>Konfigurowanie i zarządzanie

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Wystąpił problem podczas tworzenia wystąpienia usługi w chmurze ze sprężyną Azure

Po skonfigurowaniu wystąpienia usługi w chmurze ze sprężyną na platformie Azure za pomocą Azure Portal, Chmura Wiosenna platformy Azure wykonuje weryfikację.

Ale jeśli spróbujesz skonfigurować wystąpienie usługi chmurowej Azure sprężynowej przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) lub [szablonu Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), sprawdź, czy:

* Subskrypcja jest aktywna.
* Lokalizacja jest [obsługiwana](spring-cloud-faq.md) przez chmurę wiosenną platformy Azure.
* Grupa zasobów dla wystąpienia została już utworzona.
* Nazwa zasobu jest zgodna z regułą nazewnictwa. Musi zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak musi być literą. Ostatni znak musi być literą lub cyfrą. Wartość musi zawierać od 2 do 32 znaków.

Jeśli chcesz skonfigurować wystąpienie usługi w chmurze Azure wiosny przy użyciu szablonu Menedżer zasobów, najpierw zapoznaj się z tematem [Struktura i składnia Azure Resource Manager szablonów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Nazwa wystąpienia usługi w chmurze Azure sprężynowego zostanie użyta do żądania nazwy domeny podrzędnej w obszarze `azureapps.io`, więc instalacja nie powiedzie się, jeśli wystąpi konflikt między nazwą a istniejącą. Więcej szczegółów można znaleźć w dziennikach aktywności.

### <a name="i-cant-deploy-a-jar-package"></a>Nie można wdrożyć pakietu JAR

Nie można przekazać pakietu/Source w pliku archiwum Java (JAR) przy użyciu szablonu Menedżer zasobów Azure Portal lub.

Podczas wdrażania pakietu aplikacji przy użyciu [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)platformy Azure interfejs wiersza polecenia platformy Azure okresowo sonduje postęp wdrażania i na końcu wyświetla wynik wdrożenia.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Upewnij się, że aplikacja jest spakowana w prawidłowym [pliku wykonywalnym jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Jeśli plik nie zostanie poprawnie spakowany, zostanie wyświetlony komunikat o błędzie podobny do następującego:

> "Błąd: nieprawidłowy lub uszkodzony jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nie można wdrożyć pakietu źródłowego

Nie można przekazać pakietu JAR/Source przy użyciu Azure Portal lub szablonu Menedżer zasobów.

Podczas wdrażania pakietu aplikacji przy użyciu [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)platformy Azure interfejs wiersza polecenia platformy Azure okresowo sonduje postęp wdrażania i na końcu wyświetla wynik wdrożenia.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki kompilowania i wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Należy jednak pamiętać, że jedno wystąpienie usługi w chmurze Azure wiosennej może wyzwolić tylko jedno zadanie kompilacji w jednym z nich. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji](spring-cloud-quickstart-launch-app-portal.md) i [Konfigurowanie środowiska tymczasowego w chmurze Azure wiosennej](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Nie można zarejestrować mojej aplikacji

W większości przypadków taka sytuacja występuje, gdy *wymagane zależności* i *odnajdowanie usług* nie są prawidłowo skonfigurowane w pliku modelu obiektów projektu (pliku POM). Po jego skonfigurowaniu wbudowany punkt końcowy serwera rejestru usługi zostanie dodany jako zmienna środowiskowa do aplikacji. Aplikacje następnie rejestrują się na serwerze rejestru usługi i odnajdują inne zależne mikrousługi.

Zaczekaj co najmniej dwie minuty, zanim nowo zarejestrowane wystąpienie zacznie odbierać ruch.

Jeśli migrujesz istniejące, sprężynowe rozwiązanie oparte na chmurze na platformie Azure, upewnij się, że wystąpienia _usługi_ ad hoc w rejestrze i _serwerze konfiguracji_ zostały usunięte (lub wyłączone), aby uniknąć konfliktu z wystąpieniami zarządzanymi w chmurze sieci platformy Azure.

Możesz również sprawdzić dzienniki _usługi Rejestr_ klienta w usłudze Azure log Analytics. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md)

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz Wprowadzenie do [log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Wykonaj zapytanie dotyczące dzienników przy użyciu [języka zapytań Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chcę sprawdzić zmienne środowiskowe aplikacji

Zmienne środowiskowe informują platformę chmurową Azure ze sprężyną, dzięki czemu platforma Azure rozumie, gdzie i jak skonfigurować usługi wchodzące w skład aplikacji. Upewnienie się, że zmienne środowiskowe są poprawne, jest to konieczne w pierwszym kroku w rozwiązywaniu potencjalnych problemów.  Aby przejrzeć zmienne środowiskowe, można użyć punktu końcowego uruchamiającego rozruchu sprężynowego.  

> [!WARNING]
> Ta procedura udostępnia zmienne środowiskowe za pomocą punktu końcowego testu.  Nie należy przechodzić, jeśli punkt końcowy testu jest publicznie dostępny lub jeśli przypisano nazwę domeny do aplikacji.

1. Przejdź do pozycji `https://<your application test endpoint>/actuator/health` (Plik > Nowy > Inny).  
    - Odpowiedź podobna do `{"status":"UP"}` wskazuje, że punkt końcowy został włączony.
    - Jeśli odpowiedź jest ujemna, uwzględnij następujące zależności w pliku *pliku pom. XML* :

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Po włączeniu punktu końcowego uruchamiającego uruchamianie sprężyny przejdź do Azure Portal i Wyszukaj stronę Konfiguracja aplikacji.  Dodaj zmienną środowiskową o nazwie `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` i `*` wartość. 

1. Uruchom ponownie aplikację.

1. Przejdź do `https://<your application test endpoint>/actuator/env` i sprawdź odpowiedź.  Powinny wyglądać następująco:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Wyszukaj węzeł podrzędny o nazwie `systemEnvironment`.  Ten węzeł zawiera zmienne środowiskowe aplikacji.

> [!IMPORTANT]
> Pamiętaj, aby wycofać narażenie zmiennych środowiskowych przed udostępnieniem aplikacji publicznie.  Przejdź do Azure Portal, Wyszukaj stronę Konfiguracja aplikacji i Usuń tę zmienną środowiskową: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nie mogę znaleźć metryk lub dzienników dla mojej aplikacji

Przejdź do pozycji **Zarządzanie aplikacjami** , aby upewnić się, że Stany aplikacji są _uruchomione_ i w _górę_.

Jeśli są wyświetlane metryki z _JVM_ , ale nie metryki z _Tomcat_, sprawdź, czy w pakiecie aplikacji jest włączona zależność `spring-boot-actuator` i czy rozruch został pomyślnie uruchomiony.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Jeśli dzienniki aplikacji można zarchiwizować na koncie magazynu, ale nie są wysyłane do usługi Azure Log Analytics, należy sprawdzić, czy [obszar roboczy został skonfigurowany prawidłowo](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). W przypadku korzystania z bezpłatnej warstwy usługi Azure Log Analytics należy pamiętać, że [w warstwie Bezpłatna nie jest oferowana umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
