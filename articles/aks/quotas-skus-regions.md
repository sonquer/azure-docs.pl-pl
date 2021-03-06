---
title: Limity przydziału, jednostki SKU i dostępność regionów w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat przydziałów domyślnych, rozmiarów SKU maszyn wirtualnych z ograniczeniami i dostępności regionów usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 1bb928f8e7a1f56c4b6b437f260d875662bff60f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469838"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały dla zasobów i funkcji. Niektóre jednostki SKU maszyny wirtualnej są również ograniczone do użycia.

W tym artykule opisano domyślne limity zasobów dla zasobów usługi Azure Kubernetes Service (AKS) i dostępność AKS w regionach platformy Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruktura aprowizowana

Do infrastruktury aprowizowanej mają zastosowanie wszystkie inne ograniczenia dotyczące sieci, mocy obliczeniowej i magazynu. Aby uzyskać odpowiednie limity, zobacz [limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Podczas uaktualniania klastra AKS są tymczasowo zużywane dodatkowe zasoby. Te zasoby obejmują dostępne adresy IP w podsieci sieci wirtualnej lub limit przydziału vCPU maszyny wirtualnej. W przypadku korzystania z kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w AKS) jedyną zatwierdzona podejściem do zastosowania najnowszych aktualizacji do węzłów jest wykonanie operacji uaktualnienia. Niepowodzenie procesu uaktualniania klastra może wskazywać, że nie masz dostępnej przestrzeni adresów IP lub przydziału vCPU do obsługi tych zasobów tymczasowych. Aby uzyskać więcej informacji na temat procesu uaktualniania węzła systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Ograniczone rozmiary maszyn wirtualnych

Każdy węzeł w klastrze AKS zawiera stałą ilość zasobów obliczeniowych, takich jak vCPU i pamięć. Jeśli węzeł AKS zawiera niewystarczające zasoby obliczeniowe, może to oznaczać, że awarie nie będą działać prawidłowo. Aby upewnić się, że wymagane jest niezawodne planowanie *polecenia* i aplikacji, **nie używaj następujących jednostek SKU maszyny wirtualnej w AKS**:

- Standardowa_A0
- Standardowa_A1
- Standardowa_A1_v2
- Standard_B1s
- Standard_B1ms
- Standardowa_F1
- Standardowa_F1s

Aby uzyskać więcej informacji na temat typów maszyn wirtualnych i ich zasobów obliczeniowych, zobacz Sizes [for Virtual Machines na platformie Azure][vm-skus].

## <a name="region-availability"></a>Dostępność w danym regionie

Aby uzyskać najnowszą listę, w której można wdrażać i uruchamiać klastry, zobacz [dostępność regionu AKS][region-availability].

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób obsługuje zwiększenie, zażądaj zwiększenia przez [żądanie pomocy technicznej platformy Azure][azure-support] (w polu **typ problemu**wybierz pozycję **przydział**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
