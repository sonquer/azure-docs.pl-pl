---
title: Av2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Av2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1e14c633ed11bb628a68756e72e78073603643a1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493854"
---
# <a name="av2-series"></a>Seria Av2

Maszyny wirtualne z serii Av2 można wdrażać na różnych typach sprzętu i procesorach. Maszyny wirtualne z serii Av2 mają wydajność procesora CPU i konfiguracje pamięci, które najlepiej pasują do obciążeń poziomu wejścia, takich jak programowanie i testowanie. Rozmiar jest ograniczany w celu oferowania spójnej wydajności procesora dla uruchomionego wystąpienia, niezależnie od sprzętu, na którym jest wdrożony. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej. Przykładowe przypadki użycia obejmują serwery deweloperskie i testowe, serwery sieci Web o małym ruchu, małe i średnie bazy danych, sprawdzanie poprawności koncepcji i repozytoria kodu.

ACU: 100

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standardowa_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standardowa_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standardowa_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standardowa_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standardowa_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standardowa_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standardowa_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.