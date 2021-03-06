---
title: Przenoszenie maszyny wirtualnej z systemem Linux na platformie Azure
description: Przenieś maszynę wirtualną z systemem Linux do innej subskrypcji platformy Azure lub grupy zasobów w Menedżer zasobów model wdrażania.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 2ba8a8cdc324f46e25f9665cfce0aa07fc948e88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979042"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Przenoszenie maszyny wirtualnej z systemem Linux do innej subskrypcji lub grupy zasobów
W tym artykule pokazano, jak przenieść maszynę wirtualną z systemem Linux między grupami zasobów lub subskrypcjami. Przenoszenie maszyny wirtualnej między subskrypcjami może być przydatne, jeśli utworzono maszynę wirtualną w osobistej subskrypcji, a teraz chcesz przenieść ją do subskrypcji firmy.

> [!IMPORTANT]
>W tej chwili nie można przenieść Managed Disks platformy Azure.
>
>Nowe identyfikatory zasobów są tworzone w ramach przenoszenia. Po przeniesieniu maszyny wirtualnej należy zaktualizować narzędzia i skrypty w celu użycia nowych identyfikatorów zasobów.
>
>

## <a name="use-the-azure-cli-to-move-a-vm"></a>Przenoszenie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure


Aby można było przenieść maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure, należy upewnić się, że subskrypcje źródłowe i docelowe znajdują się w tej samej dzierżawie. Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj polecenie [AZ Account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Jeśli identyfikatory dzierżawy dla subskrypcji źródłowej i docelowej nie są takie same, należy skontaktować się z [pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , aby przenieść zasoby do nowej dzierżawy.

Aby pomyślnie przenieść maszynę wirtualną, należy przenieść maszynę wirtualną i wszystkie jej zasoby pomocnicze. Użyj polecenia [AZ Resource list](/cli/azure/resource) , aby wyświetlić listę wszystkich zasobów w grupie zasobów i ich identyfikatorów. Pomaga w potoku danych wyjściowych tego polecenia do pliku, aby można było skopiować i wkleić identyfikatory do nowszych poleceń.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Aby przenieść maszynę wirtualną i jej zasoby do innej grupy zasobów, użyj [AZ Resource Move](/cli/azure/resource). Poniższy przykład pokazuje, jak przenieść maszynę wirtualną i najpopularniejsze zasoby, których wymaga. Użyj parametru **-ID** i przekaż listę oddzielonych przecinkami (bez spacji) identyfikatorów dla zasobów do przeniesienia.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Jeśli chcesz przenieść maszynę wirtualną i jej zasoby do innej subskrypcji, Dodaj parametr **--Destination-** Subscription, aby określić subskrypcję docelową.

Gdy zostanie wyświetlony monit o potwierdzenie, że chcesz przenieść określone zasoby, wpisz **Y** , aby potwierdzić.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Następne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcjami. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
