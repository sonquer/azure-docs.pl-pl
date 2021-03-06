---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183243"
---
## <a name="update-resources"></a>Aktualizowanie zasobów

Istnieją pewne ograniczenia, w jakie mogą być aktualizowane. Można zaktualizować następujących elementów: 

Galeria obrazów udostępnionych:
- Opis

definicję obrazu:
- Zalecane procesorów wirtualnych Vcpu
- Zalecana ilość pamięci
- Opis
- Data

Wersja obrazu:
- Liczba replik regionalne
- Regiony docelowe
- Wykluczenie z najnowsze
- Data

Jeśli planowane jest dodawanie regionów repliki, nie usuwaj zarządzanego obrazu źródłowego. Obrazu zarządzanego źródła jest wymagany dla replikacji wersję obrazu na dodatkowe regiony. 

Opis elementu galerii przy użyciu aktualizacji ([aktualizacji sig az](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Zaktualizuj opis definicję obrazu przy użyciu [az sig definicję obrazu aktualizacji](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Zaktualizuj wersję obrazu, aby dodać region, aby replikować za pomocą [az sig wersję obrazu aktualizacji](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Ta zmiana zajmie trochę czasu, jak obraz, który pobiera replikowane do nowego regionu.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Usuwanie zasobów

Musisz usunąć zasoby w odwrotnej kolejności, usuwając wersję obrazu. Po usunięciu wszystkich wersji obrazu, możesz usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazu, możesz usunąć galerii. 

Usuń wersję obrazu przy użyciu [Usuń wersję obrazu az sig](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Usuń definicję obrazu przy użyciu [Usuń definicję obrazu az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Usuń z galerii obrazów przy użyciu [Usuń az sig](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
