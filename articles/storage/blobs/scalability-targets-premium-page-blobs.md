---
title: Elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium
titleSuffix: Azure Storage
description: Konto usługi BLOB Storage na stronie wydajności Premium jest zoptymalizowane pod kątem operacji odczytu i zapisu. Ten typ konta magazynu wykonuje kopię zapasową dysku niezarządzanego dla maszyny wirtualnej platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756253"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Cele skalowalności i wydajności dla kont usługi BLOB Storage na stronie Premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Skalowanie obiektów docelowych dla kont obiektów BLOB na stronie Premium

Konto usługi BLOB Storage na stronie wydajności w warstwie Premium jest zoptymalizowane pod kątem operacji odczytu i zapisu. Ten typ konta magazynu wykonuje kopię zapasową dysku niezarządzanego dla maszyny wirtualnej platformy Azure.

> [!NOTE]
> Firma Microsoft zaleca używanie dysków zarządzanych z maszynami wirtualnymi platformy Azure, jeśli jest to możliwe. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Azure Disk Storage przegląd dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/managed-disks-overview.md).

Dla kont magazynu obiektów BLOB na stronie Premium są dostępne następujące elementy docelowe skalowalności:

| Łączna pojemność konta                            | Łączna przepustowość dla lokalnego nadmiarowego konta magazynu                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Pojemność dysku: 4 TB (pojedynczy dysk)/35 TB (łączna liczba wszystkich dysków) <br>Pojemność migawki: 10 TB | Do 50 Gigabit na sekundę dla ruchu przychodzącego<sup>1</sup> + wychodzącego<sup>2</sup> |

<sup>1</sup> wszystkie dane (żądania) wysyłane do konta magazynu

<sup>2</sup> wszystkie dane (odpowiedzi) odbierane z konta magazynu

Konto obiektu BLOB na stronie Premium jest kontem ogólnego przeznaczenia skonfigurowanym na potrzeby wydajności w warstwie Premium. Zalecane jest używanie kont magazynu ogólnego przeznaczenia w wersji 2.

Jeśli używasz kont usługi BLOB Storage w warstwie Premium dla dysków niezarządzanych, a aplikacja przekroczy cele skalowalności jednego konta magazynu, firma Microsoft zaleca migrację do usługi Managed Disks. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Azure Disk Storage przegląd dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/managed-disks-overview.md) lub [Azure Disk Storage przegląd dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/managed-disks-overview.md).

Jeśli nie można przeprowadzić migracji do dysków zarządzanych, skompiluj aplikację tak, aby korzystała z wielu kont magazynu, i Podziel swoje dane na te konta magazynu. Jeśli na przykład chcesz dołączyć dyski o pojemności 51 TB na wiele maszyn wirtualnych, rozłożyć je na dwa konta magazynu. 35 TB to limit dla pojedynczego konta magazynu w warstwie Premium. Upewnij się, że pojedyncze konto magazynu wydajności Premium nigdy nie ma więcej niż 35 TB dysków z zainicjowaną obsługą.

## <a name="see-also"></a>Zobacz także

- [Cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md)
- [Cele skalowalności dla kont magazynu blokowych obiektów BLOB w warstwie Premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Ograniczenia i limity subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
