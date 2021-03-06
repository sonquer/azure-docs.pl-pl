---
title: Jak zarządzać jednoczesnymi zapisami do zasobów
titleSuffix: Azure Cognitive Search
description: Używaj optymistycznej współbieżności, aby uniknąć kolizji w połowie w przypadku aktualizacji lub usunięć do usługi Azure Wyszukiwanie poznawcze indexes, indeksatorów i źródeł danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792214"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Jak zarządzać współbieżnością w usłudze Azure Wyszukiwanie poznawcze

Podczas zarządzania zasobami Wyszukiwanie poznawcze platformy Azure, takimi jak indeksy i źródła danych, ważne jest, aby bezpiecznie aktualizować zasoby, szczególnie w przypadku, gdy dostęp do zasobów odbywa się współbieżnie przez różne składniki aplikacji. Gdy dwaj klienci jednocześnie aktualizują zasób bez koordynacji, możliwe są sytuacje wyścigu. Aby tego uniknąć, usługa Azure Wyszukiwanie poznawcze oferuje *optymistyczny model współbieżności*. Nie ma blokad dla zasobu. Zamiast tego istnieje element ETag dla każdego zasobu, który identyfikuje wersję zasobu, aby można było tworzyć jednostki żądań, które unikają przypadkowego nadpisywania.

> [!Tip]
> Kod koncepcyjny w [przykładowym C# rozwiązaniu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) wyjaśnia, jak działa kontrola współbieżności w usłudze Azure wyszukiwanie poznawcze. Kod tworzy warunki, które wywołują kontrolę współbieżności. Odczytywanie [poniższego fragmentu kodu](#samplecode) jest prawdopodobnie wystarczające dla większości deweloperów, ale jeśli chcesz go uruchomić, edytuj plik appSettings. JSON, aby dodać nazwę usługi i klucz API-Key administratora. Podano adres URL usługi `http://myservice.search.windows.net`, nazwa usługi jest `myservice`.

## <a name="how-it-works"></a>Zasady działania

Współbieżność optymistyczna jest implementowana za poorednictwem sprawdzania warunku dostępu w interfejsie API wywołuje zapis do indeksów, indeksatorów, źródeł danych i zasobów synonymMap.

Wszystkie zasoby mają [*tag jednostki (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) , który zawiera informacje o wersji obiektu. Sprawdzając najpierw element ETag, można uniknąć współbieżnych aktualizacji w typowym przepływie pracy (Get, Modify lokalnie, Update), upewniając się, że element ETag zasobu jest zgodny z lokalną kopią.

+ Interfejs API REST używa elementu [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w nagłówku żądania.
+ Zestaw SDK platformy .NET ustawia element ETag za pomocą obiektu accessCondition, ustawiając opcję [if-Match | Nagłówek if-Match-none](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w zasobie. Każdy obiekt dziedziczenia z [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) ma obiekt accessCondition.

Za każdym razem, gdy aktualizujesz zasób, jego element ETag zmienia się automatycznie. W przypadku zaimplementowania zarządzania współbieżnością wszystkie wykonywane czynności są wykonywane w ramach żądania aktualizacji, które wymaga, aby zasób zdalny miał ten sam element ETag, jak kopia zasobu zmodyfikowanego na kliencie. Jeśli zasób zdalny został już zmieniony przez proces współbieżny, element ETag nie będzie zgodny z warunkiem wstępnym, a żądanie zakończy się niepowodzeniem z użyciem protokołu HTTP 412. Jeśli używasz zestawu SDK platformy .NET, te manifesty jako `CloudException`, w których Metoda rozszerzenia `IsAccessConditionFailed()` zwraca wartość true.

> [!Note]
> Istnieje tylko jeden mechanizm współbieżności. Jest on zawsze używany niezależnie od tego, który interfejs API jest używany do aktualizacji zasobów.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Przypadki użycia i przykładowy kod

Poniższy kod demonstruje accessCondition checks dla operacji aktualizacji klucza:

+ Niepowodzenie aktualizacji, jeśli zasób już nie istnieje
+ Niepowodzenie aktualizacji w przypadku zmiany wersji zasobu

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Przykładowy kod z [programu DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Wzorzec projektowy

Wzorzec projektowy służący do implementowania optymistycznej współbieżności powinien zawierać pętlę, która ponawia próbę sprawdzenia warunku dostępu, test dla warunku dostępu i opcjonalnie pobiera zaktualizowany zasób przed podjęciem próby ponownego zastosowania zmian.

Ten fragment kodu ilustruje Dodawanie synonymMap do indeksu, który już istnieje. Ten kod pochodzi z [przykładu C# synonimu dla usługi Azure wyszukiwanie poznawcze](search-synonyms-tutorial-sdk.md).

Fragment kodu Pobiera indeks "Hotele", sprawdza wersję obiektu w operacji aktualizacji, zgłasza wyjątek, jeśli warunek nie powiedzie się, a następnie ponawia próbę wykonania operacji (maksymalnie trzy razy), rozpoczynając od pobierania indeksu z serwera w celu uzyskania najnowszej wersji.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [ C# przykładami synonimów](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) , aby uzyskać więcej informacji na temat sposobu bezpiecznego aktualizowania istniejącego indeksu.

Spróbuj zmodyfikować jeden z poniższych przykładów, aby dołączyć elementy ETag lub AccessCondition.

+ [Przykład interfejsu API REST w witrynie GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Przykład zestawu .NET SDK w witrynie GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). To rozwiązanie zawiera projekt "DotNetEtagsExplainer" zawierający kod przedstawiony w tym artykule.

## <a name="see-also"></a>Zobacz także

[Typowe nagłówki żądań HTTP i odpowiedzi](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[kodów stanu HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[operacji indeksu (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
