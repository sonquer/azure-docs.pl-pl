---
title: Rozmiary maszyn wirtualnych platformy Azure — HPC | Microsoft Docs
description: Wyświetla listę różnych rozmiarów dostępnych w przypadku maszyn wirtualnych o wysokiej wydajności obliczeniowych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493555"
---
# <a name="high-performance-compute-vm-sizes"></a>Rozmiary maszyn wirtualnych o wysokiej wydajności

Maszyny wirtualne z serii H platformy Azure zaprojektowano w celu zapewnienia wydajności klasy lidera, skalowalności MPI i efektywności kosztów dla różnych rzeczywistych obciążeń platformy HPC.

[Seria HBv2](hbv2-series.md) Funkcja maszyny wirtualne 200 GB/s — karta Mellanox HDR InfiniBand, podczas gdy maszyna wirtualna z 100 serii GB/s Mellanox EDR InfiniBand. Każdy z tych typów maszyn wirtualnych jest podłączony do nieblokującego drzewa FAT w celu zoptymalizowanej i spójnej wydajności RDMA. Maszyny wirtualne HBv2 obsługują Routing adaptacyjny oraz transport podłączony dynamicznie (DCT) w dodatkowych standardach do standardowych wersji RC i UD Transports. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

[HB — seria](hb-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, jawna, skończona analiza elementów i modelowanie pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma AMD EPYC zapewnia ponad 260 GB/s przepustowości pamięci.

[Seria HC](hc-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na gęstym obliczaniu, takich jak niejawne ograniczone analizy elementów, biocząsteczkowa i obliczeniowa. Funkcja maszyn wirtualnych HC 44 rdzeni procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma Intel Xeon Platinum obsługuje bogate ekosystemy narzędzi programistycznych firmy Intel, takich jak biblioteka jądra matematycznych firmy Intel.

[Seria H](h-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji z wysoką częstotliwością procesora CPU lub dużą ilością pamięci na podstawowe wymagania. Maszyny wirtualne serii H funkcja 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, 7 lub 14 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Funkcja serii H 56 GB/s karta Mellanox FDR InfiniBand w nieblokującej konfiguracji drzewa FAT dla spójnej wydajności RDMA. Maszyny wirtualne z serii H obsługują technologię Intel MPI 5. x i MS-MPI.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- **Subskrypcja platformy Azure** — aby wdrożyć więcej niż kilka wystąpień intensywnie korzystających z obliczeń, weź pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- **Cennik i dostępność** — te rozmiary maszyn wirtualnych są oferowane tylko w warstwie cenowej standardowa. Sprawdź dostępność [produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) w regionach świadczenia usługi Azure.
- **Przydział rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym dla serii H. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-supportability/how-to-create-azure-support-request.md) . (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).

  > [!NOTE]
  > Skontaktuj się z pomocą techniczną platformy Azure, jeśli potrzebujesz dużej pojemności. Przydziały platformy Azure to limity kredytowe, a nie gwarancje wydajności. Niezależnie od limitu przydziału opłata jest naliczana tylko za używane rdzenie.
  
- **Sieć wirtualna** — [Sieć wirtualna](https://azure.microsoft.com/documentation/services/virtual-network/) platformy Azure nie jest wymagana do korzystania z wystąpień intensywnie korzystających z obliczeń. Jednak w przypadku wielu wdrożeń potrzebna jest co najmniej oparta na chmurze usługa Azure Virtual Network lub połączenie lokacja-lokacja, jeśli trzeba uzyskać dostęp do zasobów lokalnych. W razie potrzeby utwórz nową sieć wirtualną, aby wdrożyć wystąpienia. Dodawanie maszyn wirtualnych intensywnie korzystających z obliczeń do sieci wirtualnej w grupie koligacji nie jest obsługiwane.
- Zmiana **rozmiaru** — ze względu na ich wyspecjalizowany sprzęt można zmienić tylko wystąpienia intensywnie korzystające z obliczeń w obrębie tej samej rodziny (serii H lub serii A). Na przykład można zmienić rozmiar maszyny wirtualnej serii H tylko z jednego rozmiaru serii H na inny. Ponadto nie jest obsługiwane Zmienianie rozmiaru z intensywnie korzystającej z wielkości obliczeniowej na rozmiar intensywnie korzystający z obliczeń.  
## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA

Podzestaw wystąpień intensywnie korzystających z obliczeń (A8, A9, H16r, H16mr, HB i HC) ma interfejs sieciowy do łączności z funkcją zdalnego bezpośredniego dostępu do pamięci (RDMA). Wybrane rozmiary serii N oznaczone przy użyciu opcji "r", takie jak konfiguracje NC24rs (NC24rs_v2 i NC24rs_v3), również obsługują funkcję RDMA. Ten interfejs jest poza standardowym interfejsem sieciowym platformy Azure dostępnym dla innych rozmiarów maszyn wirtualnych.

Ten interfejs umożliwia wystąpienia z obsługą funkcji RDMA, które komunikują się za pośrednictwem sieci InfiniBand (IB), działającą w stawkach EDR w przypadku HB, HC, FDR stawek dla maszyn wirtualnych z serii N, H16mr i RDMA i częstotliwości QDR dla maszyn wirtualnych A8 i A9. Te możliwości RDMA umożliwiają zwiększenie skalowalności i wydajności niektórych aplikacji MPI (Message Passing Interface). Aby uzyskać więcej informacji na temat szybkości, zobacz szczegóły w tabelach na tej stronie.

> [!NOTE]
> Na platformie Azure funkcja IP over IB jest obsługiwana tylko na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (SR-IOV dla InfiniBand, obecnie HB i HC). Funkcja RDMA przez IB jest obsługiwana dla wszystkich wystąpień z obsługą funkcji RDMA.
- **System operacyjny** — Windows Server 2016 na wszystkich powyższych maszynach wirtualnych serii HPC. System Windows Server 2012 R2, Windows Server 2012 są również obsługiwane na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (z tym wyjątkiem HB i HC).

- **MPI** — rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure (HB, HC) umożliwiają użycie niemal wszystkich wersji MPI, które mają być używane z Mellanox OFED.
W przypadku maszyn wirtualnych z obsługą funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikacji między wystąpieniami. W związku z tym obsługiwane są tylko programy Microsoft MPI (MS-MPI) 2012 R2 lub nowsze i Intel MPI 5. x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami usługi Azure RDMA.

- **Rozszerzenie maszyny wirtualnej InfiniBandDriverWindows** — na maszynach wirtualnych obsługujących funkcję RDMA Dodaj rozszerzenie InfiniBandDriverWindows, aby włączyć funkcję InfiniBand. To rozszerzenie maszyny wirtualnej systemu Windows instaluje sterowniki funkcji bezpośrednie sieci systemu Windows (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) na potrzeby łączności RDMA.
W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy pamiętać, że rozszerzenie maszyny wirtualnej HpcVmDrivers jest przestarzałe; nie zostanie ona zaktualizowana. Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej, można użyć poleceń cmdlet [Azure PowerShell](/powershell/azure/overview) . 

  Następujące polecenie instaluje najnowszą wersję 1,0 rozszerzenia InfiniBandDriverWindows na istniejącej maszynie wirtualnej obsługującej funkcję RDMA o nazwie *myVM* wdrożonej w grupie zasobów o nazwie Moja *zasobów* w regionie *zachodnie stany USA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternatywnie rozszerzenia maszyn wirtualnych można dołączać do szablonów Azure Resource Manager, aby ułatwić wdrażanie, z następującym elementem JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Następujące polecenie instaluje najnowszą wersję 1,0 rozszerzenia InfiniBandDriverWindows na wszystkich maszynach wirtualnych z obsługą funkcji RDMA w istniejącym zestawie skalowania maszyn wirtualnych o nazwie *myVMSS* wdrożoną w grupie zasobów o nazwie Moja *zasobów*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Możesz również korzystać z rozszerzeń dla maszyn wirtualnych wdrożonych w [klasycznym modelu wdrażania](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Przestrzeń adresów sieciowych RDMA** — sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchamiać aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.

## <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Platforma Azure oferuje kilka opcji tworzenia klastrów maszyn wirtualnych z systemem Windows HPC, które mogą komunikować się za pomocą sieci RDMA, w tym: 

- **Maszyny wirtualne** — Wdróż maszyny wirtualne z obsługą funkcji RDMA w tym samym zestawie dostępności (w przypadku korzystania z Azure Resource Manager modelu wdrażania). W przypadku korzystania z klasycznego modelu wdrażania należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

- **Zestawy skalowania maszyn wirtualnych** — w zestawie skalowania maszyn wirtualnych należy ograniczyć wdrożenie do pojedynczej grupy umieszczania. Na przykład w szablonie Menedżer zasobów ustaw właściwość `singlePlacementGroup` na `true`. 

- **MPI między maszynami wirtualnymi** — jeśli jest to wymagane między maszynami wirtualnymi, upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie dostępności lub na maszynie wirtualnej tego samego zestawu skalowania.

- **Azure CycleCloud** — Tworzenie klastra HPC w [usłudze Azure CycleCloud](/azure/cyclecloud/) w celu uruchamiania zadań MPI w węzłach systemu Windows.

- **Azure Batch** — utwórz pulę [Azure Batch](/azure/batch/) do uruchamiania obciążeń MPI w węzłach obliczeniowych systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpień obsługujących funkcję RDMA lub GPU w pulach usługi Batch](../batch/batch-pool-compute-intensive-sizes.md). Zobacz również projekt [stoczni usługi Batch](https://github.com/Azure/batch-shipyard) do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

- Pakiet **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) zawiera środowisko uruchomieniowe dla MS-MPI, które używa sieci usługi Azure RDMA w przypadku wdrożenia na maszynach wirtualnych z systemem Linux obsługujących funkcję RDMA. Na przykład wdrożenia, zobacz [Konfigurowanie klastra RDMA systemu Linux przy użyciu pakietu HPC Pack w celu uruchamiania aplikacji MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Aby wyświetlić listy kontrolne do używania wystąpień intensywnie korzystających z obliczeń z pakietem HPC Pack w systemie Windows Server, zobacz [Konfigurowanie klastra RDMA systemu Linux przy użyciu pakietu HPC Pack w celu uruchamiania aplikacji MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Aby korzystać z wystąpień intensywnie korzystających z obliczeń podczas uruchamiania aplikacji MPI przy użyciu Azure Batch, zobacz temat [Używanie zadań z wielu wystąpień w celu uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w programie Azure Batch](../batch/batch-mpi.md).

- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.