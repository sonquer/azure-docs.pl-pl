---
title: 'Wyłącz zasady sieciowe dla źródłowego adresu IP usługi linku prywatnego platformy Azure '
description: Dowiedz się, jak wyłączyć zasady sieciowe dla prywatnego linku platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453007"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Wyłącz zasady sieciowe dla źródłowego adresu IP usługi linku prywatnego

Aby można było wybrać źródłowy adres IP dla usługi linku prywatnego, w podsieci jest wymagane jawne ustawienie Wyłącz `privateLinkServiceNetworkPolicies`. To ustawienie dotyczy tylko określonego prywatnego adresu IP, który został wybrany jako źródłowy adres IP usługi łącza prywatnego. W przypadku innych zasobów w podsieci dostęp jest kontrolowany na podstawie definicji reguł zabezpieczeń sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). 
 
W przypadku korzystania z dowolnego klienta platformy Azure (środowiska PowerShell, interfejsu wiersza polecenia lub szablonów) do zmiany tej właściwości wymagany jest dodatkowy krok. Zasady można wyłączyć za pomocą usługi Cloud Shell z poziomu Azure Portal lub instalacji lokalnych Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonów Azure Resource Manager.  
 
Wykonaj poniższe kroki, aby wyłączyć zasady sieci usługi link prywatny dla sieci wirtualnej o nazwie *myVirtualNetwork* z *domyślną* podsiecią hostowaną w grupie zasobów o nazwie Moja *resourceName*. 

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Korzystanie z szablonu
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu szablonu Azure Resource Manager.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](private-endpoint-overview.md)
 
