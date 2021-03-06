---
title: Microsoft Graph interfejsów API dla usług PIM (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Zawiera informacje o korzystaniu z Microsoft Graph interfejsów API dla Azure AD Privileged Identity Management (PIM) (wersja zapoznawcza).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638667"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph interfejsów API dla Privileged Identity Management (wersja zapoznawcza)

Wszystkie Privileged Identity Management zadania można wykonać przy użyciu [Microsoft Graph interfejsów API](https://developer.microsoft.com/graph/docs/concepts/overview) dla Azure Active Directory. W tym artykule opisano ważne koncepcje dotyczące używania Microsoft Graph interfejsów API dla Privileged Identity Management.

Szczegółowe informacje na temat interfejsów API programu Microsoft Graph można znaleźć w [dokumentacji interfejsu API usługi Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Interfejsy API w wersji beta w programie Microsoft Graph są w wersji zapoznawczej i mogą ulec zmianie. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych.
>
> Mimo że wszystkie operacje PIM są obsługiwane za pomocą naszych poleceń interfejs API programu Graph, do zatwierdzania żądań aktywacji używany jest inny system. Interfejs API programu Graph do zatwierdzenia jest obecnie opracowywany i zostanie opublikowany w ciągu następnych kilku miesięcy.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby wywołać Microsoft Graph interfejsów API dla Privileged Identity Management, musisz mieć **co najmniej jedno** z następujących uprawnień:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Ustawianie uprawnień

Aby aplikacje mogły wywołać Microsoft Graph interfejsów API dla Privileged Identity Management, muszą mieć wymagane uprawnienia. Najprostszym sposobem określenia wymaganych uprawnień jest użycie [platformy wyrażania zgody w usłudze Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Ustawianie uprawnień w Eksploratorze programu Graph

Jeśli do testowania wywołań używasz Eksploratora programu Graph, możesz określić uprawnienia w tym narzędziu.

1. Zaloguj się do [Eksploratora programu Graph](https://developer.microsoft.com/graph/graph-explorer) jako administrator globalny.

1. Kliknij pozycję **Modyfikuj uprawnienia**.

    ![Eksplorator programu Graph — modyfikowanie uprawnień](./media/pim-apis/graph-explorer.png)

1. Zaznacz pola wyboru obok uprawnień, które chcesz uwzględnić. Uprawnienia `PrivilegedAccess.ReadWrite.AzureAD` nie są jeszcze dostępne w Eksploratorze programu Graph.

    ![Eksplorator programu Graph — modyfikowanie uprawnień](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kliknij pozycję **Modyfikuj uprawnienia**, aby zastosować zmiany uprawnień.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja interfejsu API usługi Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
