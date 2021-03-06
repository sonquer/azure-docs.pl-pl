---
title: Tworzenie i zabezpieczanie kluczy interfejsu API i usługi Query oraz zarządzanie nimi
titleSuffix: Azure Cognitive Search
description: Klucz API-Key kontroluje dostęp do punktu końcowego usługi. Klucze administratora przyznają prawo do zapisu. Klucze zapytań można utworzyć dla dostępu tylko do odczytu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794371"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Tworzenie i zarządzanie kluczami interfejsu API dla usługi Wyszukiwanie poznawcze platformy Azure

Wszystkie żądania do usługi wyszukiwania potrzebują klucza API-tylko do odczytu, który został wygenerowany dla usługi. Klucz API-Key jest jedynym mechanizmem uwierzytelniania dostępu do punktu końcowego usługi wyszukiwania i musi być dołączony do każdego żądania. W [rozwiązaniach REST](search-get-started-postman.md)klucz API-Key jest zazwyczaj określony w nagłówku żądania. W [rozwiązaniach platformy .NET](search-howto-dotnet-sdk.md#core-scenarios)klucz jest często określany jako ustawienie konfiguracji, a następnie przekazywać jako [poświadczenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klucz administratora) lub [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klucz zapytania) w [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Klucze są tworzone wraz z usługą wyszukiwania podczas aprowizacji usług. Można wyświetlać i uzyskiwać kluczowe wartości w [Azure Portal](https://portal.azure.com).

![Strona portalu, ustawienia, sekcja klucze](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co to jest klucz API-Key

Klucz API-Key jest ciągiem zawierającym losowo wygenerowane liczby i litery. Za pomocą [uprawnień opartych na rolach](search-security-rbac.md)można usuwać lub odczytywać klucze, ale nie można zastąpić klucza za pomocą hasła zdefiniowanego przez użytkownika ani używać Active Directory jako podstawowej metodologii uwierzytelniania do uzyskiwania dostępu do operacji wyszukiwania. 

Do uzyskiwania dostępu do usługi wyszukiwania są używane dwa typy kluczy: administrator (odczyt i zapis) i zapytanie (tylko do odczytu).

|Klucz|Opis|Limity|  
|---------|-----------------|------------|  
|Administratora|Przyznaje pełne prawa do wszystkich operacji, w tym możliwość zarządzania usługą, tworzenia i usuwania indeksów, indeksatorów i źródeł danych.<br /><br /> Dwa klucze administratora, określane jako klucze *podstawowe* i *pomocnicze* w portalu, są generowane podczas tworzenia usługi i mogą być osobno ponownie generowane na żądanie. Posiadanie dwóch kluczy umożliwia przewinięcie jednego klucza podczas korzystania z drugiego klucza w celu uzyskania ciągłego dostępu do usługi.<br /><br /> Klucze administratora są określone tylko w nagłówkach żądań HTTP. Nie można umieścić klucza API-Key administratora w adresie URL.|Maksymalnie 2 na usługę|  
|Zapytanie|Przyznaje dostęp tylko do odczytu do indeksów i dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.<br /><br /> Klucze zapytań są tworzone na żądanie. Można je tworzyć ręcznie w portalu lub programowo za pośrednictwem [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klucze zapytania można określić w nagłówku żądania HTTP dla operacji wyszukiwania, sugestii lub wyszukiwania. Alternatywnie można przekazać klucz zapytania jako parametr w adresie URL. W zależności od sposobu, w jaki aplikacja kliencka formułuje żądanie, może być łatwiejsze przekazanie klucza jako parametru zapytania:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 za usługę|  

 Wizualnie nie istnieje różnica między kluczem administratora lub kluczem zapytania. Oba klucze są ciągami składającymi się z 32 losowo generowanych znaków alfanumerycznych. W przypadku utraty informacji o typie klucza określonego w aplikacji można [sprawdzić wartości kluczy w portalu](https://portal.azure.com) lub użyć [interfejsu API REST](https://docs.microsoft.com/rest/api/searchmanagement/) , aby zwrócić wartość i typ klucza.  

> [!NOTE]  
>  Jest uznawana za słabą praktyczną ochronę danych, takich jak `api-key` w identyfikatorze URI żądania. Z tego powodu usługa Azure Wyszukiwanie poznawcze akceptuje tylko klucz zapytania jako `api-key` w ciągu zapytania i należy unikać tego, chyba że zawartość indeksu powinna być publicznie dostępna. Zgodnie z ogólną regułą zaleca się przekazanie `api-key` jako nagłówka żądania.  

## <a name="find-existing-keys"></a>Znajdowanie istniejących kluczy

Klucze dostępu można uzyskać w portalu lub za pomocą [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/). Aby uzyskać więcej informacji, zobacz [Manage admin and Query API-Keys](search-security-api-keys.md).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wyświetl listę [usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dla Twojej subskrypcji.
3. Wybierz usługę i na stronie Przegląd kliknij pozycję **ustawienia** >**klucze** , aby wyświetlić klucze administratora i kwerendy.

   ![Strona portalu, ustawienia, sekcja klucze](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Utwórz klucze zapytań

Klucze zapytań są używane na potrzeby dostępu tylko do odczytu do dokumentów w indeksie dla operacji docelowych kolekcji dokumentów. Zapytania wyszukiwania, filtrowania i sugestii to wszystkie operacje, które pobierają klucz zapytania. Wszystkie operacje tylko do odczytu, które zwracają dane systemowe lub definicje obiektów, takie jak definicja indeksu lub stan indeksatora, wymagają klucza administratora.

Ograniczanie dostępu i operacji w aplikacjach klienckich jest niezbędne do ochrony zasobów wyszukiwania w usłudze. Zawsze używaj klucza zapytania zamiast klucza administratora dla dowolnego zapytania pochodzącego z aplikacji klienckiej.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wyświetl listę [usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dla Twojej subskrypcji.
3. Wybierz usługę i na stronie Przegląd kliknij pozycję **ustawienia** >**klucze**.
4. Kliknij pozycję **Zarządzaj kluczami zapytań**.
5. Użyj klucza zapytania już wygenerowanego dla usługi lub Utwórz do 50 nowych kluczy zapytań. Domyślny klucz zapytania nie ma nazwy, ale dodatkowe klucze zapytania można nazwać do zarządzania.

   ![Tworzenie lub używanie klucza zapytania](media/search-security-overview/create-query-key.png) 

> [!Note]
> Przykład kodu przedstawiający użycie klucza zapytania można znaleźć w temacie [Query a Azure wyszukiwanie poznawcze index in C# ](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

Dla każdej usługi są tworzone dwa klucze administracyjne, dzięki czemu można obrócić klucz podstawowy przy użyciu klucza pomocniczego dla ciągłości biznesowej.

1. Na stronie **ustawienia** >**klucze** skopiuj klucz pomocniczy.
2. W przypadku wszystkich aplikacji zaktualizuj ustawienia interfejsu API-Key, aby użyć klucza pomocniczego.
3. Wygeneruj ponownie klucz podstawowy.
4. Zaktualizuj wszystkie aplikacje, aby użyć nowego klucza podstawowego.

Jeśli przypadkowo ponownie wygenerowano oba klucze jednocześnie, wszystkie żądania klientów korzystające z tych kluczy zakończą się niepowodzeniem z niedozwolonym protokołem HTTP 403. Jednak zawartość nie zostanie usunięta i nie jest trwale zablokowana. 

Nadal możesz uzyskać dostęp do usługi za pomocą portalu lub warstwy zarządzania ([interfejs API REST](https://docs.microsoft.com/rest/api/searchmanagement/), program [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)lub Azure Resource Manager). Funkcje zarządzania działają za pomocą identyfikatora subskrypcji, a nie klucza API usługi, a tym samym nadal są dostępne, nawet jeśli nie są używane klucze API-Keys. 

Po utworzeniu nowych kluczy za pośrednictwem portalu lub warstwy zarządzania dostęp jest przywracany do zawartości (indeksy, indeksatory, źródła danych, mapy synonimów) po utworzeniu nowych kluczy i udostępnieniu tych kluczy na żądaniach.

## <a name="secure-api-keys"></a>Secure API-Keys
Zabezpieczenia klucza są zapewnione przez ograniczenie dostępu za pośrednictwem portalu lub Menedżer zasobów interfejsów (program PowerShell lub interfejs wiersza polecenia). Jak wspomniano, Administratorzy subskrypcji mogą wyświetlać i generować ponownie wszystkie klucze API-Keys. Jako środek ostrożności Przejrzyj przypisania ról, aby zrozumieć, kto ma dostęp do kluczy administratora.

+ Na pulpicie nawigacyjnym usługi kliknij pozycję **Kontrola dostępu (IAM)** , a następnie kartę **przypisania ról** , aby wyświetlić przypisania ról dla usługi.

Członkowie następujących ról mogą wyświetlać i ponownie generować klucze: właściciel, współautor, [Search Service Współautorzy](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> W przypadku dostępu opartego na tożsamościach za pomocą wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, dla których obiekt żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtry zabezpieczeń](search-security-trimming-for-azure-search.md) i [zabezpieczanie przy Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu oparta na rolach na platformie Azure Wyszukiwanie poznawcze](search-security-rbac.md)
+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Artykuł poświęcony wydajności i optymalizacji](search-performance-optimization.md)