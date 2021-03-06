---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej w jednym zasobie dostępu do innego zasobu przy użyciu Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547288"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie zarządzanej tożsamości dostępu do zasobu za pomocą Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. W tym artykule pokazano, jak udostępnić maszynę wirtualną platformy Azure lub zarządzaną tożsamość zestawu skalowania maszyn wirtualnych do konta usługi Azure Storage przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu do tożsamości zarządzanej do innego zasobu za pomocą RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład na [maszynie wirtualnej platformy Azure](qs-configure-portal-windows-vm.md) lub w [usłudze Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w ramach której skonfigurowano tożsamość zarządzaną.

2. Przejdź do żądanego zasobu, w którym chcesz zmodyfikować kontrolę dostępu. W tym przykładzie przydajemy maszynom wirtualnym platformy Azure dostęp do konta magazynu, więc przejdźmy do konta magazynu.

3. Wybierz stronę **Kontrola dostępu (IAM)** zasobu, a następnie wybierz pozycję **+ Dodaj przypisanie roli**. Następnie określ **rolę**, **Przypisz dostęp do**i określ odpowiednią **subskrypcję**. W obszarze kryteria wyszukiwania powinien zostać wyświetlony zasób. Wybierz zasób, a następnie wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu kontroli dostępu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md).
- Aby włączyć zarządzaną tożsamość w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu Azure Portal](qs-configure-portal-windows-vmss.md).


