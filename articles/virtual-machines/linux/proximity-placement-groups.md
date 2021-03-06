---
title: Używanie grup umieszczania sąsiedztwa dla maszyn wirtualnych z systemem Linux
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla maszyn wirtualnych z systemem Linux na platformie Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171053"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu interfejsu wiersza polecenia platformy Azure

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania zbliżeniowe
Utwórz grupę umieszczania sąsiedztwa przy użyciu [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Wyświetl listę grup umieszczania zbliżeniowe

Możesz wyświetlić listę wszystkich grup umieszczania w sąsiedztwie przy użyciu polecenia [AZ PPG list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie umieszczania bliskości przy użyciu [nowej AZ VM](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Maszynę wirtualną można zobaczyć w grupie położenia zbliżeniowe przy użyciu polecenia [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `--ppg` parametru z parametrem [AZ VM Availability-Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) w celu utworzenia zestawu dostępności i wszystkich maszyn wirtualnych w zestawie dostępności również zostanie utworzona w tej samej grupie umieszczania sąsiedztwa.

## <a name="scale-sets"></a>Zestawy skalowania

Zestaw skalowania można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `--ppg` parametru z [AZ VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) , aby utworzyć zestaw skalowania i wszystkie wystąpienia zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/ppg) dla grup umieszczania sąsiedztwa.
