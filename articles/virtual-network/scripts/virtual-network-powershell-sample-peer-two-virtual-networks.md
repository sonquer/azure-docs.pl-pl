---
title: Komunikacja równorzędna dwóch sieci wirtualnych — przykład skryptu Azure PowerShell
description: Przykładowy skrypt programu Azure PowerShell — komunikacja równorzędna dwóch sieci wirtualnych
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 4061997aa2efbae250b30fc58cef06b1249c2b8f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091303"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Przykładowy skrypt tworzący komunikację równorzędną dwóch sieci wirtualnych

Ten przykładowy skrypt tworzy i łączy dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci platformy Azure. Po uruchomieniu skryptu utworzysz komunikację równorzędną pomiędzy dwiema sieciami wirtualnymi.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/powershell) lub z lokalnej instalacji programu PowerShell. Jeśli używasz programu PowerShell lokalnie, ten skrypt wymaga modułu Az PowerShell w wersji 5.4.1 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w następującej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Tworzy sieć wirtualną i podsieć platformy Azure. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Tworzy komunikację równorzędną między dwiema sieciami wirtualnymi.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla sieci wirtualnej można znaleźć w temacie [Virtual network PowerShell samples](../powershell-samples.md) (Przykładowe skrypty programu PowerShell dla sieci wirtualnej).