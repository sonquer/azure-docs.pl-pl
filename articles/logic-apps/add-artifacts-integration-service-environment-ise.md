---
title: Dodawanie artefaktów do środowisk usługi integracji
description: Dodawanie aplikacji logiki, kont integracji, łączników niestandardowych i łączników zarządzanych do środowiska usługi integracji (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e2505d8ee8b8539f158c0a549bedfcd69a954e24
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191685"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Dodaj artefakty do środowiska usługi integracji (ISE) w Azure Logic Apps

Po utworzeniu [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Dodaj artefakty, takie jak aplikacje logiki, konta integracji i łączniki, aby mogły uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure. Na przykład zarządzane łączniki ISE, które staną się dostępne po utworzeniu ISE, nie są automatycznie wyświetlane w Projektancie aplikacji logiki. Aby można było używać tych łączników ISE, należy ręcznie [dodać i wdrożyć te łączniki do ISE](#add-ise-connectors-environment) , aby były wyświetlane w Projektancie aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* ISE, który został utworzony w celu uruchomienia aplikacji logiki. Jeśli nie masz elementu ISE, [najpierw utwórz ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Tworzenie aplikacji logiki

Aby skompilować Aplikacje logiki, które działają w środowisku usługi integracji (ISE), wykonaj następujące kroki:

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje logiki** > **Dodaj**.

   ![Dodaj nową aplikację logiki do ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Podaj informacje o aplikacji logiki, którą chcesz utworzyć, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Yes | Nazwa aplikacji logiki do utworzenia |
   | **Subskrypcja** | Yes | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | Yes | Nazwa grupy zasobów platformy Azure (nowej lub istniejącej) do użycia |
   | **Lokalizacja** | Yes | W obszarze **środowiska usługi integracji**wybierz opcję ISE, która ma być używana, jeśli nie została jeszcze wybrana. <p><p> **Ważne**: Aby używać aplikacji logiki z kontem integracji, oba muszą używać tego samego ISE. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Kontynuuj [Tworzenie aplikacji logiki w zwykły sposób](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   W przypadku różnic między działami wyzwalaczami i działaniami oraz sposobie ich etykietowania w przypadku używania ISE w porównaniu z globalną usługą Logic Apps, zobacz [izolowany a globalny w przeglądzie ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Aby zarządzać aplikacjami logiki i połączeniami interfejsu API w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Tworzenie kont integracji

W oparciu o [ISEą jednostkę SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) wybraną podczas tworzenia ISE obejmuje specyficzne użycie konta integracji bez dodatkowych kosztów. Aplikacje logiki, które istnieją w środowisku usługi integracji (ISE), mogą odwoływać się tylko do kont integracji, które istnieją w tym samym ISE. Aby konto integracji działało z usługą Logic Apps w ISE, zarówno konto integracji, jak i Aplikacje logiki muszą używać tego *samego środowiska* , co ich lokalizacja. Aby uzyskać więcej informacji na temat kont integracji i ISEs, zobacz [konta integracji z ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Aby utworzyć konto integracji korzystające z ISE, wykonaj następujące kroki:

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. W menu ISE w obszarze **Ustawienia**wybierz pozycję **konta integracji** > **Dodaj**.

   ![Dodaj nowe konto integracji do ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Podaj informacje o aplikacji logiki, którą chcesz utworzyć, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Yes | Nazwa konta integracji, które chcesz utworzyć. |
   | **Subskrypcja** | Yes | Nazwa subskrypcji platformy Azure, która ma być używana |
   | **Grupa zasobów** | Yes | Nazwa grupy zasobów platformy Azure (nowej lub istniejącej) do użycia |
   | **Warstwa cenowa** | Yes | Warstwa cenowa do użycia dla konta integracji |
   | **Lokalizacja** | Yes | W obszarze **środowiska usługi integracji**zaznacz te same ISE, które są używane przez aplikacje logiki, jeśli nie zostały jeszcze wybrane. <p><p> **Ważne**: Aby korzystać z konta integracji z usługą Logic Apps, oba muszą używać tego samego ISE. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. [Połącz aplikację logiki z kontem integracji w zwykły sposób](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Kontynuuj dodawanie artefaktów do konta integracji, takiego jak [partnerzy handlowi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Aby zarządzać kontami integracji w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Dodawanie łączników ISE

Łączniki zarządzane przez firmę Microsoft, które staną się dostępne po utworzeniu ISE, nie są automatycznie wyświetlane w selektorze łącznika w Projektancie aplikacji logiki. Aby można było używać tych łączników ISE, należy ręcznie dodać i wdrożyć te łączniki do ISE, aby były wyświetlane w Projektancie aplikacji logiki.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki zarządzane**. Na pasku narzędzi wybierz pozycję **Dodaj**.

   ![Wyświetlanie łączników zarządzanych](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. W okienku **Dodaj nowe łączniki zarządzane** Otwórz listę **Znajdź łącznik** . Wybierz łącznik ISE, który ma być używany, ale jeszcze nie został wdrożony w ISE. Wybierz pozycję **Utwórz**.

   ![Wybierz łącznik ISE, który ma zostać wdrożony w ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Tylko łączniki ISE, które są uprawnione, ale nie zostały jeszcze wdrożone do ISE, są dostępne do wyboru. Łączniki, które są już wdrożone w ISE, są niedostępne do wyboru.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Tworzenie łączników niestandardowych

Aby używać łączników niestandardowych w ISE, Utwórz te łączniki niestandardowe bezpośrednio wewnątrz ISE.

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. Z menu ISE w obszarze **Ustawienia**wybierz pozycję **łączniki niestandardowe** > **Dodaj**.

   ![Tworzenie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Podaj nazwę, subskrypcję platformy Azure i grupę zasobów platformy Azure (nową lub istniejącą) do użycia w łączniku niestandardowym.

1. Z listy **Lokalizacja** w sekcji **środowiska usługi integracji** wybierz tę samą ISEę, w której używane są aplikacje logiki, a następnie wybierz pozycję **Utwórz**, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Wybierz nowy łącznik niestandardowy, a następnie wybierz pozycję **Edytuj**, na przykład:

   ![Wybieranie i edytowanie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Kontynuuj, tworząc łącznik w zwykły sposób z [definicji openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) lub [protokołu SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Aby zarządzać łącznikami niestandardowymi w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzaj środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md)
