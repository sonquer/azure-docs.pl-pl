---
title: Wykonywanie zapytań dotyczących danych ze środowiska w C# wersji zapoznawczej przy użyciu polecenia-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights przy użyciu aplikacji C#, która została zapisywana w.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980991"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights w wersji zapoznawczej przy użyciuC#

W C# tym przykładzie przedstawiono sposób wykonywania zapytań dotyczących danych z [interfejsów API dostępu do danych w wersji zapoznawczej](https://docs.microsoft.com/rest/api/time-series-insights/preview) w środowiskach Azure Time Series Insights w wersji zapoznawczej

> [!TIP]
> Wyświetl przykłady C# kodu w wersji zapoznawczej w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod ilustruje następujące funkcje:

* Obsługa autogeneracji zestawu SDK z [usługi Azure AutoRest](https://github.com/Azure/AutoRest).
* Jak uzyskać token dostępu za pośrednictwem Azure Active Directory przy użyciu [programu Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Jak przekazać token uzyskany dostęp w nagłówku `Authorization` kolejnych żądań interfejsu API uzyskiwania dostępu do danych. 
* Przykład zawiera interfejs konsoli pokazujący, jak są wysyłane żądania HTTP do:

    * [Interfejs API środowisk w wersji zapoznawczej](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Pobierz interfejs API dostępności środowisk](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) i [Pobierz interfejs API schematu zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Interfejs API zapytań w wersji zapoznawczej](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Pobierz](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)interfejs API zdarzeń, [Pobierz interfejs API serii](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)i [Pobierz interfejs API serii agregowanych](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Interfejsy API modelu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Pobierz hierarchie API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) i [HIERARCHIe interfejsu API usługi Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Pobieranie typów interfejsu](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) API usługi [Batch i typów](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Uzyskiwanie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) interfejsu API usługi Batch wystąpień i [wystąpień](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Zaawansowane funkcje [wyszukiwania](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) i [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) .

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i Instalator

Przed skompilowaniem i uruchomieniem przykładowego kodu wykonaj następujące czynności:

1. [Zainicjuj obsługę środowiska Azure Time Series Insights w wersji zapoznawczej](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Skonfiguruj środowisko Azure Time Series Insights dla Azure Active Directory zgodnie z opisem w temacie [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). 
1. Uruchom [GenerateCode. bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) określony w [README.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) w celu wygenerowania zależności klienta Time Series Insights w wersji zapoznawczej.
1. Otwórz rozwiązanie `TSIPreviewDataPlaneclient.sln` i ustaw `DataPlaneClientSampleApp` jako projekt domyślny w programie Visual Studio.
1. Zainstaluj wymagane zależności projektu, wykonując kroki opisane [poniżej](#project-dependencies) , i skompiluj przykład do pliku wykonywalnego `.exe`.
1. Uruchom plik `.exe`, klikając go dwukrotnie.

## <a name="project-dependencies"></a>Zależności projektu

Zalecamy użycie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2 +

Przykładowy kod zawiera kilka wymaganych zależności, które można wyświetlić w pliku [Packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) .

Pobierz pakiety w programie Visual Studio 2019, wybierając opcję **kompiluj** > **Kompiluj rozwiązanie** . 

Alternatywnie możesz dodać każdy pakiet przy użyciu narzędzia [NuGet 2.12 +](https://www.nuget.org/). Przykład:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#przykładowy kod

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Przykładowy kod można wykonać bez zmiany domyślnych zmiennych środowiskowych.
> * Przykładowy kod zostanie skompilowany do aplikacji konsoli wykonywalnej platformy .NET.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.
