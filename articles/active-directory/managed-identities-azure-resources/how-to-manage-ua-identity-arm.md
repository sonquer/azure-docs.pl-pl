---
title: Tworzenie & Usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Resource Manager
description: Instrukcje krok po kroku dotyczące tworzenia i usuwania zarządzanych tożsamości przypisanych przez użytkownika przy użyciu Azure Resource Manager.
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
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430016"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Resource Manager


Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z zarządzaną tożsamością w Azure Active Directory. Ta tożsamość służy do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności podania poświadczeń w kodzie. 

W tym artykule opisano tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Resource Manager.

Nie można wyświetlać i usuwać tożsamości zarządzanej przypisanej przez użytkownika przy użyciu szablonu Azure Resource Manager.  Zapoznaj się z następującymi artykułami, aby utworzyć i wyświetlić tożsamość zarządzaną przypisaną przez użytkownika:

- [Wyświetlanie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Usuwanie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="template-creation-and-editing"></a>Tworzenie i edytowanie szablonu

Podobnie jak w przypadku Azure Portal i skryptów, szablony Azure Resource Manager umożliwiają wdrażanie nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Do edycji i wdrażania szablonu są dostępne różne opcje, w tym:

- Przy użyciu [szablonu niestandardowego z portalu Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), który umożliwia tworzenie szablonu od podstaw lub na podstawie istniejącego szablonu wspólnego lub [szybkiego startu](https://azure.microsoft.com/documentation/templates/).
- Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z [oryginalnego wdrożenia](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Używanie lokalnego [edytora JSON (takiego jak vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
- Użycie [projektu grupy zasobów platformy Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dla programu Visual Studio w celu utworzenia i wdrożenia szablonu. 

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, użyj poniższego szablonu. Zastąp wartość `<USER ASSIGNED IDENTITY NAME>` własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure przy użyciu szablonu Azure Resource Manager, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).


 
