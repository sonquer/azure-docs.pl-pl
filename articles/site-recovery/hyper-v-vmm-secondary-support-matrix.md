---
title: Obsługa odzyskiwania po awarii macierzy z funkcją Hyper-V w dodatkowej lokacji programu VMM z Azure Site Recovery
description: Podsumowuje obsługę replikacji maszyny wirtualnej funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej z Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132962"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w lokacji dodatkowej

W tym artykule opisano, co jest obsługiwane w przypadku używania usługi [Azure Site Recovery](site-recovery-overview.md) do replikowania maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach System Center Virtual Machine Manager (VMM) do lokacji dodatkowej. Jeśli chcesz replikować maszyny wirtualne funkcji Hyper-V do platformy Azure, zapoznaj się z [tą matrycą obsługi](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikację można przeprowadzić tylko w lokacji dodatkowej, gdy hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.


## <a name="host-servers"></a>Serwery hosta

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2012 R2 | Na serwerach musi być uruchomionych najnowszych aktualizacji.
Windows Server 2016 |  Chmury programu VMM 2016 z mieszaniną hostów z systemami Windows Server 2016 i 2012 R2 nie są obecnie obsługiwane.<br/><br/> Wdrożenia uaktualnione z programu System Center 2012 R2 VMM 2012 R2 do programu System Center 2016 nie są obecnie obsługiwane.


## <a name="replicated-vm-support"></a>Obsługa zreplikowanej maszyny wirtualnej

Poniższa tabela zawiera podsumowanie obsługi systemu operacyjnego dla maszyn replikowanych za pomocą Site Recovery. Każde obciążenie może być uruchomione w obsługiwanym systemie operacyjnym.

**Wersja systemu Windows** | **Funkcja Hyper-V (z programem VMM)**
--- | ---
Windows Server 2016 | Wszystkie systemy operacyjne gościa [obsługiwane przez funkcję Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) w systemie Windows Server 2016 
Windows Server 2012 R2 | Wszystkie systemy operacyjne gościa [obsługiwane przez funkcję Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) w systemie Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Magazyn maszyny z systemem Linux

Można replikować tylko maszyny z systemem Linux z następującym magazynem:

- System plików (EXT3, ETX4, ReiserFS, XFS).
- Wielościeżkowe oprogramowanie — mapowanie urządzeń.
- Menedżer woluminów (LVM2).
- Serwery fizyczne z magazynem w usłudze HP CCISS Controller nie są obsługiwane.
- System plików ReiserFS jest obsługiwany tylko w systemie SUSE Linux Enterprise Server 11 z dodatkiem SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfiguracja sieci-Host/maszyna wirtualna gościa

**Konfiguracja** | **Obsługiwane**  
--- | --- 
Hostowanie — Tworzenie zespołu kart interfejsu sieciowego | Yes 
Host-sieć VLAN | Yes 
Host-IPv4 | Yes 
Host-IPv6 | Nie 
Maszyna wirtualna gościa — Tworzenie zespołu kart interfejsu sieciowego | Nie
Maszyna wirtualna gościa — IPv4 | Yes
Maszyna wirtualna gościa — IPv6 | Nie
Maszyna wirtualna gościa — system Windows/Linux — statyczny adres IP | Yes
Maszyna wirtualna gościa — wiele kart sieciowych | Yes


## <a name="storage"></a>Magazyn

### <a name="host-storage"></a>Magazyn hosta

**Magazyn (Host)** | **Obsługiwane**
--- | --- 
NFS | Nie dotyczy
SMB 3.0 |  Yes
SAN (ISCSI) | Yes
Wiele ścieżek (MPIO) | Yes

### <a name="guest-or-physical-server-storage"></a>Magazyn Gości lub serwer fizyczny

**Konfiguracja** | **Obsługiwane**
--- | --- | 
VMDK |  Nie dotyczy
VHD/VHDX | Tak (do 16 dysków)
Maszyna wirtualna generacji 2 | Yes
Udostępniony dysk klastra | Nie
Zaszyfrowany dysk | Nie
UEFI| Nie dotyczy
NFS | Nie
SMB 3.0 | Nie
RDM | Nie dotyczy
Dysk > 1 TB | Yes
Wolumin z dyskiem rozłożonym > 1 TB<br/><br/> LVM | Yes
Miejsca do magazynowania | Yes
Gorące Dodawanie/usuwanie dysku | Nie
Wykluczanie dysku | Yes
Wiele ścieżek (MPIO) | Yes

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane**
--- | --- 
Przenoszenie magazynów między grupami zasobów (w ramach subskrypcji lub między subskrypcjami) |  Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach subskrypcji lub między subskrypcjami) | Nie

## <a name="azure-site-recovery-provider"></a>Dostawca Azure Site Recovery

Dostawca koordynuje komunikację między serwerami programu VMM. 

**Ostatnia** | **Aktualizacje**
--- | --- 
5.1.19 ([dostępne z portalu](https://aka.ms/downloaddra) | [Najnowsze funkcje i poprawki](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej](tutorial-vmm-to-vmm.md)

