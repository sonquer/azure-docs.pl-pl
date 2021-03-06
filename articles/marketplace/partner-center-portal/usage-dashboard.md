---
title: Pulpit nawigacyjny użycia w komercyjnej analizie Marketplace w centrum partnerskim
description: Dowiedz się, jak uzyskać dostęp do wszystkich maszyn wirtualnych, w których są dostępne metryki dotyczące rozliczeń.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: fb0e0e8f1a514ab7d21e97e1fb2aaed3ea39b088
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481076"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Pulpit nawigacyjny użycia w komercyjnej analizie Marketplace

Ten artykuł zawiera informacje na temat pulpitu nawigacyjnego użycie w centrum partnerskim. Ten pulpit nawigacyjny wyświetla wszystkie maszyny wirtualne, które oferuje metryki użycia i naliczanych rozliczeń na dwóch oddzielnych kartach: użycie maszyn wirtualnych i zliczanie opłat.

Aby uzyskać dostęp do pulpitu nawigacyjnego użycie, Otwórz pulpit nawigacyjny **[Analizuj](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** w obszarze komercyjny Portal Marketplace.

>[!NOTE]
> Aby uzyskać szczegółowe definicje terminologii dotyczącej analizy, zobacz [często zadawane pytania i terminologię dotyczącą komercyjnej analizy portalu Marketplace](./faq-terminology.md).

## <a name="usage-dashboard"></a>Pulpit nawigacyjny użycia

Pulpit nawigacyjny użycia przedstawia metryki dla całej maszyny wirtualnej (VM), które zapewniają użycie i pomiarowe użycie rozliczeń. Znajdują się one na dwóch oddzielnych kartach: użycie maszyn wirtualnych i obciążenie taryfowe.

Na karcie użycie maszyny wirtualnej znajdują się graficzne reprezentacje następujących elementów:

- [Podsumowanie użycia](#usage-summary)
- [Użycie według położenia geograficznego](#usage-by-geography)
- [Użycie przez oferty](#usage-by-offers)
- [Trend użycia według ofert i jednostek SKU](#usage-trend-by-offers-and-skus)
- [Użycie według typu oferty](#usage-by-offer-type)
- [Użycie według rozmiaru maszyny wirtualnej](#usage-by-vm-size)
- [Użycie według kanału sprzedaży](#usage-by-sales-channel)
- [Szczegółowe dane użycia](#detailed-usage-data)

> [!NOTE]
> Raporty analityczne są wyświetlane inaczej w portal Cloud Partner (CPP) i centrum partnerskie. **Informacje o sprzedawcy** w programie CPP mają kartę zamówienia i użycie, która wyświetla dane dla ofert opartych na użyciu i nieopartych na użyciu. W centrum partnerskim metryki użycia są wyświetlane na osobnej stronie.

### <a name="usage-summary"></a>Podsumowanie użycia

W tabeli Podsumowanie użycia są wyświetlane godziny użycia klientów dla wszystkich zakupionych ofert.

- Znormalizowane godziny użycia są definiowane jako godziny użycia znormalizowane w celu uwzględnienia liczby rdzeni maszyn wirtualnych ([liczba rdzeni maszyn wirtualnych] x [godz. użycia pierwotnego]). Maszyny wirtualne oznaczone jako "SHAREDCORE" wykorzystują 1/6 (lub 0,1666) jako mnożnik [liczba rdzeni maszyn wirtualnych].
- Godziny użycia pierwotnego są definiowane jako czas działania maszyn wirtualnych w godzinach.
- Wartość procentowa reprezentuje zmianę wzrostu zużycia dla wybranego zakresu dat ([ostatnie użycie miesiąca – pierwsze użycie miesiąca])/użycie pierwszego miesiąca).
- Zielone Trójkąty wskazujące zmianę wzrostu.
- Czerwony trójkąt skierowany w dół wskazuje negatywną zmianę wzrostu względem poprzedniego miesiąca.
- Wykresy mikro bar reprezentują wartości miesięczne. Możesz wyświetlić wartość dla każdego miesiąca, umieszczając kursor nad kolumnami na wykresie.

### <a name="usage-by-geography"></a>Użycie według położenia geograficznego

**Znormalizowane użycie według lokalizacji geograficznej** mapę cieplną wyświetla godziny użycia mapowane zgodnie z krajem klienta. Odmiana koloru kraju reprezentuje znormalizowane stężenie użycia. Przywróć widok oryginalny, naciskając przycisk Home ( **Strona główna** ) na mapie.

### <a name="usage-by-offers"></a>Użycie przez oferty

- Wykres kołowy **znormalizowane użycie przez oferty** przedstawia podział znormalizowanych godzin użycia według ofert zgodnie z wybranym zakresem dat. 5 najważniejszych ofert są wyświetlane w grafie, podczas gdy reszta jest zgrupowana w kategorii "REST All".
- Wykres słupkowy przedstawia trend wzrostu miesiąc po miesiącu dla wybranego zakresu dat. Kolumny miesiąc przedstawiają godziny użycia z ofert o największej godzinie użycia dla danego miesiąca. Wykres liniowy przedstawia trend procentu wzrostu wykreślony na pomocniczej osi Y.
- Użyj suwaka u góry wykresu, aby przewinąć w prawo do lewej wzdłuż osi x i/lub skupić się na określonych punktach danych.

### <a name="usage-trend-by-offers-and-skus"></a>Trend użycia według ofert i jednostek SKU

Ten wykres przedstawia trend znormalizowanych użycia dla wybranych jednostek SKU oferty. Oferta rankingów zawiera najpopularniejsze oferty 50 o największym użyciu i są posortowane według godzin użycia. Rankingów SKU zawiera listę głównych jednostek SKU 50 o największym użyciu dla wybranej oferty.

### <a name="usage-by-offer-type"></a>Użycie według typu oferty

- Wykres kołowy **użycie według typu oferty** organizuje użycie zgodnie z typem oferty.
- Pierwsze oferty są wyświetlane na wykresie, a pozostałe oferty są pogrupowane jako "REST All".
- Wykres **trendu** przedstawia trendy dotyczące wzrostu miesiąca na miesiąc. Kolumna month reprezentuje użycie według typów oferty najwyższej w danym miesiącu.

### <a name="usage-by-vm-size"></a>Użycie według rozmiaru maszyny wirtualnej

Ten wykres przedstawia trend użycia dla wybranych rozmiarów maszyn wirtualnych (maksymalnie 5) wszystkich ofert/jednostek SKU. Wykres kolumnowy jest układany w godzinach użycia wybranych rozmiarów maszyn wirtualnych.

W rankingów są wyświetlane najbardziej 50 rozmiary maszyn wirtualnych o największym wykorzystaniu i posortowane według godzin użycia.

### <a name="usage-by-sales-channel"></a>Użycie według kanału sprzedaży

- Wykres kołowy użycie według kanału sprzedaży organizuje użycie zgodnie z kanałem sprzedaży
- Na wykresie zostanie wyświetlony górny kanał sprzedaży z najwyższym użyciem, a pozostała część kanału sprzedaży jest zgrupowana jako "REST All".
- Kolumna month reprezentuje użycie według najlepszych kanałów sprzedaży w danym miesiącu.
- Funkcje tego wykresu są takie same jak wykres "użycie przez oferty"

### <a name="detailed-usage-data"></a>Szczegółowe dane użycia

W **tabeli Szczegóły użycia** zostanie wyświetlona lista numerowana pierwszych rekordów użycia 1000 posortowanych według użycia.

- Każda kolumna w siatce jest do sortowania.
- Dane można wyodrębnić do pliku CSV, jeśli liczba rekordów jest mniejsza niż 1000.
- Jeśli liczba rekordów przekracza 1000, dane eksportu będą asynchroniczne umieszczane na stronie pobierania, która będzie dostępna w ciągu następnych 30 dni.
- Filtry można zastosować do **szczegółowych danych użycia** , aby wyświetlić tylko te dane, które Cię interesują. Dane można filtrować według kraju, kanału sprzedaży, typu licencji Marketplace, typu użycia, nazwy oferty, typu oferty, bezpłatnych wersji próbnych, identyfikatora subskrypcji witryny Marketplace, identyfikatora klienta i nazwy firmy.

> [!NOTE]
> Wybierz **Typ użycia** w filtrze strony, aby wyświetlić wykresy na stronie w widoku "znormalizowany widok" lub "widok nieprzetworzony". Domyślnym widokiem tych wykresów jest "widok znormalizowany".

**Filtry stron użycia** są stosowane na poziomie strony. Możesz wybrać wiele filtrów, aby renderować wykres dla kryteriów, które chcesz wyświetlić, oraz danych, które mają być wyświetlane w siatce/eksporcie "szczegółowe dane użycia". Filtry są stosowane do danych wyodrębnionych dla zakresu danych wybranego w prawym górnym rogu strony Orders (zamówienia).

- **Typy ofert** i **nazwy ofert** są wyświetlane tylko dla ofert, które zostały nabyte w wybranym zakresie dat. Nazwy ofert na liście są wyświetlane dla typów ofert wybranych z listy.
- Wybór domyślny to "All" dla każdej opcji filtru, z wyjątkiem **typu użycia**. Domyślny wybór dla **typu użycia** to znormalizowane użycie. Aby wyświetlić surowe użycie na wykresach, wybierz pozycję "użycie pierwotne".
- Zastosowane filtry pokazują wybrane opcje liczby dla wybranych opcji filtrowania. Zastosowane filtry nie są wyświetlane dla opcji domyślnych.

> [!NOTE]
> Szczegółowa definicja każdego z pól w siatce "szczegółowe dane zamówienia", filtry stron i wszystkie możliwe wybory są zdefiniowane w sekcji słownik danych artykułu [często zadawane pytania i terminologia](link needed) .

Karta **taryfowe użycie rozliczeń** przedstawia informacje o użyciu dla typów ofert, w których użycie jest mierzone przez wymiar miernika. Typ nadwyżki oferty SaaS jest obecnie prezentowany. Na karcie przedstawiono graficzną reprezentację trendów nadwyżkowych w przypadku używania rozliczeń SaaS z naliczaniem:

- **Trend nadwyżkowy według wymiaru miernika**: przedstawia miesięczną wartość trendu nadwyżkowego dla wybranego wymiaru licznika oferty. Oś X reprezentuje miesiąc, a oś Y reprezentuje ilość użycia. Jednostka pomiaru licznika niestandardowego jest również wyświetlana na osi Y.
- **Trend nadwyżkowy według jednostki SKU**: reprezentuje trend ilości użycia wybranego wymiaru licznika według jednostek SKU. Wyświetlone jednostki SKU będą reprezentować 5 głównych jednostek SKU z największą ilością użycia wybranej oferty.
- **Trend nadwyżkowy według najlepszych 50 klientów**: oferty z największą ilością w wysokości 50 są wyświetlane na ***tablicy lidera*** i są klasyfikowane według najwyższego użycia miernika niestandardowego. Wybierz klienta w rankingów, aby wyświetlić trend użycia wybranego wymiaru miernika.
- **Trend nadwyżkowy według klientów najczęściej**: przedstawia najważniejsze percentyle klienta, które przyczyniają się do% całkowitego użycia. Najwyższy percentyl klienta jest wyświetlany wzdłuż osi X i jest określany przez ilość użycia klienta. Oś Y Wyświetla ilość użycia. Możesz wyświetlić szczegóły, umieszczając kursor nad punktami wzdłuż wykresu liniowego.

> [!NOTE]
> Szczegóły użycia i wszystkie wykresy na tej stronie są wyświetlane dla danego wymiaru licznika dla filtru strony.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem raportów analitycznych dostępnych na rynku komercyjnym centrum partnerskiego, zobacz [Analiza portalu komercyjnego w centrum partnerskim](./analytics.md).
- W przypadku grafów, trendów i wartości zagregowanych danych, które podsumowują aktywność witryny Marketplace dla oferty, zobacz [pulpit nawigacyjny podsumowania w portalu komercyjnej analizy Marketplace](./summary-dashboard.md).
- Aby uzyskać informacje o zamówieniach w formacie graficznym i do pobrania, zobacz temat [Zamówienie pulpitu nawigacyjnego w portalu komercyjnej analizy Marketplace](./orders-dashboard.md).
- Aby uzyskać szczegółowe informacje na temat klientów, w tym trendów wzrostu, zobacz [pulpit nawigacyjny klienta w komercyjnej analizie portalu Marketplace](./customer-dashboard.md).
- Aby uzyskać listę żądań pobierania w ciągu ostatnich 30 dni, zobacz [pulpit nawigacyjny w portalu komercyjnej analizy Marketplace](./downloads-dashboard.md).
- Aby wyświetlić skonsolidowany widok opinii klientów na temat ofert w witrynie Azure Marketplace i AppSource, zobacz [pulpit nawigacyjny oceny i przeglądów w portalu komercyjnej analizy Marketplace](./ratings-reviews.md).
- Często zadawane pytania dotyczące analizy komercyjnej portalu Marketplace i kompleksowego słownika warunków dotyczących danych można znaleźć [w temacie często zadawane pytania i terminologia dotyczące komercyjnej analizy w portalu Marketplace](./faq-terminology.md).
