---
title: Pulpit nawigacyjny omówienia usługi Azure Application Insights | Microsoft Docs
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights i funkcji pulpitu nawigacyjnego.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/03/2019
ms.openlocfilehash: 45c04cc32733873061bd6ff44b2e5484b106a893
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406651"
---
# <a name="application-insights-overview-dashboard"></a>Pulpit nawigacyjny przeglądu Application Insights

Application Insights zawsze udostępnia okienko podsumowania podsumowujące, które umożliwia szybkie i natychmiastowe ocenę kondycji i wydajności aplikacji. Nowy pulpit nawigacyjny omówienia oferuje szybsze środowisko pracy.

## <a name="how-do-i-test-out-the-new-experience"></a>Jak mogę sprawdzić nowe środowisko?

Nowy pulpit nawigacyjny przeglądu jest teraz uruchamiany domyślnie:

![Okienko podglądu przeglądu](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Lepsza wydajność

Wybór zakresu czasu jest uproszczony dla prostego interfejsu jednego kliknięcia.

![Przedział czasu](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Ogólna wydajność została znacznie zwiększona. Masz dostęp z jednym kliknięciem do popularnych funkcji, takich jak **Wyszukiwanie** i **Analiza**. Każda domyślna funkcja dynamicznego aktualizowania kafelka kluczowego wskaźnika wydajności zapewnia wgląd w odpowiednie funkcje Application Insights. Aby dowiedzieć się więcej o żądaniach zakończonych niepowodzeniem, wybierz **Błędy** w nagłówku **badania** :

![Błędy](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji

Pulpit nawigacyjny aplikacji korzysta z istniejącej technologii pulpitu nawigacyjnego w systemie Azure, aby zapewnić w pełni dostosowywalny widok jednego okienka kondycji i wydajności aplikacji.

Aby uzyskać dostęp do domyślnego pulpitu nawigacyjnego, wybierz pozycję _pulpit nawigacyjny aplikacji_ w lewym górnym rogu.

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Jeśli po raz pierwszy uzyskujesz dostęp do pulpitu nawigacyjnego, zostanie uruchomiony widok domyślny:

![Widok pulpitu nawigacyjnego](./media/overview-dashboard/0001-dashboard.png)

Jeśli chcesz, możesz zachować widok domyślny. Możesz również dodawać i usuwać z pulpitu nawigacyjnego, aby najlepiej dopasować się do potrzeb zespołu.

> [!NOTE]
> Wszyscy użytkownicy z dostępem do zasobów Application Insights współdzielą ten sam interfejs pulpitu nawigacyjnego aplikacji. Zmiany wprowadzone przez jednego użytkownika spowodują modyfikację widoku dla wszystkich użytkowników.

Aby przejść z powrotem do środowiska przeglądu, wybierz:

![Przycisk przegląd](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wybierzesz pozycję **Konfiguruj ustawienia kafelków** i ustawisz niestandardowy zakres czasu przekraczający 31 dni, ten pulpit nawigacyjny nie będzie wyświetlany dłużej niż 31 dni, nawet z domyślnym przechowywaniem danych wynoszącym 90 dni. Obecnie nie ma żadnego obejścia tego problemu.

## <a name="next-steps"></a>Następne kroki

- [Lejki](../../azure-monitor/app/usage-funnels.md)
- [Przechowywanie](../../azure-monitor/app/usage-retention.md)
- [User Flows (Przepływy użytkowników)](../../azure-monitor/app/usage-flows.md)
