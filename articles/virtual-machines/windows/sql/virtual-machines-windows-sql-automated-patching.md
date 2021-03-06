---
title: Automatyczne stosowanie poprawek dla maszyn wirtualnych SQL Server (Menedżer zasobów) | Microsoft Docs
description: W tym artykule wyjaśniono funkcję zautomatyzowanego stosowania poprawek dla SQL Server Virtual Machines działających na platformie Azure przy użyciu Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b48ccede9ca3330d356fa75d4df34789e31eb916
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350742"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatyczne stosowanie poprawek dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatyczne stosowanie poprawek ustanawia okno obsługi dla maszyny wirtualnej platformy Azure z systemem SQL Server. Automatyczne aktualizacje można instalować tylko w tym oknie konserwacji. W przypadku programu SQL Server to ograniczenie gwarantuje, że aktualizacje systemu i wszystkie związane z nimi ponowne uruchomienia będą występować w czasie najlepszym dla bazy danych. 

> [!IMPORTANT]
> Instalowane są tylko aktualizacje systemu Windows i SQL Server oznaczone jako **Ważne** . Pozostałe aktualizacje programu SQL Server, na przykład aktualizacje zbiorcze, należy instalować ręcznie. 

Automatyczne stosowanie poprawek zależy od [rozszerzenia agenta IaaS w programie SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby używać zautomatyzowanej poprawki, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia Azure PowerShell](/powershell/azure/overview) , jeśli planujesz skonfigurować automatyczne stosowanie poprawek za pomocą programu PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatyczne stosowanie poprawek polega na rozszerzeniu SQL Server IaaS Agent. Bieżące obrazy galerii maszyn wirtualnych SQL Domyślnie Dodaj to rozszerzenie. Aby uzyskać więcej informacji, zobacz [SQL Server rozszerzenia agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego stosowania poprawek. Rzeczywiste czynności konfiguracyjne różnią się w zależności od tego, czy używasz poleceń Azure Portal lub Azure Windows PowerShell.

| Ustawienie | Możliwe wartości | Opis |
| --- | --- | --- |
| **Automatyczne stosowanie poprawek** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczne stosowanie poprawek dla maszyny wirtualnej platformy Azure. |
| **Harmonogram konserwacji** |Codzienne, poniedziałek, wtorek, środa, czwartek, piątek, Sobota, niedziela |Harmonogram pobierania i instalowania aktualizacji systemu Windows, SQL Server i Microsoft dla maszyny wirtualnej. |
| **Godzina rozpoczęcia konserwacji** |0-24 |Godzina rozpoczęcia aktualizowania maszyny wirtualnej. |
| **Czas trwania okna obsługi** |30-180 |Liczba minut, przez jaką można ukończyć pobieranie i instalowanie aktualizacji. |
| **Kategoria poprawek** |Ważne | Kategoria aktualizacji systemu Windows do pobrania i zainstalowania.|

## <a name="configuration-in-the-portal"></a>Konfiguracja w portalu
Za pomocą Azure Portal można skonfigurować automatyczne stosowanie poprawek podczas aprowizacji lub istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
Użyj Azure Portal, aby skonfigurować automatyczne stosowanie poprawek podczas tworzenia nowej SQL Server maszyny wirtualnej w modelu wdrażania Menedżer zasobów.

Na karcie **ustawienia SQL Server** wybierz pozycję **Zmień konfigurację** w obszarze **zautomatyzowane stosowanie poprawek**. Poniższy zrzut ekranu Azure Portal przedstawia blok **zautomatyzowanego stosowania poprawek SQL** .

![Automatyczne stosowanie poprawek SQL w Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

W przypadku kontekstu zapoznaj się z tematem kompletny temat dotyczący [inicjowania obsługi SQL Server maszyny wirtualnej na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących SQL Server maszyn wirtualnych Otwórz [zasób usługi SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) i wybierz pozycję **poprawka** w obszarze **Ustawienia**. 

![Automatyczne stosowanie poprawek SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Po zakończeniu kliknij przycisk **OK** w dolnej części bloku **Konfiguracja SQL Server** , aby zapisać zmiany.

Jeśli po raz pierwszy włączysz automatyczne stosowanie poprawek, platforma Azure skonfiguruje SQL Server agenta IaaS w tle. W tym czasie Azure Portal mogą nie być wyświetlane, że jest skonfigurowane automatyczne stosowanie poprawek. Poczekaj kilka minut, aż zostanie zainstalowany agent, który został skonfigurowany. Po tym, Azure Portal odzwierciedla nowe ustawienia.

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
Po zainicjowaniu obsługi administracyjnej maszyny wirtualnej SQL użyj programu PowerShell, aby skonfigurować automatyczne stosowanie poprawek.

W poniższym przykładzie program PowerShell służy do konfigurowania automatycznej poprawki na istniejącej maszynie wirtualnej SQL Server. Polecenie **New-AzVMSqlServerAutoPatchingConfig** konfiguruje nowe okno obsługi dla aktualizacji automatycznych.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Jeśli rozszerzenie nie zostało jeszcze zainstalowane, zainstalowanie rozszerzenia spowoduje ponowne uruchomienie usługi SQL Server.

Na podstawie tego przykładu w poniższej tabeli opisano praktyczny wpływ na docelową maszynę wirtualną platformy Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Poprawki są instalowane w każdym czwartek. |
| **MaintenanceWindowStartingHour** |Rozpocznij aktualizacje o 11:10:00. |
| **MaintenanceWindowsDuration** |Poprawki muszą być zainstalowane w ciągu 120 minut. Na podstawie czasu rozpoczęcia muszą one zostać wykonane przez 1:13:00. |
| **PatchCategory** |Jedynym możliwym ustawieniem dla tego parametru jest **Ważne**. Spowoduje to zainstalowanie aktualizacji Windows Update oznaczonej jako ważne; nie są instalowane żadne SQL Server aktualizacje, które nie są uwzględnione w tej kategorii. |

Zainstalowanie i skonfigurowanie agenta SQL Server IaaS może potrwać kilka minut.

Aby wyłączyć automatyczne stosowanie poprawek, Uruchom ten sam skrypt bez parametru **-enable** dla **New-AzVMSqlServerAutoPatchingConfig**. Brak parametru **-enable** sygnalizuje polecenie, aby wyłączyć tę funkcję.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [SQL Server rozszerzenia agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

