---
title: Jak monitorować i zmniejszać ograniczenie przepustowości — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak monitorować, diagnozować i ograniczać problemy z wydajnością, które powodują opóźnienia i ograniczanie przepustowości w Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314831"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorowanie i ograniczanie ograniczania przepustowości w celu zmniejszenia opóźnień w Azure Time Series Insights

Gdy ilość danych przychodzących przekroczy konfigurację środowiska, może wystąpić opóźnienie lub ograniczenie w Azure Time Series Insights.

Można uniknąć opóźnień i ograniczania przepustowości przez prawidłowe skonfigurowanie środowiska pod kątem ilości danych, które mają być analizowane.

Najprawdopodobniej napotkasz opóźnienia i ograniczanie przepustowości, gdy:

- Dodaj źródło zdarzenia, które zawiera stare dane, które mogą przekroczyć przydzieloną stawkę za ruch przychodzący (Time Series Insights będzie musiał wychwycić).
- Dodaj więcej źródeł zdarzeń do środowiska, co spowodowało skok z dodatkowych zdarzeń (co może przekroczyć pojemność środowiska).
- Wypchnij duże ilości zdarzeń historycznych do źródła zdarzeń, co spowodowało zwłokę (Time Series Insights będzie konieczne przechwycenie).
- Dołącz dane referencyjne do telemetrii, co skutkuje większym rozmiarem zdarzenia. Z punktu widzenia ograniczenia przepustowości pakiet danych ingressed o rozmiarze pakietu 32 KB jest traktowany jako zdarzenia 32 o wielkości 1 KB. Maksymalny dozwolony rozmiar zdarzenia to 32 KB; pakiety danych o rozmiarze większym niż 32 KB są obcinane.

## <a name="video"></a>Wideo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Dowiedz się więcej na temat Time Series Insights zachowania związanego z transferem danych przychodzących i sposobu ich planowania.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitoruj opóźnienia i ograniczanie przepustowości przy użyciu alertów

Alerty mogą ułatwić diagnozowanie i łagodzenie problemów opóźnienia występujących w danym środowisku.

1. W Azure Portal Wybierz środowisko Time Series Insights. Następnie wybierz pozycję **alerty**.

   [![dodać alertu do środowiska Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Wybierz pozycję **+ Nowa reguła alertu**. Zostanie wyświetlony panel **Utwórz regułę** . Wybierz pozycję **Dodaj** w obszarze **warunek**.

   [![dodawania okienka alertu](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Następnie skonfiguruj dokładne warunki logiki sygnału.

   [Konfiguracja logiki sygnałów ![](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   W tym miejscu możesz skonfigurować alerty przy użyciu następujących warunków:

   |Metryka  |Opis  |
   |---------|---------|
   |**Bajty odebrane z ruchu przychodzącego**     | Liczba nieprzetworzonych bajtów odczytanych ze źródeł zdarzeń. Licznik nieprzetworzony zazwyczaj zawiera nazwę właściwości i wartość.  |  
   |**Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących**     | Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich Event Hubs platformy Azure lub źródeł zdarzeń platformy Azure IoT Hub.      |
   |**Odebrane komunikaty transferu danych przychodzących**   | Liczba komunikatów odczytywanych ze wszystkich Event Hubs lub źródeł zdarzeń usługi IoT Hub.        |
   |**Bajty przechowywane w ruchu przychodzącym**     | Łączny rozmiar zdarzeń przechowywanych i dostępnych dla zapytania. Rozmiar jest obliczany tylko na wartości właściwości.        |
   |**Zdarzenia związane** z transferem danych przychodzących     |   Liczba zdarzeń spłaszczonych przechowywanych i dostępnych dla kwerendy.      |
   |    **opóźnienia czasu odbierania komunikatu przychodzącego**|  Różnica w sekundach między upływem czasu, w którym komunikat jest przełączany w źródle zdarzenia i czas przetwarzania w danych wejściowych.      |
   |    **opóźnienia liczby odebranych komunikatów** przychodzących|  Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia i numerem sekwencyjnym komunikatu przetwarzanego w ramach ruchu przychodzącego.      |

   Wybierz pozycję **Done** (Gotowe).

1. Po skonfigurowaniu żądanej logiki sygnałów Przejrzyj wybraną regułę alertów wizualnie.

   [Widok opóźnienia ![i tworzenie wykresów](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Ograniczanie i zarządzanie ruchem przychodzącym

* W przypadku ograniczenia przepustowości zostanie zarejestrowana wartość *opóźnienia czasu komunikatu przychodzącego* informującego o tym, ile sekund w środowisku usługi Time Series Insights jest rzeczywisty czas, jaki komunikat trafi do źródła zdarzenia (z wyłączeniem czasu indeksowania w ramach APPX). 30-60 sekund).  

  *Opóźnienie liczby przychodzących komunikatów* przychodzących powinno również mieć wartość, co pozwala określić liczbę komunikatów znajdujących się za Ciebie.  Najprostszym sposobem na przezwyciężenie jest zwiększenie pojemności środowiska do rozmiaru, który umożliwi pokonanie różnic.  

  Na przykład jeśli środowisko S1 wykazuje opóźnienie komunikatów 5 000 000, można zwiększyć rozmiar środowiska do sześciu jednostek przez około raz dziennie, aby przechwycić.  Możesz jeszcze bardziej zwiększyć szybkość przechwytywania. Okres przechwytywania jest typowym wystąpieniem podczas wstępnej aprowizacji środowiska, szczególnie w przypadku łączenia go ze źródłem zdarzeń, które zawiera już zdarzenia w nim lub w przypadku zbiorczego przekazywania wielu danych historycznych.

* Inną techniką jest ustawienie alertu dotyczącego **zdarzeń związanych** z transferem danych przychodzących > = próg nieco niższy od całkowitej pojemności środowiska przez okres 2 godzin.  Ten alert może pomóc w zrozumieniu, czy ma on ciągle pojemność, co oznacza wysokie prawdopodobieństwo opóźnienia. 

  Na przykład jeśli masz trzy jednostki S1 z obsługą administracyjną (lub 2100 zdarzeń na minutę), możesz ustawić alert dotyczący **zdarzeń** związanych z transferem danych przychodzących dla zdarzeń > = 1900 przez 2 godziny. W przypadku ciągłego przekraczania tego progu, w związku z czym wyzwalany jest alert, jest to najkorzystniej obsługiwane.  

* Jeśli podejrzewasz, że masz ograniczenie przepustowości, możesz porównać **odebrane komunikaty przychodzące** z komunikatami egressed źródła zdarzeń.  Jeśli ruch przychodzący do centrum zdarzeń jest większy niż **odebrane komunikaty**transferu danych przychodzących, prawdopodobnie Time Series Insights są ograniczone.

## <a name="improving-performance"></a>Poprawianie wydajności

Aby zmniejszyć ograniczenia przepustowości lub opóźnienia, najlepszym sposobem na jego poprawienie jest zwiększenie wydajności środowiska.

Można uniknąć opóźnień i ograniczania przepustowości przez prawidłowe skonfigurowanie środowiska pod kątem ilości danych, które mają być analizowane. Aby uzyskać więcej informacji na temat dodawania pojemności do środowiska, Przeczytaj [skalę środowiska](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj informacje o [diagnozowaniu i rozwiązywaniu problemów w środowisku Time Series Insightsu](time-series-insights-diagnose-and-solve-problems.md).

- Dowiedz się [, jak skalować środowisko Time Series Insights](time-series-insights-how-to-scale-your-environment.md).