---
title: Obsługiwane typy zasobów za poorednictwem Azure Resource Health | Microsoft Docs
description: Obsługiwane typy zasobów za pomocą usługi Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3a61c8b90f4dc5e52a875488afb5374e3d6b7ce4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717074"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy zasobów i kontrole kondycji w usłudze Azure Resource Health
Poniżej znajduje się kompletna lista wszystkich testów wykonywanych przez zasoby według typów zasobów.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer jest uruchomiony i działa?</li><li>Czy na serwerze zabrakło pamięci?</li><li>Czy serwer jest uruchamiany?</li><li>Czy serwer jest odzyskiwany?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy usługa API Management jest uruchomiona i działa?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy konto w usłudze Batch jest uruchomione?</li><li>Czy limit przydziału puli został przekroczony dla tego konta usługi Batch?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy wszystkie węzły pamięci podręcznej działają i działają?</li><li>Czy można uzyskać dostęp do pamięci podręcznej z poziomu centrum danych?</li><li>Czy pamięć podręczna osiągnęła maksymalną liczbę połączeń?</li><li> Czy pamięć podręczna przekroczyła dostępną pamięć? </li><li>Czy w pamięci podręcznej występuje duża liczba błędów stron?</li><li>Czy pamięć podręczna jest mocno obciążona?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Wykonane sprawdzenia|
|---|
|<ul> <li>Czy dostępny jest dodatkowy Portal dla operacji konfiguracji sieci CDN?</li><li>Czy istnieją jakieś problemy z dostarczaniem punktów końcowych usługi CDN?</li><li>Czy użytkownicy mogą zmieniać konfigurację swoich zasobów usługi CDN?</li><li>Czy zmiany konfiguracji są propagowane z oczekiwaną szybkością?</li><li>Czy użytkownicy mogą zarządzać konfiguracją usługi CDN przy użyciu Azure Portal, programu PowerShell lub interfejsu API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy ukończono rozruch systemu operacyjnego hosta?</li><li>Czy kontener maszyny wirtualnej został zainicjowany i włączony?</li><li>Czy istnieje połączenie sieciowe między hostem a kontem magazynu?</li><li>Czy ukończono rozruch systemu operacyjnego gościa?</li><li>Czy nadal trwa planowana konserwacja?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy konto można połączyć z poziomu centrum danych?</li><li>Czy dostawca zasobów Cognitive Services jest dostępny?</li><li>Czy usługa poznawcze jest dostępna w odpowiednim regionie?</li><li>Czy operacje odczytu mogą być wykonywane na koncie magazynu zawierającym metadane zasobu?</li><li>Czy osiągnięto limit przydziału wywołań interfejsu API?</li><li>Czy osiągnięto limit odczytu wywołania interfejsu API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer hostujący tę maszynę wirtualną w górę i w działaniu?</li><li>Czy ukończono rozruch systemu operacyjnego hosta?</li><li>Czy kontener maszyny wirtualnej został zainicjowany i włączony?</li><li>Czy istnieje połączenie sieciowe między hostem a kontem magazynu?</li><li>Czy ukończono rozruch systemu operacyjnego gościa?</li><li>Czy nadal trwa planowana konserwacja?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy istnieją błędy przebiegu potoku?</li><li>Czy klaster obsługuje Data Factory w dobrej kondycji?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy użytkownicy napotykali problemy z przesyłaniem i wyświetlaniem ich Data Lake Analytics zadań?</li><li>Czy nie można wykonać zadań Data Lake Analytics z powodu błędów systemu?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy użytkownicy napotykali problemy z przekazywaniem danych do Data Lake Store?</li><li>Czy użytkownicy napotykali problemy z pobieraniem danych z Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. datamigration/Services
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy nie można zainicjować obsługi administracyjnej usługi migracji bazy danych?</li><li>Czy usługa migracji bazy danych została zatrzymana z powodu nieaktywności lub żądania użytkownika?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. dataudział/konta
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy konto udostępniania danych jest uruchomione?</li><li>Czy klaster obsługujący udział danych jest dostępny?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy Centrum IoT jest uruchomione?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy nie zostały obsłużone żadne żądania bazy danych lub kolekcji z powodu niedostępności usługi Azure Cosmos DB?</li><li>Czy nie zostały obsłużone żadne żądania dokumentu z powodu niedostępności usługi Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/przestrzenie nazw
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy Event Hubs przestrzeni nazw występują błędy wygenerowane przez użytkownika?</li><li>Czy Event Hubs przestrzeń nazw jest obecnie uaktualniana?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy usługi podstawowe są dostępne w klastrze HDInsight?</li><li>Czy klaster usługi HDInsight może uzyskać dostęp do klucza szyfrowania BYOK w spoczynku?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy żądania do magazynu kluczy kończą się niepowodzeniem ze względu na problemy z platformą Azure Key magazynu?</li><li>Czy żądania kierowane do magazynu kluczy są ograniczone ze względu na zbyt wiele żądań wykonywanych przez klienta?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. MachineLearning/WebServices
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy usługa sieci Web jest uruchomiona?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy usługa Media Service jest uruchomiona?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateways
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy wydajność Application Gateway obniżana?</li><li>Czy Application Gateway jest dostępna?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy jest podłączony tunel VPN?</li><li>Czy istnieją konflikty konfiguracji w ramach połączenia?</li><li>Czy wstępnie udostępnione klucze są poprawnie skonfigurowane?</li><li>Czy urządzenie sieci VPN jest osiągalne?</li><li>Czy istnieją niezgodności w zasadach zabezpieczeń protokołu IPSec/IKE?</li><li>Czy połączenie sieci VPN S2S zostało prawidłowo zainicjowane lub w stanie niepowodzenia?</li><li>Czy połączenie między sieciami WIRTUALNYmi zostało prawidłowo zainicjowane lub w stanie awarii?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy obwód ExpressRoute jest w dobrej kondycji?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy frontony drzwi reagują na błędy w sondach kondycji?</li><li>Czy zmiany konfiguracji są opóźnione?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy punkty końcowe równoważenia obciążenia są dostępne?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy Brama sieci VPN jest dostępna z Internetu?</li><li>Czy VPN Gateway jest w trybie wstrzymania?</li><li>Czy usługa sieci VPN jest uruchomiona na bramie?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy w przestrzeni nazw mogą być wykonywane operacje, takie jak rejestracja, instalacja czy wysyłanie?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/obszary robocze
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy istnieją opóźnienia indeksowania dla obszaru roboczego?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy zasób wydajności jest uruchomiony i działa?</li><li>Czy wszystkie obciążenia są uruchomione i działają?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy system operacyjny hosta jest uruchomiony?</li><li>Czy obszar roboczycollection jest dostępny spoza centrum danych?</li><li>Czy dostawca zasobów Power BI jest dostępny?</li><li>Czy usługa Power BI jest dostępna w odpowiednim regionie?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy w klastrze można wykonywać operacje diagnostyczne?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy klienci napotykali błędy wygenerowane przez użytkownika Service Bus?</li><li>Czy użytkownicy napotykają wzrost błędów przejściowych ze względu na uaktualnienie Service Bus przestrzeni nazw?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/bazy danych
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy baza danych jest w pełni uruchomiona?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/serwer/bazy danych
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy wystąpiły logowania do bazy danych?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy żądania odczytu danych z konta magazynu kończą się niepowodzeniem z powodu problemów z platformą Azure Storage?</li><li>Czy żądania zapisu danych na koncie magazynu kończą się niepowodzeniem z powodu problemów z platformą Azure Storage?</li><li>Czy klaster magazynu, w którym znajduje się konto magazynu, jest niedostępny?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy wszystkie hosty, na których wykonywane jest zadanie, są uruchamiane?</li><li>Czy nie można uruchomić zadania?</li><li>Czy istnieją bieżące uaktualnienia środowiska uruchomieniowego?</li><li>Czy zadanie jest w oczekiwanym stanie (na przykład uruchomione lub zatrzymane przez klienta)?</li><li>Czy podczas wykonywania zadania brakuje wyjątków pamięci?</li><li>Czy są planowane aktualizacje obliczeń?</li><li>Czy Menedżer wykonywania (plan kontroli) jest dostępny?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy Internet Information Services jest uruchomiona?</li><li>Czy jest uruchomiony moduł równoważenia obciążenia?</li><li>Czy plan App Service można osiągnąć z poziomu centrum danych?</li><li>Czy jest dostępne konto magazynu obsługujące zawartość witryn dla farma serwerów?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Wykonane sprawdzenia|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy jest uruchomiony internetowy serwer informacyjny?</li><li>Czy jest uruchomiony moduł równoważenia obciążenia?</li><li>Czy można uzyskać dostęp do aplikacji sieci Web z poziomu centrum danych?</li><li>Czy konto magazynu obsługujące zawartość witryny jest dostępne?</li></ul>|

## <a name="next-steps"></a>Następne kroki
-  Zobacz [wprowadzenie do Azure Service Health pulpitu nawigacyjnego](service-health-overview.md) i [wprowadzenie do Azure Resource Health](resource-health-overview.md) , aby dowiedzieć się więcej o nich. 
-  [Często zadawane pytania dotyczące Azure Resource Health](resource-health-faq.md)
- Skonfiguruj alerty, aby otrzymywać powiadomienia o problemach z kondycją. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alertów dla zdarzeń usługi Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
