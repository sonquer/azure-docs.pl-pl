---
title: Przyznawanie uprawnień użytkownikom do określonych zasad laboratorium | Microsoft Docs
description: Dowiedz się, jak udzielić użytkownikom uprawnień do określonych zasad laboratorium w DevTest Labs na podstawie potrzeb poszczególnych użytkowników
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028437"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Przyznawanie uprawnień użytkownikom do określonych zasad laboratorium
## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób użycia programu PowerShell do przyznawania użytkownikom uprawnień do określonych zasad laboratorium. W ten sposób uprawnienia mogą być stosowane w zależności od potrzeb poszczególnych użytkowników. Na przykład możesz chcieć udzielić określonemu użytkownikowi możliwości zmiany ustawień zasad maszyny wirtualnej, ale nie zasad kosztów.

## <a name="policies-as-resources"></a>Zasady jako zasoby
Zgodnie z opisem w artykule [Access Control opartym na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md) kontrola RBAC umożliwia precyzyjne zarządzanie dostępem do zasobów na platformie Azure. Korzystając z funkcji RBAC, można oddzielić cła w zespole DevOps i przyznać dostęp tylko do użytkowników, których potrzebują do wykonywania swoich zadań.

W DevTest Labs zasady to typ zasobu, który umożliwia działanie RBAC akcji **Microsoft. wspólny/Labs/policySets/Policy/** . Każda zasada laboratorium jest zasobem w typie zasobu zasad i może być przypisana jako zakres do roli RBAC.

Na przykład, aby przyznać użytkownikom uprawnienia do odczytu/zapisu dla **dozwolonych rozmiarów maszyn wirtualnych** , należy utworzyć rolę niestandardową, która współpracuje z **pakietem Microsoft. wspólny/Labs/policySets/** Policy/Action, a następnie przypisać odpowiednich użytkowników do programu Ta rola niestandardowa w zakresie **Microsoft. wspólny/Labs/policySets/policies/AllowedVmSizesInLab**.

Aby dowiedzieć się więcej na temat ról niestandardowych w RBAC, zobacz [Kontrola dostępu ról niestandardowych](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tworzenie roli niestandardowej laboratorium przy użyciu programu PowerShell
Aby rozpocząć, musisz [zainstalować Azure PowerShell](/powershell/azure/install-az-ps). 

Po skonfigurowaniu poleceń cmdlet Azure PowerShell można wykonać następujące zadania:

* Wyświetl listę wszystkich operacji/akcji dla dostawcy zasobów
* Wyświetl listę akcji w określonej roli:
* Tworzenie roli niestandardowej

Poniższy skrypt programu PowerShell ilustruje przykłady wykonywania następujących zadań:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Przypisywanie uprawnień użytkownikowi dla określonych zasad przy użyciu ról niestandardowych
Po zdefiniowaniu ról niestandardowych można przypisać je do użytkowników. Aby przypisać rolę niestandardową użytkownikowi, należy najpierw uzyskać identyfikator **objectid** reprezentujący tego użytkownika. W tym celu należy użyć polecenia cmdlet **Get-AzADUser** .

W poniższym przykładzie identyfikator **objectid** użytkownika *SomeUser* to 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Po utworzeniu **identyfikatora** obiektu użytkownika i niestandardowej nazwy roli można przypisać tę rolę użytkownikowi przy użyciu polecenia cmdlet **New-AzRoleAssignment** :

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

W poprzednim przykładzie używane są zasady **AllowedVmSizesInLab** . Można użyć dowolnej z następujących zasad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po udzieleniu uprawnień użytkownika do określonych zasad laboratorium należy wziąć pod uwagę następujące czynności:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratorium](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

