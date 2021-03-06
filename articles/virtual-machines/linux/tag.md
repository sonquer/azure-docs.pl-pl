---
title: Jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej platformy Azure z systemem Linux utworzonej na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: cb8ec68ac0be4cf4480f5d75f987707cf7393003
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973202"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure
W tym artykule opisano różne sposoby tagowania maszyny wirtualnej z systemem Linux na platformie Azure za pomocą modelu wdrażania Menedżer zasobów. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 15 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, że Tagi są obsługiwane tylko dla zasobów utworzonych za pośrednictwem modelu wdrażania Menedżer zasobów.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby rozpocząć, musisz zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

Można wyświetlić wszystkie właściwości danej maszyny wirtualnej, w tym Tagi, za pomocą tego polecenia:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Aby dodać nowy tag maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure, można użyć polecenia `azure vm update` wraz z parametrem tag **--Set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć Tagi, można użyć parametru **--Remove** w poleceniu `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teraz, po zastosowaniu tagów do naszych zasobów interfejsu wiersza polecenia platformy Azure i portalu, zapoznaj się z informacjami na temat użycia, aby zobaczyć Tagi w portalu rozliczeń.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie][Azure Resource Manager Overview] i [Używanie tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure][Understanding your Azure Bill] i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
