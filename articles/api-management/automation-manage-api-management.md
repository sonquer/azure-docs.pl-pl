---
title: Zarządzanie usługą Azure API Management przy użyciu Azure Automation
description: Dowiedz się, w jaki sposób usługa Azure Automation może być używana do zarządzania usługą Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072064"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Zarządzanie usługą Azure API Management przy użyciu Azure Automation
W tym przewodniku przedstawiono usługę Azure Automation i sposób jej użycia do uproszczenia zarządzania usługą Azure API Management.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure, która upraszcza zarządzanie chmurą poprzez automatyzację procesów. Za pomocą Azure Automation, ręcznie, powtarzane, długotrwałe i podatne na błędy zadania mogą być zautomatyzowane, aby zwiększyć niezawodność, wydajność i czas do osiągnięcia korzyści dla organizacji.

Azure Automation zapewnia wysoce niezawodny aparat wykonywania przepływów pracy o wysokiej dostępności, który skaluje się odpowiednio do potrzeb. W Azure Automation procesy mogą być uruchamiane ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania będą wykonywane dokładnie w razie potrzeby.

Zmniejsz koszty operacyjne i zwolnij ją i DevOps personel, aby skoncentrować się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Jak można Azure Automation pomóc w zarządzaniu usługą Azure API Management?
API Management można zarządzać w Azure Automation za pomocą [poleceń cmdlet programu Windows PowerShell dla interfejsu API usługi Azure API Management](https://docs.microsoft.com/powershell/module/az.apimanagement). W Azure Automation można napisać skrypty przepływu pracy programu PowerShell do wykonywania wielu zadań API Management za pomocą poleceń cmdlet. Możesz również sparować te polecenia cmdlet w Azure Automation z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w ramach usług platformy Azure i systemów innych firm.

Oto kilka przykładów użycia API Management z programem PowerShell:

* [Przykłady programu Azure PowerShell dla usługi API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Azure Automation i jak można jej użyć do zarządzania usługą Azure API Management, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zapoznaj się z [samouczkiem wprowadzenie](../automation/automation-first-runbook-graphical.md)Azure Automation.

