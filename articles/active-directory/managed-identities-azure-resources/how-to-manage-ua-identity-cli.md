---
title: Zarządzanie tożsamością zarządzaną przez użytkownika — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547510"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure


Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z zarządzaną tożsamością w Azure Active Directory. Ta tożsamość służy do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności podania poświadczeń w kodzie. 

Ten artykuł zawiera informacje na temat tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, można korzystać z trzech opcji:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (zobacz następną sekcję).
    - Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. Zaloguj się do platformy Azure przy użyciu `az login`przy użyciu konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć tożsamość zarządzaną przypisaną przez użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Za pomocą polecenia [AZ Identity Create](/cli/azure/identity#az-identity-create) Utwórz tożsamość zarządzaną przypisaną przez użytkownika. `-g` parametr określa grupę zasobów, w której ma zostać utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jego nazwę. Zastąp wartości parametrów `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga [operatora zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Aby wyświetlić tożsamości zarządzane przypisane przez użytkownika, użyj polecenia [AZ Identity list](/cli/azure/identity#az-identity-list) . Zastąp `<RESOURCE GROUP>` własną wartością:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
W odpowiedzi JSON tożsamości zarządzane przypisane przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj polecenia [AZ Identity Delete](/cli/azure/identity#az-identity-delete) .  Parametr-n określa nazwę i parametr-g określa grupę zasobów, w której utworzono tożsamość zarządzaną przez użytkownika. Zastąp wartości parametrów `<USER ASSIGNED IDENTITY NAME>` i `<RESOURCE GROUP>` własnymi wartościami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, do którego została przypisana. Usuń te z maszyny wirtualnej/VMSS przy użyciu polecenia `az vm/vmss identity remove`

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z pełną listą poleceń dotyczących tożsamości interfejsu wiersza polecenia platformy Azure, zobacz [AZ Identity](/cli/azure/identity).

Aby uzyskać informacje na temat sposobu przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
