---
title: Rozwiązywanie problemów występujących podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB
description: Typowe problemy, obejścia i kroki diagnostyczne podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f3af350c96d1dd9eaf4773db503acb10d8a08a8f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441115"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB

W tym artykule opisano typowe problemy, obejścia i kroki diagnostyczne, gdy [do Cosmos DB jest używany wyzwalacz Azure Functions](change-feed-functions.md).

## <a name="dependencies"></a>Zależności

Wyzwalacz Azure Functions i powiązania dla Cosmos DB są zależne od pakietów rozszerzeń przez podstawowy Azure Functions środowiska uruchomieniowego. Należy zawsze aktualizować te pakiety, ponieważ mogą one zawierać poprawki i nowe funkcje, które mogą rozwiązać ewentualne potencjalne problemy, które mogą wystąpić:

* W przypadku Azure Functions v2, zobacz [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Aby uzyskać Azure Functions V1, zobacz [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ten artykuł będzie zawsze odnosił się do Azure Functions v2, gdy środowisko uruchomieniowe zostanie wymienione, chyba że zostanie jawnie określony.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Korzystaj z zestawu SDK Azure Cosmos DB niezależnie

Kluczową funkcją pakietu rozszerzenia jest zapewnienie obsługi wyzwalacza Azure Functions i powiązań dla Cosmos DB. Zawiera również [zestaw Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), który jest przydatny, jeśli chcesz korzystać z Azure Cosmos DB programowo bez użycia wyzwalacza i powiązań.

Jeśli chcesz użyć zestawu SDK Azure Cosmos DB, upewnij się, że nie dodajesz do projektu innego odwołania do pakietu NuGet. Zamiast tego **pozwól, aby odwołanie do zestawu SDK było rozpoznawane za pomocą pakietu rozszerzenia Azure Functions "** . Korzystanie z zestawu SDK Azure Cosmos DB niezależnie od wyzwalacza i powiązań

Ponadto, jeśli ręcznie tworzysz własne wystąpienie [klienta Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), należy postępować zgodnie ze wzorcem, który ma tylko jedno wystąpienie klienta [przy użyciu pojedynczego podejścia do wzorca](../azure-functions/manage-connections.md#documentclient-code-example-c). Ten proces pozwoli uniknąć potencjalnych problemów z gniazdem w operacjach.

## <a name="common-scenarios-and-workarounds"></a>Typowe scenariusze i obejścia

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funkcja platformy Azure kończy się niepowodzeniem i nie istnieje kolekcja komunikatów o błędach

Funkcja platformy Azure kończy się niepowodzeniem z komunikatem o błędzie "Nazwa kolekcji źródłowej" (w bazie danych "database name") lub kolekcja dzierżaw "Collection2-Name" (w bazie danych "Database2-Name") nie istnieje. Obie kolekcje muszą istnieć przed rozpoczęciem odbiorniku. Aby automatycznie utworzyć kolekcję dzierżawy, ustaw dla opcji "CreateLeaseCollectionIfNotExists" wartość "true".

Oznacza to, że jeden lub oba kontenery usługi Azure Cosmos wymagane do działania wyzwalacza nie istnieją lub nie są dostępne dla funkcji platformy Azure. Sam błąd informuje o tym, **które usługi Azure Cosmos Database i kontenerów są wywoływane** na podstawie konfiguracji.

1. Sprawdź `ConnectionStringSetting` atrybutu i **odwołuje się do ustawienia, które istnieje w aplikacja funkcji platformy Azure**. Wartość w tym atrybucie nie powinna być ciągiem połączenia, ale nazwą ustawienia konfiguracji.
2. Sprawdź, czy `databaseName` i `collectionName` istnieją na koncie usługi Azure Cosmos. Jeśli używasz automatycznego zastąpienia wartości (przy użyciu wzorców `%settingName%`), upewnij się, że nazwa ustawienia istnieje w usłudze Azure aplikacja funkcji.
3. Jeśli nie określisz `LeaseCollectionName/leaseCollectionName`, wartość domyślna to "dzierżawy". Sprawdź, czy kontener istnieje. Opcjonalnie można ustawić atrybut `CreateLeaseCollectionIfNotExists` w wyzwalaczu, aby `true` go utworzyć automatycznie.
4. Sprawdź [konfigurację zapory konta usługi Azure Cosmos](how-to-configure-firewall.md) , aby zobaczyć, że nie blokuje ona funkcji platformy Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Nie można uruchomić funkcji platformy Azure z opcją "udostępniona kolekcja przepływności powinna mieć klucz partycji"

Poprzednie wersje rozszerzenia Azure Cosmos DB nie obsługiwały kontenera dzierżaw, który został utworzony w ramach [udostępnionej bazy danych przepływności](./set-throughput.md#set-throughput-on-a-database). Aby rozwiązać ten problem, zaktualizuj rozszerzenie [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , aby uzyskać najnowszą wersję.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Nie można uruchomić funkcji platformy Azure z "kolekcją dzierżawy, jeśli partycjonowany, musi mieć klucz partycji równy identyfikatorowi".

Ten błąd oznacza, że bieżący kontener dzierżawy jest podzielony na partycje, ale Ścieżka klucza partycji nie jest `/id`. Aby rozwiązać ten problem, należy ponownie utworzyć kontener dzierżawy z `/id` jako klucz partycji.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zobaczysz "wartość nie może być równa null. Nazwa parametru: o "w dziennikach Azure Functions podczas próby uruchomienia wyzwalacza

Ten problem pojawia się, jeśli używasz Azure Portal i próbujesz wybrać przycisk **Uruchom** na ekranie podczas inspekcji funkcji platformy Azure, która używa wyzwalacza. Wyzwalacz nie wymaga wybrania opcji Uruchom do uruchomienia, zostanie automatycznie uruchomiony po wdrożeniu funkcji platformy Azure. Jeśli chcesz sprawdzić strumień dzienników funkcji platformy Azure na Azure Portal, po prostu przejdź do monitorowanego kontenera i Wstaw nowe elementy, zostanie automatycznie wyświetlony wyzwalacz.

### <a name="my-changes-take-too-long-to-be-received"></a>Moje zmiany są zbyt długie, aby można je było odebrać

Ten scenariusz może mieć wiele przyczyn i należy sprawdzić wszystkie z nich:

1. Czy funkcja platformy Azure została wdrożona w tym samym regionie co konto usługi Azure Cosmos? W celu uzyskania optymalnego opóźnienia sieci zarówno funkcja platformy Azure, jak i konto usługi Azure Cosmos powinny znajdować się w tym samym regionie świadczenia usługi Azure.
2. Czy zmiany pojawiają się w kontenerze usługi Azure Cosmos w sposób ciągły czy sporadyczny?
Jeśli w ten drugi sposób, może wystąpić pewne opóźnienie między zapisywaniem zmian a ich pobieraniem przez funkcję platformy Azure. Wynika to z faktu, że wewnętrznie, gdy wyzwalacz sprawdza istnienie zmian w kontenerze usługi Azure Cosmos i nie znajduje żadnych oczekujących na odczytanie, będzie w stanie uśpienia przez konfigurowalny czas (domyślnie 5 sekund) przed sprawdzeniem istnienia nowych zmian (w celu uniknięcia wysokiego użycia jednostek żądań). Ten czas uśpienia można skonfigurować za pomocą ustawienia `FeedPollDelay/feedPollDelay` w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) wyzwalacza (oczekiwana jest wartość w milisekundach).
3. Kontener usługi Azure Cosmos może być [oceniany proporcjonalnie](./request-units.md).
4. W wyzwalaczu można użyć atrybutu `PreferredLocations`, aby określić rozdzieloną przecinkami listę regionów świadczenia usługi Azure, aby zdefiniować niestandardową kolejność połączeń.

### <a name="some-changes-are-missing-in-my-trigger"></a>W moim wyzwalaczu brakuje niektórych zmian

Jeśli okaże się, że niektóre zmiany, które wystąpiły w kontenerze usługi Azure Cosmos, nie są wybierane przez funkcję systemu Azure, istnieje etap wstępnego badania, który musi zostać przeprowadzona.

Gdy funkcja platformy Azure otrzymuje zmiany, często przetwarza je i może opcjonalnie wysłać wynik do innego miejsca docelowego. Podczas badania brakujących zmian upewnij się, że **miary są odbierane w punkcie** pozyskiwania (gdy funkcja platformy Azure zostanie uruchomiona), a nie w miejscu docelowym.

Jeśli w miejscu docelowym brakuje niektórych zmian, może to oznaczać, że wystąpił błąd podczas wykonywania funkcji platformy Azure po odebraniu zmian.

W tym scenariuszu najlepszym sposobem działania jest dodanie bloków `try/catch` w kodzie i wewnątrz pętli, które mogą przetwarzać zmiany, wykrywanie wszelkich błędów dla określonego podzestawu elementów i ich odpowiednie obsłużenie (wysłanie ich do innego magazynu w celu dalszej analizy lub ponowienie próby). 

> [!NOTE]
> Wyzwalacz Azure Functions dla Cosmos DB domyślnie nie będzie próbować partii zmian, jeśli wystąpił nieobsługiwany wyjątek podczas wykonywania kodu. Oznacza to, że powodem, że zmiany nie dotarły do lokalizacji docelowej, jest to, że nie można ich przetworzyć.

Jeśli okaże się, że niektóre zmiany nie zostały odebrane w ogóle przez wyzwalacz, najbardziej typowym scenariuszem jest **uruchomienie innej funkcji platformy Azure**. Może to być inna funkcja platformy Azure wdrożona na platformie Azure lub funkcja platformy Azure działająca lokalnie na komputerze dewelopera, który ma **dokładnie taką samą konfigurację** (te same kontenery monitorowane i dzierżawy), a ta funkcja platformy Azure służy do kradzieży podzbioru zmian, które mają być przetwarzane przez funkcję platformy Azure.

Ponadto można sprawdzić poprawność scenariusza, Jeśli wiesz, ile wystąpień aplikacja funkcji platformy Azure jest uruchomionych. W przypadku inspekcji kontenera dzierżaw i policzania liczby elementów dzierżawy w ramach, różne wartości właściwości `Owner` muszą być równe liczbie wystąpień aplikacja funkcji. Jeśli jest więcej właścicieli niż znanych wystąpień aplikacji funkcji platformy Azure, oznacza to, że ci dodatkowi właściciele „kradną” zmiany.

Aby obejść ten problem, należy zastosować `LeaseCollectionPrefix/leaseCollectionPrefix` do funkcji z nową/inną wartością lub alternatywnie testować przy użyciu nowego kontenera dzierżaw.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>Należy ponownie uruchomić i ponownie przetworzyć wszystkie elementy w moim kontenerze od początku 
Aby ponownie przetworzyć wszystkie elementy w kontenerze od początku:
1. Zatrzymaj funkcję platformy Azure, jeśli jest aktualnie uruchomiona. 
1. Usuń dokumenty z kolekcji dzierżaw (lub Usuń i ponownie Utwórz kolekcję dzierżawy, aby była pusta)
1. Ustaw atrybut [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger w funkcji na wartość true. 
1. Uruchom ponownie funkcję platformy Azure. Teraz odczytywane i przetwarzane są wszystkie zmiany od początku. 

Ustawienie [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) na wartość true spowoduje, że funkcja platformy Azure rozpocznie odczytywanie zmian od początku historii kolekcji zamiast bieżącego czasu. Działa to tylko wtedy, gdy nie zostały już utworzone dzierżawy (tj. dokumenty w kolekcji dzierżawy). Ustawienie tej właściwości na wartość true, jeśli już utworzono dzierżawy nie ma żadnego wpływu; w tym scenariuszu, gdy funkcja zostanie zatrzymana i uruchomiona ponownie, rozpocznie odczytywanie z ostatniego punktu kontrolnego zgodnie z definicją w kolekcji dzierżawy. Aby ponownie przetworzyć dane od początku, wykonaj powyższe kroki 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Powiązanie można wykonać tylko przy użyciu\<IReadOnlyList dokumentu > lub JArray

Ten błąd występuje, jeśli projekt Azure Functions (lub dowolny projekt, do którego istnieje odwołanie) zawiera ręczne odwołanie NuGet do zestawu Azure Cosmos DB SDK z inną wersją niż podana przez [rozszerzenie Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Aby obejść tę sytuację, Usuń ręczne odwołanie do narzędzia NuGet, które zostało dodane i pozwól, aby Azure Cosmos DB odwołanie do zestawu SDK przez pakiet rozszerzenia Cosmos DB Azure Functions.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Zmienianie interwału sondowania funkcji platformy Azure na potrzeby wykrywania zmian

Zgodnie z wcześniejszym opisem w przypadku, gdy zmiany nie zostaną [odebrane](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received), funkcja platformy Azure będzie w stanie uśpienia przez konfigurowalny czas (domyślnie 5 sekund) przed sprawdzeniem nowych zmian (aby uniknąć używania wysokiego poziomu ru). Ten czas uśpienia można skonfigurować za pomocą ustawienia `FeedPollDelay/feedPollDelay` w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) wyzwalacza (oczekiwana jest wartość w milisekundach).

## <a name="next-steps"></a>Następne kroki

* [Włącz monitorowanie dla Azure Functions](../azure-functions/functions-monitoring.md)
* [Rozwiązywanie problemów z Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
