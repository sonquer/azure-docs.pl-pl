---
title: Wymagania dotyczące usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Zrozumienie wymagań sprzętu i oprogramowania dla usługi Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978470"
---
# <a name="azure-importexport-system-requirements"></a>Wymagania dotyczące systemu Azure Import/Export

W tym artykule opisano ważne wymagania dotyczące usługi Azure Import/Export. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed korzystania z usługi Import/Export, a następnie wrócić do niego zgodnie z potrzebami podczas operacji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby przygotować dyski twarde, za pomocą narzędzia WAImportExport, następujące **64-bitowego systemu operacyjnego, który obsługuje szyfrowanie dysków funkcją BitLocker** są obsługiwane.


|Platforma |Wersja |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8.1 Pro, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Inne wymagane oprogramowanie klienta Windows

|Platforma |Wersja |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

Usługa Azure Import/Export obsługuje następujące typy kont magazynu:

- Standardowe konta magazynu Ogólnego przeznaczenia v2 (zalecane w przypadku większości scenariuszy)
- Konta usługi Blob Storage
- Konta magazynu Ogólnego przeznaczenia V1 (zarówno w przypadku wdrożeń klasycznych, jak i Azure Resource Manager),

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [omówienie kont magazynu platformy Azure](storage-account-overview.md).

Każde zadanie może służyć do przesyłania danych do lub z tylko jednego konta magazynu. Innymi słowy zadanie importu/eksportu pojedynczej nie mogą rozciągać się na wielu kontach magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [sposób tworzenia konta magazynu](storage-account-create.md).

> [!IMPORTANT]
> Usługa Azure Import/Eksport obsługują kont magazynu, gdzie [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) funkcja została włączona. 

## <a name="supported-storage-types"></a>Obsługiwane typy

Poniższa lista typów magazynu jest obsługiwana przy użyciu usługi Azure Import/Export.


|Zadanie  |Usługa Storage |Obsługiwane  |Brak obsługi  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Usługa Azure File storage       | Blokowe obiekty BLOB i stronicowe obiekty BLOB, obsługiwane <br><br> Obsługiwane pliki          |
|Eksportuj     |   Azure Blob Storage       | Blokowe obiekty BLOB, stronicowe obiekty BLOB i obiekty BLOB dołączania obsługiwane         | Usługa pliki systemu Azure nie jest obsługiwane


## <a name="supported-hardware"></a>Obsługiwane usługi sprzętowego

Dla usługi Azure Import/Export potrzebne są obsługiwane dyski do kopiowania danych.

### <a name="supported-disks"></a>Obsługiwane dyski

Poniższa lista dysków jest obsługiwane do użytku z usługi Import/Export.


|Typ dysku  |Rozmiar  |Obsługiwane |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3,5"       |SATA II SATA III         |

Następujące typy dysków nie są obsługiwane:
- USBs.
- Zewnętrzny dysk twardy z wbudowaną adapterem USB.
- Dyski, które znajdują się w obudowie zewnętrznego dysku twardego.

Zadania importu/eksportu pojedynczego może mieć:
- Maksymalnie 10 HDD/SSD.
- Kombinacja HDD/SSD o dowolnym rozmiarze.

Duża liczba dysków mogły być rozkładane na wiele zadań i nie ma nieograniczoną liczbę zadań, które mogą być tworzone. Zadań importu jest przetwarzany tylko pierwszy wolumin danych na dysku. Ilość danych muszą być sformatowane jako NTFS.

Podczas przygotowywania dysków twardych i kopiowanie danych przy użyciu narzędzia WAImportExport, można użyć zewnętrznej karty USB. Większość standardowych USB 3.0 lub nowszej adapterów powinny działać.


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST wyeksportować importu Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
