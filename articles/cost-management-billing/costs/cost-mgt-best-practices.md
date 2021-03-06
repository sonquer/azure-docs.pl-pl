---
title: Optymalizowanie inwestycji w chmurę za pomocą usługi Azure Cost Management | Microsoft Docs
description: Ten artykuł pomaga zmaksymalizować korzyści z inwestycji w chmurę, ograniczyć koszty i ocenić, na co wydawane są pieniądze.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 6d13bf4b2a3abd3a1552ac656a6a15fed8c7cb60
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201071"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Jak zoptymalizować inwestycję w chmurę za pomocą usługi Azure Cost Management

Usługa Azure Cost Management oferuje narzędzia do planowania, analizowania i ograniczania wydatków w celu zmaksymalizowania korzyści z inwestycji w chmurę. Ten dokument przedstawia metodyczne podejście do zarządzania kosztami i wyszczególnia narzędzia pozwalające sprostać wyzwaniom kosztowym organizacji. Platforma Azure ułatwia tworzenie i wdrażanie rozwiązań w chmurze. Należy jednak pamiętać, aby te rozwiązania były zoptymalizowane pod kątem zminimalizowania kosztów dla organizacji. Postępowanie zgodnie z zasadami opisanymi w tym dokumencie i używanie naszych narzędzi pomoże osiągnąć sukces organizacji.

## <a name="methodology"></a>Metodologia

Zarządzanie kosztami jest problemem o charakterze organizacyjnym i powinno być stałą praktyką, która zaczyna się przed poniesieniem wydatków na zasoby w chmurze. Aby pomyślnie wdrożyć zarządzanie kosztami i zoptymalizować koszty, organizacja musi:

- być przygotowana na sukces dzięki odpowiednim narzędziom,
- odpowiadać za koszty,
- podejmować odpowiednie działania w celu optymalizacji wydatków.

Trzy najważniejsze grupy, wymienione poniżej, muszą być dopasowane w organizacji, aby zapewnić skuteczne zarządzanie kosztami.

- **Finanse** — osoby odpowiedzialne za zatwierdzanie żądań budżetowych w organizacji w oparciu o prognozy wydatków w chmurze. Opłacają one odpowiednie rachunki i przypisują koszty różnym zespołom, aby zwiększyć odpowiedzialność.
- **Menedżerowie** — osoby podejmujące decyzje biznesowe w organizacji, które muszą zrozumieć wydatki na chmurę, aby znaleźć najlepsze sposoby wydatkowania.
- **Zespoły odpowiedzialne za aplikacje** — inżynierowie zarządzający zasobami w chmurze na co dzień, tworzący usługi pod kątem zaspokajania potrzeb organizacji. Zespoły te potrzebują elastyczności, aby zapewnić możliwie największą wartość w ramach zdefiniowanych budżetów.

### <a name="key-principles"></a>Najważniejsze zasady

Skorzystaj z zasad przedstawionych poniżej, aby zapewnić swojej organizacji odpowiednie warunki do skutecznego zarządzania kosztami w chmurze.

#### <a name="planning"></a>Planowanie

Kompleksowe, wstępne planowanie pozwala na dostosowanie wykorzystania chmury do konkretnych wymagań biznesowych. Zadaj sobie pytania:

- Jaki problem biznesowy chcę rozwiązać?
- Jakich wzorców użycia oczekuję w odniesieniu do swoich zasobów?

Twoje odpowiedzi pomogą Ci wybrać odpowiednią ofertę. Określają one infrastrukturę, z jakiej należy korzystać, oraz sposób jej używania w celu maksymalizacji wydajności platformy Azure.

#### <a name="visibility"></a>Widoczność

Przy dobrej strukturze usługa Cost Management ułatwia informowanie użytkowników o kosztach związanych z platformą Azure, za które są odpowiedzialni, lub wydatkach, które generują. Platforma Azure oferuje usługi pozwalające uzyskać wgląd w informacje dotyczące tego, *gdzie* wydatkowane są pieniądze. Korzystaj z tych narzędzi. Mogą one pomóc znajdować marnowane i nieużywane zasoby oraz maksymalizować możliwości w zakresie oszczędzania kosztów.

#### <a name="accountability"></a>Odpowiedzialność

Przypisuj koszty w organizacji, aby upewnić się, że odpowiednie osoby przejmą odpowiedzialność za wydatki swojego zespołu. Aby w pełni zrozumieć wydatki związane z platformą Azure w organizacji, należy zorganizować zasoby w sposób pozwalający zmaksymalizować uzyskiwanie szczegółowych informacji o przypisywaniu kosztów. Dobra organizacja pomaga zarządzać kosztami i ograniczać je oraz wymusza na pracownikach odpowiedzialność za efektywne wydatkowanie środków w organizacji.

#### <a name="optimization"></a>Optymalizacja

Podejmij działania w celu zmniejszenia wydatków. Uzyskaj maksymalne wyniki w oparciu o ustalenia zebrane na drodze planowania i zwiększenie widoczności kosztów. Możesz rozważyć optymalizację w zakresie zakupów i licencjonowania oraz zmiany dotyczące wdrażania infrastruktury omówione w dalszej części tego dokumentu.

#### <a name="iteration"></a>Iteracja

Wszyscy w organizacji muszą uczestniczyć w cyklu życia zarządzania kosztami. Muszą oni być zaangażowani na bieżąco, aby zoptymalizować koszty. Rygorystycznie podchodź do tego iteracyjnego procesu i uczyń z niego kluczową zasadę odpowiedzialnego zapewniania ładu w chmurze w swojej organizacji.

![Diagram najważniejszych zasad pokazujący widoczność, odpowiedzialność i optymalizację](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planowanie z myślą o kosztach

Przed wdrożeniem zasobów w chmurze dokonaj oceny następujących elementów:

- Oferta platformy Azure, która najlepiej odpowiada Twoim potrzebom
- Zasoby, których planujesz użyć
- Ile mogą kosztować

Platforma Azure udostępnia narzędzia ułatwiające proces oceny. Narzędzia te mogą zapewnić dobre wyobrażenie o inwestycji niezbędnej do obsługi Twoich obciążeń. Następnie możesz wybrać najlepszą konfigurację dla danej sytuacji.

### <a name="azure-onboarding-options"></a>Opcje dołączania platformy Azure

Pierwszym krokiem w celu zmaksymalizowania doświadczenia w ramach usługi Cost Management jest zbadanie i wybór najlepszej dla siebie oferty platformy Azure. Pomyśl nad tym, w jaki sposób zamierzasz używać platformy Azure w przyszłości. Zastanów się również, jak ma być skonfigurowany model rozliczeń. Przy podejmowaniu decyzji należy wziąć pod uwagę następujące pytania:

- Jak długo zamierzam używać platformy Azure? Czy przeprowadzam testy, czy też planuję budowę długoterminowej infrastruktury?
- W jaki sposób chcę płacić za platformę Azure? Czy mam płacić z góry i uzyskać obniżoną cenę, czy otrzymać fakturę na koniec miesiąca?

Aby dowiedzieć się więcej o różnych opcjach, odwiedź stronę [Kupowanie platformy Azure](https://azure.microsoft.com/pricing/purchase-options/). Poniżej przedstawiono kilka najczęstszych modeli rozliczeń.

#### <a name="free"></a>[Bezpłatna](https://azure.microsoft.com/free/)

- 12 miesięcy popularnych bezpłatnych usług
- 200 USD środków na zapoznawanie się z usługami przez 30 dni
- Ponad 25 usług, które są zawsze bezpłatne

#### <a name="pay-as-you-go"></a>[Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p)

- Brak kwot minimalnych oraz zobowiązań
- Konkurencyjne ceny
- Płatność wyłącznie za rzeczywiste użycie
- Możliwość anulowania w dowolnym momencie

#### <a name="enterprise-agreement"></a>[Umowa Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Możliwości w zakresie zobowiązań pieniężnych z góry
- Dostęp do niższych cen platformy Azure

## <a name="estimate-the-cost-of-your-solution"></a>Szacowanie kosztów rozwiązania

Przed wdrożeniem infrastruktury należy ocenić, ile będzie kosztować rozwiązanie. Ocena ta pomoże w przygotowaniu z góry budżetu dla obciążenia w organizacji. Następnie można użyć budżetu w czasie, aby dokonać porównania prawidłowości wstępnego oszacowania. Można też porównać go z rzeczywistym kosztem wdrożonego rozwiązania.

### <a name="azure-pricing-calculator"></a>Kalkulator cen platformy Azure

Kalkulator cen platformy Azure umożliwia mieszanie i dopasowywanie różnych kombinacji usług platformy Azure pod kątem sprawdzenia szacunkowych kosztów. Platforma Azure umożliwia wdrażanie rozwiązań z wykorzystaniem różnych sposobów — każdy może mieć wpływ na całkowite wydatki. Wczesne przeanalizowanie wszystkich potrzeb infrastrukturalnych związanych z wdrożeniem chmury pomaga efektywniej wykorzystywać to narzędzie. Może to pomóc w uzyskaniu wiarygodnej wyceny szacunkowych wydatków na platformie Azure.

Aby uzyskać więcej informacji, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate to usługa, która ocenia bieżące obciążenia organizacji w lokalnych centrach danych. Zapewnia ona szczegółowe informacje o tym, czego możesz potrzebować od zastępczego rozwiązania platformy Azure. Na początek usługa Migrate przeprowadza analizę lokalnych maszyn w celu sprawdzenia, czy migracja jest możliwa. Następnie proponuje rozmiary maszyn wirtualnych na platformie Azure zapewniające zmaksymalizowanie wydajności. Wreszcie przygotowuje również oszacowanie kosztów rozwiązania opartego na platformie Azure.

Aby uzyskać więcej informacji, zobacz [Azure Migrate](../../site-recovery/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analizowanie kosztów i zarządzanie nimi

Śledź na bieżąco zmiany kosztów organizacji w czasie. Użyj następujących technik, aby właściwie zrozumieć swoje wydatki i zarządzać nimi.

### <a name="organize-and-tag-your-resources"></a>Organizowanie i tagowanie zasobów

Organizuj zasoby z uwzględnieniem kosztów. Podczas tworzenia subskrypcji i grup zasobów weź pod uwagę zespoły odpowiedzialne za powiązane koszty. Upewnij się, że w raportowaniu uwzględniana jest Twoja organizacja. Subskrypcje i grupy zasobów stanowią dobre zasobniki do organizowania wydatków i przyporządkowywania ich w całej organizacji. Tagi stanowią dobrą metodę przyporządkowywania kosztów. Możesz używać tagów jako filtrów. Dodatkowo możesz używać ich jako kryterium grupowania podczas analizowania danych i badania kosztów. Klienci z umową Enterprise Agreement mogą również tworzyć działy i umieszczać w nich subskrypcje. Organizacja oparta na kosztach na platformie Azure pomaga zapewnić odpowiedzialność właściwych osób w organizacji za zmniejszanie wydatków ich zespołów.

### <a name="use-cost-analysis"></a>Używanie analizy kosztów

Analiza kosztów pozwala na dokładne analizowanie kosztów organizacyjnych przez podział i grupowanie kosztów z użyciem standardowych właściwości zasobów. Weź pod uwagę poniższe typowe pytania jako wytyczne podczas analizy. Regularne odpowiadanie na te pytania pozwoli Ci mieć dostęp do szerszych informacji i umożliwi bardziej świadome podejmowanie decyzji.

- **Szacunkowe koszty dla bieżącego miesiąca** — jakie koszty poniesiono do tej pory w tym miesiącu? Czy zmieszczę się w budżecie?
- **Badanie anomalii** — przeprowadzaj rutynowe kontrole, aby upewnić się, że koszty mieszczą się w rozsądnym zakresie normalnego użytkowania. Jakie są trendy? Czy istnieją jakieś elementy odstające?
- **Uzgadnianie faktur** — czy mój ostatni zafakturowany koszt jest większy w porównaniu z poprzednim miesiącem? Jak zmieniały się wydatki z miesiąca na miesiąc?
- **Wewnętrzne obciążenie zwrotne** — teraz, gdy znam wysokość naliczanych opłat, w jaki sposób należy je rozbić na moją organizację?

Aby uzyskać więcej informacji, zobacz [analiza kosztów](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Eksportowanie danych dotyczących rozliczeń według harmonogramu

Czy potrzebujesz importować dane dotyczące rozliczeń do systemu zewnętrznego, takiego jak pulpit nawigacyjny lub system finansowy? Skonfiguruj automatyczne eksporty do usługi Azure Storage i uniknij konieczności ręcznego pobierania plików co miesiąc. Następnie możesz łatwo skonfigurować automatyczną integrację z innymi systemami, aby zapewnić stałą synchronizację danych rozliczeniowych.

Aby uzyskać więcej informacji na temat eksportowania danych dotyczących rozliczeń, zobacz [Eksportowanie danych i zarządzanie nimi](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Tworzenie budżetów

Po zidentyfikowaniu i przeanalizowaniu wzorców wydatków ważne jest, aby zacząć ustalać limity dla siebie i swoich zespołów. Budżety platformy Azure umożliwiają tworzenie budżetu opartego na kosztach lub opartego na użyciu z wieloma progami i alertami. Pamiętaj o regularnym przeglądaniu budżetów, które tworzysz, aby sprawdzić stan wykorzystania budżetu i w razie potrzeby wprowadzić zmiany. Budżety platformy Azure umożliwiają również skonfigurowanie automatycznego wyzwalacza, gdy zostanie osiągnięty określony próg budżetu. Można na przykład skonfigurować usługę do zamykania maszyn wirtualnych. Można też przenieść infrastrukturę do innej warstwy cenowej w odpowiedzi na wyzwalacz budżetu.

Aby uzyskać więcej informacji, zobacz [Budżety platformy Azure](tutorial-acm-create-budgets.md).

Aby uzyskać więcej informacji na temat automatyzacji opartej na budżecie, zobacz [Automatyzacja oparta na budżecie](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Podejmij działania w celu optymalizacji
Aby zoptymalizować wydatki, użyj następujących sposobów.

### <a name="cut-out-waste"></a>Eliminowanie marnowanych zasobów

Po wdrożeniu infrastruktury na platformie Azure ważne jest, aby upewnić się, że jest ona używana. Najprostszą metodą, aby natychmiast rozpocząć oszczędności jest przejrzenie zasobów i usunięcie wszystkich, które nie są używane. Następnie należy ustalić, czy zasoby są wykorzystywane tak efektywnie, jak to możliwe.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor to usługa, która między innymi identyfikuje maszyny wirtualne o niskim stopniu wykorzystania z punktu widzenia użycia procesora lub sieci. Dzięki temu możesz podjąć decyzję o wyłączeniu lub zmianie rozmiaru maszyny w oparciu o szacowany koszt jej dalszego użytkowania. Usługa Advisor dostarcza również rekomendacje dotyczące zakupów wystąpień zarezerwowanych. Rekomendacje te bazują na ostatnich 30 dniach użytkowania maszyn wirtualnych. Rekomendacje te mogą pomóc w zmniejszeniu wydatków.

Aby uzyskać więcej informacji, zobacz [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Prawidłowe dopasowanie rozmiaru maszyn wirtualnych

Rozmiar maszyn wirtualnych ma znaczący wpływ na ogólny koszt platformy Azure. Liczba maszyn wirtualnych wymaganych na platformie Azure może nie odpowiadać liczbie obecnie wdrożonych maszyn w lokalnym centrum danych. Upewnij się, że dobierasz odpowiedni rozmiar do obciążeń, które planujesz uruchamiać.

Aby uzyskać więcej informacji, zobacz [Azure IaaS: prawidłowe określanie rozmiaru i koszty](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Korzystanie z rabatów przy zakupie

Platforma Azure oferuje wiele rabatów, z których Twoja organizacja powinna skorzystać, aby zaoszczędzić pieniądze.

#### <a name="azure-reservations"></a>Rezerwacje platformy Azure

Rezerwacje platformy Azure umożliwiają przedpłatę za pojemność obliczeniową maszyn wirtualnych lub usługi SQL Database za rok lub trzy lata. Przedpłaty umożliwiają uzyskanie rabatu na używane zasoby. Rezerwacje platformy Azure mogą znacząco obniżyć koszty obliczeniowe maszyny wirtualnej lub usługi SQL Database — do 72% w przypadku płatności zgodnie z rzeczywistym użyciem ze zobowiązaniem do zapłaty z góry za jeden rok lub trzy lata. Rezerwacje umożliwiają skorzystanie z rabatu na rozliczenia i nie mają wpływu na stan środowiska uruchomieniowego maszyn wirtualnych lub baz danych SQL.

Aby uzyskać więcej informacji, zobacz [Co to są rezerwacje platformy Azure?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Korzystaj z zalet użycia hybrydowego platformy Azure

Jeśli masz już licencje na system Windows Server lub program SQL Server w ramach wdrożeń lokalnych, możesz użyć programu Korzyść użycia hybrydowego platformy Azure i oszczędzać na platformie Azure. W przypadku korzystania z systemu Windows Server każda licencja obejmuje koszt systemu operacyjnego (na maksymalnie dwóch maszynach wirtualnych), a Ty ponosisz tylko koszty według podstawowej stawki obliczeniowej. Możesz korzystać z istniejących licencji na program SQL Server, aby zaoszczędzić do 55% na opcjach usługi SQL Database opartych na rdzeniach wirtualnych. Opcje obejmują program SQL Server w usłudze Azure Virtual Machines i usługę SQL Server Integration Services.

Aby uzyskać więcej informacji, zobacz [Kalkulator korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Inne zasoby

Platforma Azure oferuje również usługę pozwalającą tworzyć usługi, które wykorzystują nadwyżki wydajności na platformie Azure po obniżonych stawkach. Aby uzyskać więcej informacji, zobacz [Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Następne kroki
- Jeśli dopiero zaczynasz pracę z usługą Cost Management, przeczytaj [Co to jest usługa Azure Cost Management?](../cost-management-billing-overview.md), aby dowiedzieć się, w jaki sposób ułatwia ona monitorowanie i kontrolowanie wydatków na platformie Azure oraz optymalizowanie użycia zasobów.
