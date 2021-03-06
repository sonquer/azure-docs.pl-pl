---
title: Analizowanie kosztów platformy Azure za pomocą aplikacji usługi Power BI
description: W tym artykule wyjaśniono, jak zainstalować aplikację Azure Cost Management usługi Power BI i korzystać z niej.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.openlocfilehash: 4a50ce5c386f1b928e9f767891840c84534938a9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169707"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analizowanie kosztów za pomocą aplikacji Azure Cost Management usługi Power BI dla umów Enterprise Agreement (EA)

W tym artykule wyjaśniono, jak zainstalować aplikację Azure Cost Management usługi Power BI i korzystać z niej. Aplikacja ułatwia analizowanie kosztów platformy Azure i zarządzanie nimi w usłudze Power BI. Za pomocą aplikacji możesz monitorować koszty i trendy użycia oraz znajdować możliwości optymalizacji kosztów w celu zmniejszenia wydatków.

Aplikację pobiera się do programu Power BI Desktop. Można jej używać w takiej formie, w jakiej jest, albo dostosować do własnych potrzeb, rozszerzając domyślne filtry, widoki i wizualizacje. Następnie można w niej dołączyć dodatkowe dane w celu utworzenia niestandardowych raportów zapewniających wgląd w całość kosztów prowadzenia firmy.

Aplikacja Azure Cost Management usługi Power BI obecnie obsługuje tylko klientów z umową [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Licencja na usługę Power BI Pro](/power-bi/service-self-service-signup-for-power-bi) do zainstalowania i używania aplikacji
- Aby łączyć się z danymi, musisz używać konta [administratora przedsiębiorstwa](../manage/understand-ea-roles.md)

## <a name="installation-steps"></a>Kroki instalacji

Aby zainstalować aplikację:

1. Otwórz [aplikację Azure Cost Management usługi Power BI](https://aka.ms/costmgmt/ACMApp).
2. Na stronie usługi Power BI AppSource wybierz pozycję **Pobierz teraz**.
3. Wybierz pozycję **Kontynuuj**, aby wyrazić zgodę na warunki użytkowania i zasady ochrony prywatności.
4. W polu **Czy zainstalować tę aplikację usługi Power BI?** wybierz pozycję **Zainstaluj**.
5. W razie konieczności utwórz obszar roboczy i wybierz pozycję **Kontynuuj**.
6. Po zakończeniu instalacji zostanie wyświetlone powiadomienie z informacją, że nowa aplikacja jest gotowa.
7. Wybierz pozycję **Przejdź do aplikacji**.
8. W obszarze **Rozpoczynanie pracy z nową aplikacją** w sekcji **Połącz swoje dane** wybierz pozycję **Połącz**.  
  ![Rozpoczynanie pracy z nową aplikacją — Połącz](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. W wyświetlonym oknie dialogowym wprowadź swój numer rejestracji EA dla wartości **BillingProfileIdOrEnrollmentNumber**. Określ liczbę miesięcy, z których mają zostać pobrane dane. Pozostaw domyślną wartość pola **Zakres** (**Numer rejestracji**), a następnie wybierz pozycję **Dalej**.  
  ![Wprowadzanie informacji o rejestracji EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. Następne okno dialogowe łączy się z platformą Azure i pobiera dane wymagane przez rekomendacje dotyczące wystąpień zarezerwowanych. Pozostaw wartości domyślne zgodnie z konfiguracją, a następnie wybierz pozycję **Zaloguj się**.  
  ![Łączenie się z platformą Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. W ostatnim kroku instalacji następuje nawiązanie połączenia z rejestracją EA. Jest do tego wymagane konto [administratora przedsiębiorstwa](../manage/understand-ea-roles.md). Wybierz pozycję **Zaloguj się**, aby uwierzytelnić się w rejestracji EA. Ten krok uruchamia również akcję odświeżania danych w usłudze Power BI.  
  ![Nawiązywanie połączenia z rejestracją EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Proces odświeżania danych może zająć trochę czasu. Jego długość zależy od określonej liczby miesięcy i ilości danych, które trzeba zsynchronizować.
12. Aby sprawdzić stan odświeżania danych, wybierz kartę **Zestawy danych** w obszarze roboczym. Spójrz obok odświeżonego znacznika czasu. Jeśli aktualizowanie nadal trwa, zobaczysz wskaźnik informujący o odświeżaniu w toku.  
  ![Odświeżanie danych](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Po zakończeniu odświeżania danych wybierz aplikację Azure Cost Management, aby wyświetlić wstępnie utworzone raporty.

## <a name="reports-available-with-the-app"></a>Raporty dostępne z aplikacją

W aplikacji są dostępne następujące raporty.

**Wprowadzenie** — oferuje przydatne linki do dokumentacji i linki do przesyłania opinii.

**Przegląd konta** — ten raport przedstawia miesięczne podsumowanie informacji, w tym:

- Opłaty i środki
- Nowe zakupy
- Opłaty za witrynę Azure Marketplace
- Opłaty nadwyżkowe i łączne

**Użycie według subskrypcji i grup zasobów** — udostępnia widok kosztów w czasie i wykresy pokazujące koszt według subskrypcji i grupy zasobów.

**Użycie według usług** — udostępnia widok czasu użytkowania według kategorii miernika. Można śledzić dane użycia i przechodzić do szczegółów wszelkich anomalii w celu zrozumienia nagłych wzrostów i spadków użycia.

**5 najważniejszych czynników napędzających użycie** — raport przedstawia przefiltrowane podsumowanie kosztów według 5 najpopularniejszych kategorii mierników i odpowiadających im nazw mierników.

**Użycie Korzyści użycia hybrydowego platformy Azure dla systemu Windows Server** — raport przedstawia maszyny wirtualne z włączoną Korzyścią użycia hybrydowego platformy Azure. Pokazuje również liczbę rdzeni/procesorów wirtualnych używanych przez maszyny wirtualne.

![Pełny raport Korzyści użycia hybrydowego platformy Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

Raport wskazuje maszyny wirtualne z systemem Windows, na których Korzyść użycia hybrydowego została **włączona**, ale które mają _mniej niż_ 8 procesorów wirtualnych. Pokazuje również maszyny, na których Korzyść użycia hybrydowego **nie została włączona** i które mają 8 _lub więcej_ procesorów wirtualnych. Te informacje ułatwiają pełne wykorzystanie Korzyści użycia hybrydowego. Zastosuj korzyść do najbardziej kosztownych maszyn wirtualnych, aby zmaksymalizować potencjalne oszczędności.

![Korzyści użycia hybrydowego platformy Azure — mniej niż 8 procesorów wirtualnych i brak włączonych procesorów wirtualnych](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**Obciążenie zwrotne za wystąpienia zarezerwowane** — raport pomaga zrozumieć, gdzie zastosowano korzyść wystąpień zarezerwowanych i w jakiej wysokości, z podziałem na regiony, subskrypcje, grupy zasobów lub zasoby. Aby wyświetlić widok, raport używa danych amortyzowanego użycia.

Aby wyświetlić dane niedostatecznego wykorzystania wystąpień zarezerwowanych, można przefiltrować dane według _typu obciążenia_.

Aby uzyskać więcej informacji na temat danych amortyzacji, zobacz [Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

**Oszczędności na wystąpieniach zarezerwowanych** — raport przedstawia oszczędności uzyskane na rezerwacjach dla subskrypcji, grup zasobów i poziomów zasobów. Są w nim pokazywane następujące informacje:

- Koszt z rezerwacją
- Szacowany koszt na żądanie, jeśli rezerwacja nie została zastosowana do użycia
- Oszczędności na kosztach uzyskane w wyniku rezerwacji

 W raporcie od całkowitych oszczędności odejmowane są straty wynikające z niedostatecznego wykorzystania rezerwacji. Starty te nie zostałyby poniesione, gdyby rezerwacji nie było.

Dane amortyzowanego użycia można wykorzystać do budowania na ich podstawie.

<a name="shared-recommendation"></a>
**Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (rekomendacja udostępniona)** — raport jest podzielony między użycie maszyn wirtualnych na żądanie i użycie wystąpień zarezerwowanych maszyn wirtualnych w wybranym okresie. Zawiera rekomendacje dotyczące zakupów wystąpień zarezerwowanych maszyn wirtualnych w zakresie udostępnionym.

Aby użyć raportu, wybierz filtr przechodzenia do szczegółów.

![Raport pokrycia wystąpieniami zarezerwowanymi maszyn wirtualnych — wybieranie przechodzenia do szczegółów](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Wybierz region, który chcesz przeanalizować. Następnie wybierz grupę elastyczności rozmiarów wystąpienia i tak dalej.

Dla każdego poziomu przechodzenia do szczegółów do raportu są stosowane następujące filtry:

- Dane pokrycia po prawej stronie to filtr pokazujący ilość użycia, za które naliczane są opłaty po stawce użycia na żądanie w porównaniu z ilością objętą rezerwacją.
- Rekomendacje są również filtrowane.

Tabela rekomendacji zawiera rekomendacje dotyczące zakupu rezerwacji w oparciu o używane rozmiary maszyn wirtualnych.

Wartości _Rozmiar znormalizowany_ i _Znormalizowana ilość zalecana_ ułatwiają normalizację zakupu do najmniejszego rozmiaru dla grupy elastyczności rozmiaru wystąpienia. Te informacje są przydatne, jeśli planujesz kupić tylko jedną rezerwację dla wszystkich rozmiarów w grupie elastyczności rozmiaru wystąpienia.

![Zalecenia dotyczące wystąpień zarezerwowanych](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (pojedyncza rekomendacja)** — raport jest podzielony między użycie maszyn wirtualnych na żądanie i użycie wystąpień zarezerwowanych maszyn wirtualnych w wybranym okresie. Zawiera rekomendacje dotyczące zakupów wystąpień zarezerwowanych maszyn wirtualnych w zakresie subskrypcji.

Aby uzyskać szczegółowe informacje dotyczące sposobu korzystania z tego raportu, zobacz sekcję [Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (rekomendacja udostępniona)](#shared-recommendation).

**Zakupy wystąpień zarezerwowanych** — raport przedstawia zakupy wystąpień zarezerwowanych w określonym okresie.

**Arkusz cen** — raport przedstawia szczegółową listę cen specyficznych dla konta rozliczeniowego lub rejestracji EA.

## <a name="data-reference"></a>Opis danych

Poniższe informacje podsumowują dane dostępne za pośrednictwem aplikacji. Zawarto w nich także linki do opisów interfejsów API ze szczegółowymi informacjami o polach danych i wartościach.

| **Odwołanie do tabeli** | **Opis** |
| --- | --- |
| **AutoFitComboMeter** | Dane zawarte w aplikacji służące do normalizacji rekomendacji dotyczących wystąpień zarezerwowanych i użycia do najmniejszego rozmiaru w grupie rodzin wystąpień. |
| [**Podsumowanie salda**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Podsumowanie salda dla umów Enterprise Agreement. |
| [**Budżety**](/rest/api/consumption/budgets/get#definitions) | Szczegóły budżetu umożliwiające porównanie rzeczywistych kosztów lub użycia z istniejącymi celami budżetu. |
| [**Arkusze cen**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Mające zastosowanie stawki mierników dla podanego profilu rozliczeniowego lub rejestracji EA. |
| [**Opłaty za wystąpienia zarezerwowane**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Opłaty związane z wystąpieniami zarezerwowanymi z ostatnich 24 miesięcy. |
| [**Rekomendacje dotyczące wystąpień zarezerwowanych (udostępnione)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Rekomendacje dotyczące zakupu wystąpień zarezerwowanych w oparciu o trendy użycia wszystkich Twoich subskrypcji w ciągu ostatnich 7, 30 lub 60 dni. |
| [**Rekomendacje dotyczące wystąpień zarezerwowanych (pojedyncze)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Rekomendacje dotyczące zakupu wystąpień zarezerwowanych w oparciu o trendy użycia Twojej pojedynczej subskrypcji w ciągu ostatnich 7, 30 lub 60 dni. |
| [**Szczegóły użycia wystąpienia zarezerwowanego**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Szczegóły zużycia dla istniejących wystąpień zarezerwowanych w ciągu ostatniego miesiąca. |
| [**Podsumowanie użycia wystąpienia zarezerwowanego**](/rest/api/consumption/reservationssummaries/list) | Średni dzienny procent użycia rezerwacji na platformie Azure. |
| [**Szczegóły użycia**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Podział zużytych ilości i szacowane opłaty dla danego profilu rozliczeniowego w ramach rejestracji EA. |
| [**Amortyzowane szczegóły użycia**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Podział zużytych ilości i szacowane opłaty amortyzowane dla danego profilu rozliczeniowego w ramach rejestracji EA. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania danych, odświeżania, udostępniania raportów i dodatkowych dostosowań raportów, zobacz następujące artykuły:

- [Konfigurowanie odświeżania zaplanowanego](/power-bi/refresh-scheduled-refresh)
- [Udostępnianie pulpitów nawigacyjnych i raportów usługi Power BI współpracownikom oraz innym osobom](/power-bi/service-share-dashboards)
- [Subskrybowanie dla siebie i innych raportów oraz pulpitów nawigacyjnych w usłudze Power BI](/power-bi/service-report-subscribe)
- [Pobieranie raportu z usługi Power BI do programu Power BI Desktop](/power-bi/service-export-to-pbix)
- [Zapisywanie raportu w usłudze Power BI i programie Power BI Desktop](/power-bi/service-report-save)
- [Tworzenie raportu w usłudze Power BI przez zaimportowanie zestawu danych](/power-bi/service-report-create-new)
