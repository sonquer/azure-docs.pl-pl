---
title: Programowanie przy użyciu interfejsów API v3
titleSuffix: Azure Media Services
description: Informacje o regułach, które są stosowane do jednostek i interfejsów API podczas tworzenia z Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4a3b699c90e1fefb834f8ddfe3a23fc2a97354ec
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186126"
---
# <a name="develop-with-media-services-v3-apis"></a>Programowanie przy użyciu interfejsów API Media Services v3

Jako deweloper możesz użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Interfejs API usługi [Media Services v3](https://aka.ms/ams-v3-rest-sdk) opiera się na specyfikacji interfejsu OpenAPI (wcześniej znanej jako struktura Swagger).

W tym artykule omówiono reguły dotyczące jednostek i interfejsów API podczas opracowywania programu z Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API Azure Media Services

Aby uzyskać autoryzację w celu uzyskania dostępu do zasobów Media Services i interfejsu API Media Services, należy najpierw uwierzytelnić się. Media Services obsługuje uwierzytelnianie [oparte na Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) . Dostępne są dwie typowe opcje uwierzytelniania:
 
* **Uwierzytelnianie jednostki usługi**: używane do uwierzytelniania usługi (na przykład: aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejs API i mikrousługi). Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania. Na przykład w przypadku aplikacji sieci Web zawsze powinna być warstwą średnią, która łączy się z Media Services za pomocą nazwy głównej usługi.
* **Uwierzytelnianie użytkownika**: służy do uwierzytelniania osoby korzystającej z aplikacji w celu współdziałania z zasobami Media Services. Aplikacja interakcyjna powinna najpierw monitować użytkownika o podanie poświadczeń użytkownika. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo.

Interfejs API Media Services wymaga, aby użytkownik lub aplikacja wykonująca żądania interfejsu API REST mieli dostęp do zasobu konta Media Services i korzystała z roli **współautor** lub **właściciela** . Dostęp do interfejsu API można uzyskać przy użyciu roli **czytelnik** , ale będą dostępne tylko operacje **Get** lub **list** . Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach dla kont Media Services](rbac-overview.md).

Zamiast tworzyć jednostkę usługi, należy rozważyć użycie zarządzanych tożsamości dla zasobów platformy Azure w celu uzyskania dostępu do interfejsu API Media Services za pośrednictwem Azure Resource Manager. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Nazwa główna usługi Azure AD

Jeśli tworzysz aplikację usługi Azure AD i nazwę główną usługi, aplikacja musi znajdować się w własnej dzierżawie. Po utworzeniu aplikacji nadaj **współautorowi** aplikacji lub roli **właściciela** dostęp do konta Media Services.

Jeśli nie masz pewności, czy masz uprawnienia do tworzenia aplikacji usługi Azure AD, zobacz [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na poniższej ilustracji liczba reprezentuje przepływ żądań w kolejności chronologicznej:

![Uwierzytelnianie aplikacji warstwy środkowej przy użyciu usługi AAD z internetowego interfejsu API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikacja warstwy środkowej żąda tokenu dostępu usługi Azure AD, który ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.
   * Media Services identyfikator URI zasobu.
   * Identyfikator URI zasobu dla Media Services REST.
   * Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta.

   Aby uzyskać wszystkie konieczne wartości, zobacz [dostęp Azure Media Services interfejsu API w interfejsie wiersza polecenia platformy Azure](access-api-cli-how-to.md).

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API REST usługi Azure Media przy użyciu tokenu usługi Azure AD.
5. Warstwa środkowa pobiera dane z Media Services.

### <a name="samples"></a>Przykłady

Zapoznaj się z poniższymi przykładami, które pokazują, jak nawiązać połączenie z jednostką usługi Azure AD:

* [Połącz z usługą REST](media-rest-apis-with-postman.md)  
* [Nawiązywanie połączeń przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy Node.js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączeń przy użyciu języka Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID).

Nazwy zasobów Media Services nie mogą zawierać: "<", ">", "%", "&", ":",&#92;"", "?", "/", "*", "+", ".", znaku pojedynczego cudzysłowu ani znaków kontrolnych. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków.

Aby uzyskać więcej informacji na temat nazewnictwa Azure Resource Manager, zobacz [wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nazwy plików/obiektów BLOB w obrębie elementu zawartości

Nazwy plików/obiektów BLOB w obrębie elementu zawartości muszą spełniać zarówno [wymagania dotyczące nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , jak i [wymagania dotyczące nazw NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Przyczyną tych wymagań jest możliwość skopiowania plików z magazynu obiektów BLOB do lokalnego dysku NTFS w celu przetworzenia.

## <a name="long-running-operations"></a>Długotrwałe operacje

Operacje oznaczone `x-ms-long-running-operation` w [plikach struktury Azure Media Services Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) są długotrwałymi operacjami. 

Aby uzyskać szczegółowe informacje o sposobie śledzenia asynchronicznych operacji platformy Azure, zobacz [asynchroniczne operacje](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services ma następujące długotrwałe operacje:

* [Utwórz zdarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aktualizowanie wydarzeń na żywo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Usuń wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Uruchom wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Zatrzymaj LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Użyj parametru `removeOutputsOnStop`, aby usunąć wszystkie skojarzone wyjście na żywo podczas zatrzymywania zdarzenia.  
* [Resetuj LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Utwórz LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Usuń LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Utwórz StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aktualizacja StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Usuń StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Uruchom StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Zatrzymaj StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Skalowanie StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Po pomyślnym wysłaniu długiej operacji otrzymasz komunikat "202 zaakceptował" i musi on być sondowany o uzupełnianie operacji przy użyciu zwróconego identyfikatora operacji.

W artykule [śledzenie asynchronicznych operacji na platformie Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) wyjaśniono, jak śledzić stan asynchronicznych operacji platformy Azure za pomocą wartości zwracanych w odpowiedzi.

Tylko jedna długotrwała operacja jest obsługiwana dla danego zdarzenia na żywo lub dowolnego skojarzonego z nim wyjścia na żywo. Po uruchomieniu należy wykonać długotrwałą operację przed rozpoczęciem kolejnej długotrwałej operacji na tym samym LiveEvent lub wszystkich skojarzonych danych wyjściowych na żywo. W przypadku wydarzeń na żywo z wieloma wyjściami dynamicznymi musisz oczekiwać na ukończenie długotrwałej operacji na jednym z danych wyjściowych na żywo przed wyzwoleniem długotrwałej operacji na innych danych wyjściowych na żywo. 

## <a name="sdks"></a>Zestawy SDK

> [!NOTE]
> Zestawy SDK Azure Media Services V3 nie mają gwarancji, że są bezpieczne wątkowo. Podczas tworzenia aplikacji wielowątkowej należy dodać własną logikę synchronizacji wątków, aby chronić klienta lub użyć nowego obiektu AzureMediaServicesClient na wątek. Należy również zachować ostrożność w przypadku problemów z wielowątkowością wprowadzanych przez obiekty opcjonalne dostarczone przez kod klientowi (na przykład wystąpienie HttpClient w programie .NET).

|SDK|Dokumentacja|
|---|---|
|[Zestaw SDK platformy .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Dokumentacja platformy .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)|[Dokumentacja języka Java](https://aka.ms/ams-v3-java-ref)|
|[Zestaw SDK dla języka Python](https://aka.ms/ams-v3-python-sdk)|[Dokumentacja języka Python](https://aka.ms/ams-v3-python-ref)|
|[Zestaw SDK dla platformy Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Dokumentacja środowiska Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Zestaw SDK dla języka Go](https://aka.ms/ams-v3-go-sdk) |[Dokumentacja języka Go](https://aka.ms/ams-v3-go-ref)|
|[Zestaw SDK dla języka Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zobacz też

- [EventGrid .NET SDK zawierający zdarzenia usługi Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Kodeks postępowania — często zadawane pytania](https://opensource.microsoft.com/codeofconduct/faq/) lub skontaktuj się opencode@microsoft.com z innymi pytaniami lub komentarzami.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrowanie, porządkowanie, stronicowanie jednostek Media Services

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z Media Services przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączenia z usługą Media Services przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączenia z Media Services przy użyciu środowiska Node. js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączenia z Media Services przy użyciu języka Python](configure-connect-python-howto.md)
