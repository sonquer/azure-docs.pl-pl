---
title: NVv4 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii NVv4.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 82720a99128facee2d1617d69f1ed852b232de43
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493282"
---
# <a name="nvv4-series-preview"></a>NVv4 — seria (wersja zapoznawcza)

Maszyny wirtualne z serii NVv4 są obsługiwane przez procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU i AMD EPYC 7V12 (Rzym). Dzięki serii NVv4 platforma Azure wprowadza maszyny wirtualne ze częściowym procesorami GPU. Wybierz maszynę wirtualną o odpowiednim rozmiarze dla przyspieszanych przez procesor GPU aplikacji graficznych i pulpitów wirtualnych, rozpoczynając od 1/8. procesora GPU z 2 GiB bufora klatek do pełnego procesora GPU z 16 GiB buforem ramek. NVv4 maszyny wirtualne obecnie obsługują tylko system operacyjny gościa systemu Windows.

[Utwórz konto i uzyskaj dostęp do tych maszyn w trakcie okresu zapoznawczego](https://aka.ms/nvv4signup).
<br>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> maszyny wirtualne z serii NVv4 — technologia wielowątkowego rozwiązania AMD

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N z systemem Windows, należy zainstalować sterowniki NVIDIA lub AMD GPU.

[Rozszerzenie sterownika NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](/extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](/extensions/overview.md).

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem Konfiguracja [sterownika procesora GPU dla systemu Windows](/windows/n-series-driver-setup.md) lub [n-Series](/linux/n-series-driver-setup) w systemie Linux dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

Aby ręcznie zainstalować sterowniki procesora GPU AMD, zobacz [Instalator sterowników procesora GPU AMD dla systemu Windows](/windows/n-series-driver-setup.md) w przypadku obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.