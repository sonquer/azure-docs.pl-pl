---
title: Rezerwowanie publicznych adresów IPv6 i zakresów adresów w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak zarezerwować publiczne adresy IPv6 i zakresy adresów w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595086"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Zastrzeganie publicznego prefiksu adresu IPv6
Protokół IPv6 dla usługi Azure Virtual Network (VNet) umożliwia hostowanie aplikacji na platformie Azure z łącznością IPv6 i IPv4 zarówno w ramach sieci wirtualnej, jak i z Internetu. Oprócz rezerwowania pojedynczych adresów IPv6 można zarezerwować ciągłe zakresy adresów IPv6 platformy Azure (nazywane prefiksem IP). W tym artykule opisano sposób tworzenia publicznych adresów IP i zakresów adresów IPv6 przy użyciu Azure PowerShell i interfejsu wiersza polecenia.

> [!Important]
> Protokół IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Tworzenie pojedynczego zastrzeżonego adresu IP IPv6

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Można utworzyć pojedynczy, zarezerwowany (statyczny) adres IP IPv6 przy użyciu Azure PowerShell za pomocą polecenia [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) w następujący sposób:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

 Za pomocą polecenia [AZ Network Public-IP Create](/cli/azure/network/public-ip) można utworzyć pojedynczy zastrzeżony (statyczny) publiczny adres IP platformy Azure, który jest następujący:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Tworzenie zastrzeżonego prefiksu IPv6 (zakres)

Aby zarezerwować prefiks IPv6, należy dodać rodzinę adresów IP IPv6 do tego samego polecenia, które jest używane do tworzenia prefiksów IPv4. Następujące polecenia tworzą prefiks size/125 (8 adresów IPv6).  

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Publiczny adres IPv6 można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure z opcją [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) w następujący sposób:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Publiczny adres IPv6 można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Przydziel publiczny adres IP z zastrzeżonego prefiksu IPv6

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

 Można utworzyć statyczny publiczny adres IP IPv6 z zastrzeżonego prefiksu przez dodanie argumentu `-PublicIpPrefix` podczas tworzenia publicznego adresu IP przy użyciu Azure PowerShell. W poniższym przykładzie przyjęto założenie, że prefiks został utworzony i zapisany w zmiennej programu PowerShell o nazwie: *$MyOwnIPv 6prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
 
W poniższym przykładzie przyjęto założenie, że prefiks został utworzony i zapisany w zmiennej interfejsu wiersza polecenia o nazwie: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [prefiksu adresu IPv6](ipv6-public-ip-address-prefix.md).
- Dowiedz się więcej o [adresach IPv6](ipv6-overview.md).
