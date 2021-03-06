---
title: Powiadomienie o wycofaniu rodziny systemów operacyjnych gościa 1 | Microsoft Docs
description: Zawiera informacje o tym, kiedy wystąpiło wycofanie rodziny systemów operacyjnych gościa platformy Azure 1 i jak ustalić, czy ma to wpływ
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359585"
---
# <a name="guest-os-family-1-retirement-notice"></a>Powiadomienie o wycofaniu rodziny systemów operacyjnych gościa 1
Wycofanie rodziny systemów operacyjnych 1 zostało po raz pierwszy ogłoszone 1 czerwca 2013.

**Września 2, 2014** Rodzina systemów operacyjnych gościa platformy Azure (system operacyjny gościa) 1. x, która jest oparta na systemie operacyjnym Windows Server 2008, została oficjalnie wycofana. Wszystkie próby wdrożenia nowych usług lub uaktualnienia istniejących usług przy użyciu rodziny 1 zakończą się niepowodzeniem z komunikatem o błędzie z informacją o tym, że Rodzina systemów operacyjnych gościa 1 została wycofana.

**3 listopada 2014** Zakończono rozszerzoną obsługę rodziny systemów operacyjnych gościa 1 i została ona całkowicie wycofana. Będzie to miało wpływ na wszystkie usługi. Firma Microsoft może zatrzymać te usługi w dowolnym momencie. Nie ma gwarancji, że usługi będą nadal działać, dopóki nie zostaną ręcznie uaktualnione.

Jeśli masz dodatkowe pytania, odwiedź [fora Cloud Services](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Czy chcesz to mieć na pewno?
Na Cloud Services mają zastosowanie następujące czynności:

1. Istnieje wartość "Rodzina systemów operacyjnych =" 1 "jawnie określona w pliku ServiceConfiguration. cscfg dla usługi w chmurze.
2. Nie istnieje wartość Rodzina systemów operacyjnych jawnie określona w pliku ServiceConfiguration. cscfg dla usługi w chmurze. Obecnie w tym przypadku system używa wartości domyślnej "1".
3. W Azure Portal przedstawiono wartość rodziny systemów operacyjnych gościa jako "Windows Server 2008".

Aby dowiedzieć się, które usługi w chmurze działają z rodziną systemów operacyjnych, można uruchomić następujący skrypt w Azure PowerShell, chociaż należy najpierw [skonfigurować Azure PowerShell](/powershell/azureps-cmdlets-docs) . Aby uzyskać więcej informacji na temat skryptu, [Zobacz Rodzina systemów operacyjnych gościa platformy Azure 1 koniec cyklu życia: Czerwiec 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Na usługi w chmurze będzie miało wpływ 1 Rodzina systemu operacyjnego, jeśli kolumna Rodzina systemów operacyjnych w danych wyjściowych skryptu jest pusta lub zawiera wartość "1".

## <a name="recommendations-if-you-are-affected"></a>Zalecenia w przypadku, gdy ma to dotyczyć
Zalecamy Migrowanie ról usługi w chmurze do jednej z obsługiwanych rodzin systemów operacyjnych gościa:

**Rodzina systemów operacyjnych Gości 4. x** -Windows Server 2012 R2 *(zalecane)*

1. Upewnij się, że aplikacja korzysta z zestawu SDK 2,1 lub nowszego z programem .NET Framework 4,0, 4,5 lub 4.5.1.
2. Ustaw atrybut Rodzina systemów operacyjnych na wartość "4" w pliku ServiceConfiguration. cscfg i ponownie Wdróż usługę w chmurze.

**Rodzina systemów operacyjnych gościa 3. x** — Windows Server 2012

1. Upewnij się, że aplikacja korzysta z zestawu SDK 1,8 lub nowszego w programie .NET Framework 4,0 lub 4,5.
2. Ustaw atrybut Rodzina systemów operacyjnych na wartość "3" w pliku ServiceConfiguration. cscfg i ponownie Wdróż usługę w chmurze.

**Rodzina systemów operacyjnych gościa 2. x** — Windows Server 2008 R2

1. Upewnij się, że aplikacja korzysta z zestawu SDK 1,3 i nowszego w programie .NET Framework 3,5 lub 4,0.
2. Ustaw atrybut Rodzina systemów operacyjnych na wartość "2" w pliku ServiceConfiguration. cscfg i ponownie Wdróż usługę w chmurze.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozszerzona pomoc techniczna dla rodziny systemów operacyjnych gościa 1 — LIS 3, 2014
Usługi w chmurze w rodzinie systemów operacyjnych gościa 1 nie są już obsługiwane. Aby uniknąć przerw w działaniu usługi, należy przeprowadzić migrację z rodziny 1.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z najnowszymi [wersjami systemów operacyjnych gościa](cloud-services-guestos-update-matrix.md).
