---
title: Azure Monitor dla maszyn wirtualnych (GA) — często zadawane pytania | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, które łączy kondycję i monitorowanie wydajności systemu operacyjnego Azure VM, a także automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między niego. W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące wersji GA.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 673cda4005d9c985d8d6ee5ef1d28a3d8c241ac0
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482888"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor dla maszyn wirtualnych ogólnie dostępna (GA) często zadawanych pytań

Ta ogólna dostępność często zadawanych pytań dotyczy zmian, które są wykonywane w Azure Monitor dla maszyn wirtualnych przygotowywaniu do naszej wersji. 

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizacje dla Azure Monitor dla maszyn wirtualnych

Udostępnimy nową wersję Azure Monitor dla maszyn wirtualnych. Klienci, którzy włączają monitory platformy Azure dla maszyn wirtualnych, otrzymają teraz nową wersję, ale istniejący klienci już korzystający z programu Azure Monitor dla maszyn wirtualnych będą monitowani o uaktualnienie. Te często zadawane pytania i dokumentacja oferują wskazówki dotyczące przeprowadzania uaktualnienia w odpowiedniej skali, jeśli masz duże wdrożenia w wielu obszarach roboczych.

W przypadku tego uaktualnienia Azure Monitor dla maszyn wirtualnych dane wydajności są przechowywane w tej samej tabeli *InsightsMetrics* co [Azure monitor dla kontenerów](container-insights-overview.md), co ułatwia wykonywanie zapytań dotyczących dwóch zestawów danych. Ponadto można przechowywać bardziej zróżnicowane zestawy danych, które nie mogły być przechowywane w tabeli, która została wcześniej użyta. 

Nasze widoki wydajności używają teraz danych przechowywanych w tabeli *InsightsMetrics* .  Jeśli jeszcze nie uaktualniono do korzystania z najnowszego rozwiązania VMInsights w obszarze roboczym, na wykresach nie będą już wyświetlane informacje.  Możesz przeprowadzić uaktualnienie **z naszej strony wprowadzenie,** zgodnie z poniższym opisem.

W przypadku tego uaktualnienia Azure Monitor dla maszyn wirtualnych dane dotyczące wydajności są przechowywane w tej samej tabeli *InsightsMetrics* co [Azure monitor dla kontenerów](container-insights-overview.md) , co ułatwia wykonywanie zapytań dotyczących dwóch zestawów danych. Można również przechowywać bardziej zróżnicowane zestawy danych, które nie mogły być przechowywane w tabeli, która została wcześniej użyta. 

Zdajemy sobie sprawę, że zaproszenie istniejących klientów o uaktualnienie powoduje zakłócenia w przepływie pracy, co oznacza, że wybrano tę czynność teraz w publicznej wersji zapoznawczej, a nie później.


## <a name="what-is-changing"></a>Co się zmieni?

Opublikowano nowe rozwiązanie o nazwie VMInsights, które zawiera dodatkowe możliwości zbierania danych oraz nową lokalizację do przechowywania tych danych w obszarze roboczym Log Analytics. 

W przeszłości włączono rozwiązanie ServiceMap w obszarze roboczym i skonfigurujesz liczniki wydajności w obszarze roboczym Log Analytics, aby wysłać dane do tabeli *wydajności* . To nowe rozwiązanie wysyła dane do tabeli o nazwie *InsightsMetrics* , która jest również używana przez Azure monitor dla kontenerów. Ten schemat tabeli umożliwia przechowywanie dodatkowych metryk i zestawów danych usług, które nie są zgodne z formatem tabeli *wydajności* .

Zaktualizowaliśmy wykresy wydajnościowe, aby użyć przechowywanych danych w tabeli *InsightsMetrics* . Możesz przeprowadzić uaktualnienie, aby użyć tabeli *InsightsMetrics* **na naszej stronie wprowadzenie,** zgodnie z poniższym opisem.


## <a name="how-do-i-upgrade"></a>Jak mogę uaktualnić?
Gdy obszar roboczy Log Analytics zostanie uaktualniony do najnowszej wersji Azure Monitor do maszyn wirtualnych, zostanie uaktualniony Agent zależności na każdej z maszyn wirtualnych dołączonych do tego obszaru roboczego. Każda maszyna wirtualna wymagająca uaktualnienia zostanie zidentyfikowana na karcie **wprowadzenie** w Azure Monitor dla maszyn wirtualnych w Azure Portal. W przypadku wybrania opcji uaktualnienia maszyny wirtualnej program przeprowadzi uaktualnienie obszaru roboczego dla tej maszyny wirtualnej wraz z innymi maszynami wirtualnymi dołączonymi do tego obszaru roboczego. Można wybrać pojedynczą maszynę wirtualną lub wiele maszyn wirtualnych, grup zasobów lub subskrypcji. 

Użyj następującego polecenia, aby uaktualnić obszar roboczy przy użyciu programu PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co należy zrobić o licznikach wydajności w obszarze mój obszar roboczy, jeśli zainstaluję rozwiązanie VMInsights?

Bieżąca metoda włączania Azure Monitor dla maszyn wirtualnych używa liczników wydajności w obszarze roboczym. Nowa metoda zapisuje te dane w nowej tabeli o nazwie `InsightsMetrics`.

Po zaktualizowaniu naszego interfejsu użytkownika w celu używania danych z tabeli `InsightsMetrics` firma Microsoft zaktualizuje naszą dokumentację i przekaże ten anons za pośrednictwem wielu kanałów, w tym wyświetlanie transparentu w Azure Portal. W tym momencie można wyłączyć te [liczniki wydajności](vminsights-enable-overview.md#performance-counters-enabled) w obszarze roboczym, jeśli ich nie trzeba już używać. 

>[!NOTE]
>Jeśli masz reguły alertów odwołujące się do tych liczników w tabeli `Perf`, musisz je zaktualizować w celu odwoływania się do nowych danych przechowywanych w tabeli `InsightsMetrics`. Zapoznaj się z naszą dokumentacją dotyczącą przykładowych zapytań dzienników, których można użyć w odniesieniu do tej tabeli.
>

Jeśli zdecydujesz się na włączenie włączonych liczników wydajności, naliczanie opłat za dane pozyskane i przechowywane w tabeli `Perf` w oparciu o [Log Analytics cenach [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak ta zmiana wpłynie na moje reguły alertów?

W przypadku utworzenia [alertów dziennika](../platform/alerts-unified-log.md) , które wysyłają zapytania do tabeli `Perf`, w której znajdują się liczniki wydajności, które zostały włączone w obszarze roboczym, należy zaktualizować te reguły, aby odwoływać się do tabeli `InsightsMetrics`. Te wskazówki dotyczą również wszelkich reguł przeszukiwania dzienników przy użyciu `ServiceMapComputer_CL` i `ServiceMapProcess_CL`, ponieważ te zestawy danych są przenoszone do tabel `VMComputer` i `VMProcess`.

Będziemy aktualizować te często zadawane pytania i naszą dokumentację, aby uwzględnić przykładowe reguły alertów wyszukiwania w dzienniku dla zbieranych danych.

## <a name="how-will-this-affect-my-bill"></a>Jak wpłynie to na mój rachunek?

Opłaty są naliczane w oparciu o dane pozyskane i zachowane w obszarze roboczym Log Analytics.

Gromadzone dane wydajności na poziomie komputera są takie same, jak w przypadku danych przechowywanych w tabeli `Perf` i będą kosztowały się w przybliżeniu z tą samą ilością.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co zrobić, jeśli chcę używać tylko Service Map?

Jest to dokładne. Podczas przeglądania Azure Monitor dla maszyn wirtualnych dotyczącej nadchodzącej aktualizacji zobaczysz w Azure Portal wyświetlane są komunikaty. Po wydaniu zostanie wyświetlony monit z prośbą o aktualizację do nowej wersji. Jeśli wolisz korzystać tylko z funkcji [Maps](vminsights-maps.md) , możesz zrezygnować z uaktualnienia i nadal korzystać z funkcji maps w Azure monitor dla maszyn wirtualnych oraz do rozwiązania Service map dostępnego na kafelku obszaru roboczego lub pulpitu nawigacyjnego.

Jeśli wybrano opcję ręcznego włączania liczników wydajności w obszarze roboczym, można zobaczyć dane na niektórych z naszych wykresów wydajności wyświetlanych w Azure Monitor. Po wydaniu nowego rozwiązania będziemy aktualizować nasze wykresy wydajności, aby wykonywać zapytania dotyczące danych przechowywanych w tabeli `InsightsMetrics`. Jeśli chcesz zobaczyć dane z tej tabeli na tych wykresach, musisz przeprowadzić uaktualnienie do nowej wersji Azure Monitor dla maszyn wirtualnych.

Zmiany dotyczące przenoszenia danych z `ServiceMapComputer_CL` i `ServiceMapProcess_CL` mają wpływ na Service Map i Azure Monitor dla maszyn wirtualnych, dlatego należy zaplanować tę aktualizację.

W przypadku wybrania opcji Nie uaktualniaj do rozwiązania **VMInsights** będziemy nadal podawać starsze wersje naszych skoroszytów wydajności, które odwołują się do danych w tabeli `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Czy Service Map zestawy danych będą również przechowywane w InsightsMetrics?

Zestawy danych nie będą duplikowane, jeśli używasz obu rozwiązań. Obie oferty współdzielą zestawy danych, które będą przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort` tabele do przechowywania zbieranych przez nas zestawów danych mapy.  

W tabeli `InsightsMetrics` będą przechowywane zestawy danych maszyn wirtualnych, procesów i usług, które są zbierane i będą wypełniane tylko w przypadku korzystania z usługi Azure Monitor dla maszyn wirtualnych i rozwiązania VM Insights. Rozwiązanie Service Map nie będzie zbierać ani przechowywać danych w `InsightsMetrics` tabeli.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Czy przy użyciu rozwiązań Service Map i VMInsights w obszarze mój obszar roboczy zostaną naliczone podwójne opłaty?

Nie, dwa rozwiązania korzystają z zestawów danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`. Jeśli w obszarze roboczym znajdują się oba rozwiązania, nie będziesz mieć podwójnej opłaty za te dane.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Jeśli usunę rozwiązanie Service Map lub VMInsights, usuniemy moje dane?

Nie, dwa rozwiązania korzystają z zestawów danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`. W przypadku usunięcia jednego z tych rozwiązań te zestawy danych będą inejść, że nadal istnieje rozwiązanie, które korzysta z danych i pozostaje w obszarze roboczym Log Analytics. Musisz usunąć oba rozwiązania z obszaru roboczego, aby można było usunąć z niego dane.

## <a name="when-will-this-update-be-released"></a>Kiedy ta aktualizacja zostanie wydana?

Oczekujemy, że aktualizacja Azure Monitor dla maszyn wirtualnych na początku stycznia 2020. Gdy zaczniemy od daty wydania w styczniu, opublikujemy aktualizacje tutaj i zaprezentuje powiadomienia w Azure Portal po otwarciu Azure Monitor.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkcja kondycji jest w ograniczonej publicznej wersji zapoznawczej

Otrzymamy wiele doskonałych opinii od klientów dotyczących zestawu funkcji kondycji maszyn wirtualnych. Istnieje wiele interesów tej funkcji i ekscytację nad jej potencjałem do obsługi przepływów pracy monitorowania. Planujemy wprowadzić serię zmian w celu dodania funkcji i rozwiązania otrzymanej opinii. 

Aby zminimalizować wpływ tych zmian na nowych klientów, przeniesionomy tę funkcję do **ograniczonej publicznej wersji zapoznawczej**. Ta aktualizacja miała miejsce w październiku 2019.

Planuje ponowną uruchomienie tej funkcji kondycji w 2020, po rozpoczęciu Azure monitor dla maszyn wirtualnych.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak istniejący klienci uzyskują dostęp do funkcji kondycji?

Istniejący klienci korzystający z funkcji kondycji nadal będą mieć do niej dostęp, ale nie będzie oferowany nowym klientom.  

Aby uzyskać dostęp do tej funkcji, można dodać następującą flagę funkcji `feature.vmhealth=true` do adresu URL Azure Portal [https://portal.azure.com](https://portal.azure.com). Przykład `https://portal.azure.com/?feature.vmhealth=true`.

Możesz również użyć tego krótkiego adresu URL, który ustawia flagę funkcji automatycznie: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Jako istniejący klient można nadal korzystać z funkcji kondycji na maszynach wirtualnych, które są podłączone do istniejącej konfiguracji obszaru roboczego z funkcjonalnością kondycji.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Teraz korzystam z kondycji maszyny wirtualnej z jednym środowiskiem i chcę wdrożyć je w nowym

Jeśli jesteś istniejącym klientem korzystającym z funkcji kondycji i chcesz go użyć do nowego wdrożenia, skontaktuj się z nami w vminsights@microsoft.com, aby zażądać instrukcji.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).
