---
title: Uaktualnianie do Azure Search .NET SDK w wersji 5
titleSuffix: Azure Cognitive Search
description: Migruj kod do Azure Search .NET SDK w wersji 5 ze starszych wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793038"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Uaktualnianie do Azure Search .NET SDK w wersji 5

W przypadku korzystania z wersji 4,0-Preview lub starszej [Azure Search .NET SDK](https://aka.ms/search-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 5.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

Wersja 5 zestawu SDK Azure Search .NET zawiera pewne zmiany z wcześniejszych wersji. Są one w większości drobnych, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy. Zobacz [procedurę uaktualniania](#UpgradeSteps) , aby uzyskać instrukcje dotyczące zmiany kodu w celu użycia nowej wersji zestawu SDK.

> [!NOTE]
> Jeśli używasz wersji 2,0-Preview lub starszej, należy najpierw uaktualnić do wersji 3, a następnie uaktualnić do wersji 5. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search .NET SDK w wersji 3](search-dotnet-sdk-migration.md) .
>
> Wystąpienie usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Co nowego w wersji 5
Wersja 5 zestawu SDK Azure Search .NET jest przeznaczona dla najnowszej ogólnie dostępnej wersji interfejsu API REST Azure Search, w tym 2017-11-11. Dzięki temu można korzystać z nowych funkcji Azure Search z aplikacji .NET, w tym:

* [Synonimy](search-synonyms.md).
* Teraz można programowo uzyskać dostęp do ostrzeżeń w historii wykonywania indeksatora (zobacz Właściwość `Warning` `IndexerExecutionResult` w [dokumentacji platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) , aby uzyskać więcej informacji).
* Obsługa platformy .NET Core 2.
* Nowa struktura pakietu obsługuje tylko te części zestawu SDK, które są potrzebne (zobacz istotne [zmiany w wersji 5](#ListOfChanges) , aby uzyskać szczegółowe informacje).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
Najpierw należy zaktualizować odwołanie NuGet dla `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając pozycję "Zarządzaj pakietami NuGet...". w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany. Jeśli tak, wszystko jest gotowe!

Jeśli kompilacja nie powiedzie się, powinien pojawić się błąd kompilacji podobny do następującego:

    The name 'SuggesterSearchMode' does not exist in the current context

Następnym krokiem jest poprawienie tego błędu kompilacji. Zobacz istotne [zmiany w wersji 5](#ListOfChanges) , aby uzyskać szczegółowe informacje na temat tego, co powoduje błąd i sposób jego rozwiązania.

Należy pamiętać, że ze względu na zmiany w pakowaniu zestawu SDK Azure Search .NET, należy ponownie skompilować aplikację, aby można było użyć wersji 5. Zmiany te zostały szczegółowo opisane w [wersji 5](#ListOfChanges).

Mogą pojawić się dodatkowe ostrzeżenia kompilacji dotyczące przestarzałych metod lub właściwości. Ostrzeżenia będą zawierać instrukcje dotyczące użycia zamiast przestarzałej funkcji. Na przykład jeśli aplikacja używa metody `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType`, należy otrzymać ostrzeżenie informujące o tym, że to zachowanie jest teraz domyślnie włączone, więc wywołanie tej metody nie jest już konieczne.

Po naprawieniu błędów kompilacji lub ostrzeżeń możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK są szczegółowo opisane w temacie [co nowego w wersji 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Istotne zmiany w wersji 5

### <a name="new-package-structure"></a>Nowa struktura pakietu

Najbardziej znacząca zmiana w wersji 5 polega na tym, że zestaw `Microsoft.Azure.Search` i jego zawartość zostały podzielone na cztery oddzielne zestawy, które są teraz dystrybuowane jako cztery oddzielne pakiety NuGet:

 - `Microsoft.Azure.Search`: jest to Meta-pakiet, który zawiera wszystkie inne Azure Search pakiety jako zależności. Jeśli uaktualniasz starszą wersję zestawu SDK, po prostu uaktualnianie tego pakietu i jego ponowne kompilowanie powinno być wystarczające, aby zacząć korzystać z nowej wersji.
 - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET przy użyciu Azure Search i chcesz tylko wysyłać zapytania lub aktualizować dokumenty w swoich indeksach. Jeśli trzeba również utworzyć lub zaktualizować indeksy, mapy synonimów lub inne zasoby na poziomie usług, zamiast tego użyj pakietu `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzację w programie .NET, aby zarządzać indeksami Azure Search, mapami synonimów, indeksatorów, źródłami danych lub innymi zasobami na poziomie usług. Jeśli musisz tylko zbadać lub zaktualizować dokumenty w indeksach, Użyj zamiast tego pakietu `Microsoft.Azure.Search.Data`. Jeśli potrzebujesz wszystkich funkcji Azure Search, zamiast tego użyj pakietu `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Common`: typy wspólne, które są używane przez biblioteki Azure Search .NET. Nie należy używać tego pakietu bezpośrednio w aplikacji; Jest ona przeznaczona tylko do użycia jako zależność.
 
Ta zmiana jest technicznie przerwana, ponieważ wiele typów zostało przeniesionych między zestawami. To dlatego, że ponowne kompilowanie aplikacji jest niezbędne w celu uaktualnienia do wersji 5 zestawu SDK.

Istnieje niewielka liczba innych zmian w wersji 5, które mogą wymagać zmiany kodu oprócz odbudowy aplikacji.

### <a name="change-to-suggesters"></a>Przejdź do sugestii 

Konstruktor `Suggester` nie ma już parametru `enum` do `SuggesterSearchMode`. Ta Wyliczenie ma tylko jedną wartość i dlatego była nadmiarowa. Jeśli w wyniku tego wystąpią błędy kompilacji, wystarczy usunąć odwołania do parametru `SuggesterSearchMode`.

### <a name="removed-obsolete-members"></a>Usunięto przestarzałe składowe

Mogą pojawić się błędy kompilacji związane z metodami lub właściwościami, które zostały oznaczone jako przestarzałe we wcześniejszych wersjach, a następnie usunięte w wersji 5. W przypadku wystąpienia takich błędów poniżej przedstawiono sposób ich rozwiązywania:

- Jeśli używasz metody `IndexingParametersExtensions.IndexStorageMetadataOnly`, użyj `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` zamiast tego.
- Jeśli używasz metody `IndexingParametersExtensions.SkipContent`, użyj `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` zamiast tego.

### <a name="removed-preview-features"></a>Usunięte funkcje w wersji zapoznawczej

W przypadku uaktualniania z wersji 4,0 — wersja zapoznawcza do wersji 5 należy pamiętać, że obsługa tablic JSON i woluminów CSV dla indeksatorów obiektów BLOB została usunięta, ponieważ te funkcje są nadal w wersji zapoznawczej. W szczególnych przypadkach następujące metody klasy `IndexingParametersExtensions` zostały usunięte:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Jeśli aplikacja ma twardą zależność od tych funkcji, nie będzie można przeprowadzić uaktualnienia do wersji 5 Azure Search zestawu SDK platformy .NET. Możesz nadal używać wersji 4,0-Preview. Należy jednak pamiętać, że **nie zalecamy korzystania z wersji zapoznawczej zestawów SDK w aplikacjach produkcyjnych**. Funkcje w wersji zapoznawczej służą wyłącznie do oceny i mogą ulec zmianie.

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz więcej szczegółowych informacji na temat korzystania z zestawu SDK Azure Search platformy .NET, zobacz [temat jak to zrobić](search-howto-dotnet-sdk.md).

Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, skontaktuj się z nami, aby uzyskać pomoc dotyczącą [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem "[Azure Search]".

Dziękujemy za korzystanie z Azure Search!
