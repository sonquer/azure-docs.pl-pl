---
title: Automatyzowanie niestandardowych raportów za pomocą usługi Azure Application Insights Data
description: Automatyzowanie niestandardowych raportów codziennych/cotygodniowych/miesięcznych przy użyciu usługi Azure Application Insights Data
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 2b23374972a071421b59bedf0eb5b9358b37d7a9
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677628"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzowanie niestandardowych raportów za pomocą usługi Azure Application Insights Data

Raporty okresowe ułatwiają zespołowi informowanie o tym, jak działają usługi o krytycznym znaczeniu dla firmy. Deweloperzy, zespoły DevOps/SRE i ich menedżerów mogą wydajnie pracować dzięki automatycznym raportom niezawodnym dostarczającym szczegółowe informacje bez konieczności logowania się do portalu. Takie raporty mogą również pomóc w identyfikowaniu stopniowego wzrostu opóźnień, obciążenia lub częstotliwości niepowodzeń, które mogą nie wyzwalać żadnych reguł alertów.

Każde przedsiębiorstwo ma swoje unikatowe potrzeby związane z raportowaniem, na przykład: 

* Określone agregacja percentylu metryk lub metryki niestandardowe w raporcie.
* Różne raporty codziennie, co tydzień i miesięczne zestawienie danych dla różnych odbiorców.
* Segmentacja według atrybutów niestandardowych, takich jak region lub środowisko. 
* Grupowanie zasobów AI w jednym raporcie, nawet jeśli mogą znajdować się w różnych subskrypcjach lub grupach zasobów itd.
* Oddzielne raporty zawierające poufne metryki wysyłane do selektywnych odbiorców.
* Raporty do uczestników projektu, którzy mogą nie mieć dostępu do zasobów portalu.

> [!NOTE] 
> Cotygodniowa poczta e-mail Application Insights Digest nie zezwolił na dostosowanie i zostanie wycofana na korzyść opcji niestandardowych wymienionych poniżej. Ostatnia tygodniowa wiadomość e-mail z podsumowaniem zostanie wysłana od 11 czerwca 2018. Skonfiguruj jedną z następujących opcji, aby uzyskać podobne niestandardowe raporty (Użyj zapytania z sugestią poniżej).

## <a name="to-automate-custom-report-emails"></a>Aby zautomatyzować wiadomości e-mail dotyczące raportów niestandardowych

Można [programowo wykonywać zapytania dotyczące Application Insights](https://dev.applicationinsights.io/) danych, aby generować raporty niestandardowe zgodnie z harmonogramem. Następujące opcje mogą pomóc szybko rozpocząć pracę:

* [Automatyzowanie raportów za pomocą Microsoft Flow](automate-with-flow.md)
* [Automatyzowanie raportów za pomocą Logic Apps](automate-with-logic-apps.md)
* Użyj szablonu [funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application Insights zaplanowanego skrótu" w scenariuszu monitorowania. Ta funkcja używa SendGrid do dostarczania wiadomości e-mail. 

    ![Szablon funkcji platformy Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Przykładowe zapytanie dla cotygodniowej wiadomości e-mail
Poniższe zapytanie pokazuje sprzężenie wielu zestawów danych w celu uzyskania cotygodniowej wiadomości e-mail zawierającej podsumowanie. Dostosuj ją zgodnie z potrzebami i użyj jej w celu zautomatyzowania cotygodniowego raportu.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Raport Application Insights zaplanowanego podsumowania

1. W Azure Portal wybierz pozycję **Utwórz zasób**  > **obliczenia**  > **aplikacja funkcji**.

   ![Tworzenie zrzutu ekranu aplikacja funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-01.png)

2. Wprowadź odpowiednie informacje dla aplikacji i wybierz pozycję _Utwórz_. (Application Insights _jest_ wymagane tylko wtedy, gdy chcesz monitorować nowy aplikacja funkcji za pomocą Application Insights)

   ![Utwórz zrzut ekranu ustawień aplikacja funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-02.png)

3. Po zakończeniu wdrażania nowego aplikacja funkcji wybierz pozycję **Przejdź do zasobu**.

4. Wybierz pozycję **Nowa funkcja**.

   ![Utwórz nowy zrzut ekranu funkcji](./media/automate-custom-reports/function-app-03.png)

5. Wybierz **_szablon Application Insights zaplanowanego podsumowania_** .

     > [!NOTE]
     > Domyślnie aplikacje funkcji są tworzone przy użyciu wersji 2. x środowiska uruchomieniowego. Musisz mieć [Azure Functions środowiska uruchomieniowego w wersji](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1. x** , aby użyć szablonu Application Insights zaplanowanego podsumowania.  zrzut ekranu ![runtime ](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Zrzut ekranu szablonu nowej funkcji Application Insights](./media/automate-custom-reports/function-app-04.png)

6. Wprowadź odpowiedni adres e-mail odbiorcy dla raportu i wybierz pozycję **Utwórz**.

   ![Zrzut ekranu ustawień funkcji](./media/automate-custom-reports/function-app-05.png)

7. Wybierz **aplikacja funkcji** **funkcje platformy**  >   > **Ustawienia aplikacji**.

    ![Zrzut ekranu ustawień aplikacji funkcji platformy Azure](./media/automate-custom-reports/function-app-07.png)

8. Utwórz trzy nowe ustawienia aplikacji z odpowiednimi odpowiednimi wartościami ``AI_APP_ID``, ``AI_APP_KEY`` i ``SendGridAPI``. Wybierz pozycję **Zapisz**.

     ![Zrzut ekranu interfejsu integracji funkcji](./media/automate-custom-reports/function-app-08.png)
    
    (Wartości AI_ można znaleźć w obszarze dostęp do interfejsu API dla zasobu Application Insights, na którym chcesz utworzyć raport. Jeśli nie masz klucza interfejsu API Application Insights, istnieje możliwość **utworzenia klucza interfejsu API**.
    
   * AI_APP_ID = identyfikator aplikacji
   * AI_APP_KEY = klucz interfejsu API
   * SendGridAPI = SendGrid — klucz interfejsu API

     > [!NOTE]
     > Jeśli nie masz konta SendGrid, możesz go utworzyć. Dokumentacja usługi SendGrid dla Azure Functions jest [tutaj](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Jeśli chcesz tylko określić, jak skonfigurować SendGrid, i wygenerować klucz interfejsu API, który jest dostępny na końcu tego artykułu. 

9. Wybierz pozycję **integracja** i w obszarze wyjściowe kliknij pozycję **SendGrid ($Return)** .

     ![Zrzut ekranu wyjściowego](./media/automate-custom-reports/function-app-09.png)

10. W obszarze **Ustawienia aplikacji klucza SendGridAPI**wybierz nowo utworzone ustawienie aplikacji dla **SendGridAPI**.

     ![Uruchom aplikacja funkcji zrzut ekranu](./media/automate-custom-reports/function-app-010.png)

11. Uruchom i przetestuj aplikacja funkcji.

     ![Zrzut ekranu testu](./media/automate-custom-reports/function-app-11.png)

12. Sprawdź wiadomość e-mail, aby upewnić się, że wiadomość została wysłana/odebrana pomyślnie.

     ![Zrzut ekranu z wierszem tematu wiadomości E-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid z platformą Azure

Te kroki mają zastosowanie tylko wtedy, gdy nie masz jeszcze skonfigurowanego konta SendGrid.

1. W Azure Portal wybierz pozycję **Utwórz zasób** wyszukiwanie **SendGrid poczty E-mail** > kliknij pozycję **Utwórz** > i wypełnij instrukcje tworzenia określonych SendGrid. 

     ![Utwórz zrzut ekranu zasobów SendGrid](./media/automate-custom-reports/function-app-13.png)

2. Po utworzeniu konta w obszarze SendGrid wybierz pozycję **Zarządzaj**.

     ![Zrzut ekranu klucza interfejsu API ustawień](./media/automate-custom-reports/function-app-14.png)

3. Spowoduje to uruchomienie witryny SendGrid. Wybierz pozycję **ustawienia**  > **klucze interfejsu API**.

     ![Tworzenie i Wyświetlanie zrzutu ekranu aplikacji klucza interfejsu API](./media/automate-custom-reports/function-app-15.png)

4. Utwórz klucz interfejsu API > wybierz pozycję **utwórz & widok** (Przejrzyj dokumentację usługi SendGrid w sprawie ograniczonego dostępu, aby określić, jaki poziom uprawnień jest odpowiedni dla klucza interfejsu API. Pełny dostęp jest wybierany tutaj tylko do celów.

   ![Zrzut ekranu pełnego dostępu](./media/automate-custom-reports/function-app-16.png)

5. Skopiuj cały klucz, ta wartość jest wymagana w ustawieniach aplikacja funkcji jako wartość SendGridAPI

   ![Kopiuj zrzut ekranu klucza interfejsu API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../../azure-monitor/log-query/get-started-queries.md).
* Dowiedz się więcej na temat [programowo przeszukiwania danych Application Insights](https://dev.applicationinsights.io/)
* Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).
