---
title: Zarządzanie usługą Azure Cloud Services przy użyciu Azure Automation | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure Automation może być używana do zarządzania usługami w chmurze platformy Azure na dużą skalę.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439077"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Zarządzanie usługą Azure Cloud Services przy użyciu Azure Automation
W tym przewodniku zawarto informacje o usłudze Azure Automation i sposobach ich użycia do uproszczenia zarządzania usługami w chmurze platformy Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure, która upraszcza zarządzanie chmurą poprzez automatyzację procesów. Korzystanie z Azure Automation, długotrwałych, ręcznych, podatnych na błędy i często powtarzanych zadań może być zautomatyzowane, aby zwiększyć niezawodność, wydajność i czas do osiągnięcia korzyści dla organizacji.

Azure Automation zapewnia wysoce niezawodny i wysoce dostępny aparat wykonywania przepływu pracy, który skaluje się do potrzeb użytkownika w miarę rozwoju organizacji. W Azure Automation procesy mogą być uruchamiane ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania będą wykonywane dokładnie w razie potrzeby.

Obniż koszty operacyjne i zwolnij personel IT/DevOps, aby skoncentrować się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak można Azure Automation pomóc w zarządzaniu usługami Azure Cloud Services?
Usługi Azure Cloud Services mogą być zarządzane w Azure Automation przy użyciu poleceń cmdlet programu PowerShell dostępnych w [narzędziach Azure PowerShell](/powershell/). Azure Automation dostępne są następujące polecenia cmdlet programu PowerShell dla usługi w chmurze, dzięki którym można wykonywać wszystkie zadania zarządzania usługą w chmurze w ramach usługi. Możesz również sparować te polecenia cmdlet w Azure Automation z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w ramach usług platformy Azure i systemów innych firm.

Oto przykładowe zastosowania Azure Automation do zarządzania usługą Azure Cloud Services obejmują:

* [Ciągłe wdrażanie usługi w chmurze po każdej aktualizacji pliku cscfg lub cspkg w usłudze Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Ponowne uruchamianie wystąpień usługi w chmurze równolegle, jedna domena uaktualnienia w danym momencie](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Azure Automation i jak można jej użyć do zarządzania usługami w chmurze Azure, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o Azure Automation.

* [Przegląd Azure Automation](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
