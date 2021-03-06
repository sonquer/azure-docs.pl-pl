---
title: Tworzenie lub aktualizowanie ról niestandardowych dla zasobów platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak wyświetlać, tworzyć, aktualizować i usuwać role niestandardowe z kontrolą dostępu opartą na rolach (RBAC) dla zasobów platformy Azure przy użyciu interfejsu API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137362"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Tworzenie lub aktualizowanie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano sposób wyświetlania, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu interfejsu API REST.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę wszystkich ról niestandardowych w katalogu, należy użyć interfejsu API REST [— lista definicji ról](/rest/api/authorization/roledefinitions/list) .

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Zastąp ciąg *{Filter}* typem roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Wyświetlanie listy ról niestandardowych w zakresie

Aby wyświetlić listę ról niestandardowych w zakresie, użyj interfejsu API REST z [listą ról](/rest/api/authorization/roledefinitions/list) .

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{Filter}* typem roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Utwórz listę niestandardowych definicji ról według nazwy

Aby uzyskać informacje o roli niestandardowej według nazwy wyświetlanej, użyj [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{Filter}* nazwą wyświetlaną roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Użyj zakodowanego w adresie URL postaci dokładnej nazwy wyświetlanej roli. Na przykład `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Utwórz listę niestandardowych definicji ról według identyfikatora

Aby uzyskać informacje o roli niestandardowej przy użyciu unikatowego identyfikatora, użyj [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Użyj interfejsu API REST z [listą ról](/rest/api/authorization/roledefinitions/list) , aby uzyskać identyfikator GUID roli.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{zduplikowanych}* identyfikatorem GUID definicji roli.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj [definicji roli — interfejsu API REST — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) . Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą uprawnienia `Microsoft.Authorization/roleDefinitions/write` na wszystkich `assignableScopes`. W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Przejrzyj listę [operacji dostawcy zasobów](resource-provider-operations.md) dostępnych do utworzenia uprawnień dla roli niestandardowej.

1. Użyj narzędzia GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla niestandardowego identyfikatora roli. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Rozpocznij od następującego żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* pierwszym `assignableScopes` roli niestandardowej.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

1. W treści żądania we właściwości `assignableScopes` Zastąp ciąg *{zduplikowanych}* identyfikatorem GUID.

1. Zastąp ciąg *{Subscription}* identyfikatorem subskrypcji.

1. We właściwości `actions` Dodaj operacje, które mogą być wykonywane przez rolę.

1. We właściwości `notActions` Dodaj operacje, które są wykluczone z dozwolonej `actions`.

1. We właściwościach `roleName` i `description` Określ unikatową nazwę roli i opis. Aby uzyskać więcej informacji na temat właściwości, zobacz [role niestandardowe](custom-roles.md).

    Poniżej przedstawiono przykład treści żądania:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, użyj [definicji ról — interfejsu API REST — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) . Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą uprawnienia `Microsoft.Authorization/roleDefinitions/write` na wszystkich `assignableScopes`. W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Aby uzyskać informacje o roli niestandardowej, użyj funkcji [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [definicje ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST. Aby uzyskać więcej informacji, zobacz sekcję wcześniejsze [listy ról niestandardowych](#list-custom-roles) .

1. Rozpocznij od następującego żądania:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* pierwszym `assignableScopes` roli niestandardowej.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

1. Na podstawie informacji o roli niestandardowej Utwórz treść żądania o następującym formacie:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Zaktualizuj treść żądania, podając zmiany, które chcesz wprowadzić do roli niestandardowej.

    Poniżej przedstawiono przykład treści żądania z dodaną nową akcją ustawień diagnostycznych:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj [definicji roli — Usuń](/rest/api/authorization/roledefinitions/delete) interfejs API REST. Aby wywołać ten interfejs API, należy zalogować się przy użyciu użytkownika, do którego przypisano rolę mającą uprawnienia `Microsoft.Authorization/roleDefinitions/delete` na wszystkich `assignableScopes`. W przypadku ról wbudowanych tylko [właściciel](built-in-roles.md#owner) i [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) zawierają to uprawnienie.

1. Aby uzyskać identyfikator GUID roli niestandardowej, należy użyć definicji [ról — listy](/rest/api/authorization/roledefinitions/list) lub [definicji ról — Uzyskaj](/rest/api/authorization/roledefinitions/get) interfejs API REST. Aby uzyskać więcej informacji, zobacz sekcję wcześniejsze [listy ról niestandardowych](#list-custom-roles) .

1. Rozpocznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp element *{SCOPE}* zakresem, dla którego chcesz usunąć rolę niestandardową.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp element *{zduplikowanych}* identyfikatorem GUID roli niestandardowej.

## <a name="next-steps"></a>Następne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
