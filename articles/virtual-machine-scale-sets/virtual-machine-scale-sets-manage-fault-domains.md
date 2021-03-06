---
title: Zarządzanie domenami błędów w zestawach skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak wybrać odpowiednią liczbę domenami błędów podczas tworzenia zestawu skalowania maszyn wirtualnych.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275726"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych
Zestawy skalowania maszyn wirtualnych są tworzone z pięcioma domenami błędów domyślnie w regionach platformy Azure bez stref. W przypadku regionów obsługujących wdrożenie strefowe zestawów skalowania maszyn wirtualnych i wybrania tej opcji wartość domyślna liczby domen błędów wynosi 1 dla każdej z tych stref. Demon = 1 w tym przypadku oznacza, że wystąpienia maszyn wirtualnych należące do zestawu skalowania będą rozłożone w wielu stojakach na podstawie najlepszego wysiłku.

Można również rozważyć wyrównanie liczby domen błędów zestawu skalowania z liczbą domen błędów Managed Disks. To wyrównanie może pomóc w zapobieganiu utracie kworum, jeśli cała domena błędów Managed Disks ulegnie awarii. Licznik FD może być ustawiony na wartość mniejszą lub równą liczbie Managed Disks domen błędów dostępnych w poszczególnych regionach. Zapoznaj się z tym [dokumentem](../virtual-machines/windows/manage-availability.md) , aby dowiedzieć się więcej o liczbie Managed disks domen błędów według regionów.

## <a name="rest-api"></a>Interfejs API REST
Właściwość `properties.platformFaultDomainCount` można ustawić na wartość 1, 2 lub 3 (wartość domyślna to 5, jeśli nie zostanie określona). Zapoznaj się z dokumentacją interfejsu API REST [tutaj](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Parametr `--platform-fault-domain-count` można ustawić na wartość 1, 2 lub 3 (wartość domyślna to 5, jeśli nie zostanie określona). W [tym miejscu](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [funkcjach dostępności i nadmiarowości](../virtual-machines/windows/availability.md) dla środowisk platformy Azure.
