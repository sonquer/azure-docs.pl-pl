---
title: Projektowanie wdrożenia dzienników Azure Monitor | Microsoft Docs
description: W tym artykule opisano zagadnienia i zalecenia dla klientów przygotowujące się do wdrożenia obszaru roboczego w Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: f2d530792e1a6f598dbf2ed66889c01cc43467ed
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162246"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Projektowanie wdrożenia dzienników Azure Monitor

Azure Monitor przechowuje dane [dzienników](data-platform-logs.md) w obszarze roboczym log Analytics, który jest zasobem platformy Azure i kontenerem, w którym zbierane są dane, agregowane i obsługiwane jako granica administracyjna. Chociaż można wdrożyć co najmniej jeden obszar roboczy w ramach subskrypcji platformy Azure, istnieje kilka kwestii, które należy zrozumieć w celu zagwarantowania, że początkowe wdrożenie jest zgodne z naszymi wskazówkami, aby zapewnić opłacalność, łatwość zarządzania i skalowalność. wdrożenie spełniające potrzeby organizacji.

Dane w obszarze roboczym są zorganizowane w tabele, z których każdy przechowuje różne rodzaje danych i ma własny unikatowy zestaw właściwości na podstawie zasobów generujących dane. Większość źródeł danych będzie zapisywać w własnych tabelach w obszarze roboczym Log Analytics.

![Przykładowy model danych obszaru roboczego](./media/design-logs-deployment/logs-data-model-01.png)

Obszar roboczy Log Analytics zawiera następujące informacje:

* Lokalizacja geograficzna magazynu danych.
* Izolacja danych przez przyznanie różnym użytkownikom praw dostępu zgodnie z naszymi zalecanymi strategiami projektowania.
* Zakres konfiguracji ustawień, takich jak [warstwa cenowa](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [przechowywanie](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)i [pułapy danych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume).

Ten artykuł zawiera szczegółowe omówienie zagadnień dotyczących projektowania i migracji, Omówienie kontroli dostępu oraz zrozumienie implementacji projektu, które zalecamy dla organizacji IT.



## <a name="important-considerations-for-an-access-control-strategy"></a>Ważne zagadnienia dotyczące strategii kontroli dostępu

Określenie liczby potrzebnych obszarów roboczych ma wpływ na co najmniej jedno z następujących wymagań:

* Jesteś firmą globalną i potrzebujesz danych dzienników przechowywanych w określonych regionach w celu zachowania poufności danych lub jej zgodności.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Zarządzasz wieloma wydziałami lub grupami biznesowymi, a każdy z nich ma wyświetlać własne dane, ale nie dane od innych. Ponadto nie istnieje wymóg biznesowy dla skonsolidowanego działu krzyżowego ani widoku grupy biznesowej.

Obecnie organizacje IT są modelowane zgodnie z scentralizowanym, zdecentralizowanym lub mieszanym hybrydem obu struktur. W związku z tym następujące modele wdrażania obszarów roboczych są często używane do mapowania na jedną z następujących struktur organizacyjnych:

* **Scentralizowany**: wszystkie dzienniki są przechowywane w centralnym obszarze roboczym i zarządzane przez jednego zespołu, z Azure monitor zapewniający zróżnicowany dostęp dla poszczególnych zespołów. W tym scenariuszu można łatwo zarządzać, przeszukiwać zasoby i rejestrować je między sobą. Obszar roboczy może znacząco wzrosnąć w zależności od ilości danych zbieranych z wielu zasobów w ramach subskrypcji, z dodatkowym narzutem administracyjnym w celu utrzymania kontroli dostępu do różnych użytkowników. Ten model jest znany jako "Hub i szprych".
* **Zdecentralizowane**: każdy zespół ma własny obszar roboczy utworzony w grupie zasobów, której są właścicielami i którymi zarządza, a dane dziennika są segregowane według poszczególnych zasobów. W tym scenariuszu obszar roboczy może być zabezpieczony, a kontrola dostępu jest spójna z dostępem do zasobów, ale trudno jest rejestrować krzyżowe dzienniki. Użytkownicy, którzy potrzebują szerokiego wglądu w wiele zasobów, nie mogą analizować danych w zrozumiały sposób.
* **Hybrydowe**: wymagania dotyczące zgodności inspekcji zabezpieczeń bardziej komplikują ten scenariusz, ponieważ wiele organizacji jednocześnie implementuje oba modele wdrażania. Często wynika to z skomplikowanej, kosztownej i trudnej do utrzymania konfiguracji z przerwyami w dziennikach.

Korzystając z Log Analytics agentów do zbierania danych, należy zrozumieć następujące kwestie w celu zaplanowania wdrożenia agenta:

* Aby zbierać dane z agentów systemu Windows, można [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](../../azure-monitor/platform/agent-windows.md), nawet gdy jest on raportowany do System Center Operations Manager grupy zarządzania. Agent systemu Windows może zgłosić do czterech obszarów roboczych.
* Agent systemu Linux nie obsługuje wiele multihostingu i może być raportowany tylko do jednego obszaru roboczego.

Jeśli używasz System Center Operations Manager 2012 R2 lub nowszego:

* Każda Operations Manager grupy zarządzania może być [połączona tylko z jednym obszarem roboczym](../platform/om-agents.md). 
* Komputery z systemem Linux, które są zgłaszane do grupy zarządzania, muszą być skonfigurowane do bezpośredniego raportowania do obszaru roboczego Log Analytics. Jeśli komputery z systemem Linux są już zgłaszane bezpośrednio do obszaru roboczego i chcesz monitorować je za pomocą Operations Manager, wykonaj następujące kroki, aby [zgłosić Operations Manager do grupy zarządzania](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Program Log Analytics Agent systemu Windows można zainstalować na komputerze z systemem Windows i mieć do niego raport Operations Manager zintegrowany z obszarem roboczym i innym obszarze roboczym.

## <a name="access-control-overview"></a>Omówienie kontroli dostępu

Za pomocą kontroli dostępu opartej na rolach (RBAC) można przyznać użytkownikom i grupom tylko dostęp do danych monitorowania w obszarze roboczym. Pozwala to na dostosowanie się do modelu operacyjnego organizacji IT przy użyciu jednego obszaru roboczego do przechowywania zebranych danych włączonych dla wszystkich zasobów. Można na przykład udzielić dostępu do zespołu odpowiedzialnego za usługi infrastruktury hostowane na maszynach wirtualnych platformy Azure, a w związku z tym będzie miał dostęp tylko do dzienników generowanych przez maszyny wirtualne. Jest to nasz nowy model dziennika kontekstu zasobów. Podstawą tego modelu jest dla każdego rekordu dziennika wyemitowanego przez zasób platformy Azure, który jest automatycznie kojarzony z tym zasobem. Dzienniki są przekazywane do centralnego obszaru roboczego, który szanuje zakres i RBAC w oparciu o zasoby.

Dane, do których użytkownik ma dostęp, są określane przez kombinację czynników, które są wymienione w poniższej tabeli. Każdy z nich został opisany w poniższych sekcjach.

| współczynnik | Opis |
|:---|:---|
| [Tryb dostępu](#access-mode) | Metoda wykorzystywana przez użytkownika w celu uzyskania dostępu do obszaru roboczego.  Definiuje zakres dostępnych danych i tryb kontroli dostępu, który został zastosowany. |
| [Tryb kontroli dostępu](#access-control-mode) | Ustawienie w obszarze roboczym, które określa, czy uprawnienia są stosowane na poziomie obszaru roboczego czy zasobu. |
| [Uprawnienia](manage-access.md) | Uprawnienia zastosowane do poszczególnych lub grup użytkowników dla obszaru roboczego lub zasobu. Definiuje dane, do których użytkownik będzie miał dostęp. |
| [Kontrola RBAC na poziomie tabeli](manage-access.md#table-level-rbac) | Opcjonalne, szczegółowe uprawnienia, które mają zastosowanie do wszystkich użytkowników niezależnie od ich trybu dostępu lub trybu kontroli dostępu. Określa typy danych, do których użytkownik może uzyskać dostęp. |

## <a name="access-mode"></a>Tryb dostępu

*Tryb dostępu* odnosi się do sposobu, w jaki użytkownik uzyskuje dostęp do obszaru roboczego log Analytics i definiuje zakres danych, do których mają dostęp. 

Użytkownicy mają dwie opcje uzyskiwania dostępu do danych:

* **Obszar roboczy — kontekst**: można wyświetlić wszystkie dzienniki w obszarze roboczym, do którego masz uprawnienia. Zapytania w tym trybie są ograniczone do wszystkich danych we wszystkich tabelach w obszarze roboczym. Jest to tryb dostępu używany do uzyskiwania dostępu do dzienników z obszarem roboczym jako zakres, na przykład po wybraniu opcji **dzienniki** z menu **Azure monitor** w Azure Portal.

    ![Log Analytics kontekstu z obszaru roboczego](./media/design-logs-deployment/query-from-workspace.png)

* **Kontekst zasobu**: podczas uzyskiwania dostępu do obszaru roboczego dla określonego zasobu, grupy zasobów lub subskrypcji, na przykład po wybraniu opcji **dzienniki** z menu zasobów w Azure Portal, można wyświetlić dzienniki tylko dla zasobów we wszystkich tabelach, do których masz dostęp. Zapytania w tym trybie są ograniczone do danych skojarzonych z tym zasobem. Ten tryb umożliwia również szczegółową kontrolę RBAC.

    ![Log Analytics kontekstu z zasobu](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Dzienniki są dostępne dla zapytań kontekstu zasobów tylko wtedy, gdy zostały prawidłowo skojarzone z odpowiednim zasobem. Obecnie następujące zasoby mają ograniczenia:
    > - Komputery poza platformą Azure
    > - Service Fabric
    > - Application Insights
    >
    > Możesz sprawdzić, czy dzienniki są prawidłowo skojarzone ze swoimi zasobami, uruchamiając zapytanie i sprawdzając odpowiednie rekordy. Jeśli w [_ResourceId](log-standard-properties.md#_resourceid) właściwość jest prawidłowy identyfikator zasobu, dane są dostępne dla zapytań skoncentrowanych na zasobach.

Azure Monitor automatycznie określa tryb prawy w zależności od kontekstu, w którym jest wykonywane przeszukiwanie dzienników. Zakres jest zawsze wyświetlany w lewej górnej części Log Analytics.

### <a name="comparing-access-modes"></a>Porównywanie trybów dostępu

Poniższa tabela zawiera podsumowanie trybów dostępu:

| | Obszar roboczy — kontekst | Zasób-kontekst |
|:---|:---|:---|
| Dla kogo jest przeznaczony każdy model? | Administracja centralna. Administratorzy, którzy muszą skonfigurować zbieranie danych i użytkowników, którzy potrzebują dostępu do szerokiej gamy zasobów. Są one również wymagane dla użytkowników, którzy muszą uzyskać dostęp do dzienników dla zasobów poza platformą Azure. | Zespoły aplikacji. Administratorzy monitorowanych zasobów platformy Azure. |
| Co jest wymagane przez użytkownika do wyświetlania dzienników? | Uprawnienia do obszaru roboczego. Zobacz **uprawnienia obszaru roboczego** w obszarze [Zarządzanie dostępem przy użyciu uprawnień obszaru roboczego](manage-access.md#manage-access-using-workspace-permissions). | Dostęp do odczytu do zasobu. Zobacz **uprawnienia zasobów** w obszarze [Zarządzanie dostępem przy użyciu uprawnień platformy Azure](manage-access.md#manage-access-using-azure-permissions). Uprawnienia mogą być dziedziczone (takie jak z grupy zasobów zawierających) lub bezpośrednio przypisane do zasobu. Uprawnienie do dzienników dla zasobu zostanie automatycznie przypisane. |
| Jaki jest zakres uprawnień? | Obszary. Użytkownicy z dostępem do obszaru roboczego mogą wykonywać zapytania dotyczące wszystkich dzienników w obszarze roboczym z tabel, do których mają uprawnienia. Zobacz [tabelę kontroli dostępu](manage-access.md#table-level-rbac) | Zasób platformy Azure. Użytkownik może wysyłać zapytania do dzienników dla określonych zasobów, grup zasobów lub subskrypcji, do których mają dostęp z dowolnego obszaru roboczego, ale nie może wysyłać zapytań do dzienników innych zasobów. |
| Jak użytkownicy mogą uzyskiwać dostęp do dzienników? | <ul><li>Uruchom **dzienniki** z menu **Azure monitor** .</li></ul> <ul><li>Uruchom **dzienniki** z **log Analytics obszarów roboczych**.</li></ul> <ul><li>Ze [skoroszytów](../visualizations.md#workbooks)Azure monitor.</li></ul> | <ul><li>Uruchom **dzienniki** z menu dla zasobu platformy Azure</li></ul> <ul><li>Uruchom **dzienniki** z menu **Azure monitor** .</li></ul> <ul><li>Uruchom **dzienniki** z **log Analytics obszarów roboczych**.</li></ul> <ul><li>Ze [skoroszytów](../visualizations.md#workbooks)Azure monitor.</li></ul> |

## <a name="access-control-mode"></a>Tryb kontroli dostępu

*Tryb kontroli dostępu* jest ustawieniem w każdym obszarze roboczym, który definiuje sposób określania uprawnień dla obszaru roboczego.

* **Wymagaj uprawnień obszaru roboczego**: ten tryb kontroli nie zezwala na szczegółową kontrolę RBAC. Aby użytkownik mógł uzyskać dostęp do obszaru roboczego, muszą mieć przyznane uprawnienia do obszaru roboczego lub do określonych tabel.

    Jeśli użytkownik uzyskuje dostęp do obszaru roboczego po trybie kontekstu obszaru roboczego, ma dostęp do wszystkich danych w dowolnej tabeli, do której udzielono dostępu. Jeśli użytkownik uzyskuje dostęp do obszaru roboczego po trybie kontekstu zasobów, ma dostęp tylko do danych dla tego zasobu w dowolnej tabeli, do której udzielono dostępu.

    Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych przed marcem 2019.

* **Użyj uprawnień zasobu lub obszaru roboczego**: ten tryb kontroli zezwala na szczegółową kontrolę RBAC. Użytkownikom można udzielić dostępu tylko do danych skojarzonych z zasobami, które mogą przeglądać, przypisując uprawnienia `read` platformy Azure. 

    Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie kontekstu obszaru roboczego, mają zastosowanie uprawnienia obszaru roboczego. Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie kontekstu zasobów, sprawdzane są tylko uprawnienia do zasobów, a uprawnienia obszaru roboczego są ignorowane. Włącz funkcję RBAC dla użytkownika, usuwając je z uprawnień obszaru roboczego i zezwalając na ich rozpoznanie.

    Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych po marcu 2019.

    > [!NOTE]
    > Jeśli użytkownik ma tylko uprawnienia do zasobów w obszarze roboczym, może uzyskać dostęp do obszaru roboczego tylko przy użyciu trybu kontekstu zasobów, przy założeniu, że tryb dostępu do obszaru roboczego jest ustawiony na **Używanie uprawnień zasobu lub obszaru roboczego**.

Aby dowiedzieć się, jak zmienić tryb kontroli dostępu w portalu przy użyciu programu PowerShell lub szablonu Menedżer zasobów, zobacz [Konfigurowanie trybu kontroli dostępu](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Limit ilości woluminu pozyskiwania

Azure Monitor to usługa danych o dużej skali, która umożliwia tysiącom klientów wysyłanie terabajtów danych co miesiąc w coraz większej tempie. Domyślny próg współczynnika pozyskiwania jest ustawiony na **6 GB/min** dla obszaru roboczego. Jest to przybliżona wartość, ponieważ rzeczywisty rozmiar może się różnić między typami danych w zależności od długości dziennika i jego stosunku kompresji. Ten limit nie dotyczy danych wysyłanych z agentów lub [interfejsu API modułu zbierającego dane](data-collector-api.md).

W przypadku wysyłania danych o wyższej stawce do jednego obszaru roboczego niektóre dane zostaną usunięte, a zdarzenie jest wysyłane do tabeli *operacji* w obszarze roboczym co 6 godzin, podczas gdy próg zostanie przekroczony. Jeśli wolumin pozyskiwania w dalszym ciągu przekroczy limit szybkości lub oczekujesz, że wkrótce dojdziesz do niego, możesz poprosić o zwiększenie obszaru roboczego, otwierając żądanie pomocy technicznej.
 
Aby otrzymywać powiadomienia o takim zdarzeniu w Twoim obszarze roboczym, należy utworzyć [regułę alertu dziennika](alerts-log.md) przy użyciu następującego zapytania z podstawą logiki alertu na liczbie wyników, które nie są równe zeru.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Zalecenia

![Przykład projektu kontekstu zasobów](./media/design-logs-deployment/workspace-design-resource-context-01.png)

W tym scenariuszu zawarto projekt jednego obszaru roboczego w ramach subskrypcji organizacji IT, który nie jest ograniczony przez niezależność danych ani zgodność z przepisami lub musi być mapowany na regiony, w których są wdrażane zasoby. Umożliwia ona organizacjom bezpieczeństwo i administratorom IT skorzystanie z udoskonalonej integracji z usługą Azure Access Management i bardziej bezpieczną kontrolą dostępu.

Wszystkie zasoby, rozwiązania monitorowania i szczegółowe informacje, takie jak Application Insights i Azure Monitor dla maszyn wirtualnych, obsługujące infrastrukturę i aplikacje obsługiwane przez różne zespoły są skonfigurowane do przekazywania zebranych danych dzienników do organizacji IT scentralizowany udostępniony obszar roboczy. Użytkownicy poszczególnych zespołów otrzymują dostęp do dzienników dla zasobów, do których miał dostęp.

Po wdrożeniu architektury obszaru roboczego można wymusić to na zasobach platformy Azure przy użyciu [Azure Policy](../../governance/policy/overview.md). Zapewnia ona sposób definiowania zasad i zapewnienia zgodności z zasobami platformy Azure, dzięki czemu wysyła wszystkie dzienniki zasobów do określonego obszaru roboczego. Na przykład w przypadku maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych można używać istniejących zasad, które obliczają zgodność obszaru roboczego i raportów, lub dostosowuje się do korygowania w przypadku braku zgodności.  

## <a name="workspace-consolidation-migration-strategy"></a>Strategia migracji obszaru roboczego do konsolidacji

W przypadku klientów, którzy już wdrożyły wiele obszarów roboczych i chcą skonsolidować model dostępu do kontekstu zasobów, zalecamy wykonanie przyrostowego podejścia do migracji do zalecanego modelu dostępu i nie podjęto próby osiągnięcia tego szybko lub agresywnie. Zgodnie z podejściem stopniowanym do planowania, migrowania, weryfikowania i wycofywania po rozsądnej osi czasu można uniknąć nieplanowanych zdarzeń lub nieoczekiwanego wpływu na operacje w chmurze. Jeśli nie masz zasad przechowywania danych ze względu na zgodność lub przyczynę biznesową, musisz ocenić właściwy czas przechowywania danych w obszarze roboczym, z którego jest przeprowadzana migracja. Po ponownym skonfigurowaniu zasobów do raportowania w udostępnionym obszarze roboczym można w razie potrzeby analizować dane w oryginalnym obszarze roboczym. Po zakończeniu migracji, jeśli zamierzasz zachować dane w oryginalnym obszarze roboczym przed upływem okresu przechowywania, nie usuwaj go.

Podczas planowania migracji do tego modelu należy wziąć pod uwagę następujące kwestie:

* Zapoznaj się z przepisami branżowymi i zasadami wewnętrznymi dotyczącymi przechowywania danych.
* Upewnij się, że zespoły aplikacji mogą działać w ramach istniejących funkcji kontekstu zasobów.
* Zidentyfikuj dostęp przyznany do zasobów dla zespołów aplikacji i przetestuj go w środowisku programistycznym przed wdrożeniem w produkcji.
* Skonfiguruj obszar roboczy, aby włączyć opcję **Użyj uprawnień zasobu lub obszaru roboczego**.
* Usuń zespoły aplikacji uprawnienia do odczytu i wysyłania zapytań do obszaru roboczego.
* Włącz i skonfiguruj dowolne rozwiązania monitorujące, szczegółowe informacje, takie jak Azure Monitor kontenerów i/lub Azure Monitor dla maszyn wirtualnych, Twoje konta usługi Automation i rozwiązania do zarządzania, takie jak Update Management, uruchamianie/zatrzymywanie maszyn wirtualnych itp., które zostały wdrożone w oryginalnym obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

Aby zaimplementować uprawnienia zabezpieczeń i kontrolki zalecane w tym przewodniku, przejrzyj temat [Zarządzanie dostępem do dzienników](manage-access.md).
