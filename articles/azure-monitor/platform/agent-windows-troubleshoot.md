---
title: Rozwiązywanie problemów z agentem Log Analytics dla systemu Windows
description: Opisz objawy, przyczyny i rozwiązywanie typowych problemów z Log Analytics agentem dla systemu Windows w Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 486c68cb32b5f4c8c8a18b21d1aee139ffda45bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397453"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Jak rozwiązywać problemy z agentem Log Analytics dla systemu Windows 

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów dotyczących błędów, które mogą wystąpić w przypadku Log Analytics agenta dla systemu Windows w programie Azure Monitor i sugeruje rozwiązania, które można rozwiązać.

Jeśli żadna z powyższych czynności działa, następujących kanałów pomocy technicznej dostępne są również:

* Korzyści z pomocy technicznej klientom planu Premier mogą Otwórz żądanie obsługi z [Premier](https://premier.microsoft.com/).
* Klienci z umowami pomocy technicznej platformy Azure mogą otworzyć żądania pomocy technicznej [w witrynie Azure portal](https://manage.windowsazure.com/?getsupport=true).
* Odwiedź stronę Log Analytics opinii, aby Przegląd przesłane pomysły i usterek [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) lub nowy plik. 

## <a name="important-troubleshooting-sources"></a>Ważne źródła rozwiązywania problemów

 Aby pomóc w rozwiązywaniu problemów związanych z agentem Log Analytics dla systemu Windows, Agent rejestruje zdarzenia w dzienniku zdarzeń systemu Windows, w odniesieniu do programu *Application i Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemy z łącznością

Jeśli Agent komunikuje się za pośrednictwem serwera proxy lub zapory, mogą wystąpić ograniczenia dotyczące zapobiegania komunikacji między komputerem źródłowym a usługą Azure Monitor. W przypadku zablokowania komunikacji z powodu błędnej konfiguracji Rejestracja z obszarem roboczym może zakończyć się niepowodzeniem podczas próby zainstalowania agenta lub skonfigurowania agenta po zakończeniu instalacji w celu raportowania do dodatkowego obszaru roboczego. Komunikacja agenta może zakończyć się niepowodzeniem po pomyślnej rejestracji. W tej sekcji opisano metody rozwiązywania problemu tego typu z agentem systemu Windows.

Sprawdź, czy zapora lub serwer proxy został skonfigurowany tak, aby zezwalał na następujące porty i adresy URL opisane w poniższej tabeli. Upewnij się również, że inspekcja HTTP nie jest włączona dla ruchu w sieci Web, ponieważ może to uniemożliwić bezpieczny kanał protokołu TLS między agentem i Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Tak |  

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Istnieje kilka sposobów, aby sprawdzić, czy Agent pomyślnie komunikuje się z Azure Monitor.

- Włącz [ocenę Agent Health usługi Azure log Analytics](../insights/solution-agenthealth.md) w obszarze roboczym. Na pulpicie nawigacyjnym Agent Health Wyświetl **liczbę nieodpowiadających agentów** , aby szybko sprawdzić, czy Agent znajduje się na liście.  

- Uruchom następujące zapytanie, aby potwierdzić, że Agent wysyła puls do obszaru roboczego, do którego jest skonfigurowana do raportowania. Zastąp `<ComputerName>` rzeczywistą nazwą komputera.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Jeśli komputer pomyślnie komunikuje się z usługą, zapytanie powinno zwrócić wynik. Jeśli zapytanie nie zwróciło wyniku, najpierw sprawdź, czy Agent jest skonfigurowany do raportowania do prawidłowego obszaru roboczego. Jeśli jest prawidłowo skonfigurowany, przejdź do kroku 3 i Przeszukaj dziennik zdarzeń systemu Windows, aby ustalić, czy Agent rejestruje, jakiego problemu może uniemożliwiać komunikację z Azure Monitor.

- Inną metodą identyfikacji problemu z łącznością jest uruchomienie narzędzia **TestCloudConnectivity** . Narzędzie jest instalowane domyślnie z agentem w folderze *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. W wierszu polecenia z podwyższonym poziomem uprawnień przejdź do folderu i uruchom narzędzie. Narzędzie zwraca wyniki i wyróżnia, w których teście nie powiodło się (na przykład jeśli zostało ono powiązane z określonym portem/adresem URL, który został zablokowany). 

    ![Wyniki wykonywania narzędzia TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Przefiltruj *Operations Manager* dziennik zdarzeń **według źródeł zdarzeń** - *Usługa kondycji moduły*, *HealthService*oraz *Łącznik usługi* i przefiltruj według *ostrzeżenia* na **poziomie zdarzenia** *i sprawdź* , czy zarejestrowano zdarzenia z poniższej tabeli. Jeśli są, przejrzyj kroki rozwiązywania dotyczące każdego możliwego zdarzenia.

    |Identyfikator zdarzenia |Źródło |Opis |Rozdzielczość |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Usługa kondycji |Nie można nawiązać połączenia z usługą z agenta |Ten błąd może wystąpić, gdy Agent nie może komunikować się bezpośrednio z usługą Azure Monitor ani za pomocą zapory/serwera proxy. Sprawdź ustawienia serwera proxy agenta lub czy Zapora sieci/serwer proxy zezwala na ruch TCP z komputera do usługi.|
    |2138 |Moduły Usługa kondycji |Serwer proxy wymaga uwierzytelniania |Skonfiguruj ustawienia serwera proxy agenta i określ nazwę użytkownika/hasło wymagane do uwierzytelnienia na serwerze proxy. |
    |2129 |Moduły Usługa kondycji |Nieudane połączenie/negocjowanie protokołu SSL nie powiodło się |Sprawdź ustawienia protokołu TCP/IP karty sieciowej oraz ustawienia serwera proxy agenta.|
    |2127 |Moduły Usługa kondycji |Nie powiodło się wysyłanie danych — kod błędu |Jeśli wystąpi tylko okresowo w ciągu dnia, może to być tylko Losowa anomalia, którą można zignorować. Monitoruj, aby zrozumieć, jak często się dzieje. Jeśli zdarza się to często przez cały dzień, należy najpierw sprawdzić konfigurację sieci i ustawienia serwera proxy. Jeśli opis zawiera kod błędu HTTP 404 i jest to pierwszy raz, gdy agent próbuje wysłać dane do usługi, będzie zawierać błąd 500 z wewnętrznym kodem błędu 404. 404 nie znaleziono, co oznacza, że obszar magazynu dla nowego obszaru roboczego jest nadal zainicjowany. Przy następnym ponowieniu próby dane zostaną pomyślnie zapisane w obszarze roboczym zgodnie z oczekiwaniami. Błąd HTTP 403 może wskazywać na problem z uprawnieniami lub poświadczeniami. Więcej informacji zawiera błąd 403, aby pomóc w rozwiązaniu problemu.|
    |4000 |Łącznik usługi |Rozpoznawanie nazw DNS nie powiodło się |Komputer nie może rozpoznać adresu internetowego używanego podczas wysyłania danych do usługi. Mogą to być ustawienia programu rozpoznawania nazw DNS na komputerze, nieprawidłowe ustawienia serwera proxy lub może to być tymczasowy problem z systemem DNS dla dostawcy. Jeśli trwa okresowo, przyczyną może być przejściowy problem z siecią.|
    |4001 |Łącznik usługi |Nie można nawiązać połączenia z usługą. |Ten błąd może wystąpić, gdy Agent nie może komunikować się bezpośrednio z usługą Azure Monitor ani za pomocą zapory/serwera proxy. Sprawdź ustawienia serwera proxy agenta lub czy Zapora sieci/serwer proxy zezwala na ruch TCP z komputera do usługi.|
    |4002 |Łącznik usługi |Usługa zwróciła kod stanu HTTP 403 w odpowiedzi na zapytanie. Skontaktuj się z administratorem usługi w celu uzyskania kondycji usługi. Kwerenda zostanie ponowiona później. |Ten błąd jest zapisywana podczas początkowej fazy rejestracji agenta i zobaczysz adres URL podobny do następującego: *https://\<identyfikator obszaru roboczego >. OMS. usługi OpInsights. Azure. com/AgentService. svc/AgentTopologyRequest*. Kod błędu 403 oznacza zabroniony i może być spowodowany błędnym IDENTYFIKATORem lub kluczem obszaru roboczego lub danymi i godziną na komputerze. Jeśli czas +/-15 minut od bieżącego czasu dołączania kończy się niepowodzeniem. Aby rozwiązać ten konieczność, zaktualizuj datę i/lub strefę czasową komputera z systemem Windows.|

## <a name="data-collection-issues"></a>Problemy z zbieraniem danych

Po zainstalowaniu agenta i przesłaniu raportów do jego skonfigurowanego obszaru roboczego lub obszarów roboczych może on przestać otrzymywać informacje o konfiguracji, zbieraniu lub przekazywaniu wydajności, dzienników lub innych danych do usługi w zależności od tego, co jest włączone i ukierunkowane na komputer. Należy określić, czy:

- Czy jest to konkretny typ danych, czy wszystkie dane, które nie są dostępne w obszarze roboczym?
- Czy typ danych jest określony przez rozwiązanie lub określony jako część konfiguracji zbierania danych obszaru roboczego?
- Ile komputerów dotyczy ten wpływ? Czy do obszaru roboczego są raportowane pojedyncze lub wiele komputerów?
- Czy zadziałała i zakończyła się w określonym dniu lub nie został nigdy zebrany? 
- Czy kwerenda przeszukiwania dzienników jest używana syntaktycznie? 
- Czy Agent odebrał kiedykolwiek swoją konfigurację od Azure Monitor?

Pierwszym krokiem w rozwiązywaniu problemów jest określenie, czy komputer wysyła zdarzenie pulsu.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Jeśli zapytanie zwraca wyniki, należy określić, czy konkretny typ danych nie jest zbierany i przekazywany do usługi. Przyczyną może być to, że Agent nie otrzymuje zaktualizowanej konfiguracji z usługi lub inny objaw uniemożliwiający normalne działanie agenta. Wykonaj następujące kroki, aby kontynuować rozwiązywanie problemów.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze i ponownie uruchom usługę agenta, wpisując `net stop healthservice && net start healthservice`.
2. Otwórz dziennik zdarzeń *Operations Manager* i Wyszukaj **identyfikatory zdarzeń** *7023, 7024, 7025, 7028* i *1210* ze **źródła zdarzeń** *HealthService*.  Te zdarzenia wskazują, że Agent pomyślnie otrzyma konfigurację z Azure Monitor i aktywnie monitoruje komputer. Opis zdarzenia dla zdarzenia o IDENTYFIKATORze 1210 również określi ostatni wiersz wszystkich rozwiązań i szczegółowych informacji, które znajdują się w zakresie monitorowania w agencie.  

    ![Opis zdarzenia o IDENTYFIKATORze 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Jeśli po kilku minutach nie widzisz oczekiwanych danych w wynikach zapytania lub wizualizacji, w zależności od tego, czy oglądasz dane z rozwiązania lub szczegółowych informacji, w dzienniku zdarzeń *Operations Manager* Wyszukaj **źródła zdarzeń** *HealthService* i *Usługa kondycji moduły* i przefiltruj według *ostrzeżenia* na **poziomie zdarzeń** i sprawdź, *czy* zarejestrowano zdarzenia z poniższej tabeli.

    |Identyfikator zdarzenia |Źródło |Opis |Rozdzielczość |
    |---------|-------|------------|
    |8000 |HealthService |To zdarzenie określa, czy przepływ pracy związany z wydajnością, zdarzeniem, czy innym typem danych zbieranych nie jest w stanie przesłać dalej do usługi w celu pozyskania w obszarze roboczym. | Identyfikator zdarzenia 2136 ze źródła HealthService jest zapisywana razem z tym zdarzeniem i może wskazywać, że Agent nie może komunikować się z usługą, prawdopodobnie z powodu błędnej konfiguracji serwera proxy i ustawień uwierzytelniania, awarii sieci lub zapory sieciowej/serwera proxy nie zezwala na ruch TCP z komputera do usługi.| 
    |10102 i 10103 |Moduły Usługa kondycji |Przepływ pracy nie może rozpoznać źródła danych. |Taka sytuacja może wystąpić, jeśli określony licznik wydajności lub wystąpienie nie istnieje na komputerze lub jest niepoprawnie zdefiniowane w ustawieniach danych obszaru roboczego. Jeśli jest to [licznik wydajności](data-sources-performance-counters.md#configuring-performance-counters)określony przez użytkownika, sprawdź, czy podane informacje mają prawidłowy format i istnieją na komputerach docelowych. |
    |26002 |Moduły Usługa kondycji |Przepływ pracy nie może rozpoznać źródła danych. |Taka sytuacja może wystąpić, jeśli określony dziennik zdarzeń systemu Windows nie istnieje na komputerze. Ten błąd może być bezpiecznie ignorowany, jeśli komputer nie powinien mieć zarejestrowanego dziennika zdarzeń. w przeciwnym razie, jeśli jest to [Dziennik zdarzeń](data-sources-windows-events.md#configuring-windows-event-logs)określony przez użytkownika, sprawdź, czy podane informacje są poprawne. |

