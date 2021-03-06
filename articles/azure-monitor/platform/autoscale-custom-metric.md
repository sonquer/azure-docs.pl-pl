---
title: Skalowanie automatyczne na platformie Azure przy użyciu metryki niestandardowej
description: Dowiedz się, jak skalować zasób według metryki niestandardowej na platformie Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425123"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Wprowadzenie do automatycznego skalowania według metryki niestandardowej na platformie Azure
W tym artykule opisano sposób skalowania zasobu według metryki niestandardowej w Azure Portal.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [klastra Eksplorator danych platformy Azure](https://azure.microsoft.com/services/data-explorer/) ,   
Usługi środowisko usługi integracji i [API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Umożliwia rozpoczęcie pracy
W tym artykule przyjęto założenie, że masz aplikację sieci Web z skonfigurowanym usługą Application Insights. Jeśli jeszcze tego nie zrobiono, możesz [skonfigurować Application Insights dla witryny sieci web ASP.NET][1]

- Otwórz [Azure Portal][2]
- Kliknij ikonę Azure Monitor w okienku nawigacji po lewej stronie.
  ![uruchamiania Azure Monitor][3]
- Kliknij ustawienie automatycznego skalowania, aby wyświetlić wszystkie zasoby, dla których ma zastosowanie automatyczne skalowanie, wraz z bieżącym stanem automatycznego skalowania ![odnajdywania automatycznego skalowania w usłudze Azure monitor][4]
- Otwórz blok "Skalowanie automatyczne" w Azure Monitor i wybierz zasób, który chcesz skalować
  > Uwaga: poniższe kroki używają planu usługi App Service skojarzonego z aplikacją sieci Web, dla której skonfigurowano usługę App Insights.
- W bloku ustawienia skalowania zasobu Zwróć uwagę, że bieżąca liczba wystąpień to 1. Kliknij pozycję "Włącz Skalowanie automatyczne".
  ![ustawienia skalowania dla nowej aplikacji internetowej][5]
- Podaj nazwę ustawienia skalowania i kliknij pozycję "Dodaj regułę". Zwróć uwagę na Opcje reguły skalowania, które są otwierane w okienku kontekstowym po prawej stronie. Domyślnie ustawia opcję skalowania liczby wystąpień o 1, jeśli wartość procentowa procesora CPU zasobu przekracza 70%. Zmień źródło metryki w górnej części na "Application Insights", wybierz zasób usługi App Insights na liście rozwijanej "Resource", a następnie wybierz metrykę niestandardową, na podstawie której chcesz skalować.
  ![skalowanie według metryki niestandardowej][6]
- Podobnie jak powyżej, Dodaj regułę skalowania, która będzie skalowana w poziomie i zmniejsz liczbę skalowania o 1, jeśli Metryka niestandardowa jest niższa od wartości progowej.
  Skalowanie ![oparte na procesorach][7]
- Ustaw limity wystąpień. Na przykład jeśli chcesz skalować między 2-5 wystąpieniami w zależności od niestandardowych fluktuacji metryk, ustaw wartość "minimum" na "2", "maksimum" na "5" i "domyślne" na "2"
  > Uwaga: w przypadku wystąpienia problemu z odczytem metryk zasobów i aktualna pojemność jest niższa niż domyślna pojemność, aby zapewnić dostępność zasobu, Skalowanie automatyczne zostanie przeskalowane do wartości domyślnej. Jeśli bieżąca pojemność jest już wyższa niż domyślna pojemność, Skalowanie automatyczne nie będzie skalowane.
- Kliknij pozycję "Zapisz".

Gratulacje. Pomyślnie utworzono ustawienia skalowania w celu automatycznego skalowania aplikacji sieci Web na podstawie metryki niestandardowej.

> Uwaga: te same kroki mają zastosowanie, aby rozpocząć pracę z rolą usługi VMSS lub w chmurze.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

