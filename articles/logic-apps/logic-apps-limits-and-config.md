---
title: Limity i konfiguracja
description: Limity usługi, takie jak czas trwania, przepływność i pojemność, a także wartości konfiguracyjne, takie jak adresy IP, dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 059894d441897bd89be525abcc7e1c7ab6ba23e7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485050"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informacje o limitach i konfiguracji Azure Logic Apps

W tym artykule opisano limity i szczegóły konfiguracji dotyczące tworzenia i uruchamiania zautomatyzowanych przepływów pracy przy użyciu Azure Logic Apps. Aby uzyskać automatyzację, zobacz [limity i konfiguracja w programie do automatyzacji](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limity definicji

Poniżej przedstawiono limity dla jednej definicji aplikacji logiki:

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcje na przepływ pracy | 500 | Aby zwiększyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Dozwolona głębokość zagnieżdżenia dla akcji | 8 | Aby zwiększyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Przepływy pracy na region na subskrypcję | 1000 | |
| Wyzwalacze na przepływ pracy | 10 | Praca w widoku kodu, a nie Projektant |
| Limit przypadków przełączania zakresu | 25 | |
| Zmienne na przepływ pracy | 250 | |
| Liczba znaków na wyrażenie | 8192 | |
| Maksymalny rozmiar `trackedProperties` | 16 000 znaków |
| Nazwa `action` lub `trigger` | 80 znaków | |
| Długość `description` | 256 znaków | |
| Maksymalna `parameters` | 50 | |
| Maksymalna `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Czas trwania i limity przechowywania

Poniżej przedstawiono limity dla pojedynczego uruchomienia aplikacji logiki:

| Name (Nazwa) | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Czas trwania przebiegu | 90 dni | 366 dni | Czas wykonywania jest obliczany przy użyciu czasu rozpoczęcia przebiegu i limitu określonego *w czasie rozpoczęcia* według ustawienia przepływu pracy, [**w ciągu dni**](#change-duration). <p><p>Aby zmienić domyślny limit, który jest 90 dni, zobacz [zmiana czasu trwania](#change-duration). |
| Uruchom przechowywanie w magazynie | 90 dni | 366 dni | Przechowywanie danych jest obliczane przy użyciu czasu rozpoczęcia przebiegu i limitu, który jest określony *w bieżącym czasie* przez ustawienie przepływu pracy, [**w ciągu dni przechowywania historii uruchamiania**](#change-retention). Niezależnie od tego, czy uruchomienie przebiegu lub przekroczenia limitu czasu, obliczenia przechowywania zawsze używają czasu rozpoczęcia uruchomienia. Gdy czas trwania uruchomienia przekracza *bieżący* limit przechowywania, przebieg zostanie usunięty z historii uruchamiania. <p><p>Jeśli zmienisz to ustawienie, bieżący limit jest zawsze używany do obliczania przechowywania, niezależnie od poprzedniego limitu. Jeśli na przykład obniży się limit przechowywania z 90 dni do 30 dni, w historii uruchamiania zostanie usunięte uruchomienie o 60 dni starszej. Jeśli okres przechowywania zostanie zwiększony z 30 dni do 60 dni, w 40 historii uruchomień zostanie uruchomionych 20 dni, które zostały już starsze. <p><p>Aby zmienić domyślny limit, który jest 90 dni, zobacz [zmiana przebiegu przechowywania w magazynie](#change-retention). |
| Minimalny interwał cyklu | 1 sekunda | 1 sekunda ||
| Maksymalny interwał cyklu | 500 dni | 500 dni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Zmień czas trwania przebiegu i uruchom przechowywanie w magazynie

Aby zmienić domyślny limit czasu wykonywania i uruchomić przechowywanie w magazynie, wykonaj następujące kroki. Aby zwiększyć maksymalny limit, [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) , aby uzyskać pomoc dotyczącą Twoich wymagań.

> [!NOTE]
> W przypadku aplikacji logiki na platformie Azure z wieloma dzierżawcami domyślny limit 90 dni jest taki sam, jak maksymalny limit. Tę wartość można zmniejszyć tylko.
> W przypadku aplikacji logiki w środowisku usługi integracji można obniżyć lub zwiększyć 90-dniowy limit.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W polu wyszukiwania portalu Znajdź i wybierz pozycję **Aplikacje logiki**.

1. Wybierz, a następnie otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Opcje środowiska uruchomieniowego**na liście **przechowywanie historii uruchamiania w dniach** wybierz pozycję **niestandardowa**.

1. Przeciągnij suwak, aby zmienić żądaną liczbę dni.

1. Gdy skończysz, na pasku narzędzi **Ustawienia przepływu pracy** wybierz pozycję **Zapisz**.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limity współbieżności, zapętlania i departii

Poniżej przedstawiono limity dla pojedynczego uruchomienia aplikacji logiki:

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Współbieżność wyzwalacza | -Nieograniczone, gdy kontrola współbieżności jest wyłączona <p><p>-25 jest domyślnym limitem, gdy włączony jest formant współbieżności, którego nie można cofnąć po włączeniu formantu. Można zmienić wartość domyślną z przedziału od 1 do 50 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które mogą być uruchamiane w tym samym czasie lub równolegle. <p><p>**Uwaga**: po włączeniu współbieżności limit SplitOn zostaje zredukowany do 100 elementów na potrzeby tworzenia [wsadowych tablic](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Aby zmienić domyślny limit na wartość z przedziału od 1 do 50 włącznie, zobacz [Zmienianie wyzwalacza współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) lub [wystąpień wyzwalaczy sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maksymalna liczba oczekujących przebiegów | -Bez współbieżności minimalna liczba oczekujących uruchomień wynosi 1, a maksymalna liczba to 50. <p><p>— Za pomocą współbieżności minimalna liczba oczekujących uruchomień wynosi 10 i liczbę współbieżnych uruchomień (współbieżność wyzwalacza). Można zmienić maksymalną liczbę do 100 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które mogą czekać na uruchomienie, gdy w aplikacji logiki jest już uruchomiona Maksymalna liczba wystąpień współbieżnych. <p><p>Aby zmienić domyślny limit, zobacz [Limit uruchamiania oczekujących zmian](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementy tablicy foreach | 100,000 | Ten limit opisuje największą liczbę elementów tablicy, które może przetworzyć pętla for each. <p><p>Aby filtrować większe tablice, można użyć [akcji zapytania](logic-apps-perform-data-operations.md#filter-array-action). |
| Współbieżność foreach | 20 jest domyślnym limitem, gdy kontrola współbieżności jest wyłączona. Można zmienić wartość domyślną z przedziału od 1 do 50 włącznie. | Ten limit to największą liczbę iteracji pętli "for each", które mogą być uruchamiane w tym samym czasie lub równolegle. <p><p>Aby zmienić domyślny limit na wartość z przedziału od 1 do 50 włącznie, zobacz [zmiana "dla każdego" ograniczenia współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub [uruchomienie "dla każdej" pętli sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementy SplitOn | -100 000 bez współbieżności wyzwalacza <p><p>-100 z współbieżnością wyzwalacza | Dla wyzwalaczy, które zwracają tablicę, można określić wyrażenie używające właściwości "SplitOn", które [dzieli lub departia elementy tablicy w wielu wystąpieniach przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do przetworzenia, zamiast używać pętli "foreach". To wyrażenie odwołuje się do tablicy, która ma zostać użyta do utworzenia i uruchomienia wystąpienia przepływu pracy dla każdego elementu tablicy. <p><p>**Uwaga**: po włączeniu współbieżności limit SplitOn zostanie zmniejszony do 100 elementów. |
| Do czasu iteracji | -Wartość domyślna: 60 <p><p>-Maksimum: 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepływności

Poniżej przedstawiono limity dla jednej definicji aplikacji logiki:

### <a name="multi-tenant-logic-apps-service"></a>Usługa Logic Apps z wieloma dzierżawcami

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcja: wykonania na 5 minut | 100 000 jest limitem domyślnym, ale 300 000 jest maksymalnym limitem. | Aby zmienić domyślny limit, zobacz [Uruchamianie aplikacji logiki w trybie "Wysoka przepływność"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), która jest w wersji zapoznawczej. Można też rozłożyć obciążenie na więcej niż jedną aplikację logiki w razie potrzeby. |
| Akcja: współbieżne wywołania wychodzące | ~2,500 | Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska uruchomieniowego: współbieżne wywołania przychodzące | ~1,000 | Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska uruchomieniowego: wywołania odczytu na 5 minut  | 60,000 | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
| Punkt końcowy środowiska uruchomieniowego: wywołania wywołań na 5 minut | 45,000 | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
| Przepływność zawartości na 5 minut | 600 MB | W razie potrzeby można dystrybuować obciążenia w więcej niż jednej aplikacji. |
||||

### <a name="integration-service-environment-ise"></a>Środowisko usługi integracji (ISE)

Poniżej przedstawiono limity przepływności dla jednostki SKU w warstwie Premium:

| Name (Nazwa) | Limit | Uwagi |
|------|-------|-------|
| Limit wykonywania jednostki podstawowej | Ograniczanie systemu w przypadku, gdy pojemność infrastruktury osiągnie 80% | Zapewnia 4 000 wykonania akcji na minutę, czyli ~ 160 000 000 wykonań akcji miesięcznie | |
| Limit wykonywania jednostek skalowania | Ograniczanie systemu w przypadku, gdy pojemność infrastruktury osiągnie 80% | Każda jednostka skalowania może dostarczyć ~ 2 000 dodatkowych wykonań akcji na minutę, czyli ~ 80 000 000 więcej wykonań akcji miesięcznie | |
| Maksymalna liczba jednostek skalowania, które można dodać | 10 | |
||||

Aby przekroczyć te limity podczas normalnego przetwarzania lub uruchomić testy obciążenia, które mogą wykraczać ponad te limity, [skontaktuj się z zespołem Logic Apps](mailto://logicappsemail@microsoft.com) , aby uzyskać pomoc dotyczącą Twoich wymagań.

> [!NOTE]
> [Jednostka SKU dla deweloperów](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) nie ma opublikowanych limitów, ponieważ ta jednostka SKU nie ma żadnej umowy dotyczącej poziomu usług (SLA) ani możliwości skalowania w górę.
> Tej jednostki SKU można używać tylko na potrzeby eksperymentowania, programowania i testowania, a nie produkcji ani testowania wydajności.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limity bramy

Azure Logic Apps obsługuje operacje zapisu, w tym wstawienia i aktualizacje, za pomocą bramy. Jednak te operacje mają [limity rozmiaru ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>Limity protokołu HTTP

Poniżej przedstawiono limity pojedynczego wychodzącego lub przychodzącego wywołania HTTP:

#### <a name="timeout"></a>limit czasu

Niektóre operacje łączników powodują wywołania asynchroniczne lub Nasłuchuj żądań elementu webhook, dlatego limit czasu dla tych operacji może być dłuższy niż te limity. Aby uzyskać więcej informacji, zobacz szczegóły techniczne dla określonego łącznika oraz [wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name (Nazwa) | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Żądanie wychodzące | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Przykłady żądań wychodzących obejmują wywołania wykonywane przez wyzwalacze protokołu HTTP. <p><p>**Porada**: Aby uzyskać więcej uruchomionych operacji, użyj [asynchronicznego wzorca sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [pętli do until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Żądanie przychodzące | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Przykładowe żądania przychodzące obejmują wywołania odbierane przez wyzwalacze żądań i wyzwalacze elementu webhook. <p><p>**Uwaga**: Aby uzyskać odpowiedzi dla oryginalnego obiektu wywołującego, wszystkie kroki odpowiedzi muszą zakończyć się w ramach limitu, chyba że zostanie wywołana inna aplikacja logiki jako zagnieżdżony przepływ pracy. Aby uzyskać więcej informacji, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Rozmiar komunikatu

| Name (Nazwa) | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Rozmiar komunikatu | 100 MB | 200 MB | Aby obejść ten limit, zobacz [Obsługa dużych komunikatów przy użyciu fragmentów](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsy API mogą nie obsługiwać fragmentacji lub nawet domyślnego limitu. |
| Rozmiar komunikatu z fragmentem | 1 GB | 5 GB | Ten limit ma zastosowanie do akcji, które natywnie obsługują fragmenty i umożliwiają włączenie fragmentu w konfiguracji środowiska uruchomieniowego. <p>W przypadku środowiska usługi integracji aparat Logic Apps obsługuje ten limit, ale łączniki mają własne ograniczenia dotyczące limitu aparatu, na przykład informacje o [interfejsie API łącznika usługi Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/). Aby uzyskać więcej informacji, zobacz [Obsługa dużych komunikatów przy użyciu fragmentów](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Limity znaków

| Name (Nazwa) | Uwagi |
|------|-------|
| Limit szacowania wyrażeń | 131 072 znaków | Wyrażenia `@concat()`, `@base64()``@string()` nie mogą być dłuższe niż ten limit. |
| Limit znaków w adresie URL żądania | 16 384 znaków |
|||

#### <a name="retry-policy"></a>Zasady ponawiania

| Name (Nazwa) | Limit | Uwagi |
| ---- | ----- | ----- |
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maksymalne opóźnienie ponawiania | 1 dzień | Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Opóźnienie minimalnej próby | 5 sekund | Aby zmienić wartość domyślną, użyj [parametru zasady ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity łączników niestandardowych

Poniżej przedstawiono limity łączników niestandardowych, które można tworzyć z interfejsów API sieci Web.

| Name (Nazwa) | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 1000 na subskrypcję platformy Azure ||
| Liczba żądań na minutę dla łącznika niestandardowego | 500 żądań na minutę za połączenie | 2 000 żądań na minutę na *Łącznik niestandardowy* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Zarządzane tożsamości

| Name (Nazwa) | Limit |
|------|-------|
| Zarządzane tożsamości na aplikację logiki | Tożsamość przypisana przez system lub 1 tożsamość przypisana przez użytkownika |
| Liczba aplikacji logiki, które mają zarządzaną tożsamość w ramach subskrypcji platformy Azure na region | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity kont integracji

Dla każdej subskrypcji platformy Azure obowiązują następujące limity kont integracji:

* Jedno konto integracji w [warstwie Bezpłatna](../logic-apps/logic-apps-pricing.md#integration-accounts) na region platformy Azure

* 1 000 łączne konta integracji, w tym konta integracji w dowolnych [środowiskach usług integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zarówno dla [deweloperów, jak i Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Każdy ISE, niezależnie od tego, czy [deweloper lub Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), jest ograniczony do 5 łącznych kont integracji:

  | JEDNOSTKA SKU ISE | Limity kont integracji |
  |---------|----------------------------|
  | **Premium** | 5 — tylko [standardowe](../logic-apps/logic-apps-pricing.md#integration-accounts) konta, łącznie z jednym kontem standardowym. Nie są dozwolone żadne konta bezpłatne ani podstawowe. |
  | **Developer** | 5 łączne [bezpłatnie](../logic-apps/logic-apps-pricing.md#integration-accounts) (ograniczone do 1 konta) i [standardowe](../logic-apps/logic-apps-pricing.md#integration-accounts) łącznie lub wszystkie konta w warstwie Standardowa. Nie są dozwolone żadne konta podstawowe. Użyj [jednostki SKU dla deweloperów](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) na potrzeby eksperymentowania, programowania i testowania, ale nie na potrzeby testowania wydajności lub produkcji. |
  |||

Dodatkowe koszty dotyczą kont integracji, które są dodawane poza kontami integracji, które są dołączone do ISE. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktów na konto integracji

Poniżej przedstawiono limity liczby artefaktów dla każdej warstwy konta integracji.
Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla kont integracji, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Korzystaj z warstwy Bezpłatna tylko w scenariuszach poznawczych, a nie w scenariuszach produkcyjnych. Ta warstwa ogranicza przepływność i użycie oraz nie ma umowy dotyczącej poziomu usług (SLA).

| Artefakt | Wolne | Podstawowa | Standardowa (Standard) |
|----------|------|-------|----------|
| Umowy handlowe EDI | 10 | 1 | 1000 |
| Partnerzy handlowi EDI | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schematy | 25 | 500 | 1000 |
| Zestawów | 10 | 25 | 1000 |
| Certyfikaty | 25 | 2 | 1000 |
| Konfiguracje partii | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktów

| Artefakt | Limit | Uwagi |
| -------- | ----- | ----- |
| Zestaw | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [konta usługi Azure Storage i kontenera obiektów BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (plik XSLT) | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [Azure Logic Apps interfejsu API REST — Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Uwaga**: ilość danych lub rekordów, które może pomyślnie przetworzyć mapa, zależy od rozmiaru komunikatu i limitów czasu akcji w Azure Logic Apps. Na przykład jeśli używasz akcji HTTP, na podstawie [rozmiaru komunikatu http i limitów czasu](#request-limits), mapa może przetwarzać dane do limitu rozmiaru wiadomości HTTP, jeśli operacja kończy się w limicie limitu czasu http. |
| Schemat | 8 MB | Aby przekazać pliki o rozmiarze większym niż 2 MB, użyj [konta usługi Azure Storage i kontenera obiektów BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limity przepływności

| Punkt końcowy środowiska uruchomieniowego | Wolne | Podstawowa | Standardowa (Standard) | Uwagi |
|------------------|------|-------|----------|-------|
| Wywołania odczytu na 5 minut | 3000 | 30,000 | 60,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Wywołania wywołań na 5 minut | 3000 | 30,000 | 45,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Śledzenie wywołań na 5 minut | 3000 | 30,000 | 45,000 | W razie potrzeby można rozesłać obciążenie do więcej niż jednego konta. |
| Blokowanie współbieżnych wywołań | ~1,000 | ~1,000 | ~1,000 | Ten sam dla wszystkich jednostek SKU. Możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (AS2, X12, EDIFACT)

Poniżej przedstawiono limity rozmiaru komunikatów, które dotyczą protokołów B2B:

| Name (Nazwa) | Limit wielu dzierżawców | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 — 100 MB<br>V1 – 50 MB | v2 — 200 MB <br>V1 – 50 MB | Dotyczy kodowania i kodowania |
| X12 | 50 MB | 50 MB | Dotyczy kodowania i kodowania |
| EDIFACT | 50 MB | 50 MB | Dotyczy kodowania i kodowania |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Wyłączanie lub usuwanie aplikacji logiki

Po wyłączeniu aplikacji logiki nie są tworzone żadne nowe uruchomienia.
Wszystkie w toku i oczekujące przebiegi są kontynuowane do czasu zakończenia, co może zająć trochę czasu.

Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów.
Wszystkie trwające i oczekujące przebiegi zostają anulowane.
Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Konfiguracja zapory: adresy IP

Adresy IP używane przez Azure Logic Apps dla wywołań przychodzących i wychodzących zależą od regionu, w którym znajduje się aplikacja logiki. *Wszystkie* Aplikacje logiki, które znajdują się w tym samym regionie, używają tych samych zakresów adresów IP.

> [!NOTE]
> Niektóre wywołania narzędzia do automatyzowania, takie jak żądania **http** i **http + openapi** , przechodzą bezpośrednio przez usługę Azure Logic Apps i pochodzą z adresów IP wymienionych poniżej. Aby uzyskać więcej informacji na temat adresów IP używanych przez program do automatyzowania, zobacz [limity i konfiguracja w programie do automatyzacji](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

* Aby zapewnić obsługę wywołań tworzonych przez aplikacje logiki bezpośrednio przy użyciu [protokołu HTTP](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)i innych żądań HTTP, należy skonfigurować zaporę ze *wszystkimi* adresami IP [przychodzącymi](#inbound) *i* [wychodzącymi](#outbound) , które są używane przez usługę Logic Apps, w zależności od regionów, w których istnieją aplikacje logiki. Te adresy są wyświetlane w obszarze nagłówki **przychodzące** i **wychodzące** w tej sekcji i są sortowane według regionów.

* Aby zapewnić obsługę wywołań wywoływanych przez [Łączniki zarządzane przez firmę Microsoft](../connectors/apis-list.md) , należy skonfigurować zaporę ze *wszystkimi* [wychodzącymi](#outbound) adresami IP używanymi przez te łączniki w oparciu o regiony, w których istnieją aplikacje logiki. Te adresy są wyświetlane pod nagłówkiem **wychodzącym** w tej sekcji i są sortowane według regionów.

* Aby włączyć komunikację dla aplikacji logiki, które działają w środowisku usługi integracji (ISE), upewnij się, że [te porty są otwarte](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Jeśli aplikacje logiki mają problemy z uzyskaniem dostępu do kont usługi Azure Storage, które używają [zapór i reguł zapory](../storage/common/storage-network-security.md), dostępne są [różne opcje umożliwiające dostęp](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Na przykład aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu, które używają reguł zapory i istnieją w tym samym regionie. Jednak w przypadku zezwolenia na [wychodzące adresy IP dla łączników zarządzanych w Twoim regionie](../logic-apps/logic-apps-limits-and-config.md#outbound)Aplikacje logiki mogą uzyskać dostęp do kont magazynu znajdujących się w innym regionie, z wyjątkiem sytuacji, gdy korzystasz z usługi Azure Table Storage lub łączników queue storage platformy Azure. Aby uzyskać dostęp do Table Storage lub Queue Storage, możesz zamiast tego użyć wyzwalacza HTTP i akcji. Aby uzyskać inne opcje, zobacz [dostęp do kont magazynu za zaporą](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

* W przypadku łączników niestandardowych, [Azure Government](../azure-government/documentation-government-overview.md)i [platformy Azure w Chinach](https://docs.microsoft.com/azure/china/), stałych lub zarezerwowanych adresów IP nie są dostępne.

> [!IMPORTANT]
> Jeśli masz konfiguracje zapory skonfigurowane przed 1 września 2018, upewnij się, że są one zgodne z bieżącymi adresami IP na tych listach dla regionów, w których istnieją aplikacje logiki.

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Przychodzące adresy IP — tylko usługa Logic Apps

| Region | IP |
|--------|----|
| Australia Wschodnia | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazylia Południowa | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Kanada Środkowa | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada Wschodnia | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Środkowe stany USA | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Azja Wschodnia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| Wschodnie stany USA | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Wschodnie stany USA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francja Środkowa | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francja Południowa | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japonia Wschodnia | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japonia Zachodnia | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea Środkowa | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea Południowa | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Środkowo-północne stany USA | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa Północna | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Północna Republika Południowej Afryki | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Zachodnia Republika Południowej Afryki | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Środkowo-południowe stany USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Południowe Zjednoczone Królestwo | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Środkowo-zachodnie stany USA | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa Zachodnia | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Indie Zachodnie | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Zachodnie stany USA | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Wychodzące adresy IP — łączniki zarządzane & usługi Logic Apps Service

| Region | Adres IP Logic Apps | Adres IP łączników zarządzanych |
|--------|---------------|-----------------------|
| Australia Wschodnia | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Australia Południowo-Wschodnia | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brazylia Południowa | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Kanada Środkowa | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Kanada Wschodnia | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| Indie Środkowe | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| Środkowe stany USA | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Azja Wschodnia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| Wschodnie stany USA | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52, 40.114.40.132, 40.71.249.139 |
| Wschodnie stany USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Francja Środkowa | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Francja Południowa | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Japonia Wschodnia | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Japonia Zachodnia | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Korea Środkowa | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Korea Południowa | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| Środkowo-północne stany USA | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Europa Północna | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Północna Republika Południowej Afryki | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Zachodnia Republika Południowej Afryki | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| Środkowo-południowe stany USA | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| Indie Południowe | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Azja Południowo-Wschodnia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Południowe Zjednoczone Królestwo | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Zachodnie Zjednoczone Królestwo | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| Środkowo-zachodnie stany USA | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Europa Zachodnia | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| Indie Zachodnie | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| Zachodnie stany USA | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| Zachodnie stany USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157, 52.191.164.250 |
||||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Poznaj [typowe przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
