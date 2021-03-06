---
title: Dostęp do sieci wirtualnych platformy Azure
description: Omówienie sposobu, w jaki środowiska usług Integration Service (ISEs) ułatwiają aplikacjom logiki dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 1f743384f467e4559412fa1a46d48011b568d249
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191566"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)

Czasami Aplikacje logiki i konta integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy i usługi, które znajdują się w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, można [utworzyć *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , w którym można uruchomić aplikacje logiki i utworzyć konta integracji.

Po utworzeniu ISE platforma Azure wprowadza tę ISE do sieci wirtualnej platformy Azure *, która następnie* wdraża prywatne i izolowane wystąpienie usługi Logic Apps w sieci wirtualnej platformy Azure. To wystąpienie prywatne używa dedykowanych zasobów, takich jak magazyn, i jest uruchamiane niezależnie od publicznej "globalnej" Logic Apps usługi wielodostępnej. Oddzielenie wyizolowanego wystąpienia prywatnego i publicznego wystąpienia globalnego pomaga również ograniczyć wpływ innych dzierżawców platformy Azure na wydajność aplikacji, które są również znane jako [efekt "sąsiedzi](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)". ISE udostępnia także własne statyczne adresy IP. Te adresy IP są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, wielodostępnej usłudze.

Po utworzeniu ISE, gdy przejdziesz do tworzenia aplikacji logiki lub konta integracji, możesz wybrać swój ISE jako aplikację logiki lub konto integracji:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikacja logiki może teraz bezpośrednio uzyskiwać dostęp do systemów, które znajdują się wewnątrz lub są połączone z siecią wirtualną przy użyciu dowolnego z tych elementów, które są uruchamiane w ramach tego samego ISE, co aplikacja logiki:

* Łącznik **ISE**z etykietą dla tego systemu
* Wbudowany wyzwalacz lub akcja z oznaczeniem **rdzenia**, na przykład wyzwalacz http lub Akcja
* Łącznik niestandardowy

To omówienie zawiera szczegółowe informacje o tym, w jaki sposób usługa ISE zapewnia aplikacjom logiki i kontom integracji bezpośredni dostęp do sieci wirtualnej platformy Azure i porównuje różnice między ISE i globalną usługą Logic Apps.

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego, który różni się od planu cenowego opartego na zużyciu. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](../logic-apps/logic-apps-pricing.md).
>
> ISE również zwiększono limity czasu trwania uruchomienia, przechowywania magazynów, przepływności, żądań HTTP i przekroczeń, rozmiarów komunikatów i żądań łączników niestandardowych. 
> Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolowany a globalny

Podczas tworzenia zintegrowanego środowiska usługi (ISE) na platformie Azure Możesz wybrać sieć wirtualną platformy Azure, w której chcesz *wstrzyknąć* swój ISE. Platforma Azure następnie wprowadza lub wdraża prywatne wystąpienie usługi Logic Apps w sieci wirtualnej. Ta akcja powoduje utworzenie środowiska izolowanego, w którym można tworzyć i uruchamiać aplikacje logiki dla zasobów dedykowanych. Podczas tworzenia aplikacji logiki wybierasz ISE jako lokalizację swojej aplikacji, co zapewnia aplikacji logiki bezpośredni dostęp do sieci wirtualnej i zasobów w tej sieci.

Usługa Logic Apps w ISE zapewnia te same środowiska użytkownika i podobne funkcje jak publiczna Logic Apps globalna. Można użyć wszystkich wbudowanych wyzwalaczy, akcji i łączników zarządzanych, które są dostępne w globalnej usłudze Logic Apps. Niektóre zarządzane łączniki oferują dodatkowe wersje ISE. Różnica istnieje w miejscu, w którym są uruchamiane, oraz etykiet, które są wyświetlane w Projektancie aplikacji logiki podczas pracy w ISE.

![Łączniki z etykietami i bez nich w ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Wbudowane wyzwalacze i akcje wyświetlają etykietę **podstawową** i są zawsze uruchamiane w tym samym ISE, co aplikacja logiki. Łączniki zarządzane, które wyświetlają etykietę **ISE** , są również uruchamiane w tym samym ISE, jak w aplikacji logiki.

  Na przykład Oto kilka łączników oferujących wersje ISE:

  * Blob Storage, File Storage i Table Storage platformy Azure
  * Azure Queues, Azure Service Bus, Azure Event Hubs i IBM MQ
  * FTP i SFTP — SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 i EDIFACT

* Łączniki zarządzane, które nie wyświetlają żadnych dodatkowych etykiet, są zawsze uruchamiane w publicznej globalnej usłudze Logic Apps, ale nadal można używać tych łączników w aplikacji logiki opartej na ISE.

ISE zapewnia również zwiększone limity czasu trwania działania, przechowywania magazynu, przepływności, żądań HTTP i limitów czasu odpowiedzi, rozmiarów komunikatów i żądań łączników niestandardowych. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>Jednostki SKU ISE

Po utworzeniu ISE można wybrać jednostkę SKU dla deweloperów lub jednostkę SKU Premium. Poniżej przedstawiono różnice między tymi jednostkami SKU:

* **Developer**

  Oferuje tańsze ISE, których można użyć do eksperymentowania, programowania i testowania, ale nie do testowania produkcji ani wydajności. Jednostka SKU dla deweloperów obejmuje wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa oraz zintegrowane konto integracji z jedną [warstwą](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) dla ustalonej ceny miesięcznej. Jednak ta jednostka SKU nie obejmuje żadnej umowy dotyczącej poziomu usług (SLA), opcji skalowania pojemności lub nadmiarowości podczas odtwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje.

* **Premium**

  Oferuje ISE, którego można użyć do produkcji i obejmuje obsługę umów SLA, wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa, pojedyncze konto integracji [warstwy standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opcje skalowania pojemności i nadmiarowości podczas odtwarzania przez ustaloną cenę miesięczną.

> [!IMPORTANT]
> Opcja SKU jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić.

Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Dostęp do punktu końcowego ISE

Po utworzeniu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej.

Te punkty końcowe wpływają również na sposób, w jaki można uzyskać dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki.

* **Wewnętrzne**: prywatne punkty końcowe zezwalające na wywołania aplikacji LOGIKI w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w ramach historii uruchamiania *tylko z poziomu sieci wirtualnej*

* **Zewnętrzne**: publiczne punkty końcowe, które umożliwiają wywoływanie usługi Logic Apps w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w historii uruchamiania *spoza sieci wirtualnej*. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), upewnij się, że są one skonfigurowane przy użyciu reguł ruchu przychodzącego, aby zezwolić na dostęp do danych wejściowych i wyjściowych historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Opcja punktu końcowego dostępu jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Dostęp do lokalnych źródeł danych

W przypadku systemów lokalnych, które są połączone z siecią wirtualną platformy Azure, należy wprowadzić ISE do tej sieci, aby aplikacje logiki mogły bezpośrednio uzyskać dostęp do tych systemów przy użyciu dowolnego z następujących elementów:

* Akcja HTTP

* Łącznik ISE z etykietą dla tego systemu

  > [!NOTE]
  > Aby użyć uwierzytelniania systemu Windows za pomocą łącznika SQL Server w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), użyj wersji innej niż ISE łącznika z [lokalną bramą danych](../logic-apps/logic-apps-gateway-install.md). ISE wersja nie obsługuje uwierzytelniania systemu Windows.

* Łącznik niestandardowy

  * Jeśli masz łączniki niestandardowe wymagające lokalnej bramy danych i utworzono te łączniki poza ISE, Aplikacje logiki w ISE mogą również używać tych łączników.

  * Łączniki niestandardowe utworzone w ISE nie współpracują z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskać dostęp do lokalnych źródeł danych, które są połączone z siecią wirtualną hostującym ISE. W związku z tym aplikacje logiki w ISE najprawdopodobniej nie potrzebują bramy danych podczas komunikowania się z tymi zasobami.

W przypadku systemów lokalnych, które nie są połączone z siecią wirtualną ani nie mają łączników z ISEmi, musisz najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) , aby umożliwić aplikacjom logiki łączenie się z tymi systemami.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji z usługą ISE

Konta integracji z aplikacjami logiki można używać w środowisku usługi integracji (ISE). Jednak te konta integracji muszą używać tego *samego ISE* , co połączone Aplikacje logiki. Aplikacje logiki w ISE mogą odwoływać się tylko do tych kont integracji, które znajdują się w tym samym ISE. Podczas tworzenia konta integracji możesz wybrać swój ISE jako lokalizację konta integracji. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla kont integracji za pomocą ISE, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z izolowanych aplikacji logiki](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Dodawanie artefaktów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzaj środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
