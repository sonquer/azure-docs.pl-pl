---
title: Delegowany dostęp w programie Virtual Desktop systemu Windows — Azure
description: Jak delegować możliwości administracyjne w ramach wdrożenia pulpitu wirtualnego systemu Windows, łącznie z przykładami.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 849a3b1eb1a4648c2eaae7dd564f14b9c511fdcf
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367351"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Dostęp delegowany w usłudze Windows Virtual Desktop

Pulpit wirtualny systemu Windows ma model dostępu delegowanego, który umożliwia zdefiniowanie dostępu określonego użytkownika przez przypisanie ich do roli. Przypisanie roli ma trzy składniki: podmiot zabezpieczeń, definicja roli i zakres. Model dostępu delegowanego pulpitu wirtualnego systemu Windows jest oparty na modelu RBAC platformy Azure. Aby dowiedzieć się więcej o konkretnych przypisaniach ról i ich składnikach, zobacz [Omówienie kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md).

Dostęp delegowany pulpitu wirtualnego systemu Windows obsługuje następujące wartości dla każdego elementu przypisania roli:

* Podmiot zabezpieczeń
    * Użytkownicy
    * Nazwy główne usług
* Definicja roli
    * Wbudowane role
* Zakres
    * Grupy dzierżaw
    * Dzierżawcy
    * Pule hostów
    * Grupy aplikacji

## <a name="built-in-roles"></a>Wbudowane role

Delegowany dostęp w programie Virtual Desktop systemu Windows ma kilka wbudowanych definicji ról, które można przypisać do użytkowników i jednostek usługi.

* Właściciel pulpitu zdalnego może zarządzać wszystkimi, w tym dostępem do zasobów.
* Współautor usług pulpitu zdalnego może zarządzać wszystko, ale dostęp do zasobów.
* Czytnik usług pulpitu zdalnego może wyświetlać wszystko, ale nie może wprowadzać żadnych zmian.
* Operator RDS może wyświetlać działania diagnostyczne.

## <a name="powershell-cmdlets-for-role-assignments"></a>Polecenia cmdlet programu PowerShell dla przypisań ról

Poniższe polecenia cmdlet umożliwiają tworzenie, wyświetlanie i usuwanie przypisań ról:

* **Get-RdsRoleAssignment** wyświetla listę przypisań ról.
* **New-RdsRoleAssignment** tworzy nowe przypisanie roli.
* **Remove-RdsRoleAssignment** usuwa przypisania ról.

### <a name="accepted-parameters"></a>Akceptowane parametry

Można modyfikować podstawowe trzy polecenia cmdlet o następujących parametrach:

* **AadTenantId**: Określa identyfikator dzierżawy Azure Active Directory, z której jest członkiem jednostki usługi.
* **AppGroupName**: Nazwa grupy aplikacji Pulpit zdalny.
* **Diagnostyka**: wskazuje zakres diagnostyki. (Musi być sparowany z parametrami **infrastruktury** lub **dzierżawy** ).
* **HostPoolName**: Nazwa puli hostów pulpit zdalny.
* **Infrastruktura**: wskazuje zakres infrastruktury.
* **RoleDefinitionName**: Nazwa roli kontroli dostępu opartej na rolach usługi pulpitu zdalnego przypisanej do użytkownika, grupy lub aplikacji. (Na przykład Usługi pulpitu zdalnego Owner, Usługi pulpitu zdalnego Reader itd.)
* **ServerPrincipleName**: nazwa aplikacji Azure Active Directory.
* **SignInName**: adres e-mail użytkownika lub główna nazwa użytkownika.
* **Dzierżawca**: Nazwa dzierżawy pulpit zdalny.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z bardziej kompletną listą poleceń cmdlet programu PowerShell, które mogą być używane przez poszczególne role, zobacz [informacje dotyczące programu PowerShell](/powershell/windows-virtual-desktop/overview).

Aby uzyskać wskazówki dotyczące sposobu konfigurowania środowiska pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
