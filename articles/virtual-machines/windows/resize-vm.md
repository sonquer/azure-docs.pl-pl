---
title: Zmienianie rozmiaru maszyny wirtualnej z systemem Windows na platformie Azure
description: Zmień rozmiar maszyny wirtualnej używany przez maszynę wirtualną platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941720"
---
# <a name="resize-a-windows-vm"></a>Zmienianie rozmiaru maszyny wirtualnej z systemem Windows

W tym artykule pokazano, jak przenieść maszynę wirtualną do innego [rozmiaru maszyny wirtualnej](sizes.md).

Po utworzeniu maszyny wirtualnej można skalować ją w górę lub w dół, zmieniając rozmiar maszyny wirtualnej. W niektórych przypadkach należy najpierw cofnąć alokację maszyny wirtualnej. Może się tak zdarzyć, jeśli nowy rozmiar nie jest dostępny w klastrze sprzętowym hostującym maszynę wirtualną.

Jeśli maszyna wirtualna używa Premium Storage, upewnij się, że wybrano wersję **s** rozmiaru do uzyskania pomocy technicznej Premium Storage. Na przykład wybierz Standard_E4**s**_v3 zamiast Standard_E4_v3.

## <a name="use-the-portal"></a>Używanie portalu

1. Otwórz [Portalu Azure](https://portal.azure.com).
1. Otwórz stronę dla maszyny wirtualnej.
1. W menu po lewej stronie wybierz pozycję **rozmiar**.
1. Wybierz nowy rozmiar z listy dostępnych rozmiarów, a następnie wybierz pozycję **Zmień rozmiar**.


Jeśli maszyna wirtualna jest aktualnie uruchomiona, zmiana jej rozmiaru spowoduje jej ponowne uruchomienie. Zatrzymywanie maszyny wirtualnej może spowodować ujawnienie dodatkowych rozmiarów.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Użyj programu PowerShell, aby zmienić rozmiar maszyny wirtualnej, która nie znajduje się w zestawie dostępności

Ustaw pewne zmienne. Zastąp wartości własnymi informacjami.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętowym, w którym jest hostowana maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli odpowiedni rozmiar znajduje się na liście, uruchom następujące polecenia, aby zmienić rozmiar maszyny wirtualnej. Jeśli żądany rozmiar nie znajduje się na liście, przejdź do kroku 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Jeśli żądany rozmiar nie znajduje się na liście, uruchom następujące polecenia, aby cofnąć alokację maszyny wirtualnej, zmienić jej rozmiar i ponownie uruchomić maszynę wirtualną. Zastąp **\<newVMsize >** rozmiarem, który chcesz.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Cofnięcie przydziału maszyny wirtualnej powoduje wydanie wszystkich dynamicznych adresów IP przypisanych do maszyny wirtualnej. Nie dotyczy to dysków systemu operacyjnego i danych. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Zmienianie rozmiaru maszyny wirtualnej w zestawie dostępności przy użyciu programu PowerShell

Jeśli nowy rozmiar maszyny wirtualnej w zestawie dostępności nie jest dostępny w klastrze sprzętowym hostującym maszynę wirtualną, wszystkie maszyny wirtualne w zestawie dostępności muszą zostać cofnięte, aby zmienić rozmiar maszyny wirtualnej. Może być też konieczne zaktualizowanie rozmiaru pozostałych maszyn wirtualnych w zestawie dostępności po zmianie rozmiaru jednej maszyny wirtualnej. Aby zmienić rozmiar maszyny wirtualnej w zestawie dostępności, wykonaj następujące czynności.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Wyświetl listę rozmiarów maszyn wirtualnych, które są dostępne w klastrze sprzętowym, w którym jest hostowana maszyna wirtualna. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Jeśli jest wyświetlany żądany rozmiar, uruchom następujące polecenia, aby zmienić rozmiar maszyny wirtualnej. Jeśli nie ma go na liście, przejdź do następnej sekcji.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Jeśli żądanego rozmiaru nie ma na liście, wykonaj następujące kroki, aby cofnąć alokację wszystkich maszyn wirtualnych w zestawie dostępności, zmienić rozmiar maszyn wirtualnych, a następnie uruchom je ponownie.

Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Zmień rozmiar i ponownie uruchom maszyny wirtualne w zestawie dostępności.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkową skalowalność, należy uruchomić wiele wystąpień maszyn wirtualnych i skalować je w poziomie. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie maszyn z systemem Windows w zestawie skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

