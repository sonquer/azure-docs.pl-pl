---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 083051fd621194d39d0092046e187e0809fd62d9
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77520632"
---
Migawki przyrostowe (wersja zapoznawcza) są kopiami zapasowymi w czasie dla dysków zarządzanych, które w razie potrzeby składają się tylko ze wszystkich zmian od ostatniej migawki. Podczas próby pobrania lub użycia migawki przyrostowej jest używany pełny dysk VHD. Ta nowa możliwość tworzenia migawek dysków zarządzanych może potencjalnie spowodować, że nie są już wymagane do przechowywania całego dysku przy każdej pojedynczej migawce, chyba że zostanie wybrana opcja. Podobnie jak regularne migawki, migawki przyrostowe mogą służyć do tworzenia pełnego dysku zarządzanego lub do regularnej migawki.

Istnieje kilka różnic między migawką przyrostową i regularną migawką. Migawki przyrostowe zawsze korzystają ze standardowego magazynu HDD, niezależnie od typu magazynu dysku, a regularne migawki mogą korzystać z dysków SSD w warstwie Premium. Jeśli używasz zwykłych migawek na Premium Storage do skalowania wdrożeń maszyn wirtualnych, zalecamy używanie obrazów niestandardowych w ramach magazynu w warstwie Standardowa w [galerii obrazów udostępnionych](../articles/virtual-machines/linux/shared-image-galleries.md). Pomożemy Ci w osiągnięciu bardziej ogromnej skali z niższym kosztem. Ponadto migawki przyrostowe potencjalnie oferują lepszą niezawodność za pomocą [magazynu Strefowo nadmiarowego](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Jeśli ZRS jest dostępny w wybranym regionie, migawka przyrostowa będzie używać ZRS automatycznie. Jeśli ZRS nie jest dostępny w regionie, migawka domyślnie będzie [magazynem lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Można zastąpić to zachowanie i wybrać jeden z nich ręcznie, ale nie jest to zalecane.

Migawki przyrostowe oferują również funkcję różnicową, która jest unikatowo dostępna dla dysków zarządzanych. Umożliwiają one uzyskanie zmian między dwiema przyrostowymi migawkami tych samych dysków zarządzanych, w dół do poziomu bloku. Można użyć tej funkcji, aby zmniejszyć rozmiary danych podczas kopiowania migawek między regionami.

### <a name="supported-regions"></a>Obsługiwane regiony

Obecnie obsługiwane są tylko następujące regiony:

- Dostępna jako nasza oferta w regionach zachodnio-środkowe stany USA, Kanada Wschodnia, Kanada środkowa.
- Dostępna jako publiczna wersja zapoznawcza w regionach Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Europa Północna, regiony Południowe Azja Wschodnia.

## <a name="restrictions"></a>{1&gt;Ograniczenia&lt;1}
- Nie można obecnie utworzyć migawek przyrostowych po zmianie rozmiaru dysku (tylko w wersji zapoznawczej).
- Obecnie nie można przenosić migawek przyrostowych między subskrypcjami.
- Obecnie można generować identyfikatory URI SAS maksymalnie pięć migawek określonej rodziny migawek w danym momencie.
- Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku.
- Do siedmiu migawek przyrostowych na dysk można utworzyć co pięć minut.
- Można utworzyć łączną liczbę migawek przyrostowych 200 dla jednego dysku.

## <a name="powershell"></a>Program PowerShell

Za pomocą Azure PowerShell można utworzyć przyrostową migawkę. Potrzebna będzie Najnowsza wersja Azure PowerShell, następujące polecenie zainstaluje je lub zaktualizuje istniejącą instalację do najnowszej wersji:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po zainstalowaniu, zaloguj się do sesji programu PowerShell przy użyciu `az login`.

Aby utworzyć przyrostową migawkę z Azure PowerShell, Ustaw konfigurację przy użyciu opcji [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) z parametrem `-Incremental`, a następnie przekaż ją jako zmienną do opcji [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) za pomocą parametru `-Snapshot`.

Zastąp wartości `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`i `<yourDesiredSnapShotNameHere>` wartościami, a następnie użyj następującego skryptu, aby utworzyć migawkę przyrostową:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Można zidentyfikować przyrostowe migawki z tego samego dysku z `SourceResourceId` i `SourceUniqueId` właściwości migawek. `SourceResourceId` jest IDENTYFIKATORem zasobu Azure Resource Manager dysku nadrzędnego. `SourceUniqueId` jest wartością dziedziczoną z właściwości `UniqueId` dysku. Jeśli użytkownik usunął dysk, a następnie utworzy nowy dysk o tej samej nazwie, zmieni się wartość właściwości `UniqueId`.

Za pomocą `SourceResourceId` i `SourceUniqueId` można utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Zastąp `<yourResourceGroupNameHere>` wartością, a następnie użyj poniższego przykładu, aby wyświetlić listę istniejących migawek przyrostowych:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="cli"></a>Interfejs wiersza polecenia

Można utworzyć przyrostową migawkę przy użyciu interfejsu wiersza polecenia platformy Azure, która będzie potrzebna w najnowszej wersji interfejsu wiersza polecenia platformy Azure. 

W systemie Windows następujące polecenie zainstaluje lub zaktualizuje istniejącą instalację do najnowszej wersji:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
W systemie Linux instalacja interfejsu wiersza polecenia różni się w zależności od wersji systemu operacyjnego.  Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) dla konkretnej wersji systemu Linux.

Aby utworzyć przyrostową migawkę, użyj [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) z parametrem `--incremental`.

Poniższy przykład obejmuje tworzenie przyrostowych migawek, zastępowanie `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`i `<exampleLocation>` przy użyciu własnych wartości, a następnie uruchomienie przykładu:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Można zidentyfikować przyrostowe migawki z tego samego dysku z `SourceResourceId` i `SourceUniqueId` właściwości migawek. `SourceResourceId` jest IDENTYFIKATORem zasobu Azure Resource Manager dysku nadrzędnego. `SourceUniqueId` jest wartością dziedziczoną z właściwości `UniqueId` dysku. Jeśli użytkownik usunął dysk, a następnie utworzy nowy dysk o tej samej nazwie, zmieni się wartość właściwości `UniqueId`.

Za pomocą `SourceResourceId` i `SourceUniqueId` można utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Poniższy przykład wyświetla listę wszystkich migawek przyrostowych skojarzonych z określonym dyskiem, ale wymaga pewnego Instalatora.

Ten przykład używa JQ do wykonywania zapytań dotyczących danych. Aby uruchomić ten przykład, należy [zainstalować JQ](https://stedolan.github.io/jq/download/).

Zastąp wartości `<yourResourceGroupNameHere>` i `<exampleDiskName>` wartościami, a następnie użyj poniższego przykładu, aby wyświetlić listę istniejących migawek przyrostowych, o ile zainstalowano także JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Za pomocą szablonów Azure Resource Manager można także utworzyć przyrostową migawkę. Należy upewnić się, że apiVersion jest ustawiona na **2019-03-01** i że właściwość przyrostowa jest również ustawiona na wartość true. Poniższy fragment kodu stanowi przykład tworzenia przyrostowej migawki z szablonami Menedżer zasobów:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod pokazujący różnicowe możliwości migawek przyrostowych, korzystając z platformy .NET, zobacz [Kopiuj kopie zapasowe platformy Azure Managed disks do innego regionu z różnicową możliwością migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
