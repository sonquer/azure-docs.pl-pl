---
title: Tworzenie publicznej komunikacji równorzędnej Azure ExpressRoute i zarządzanie nią
description: Dowiedz się więcej na temat publicznej komunikacji równorzędnej Azure i zarządzaj nią
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: bae44f67a485546ba29148a114d88df198f7c3e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483091"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Tworzenie publicznej komunikacji równorzędnej ExpressRoute i zarządzanie nią

> [!div class="op_single_selector"]
> * [Artykuł — publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — publicznej komunikacji równorzędnej](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artykuł — Komunikacja równorzędna firmy Microsoft](expressroute-circuit-peerings.md#microsoftpeering)
>

W tym artykule opisano tworzenie publicznej konfiguracji routingu komunikacji równorzędnej dla obwodu usługi ExpressRoute i zarządzanie nią. Możesz również sprawdzić stan komunikacji równorzędnej, aktualizowanie jej lub usuwanie i cofanie aprowizacji. Ten artykuł dotyczy obwodów Menedżer zasobów, które zostały utworzone przed zaniechaniem publicznej komunikacji równorzędnej. Jeśli masz poprzednio istniejący obwód (utworzony przed przestarzałą publiczną komunikację równorzędną), możesz zarządzać/konfigurować publiczną komunikację równorzędną przy użyciu [Azure PowerShell](#powershell), [interfejsu wiersza polecenia platformy Azure](#cli)i [Azure Portal](#portal).

>[!NOTE]
>Publiczna Komunikacja równorzędna jest przestarzała. Nie można utworzyć publicznej komunikacji równorzędnej w nowych obwodach usługi ExpressRoute. Jeśli korzystasz z nowego obwodu usługi ExpressRoute, użyj [komunikacji równorzędnej firmy Microsoft](expressroute-circuit-peerings.md#microsoftpeering) dla usług platformy Azure.
>

## <a name="connectivity"></a>Łączność

Połączenie jest zawsze inicjowane z sieci WAN z usługami Microsoft Azure. Usługi Microsoft Azure nie będą mogły inicjować połączeń do sieci za pomocą tej domeny routingu. Jeśli obwód usługi ExpressRoute jest włączony dla publicznej komunikacji równorzędnej Azure, możesz uzyskać dostęp do [publicznych zakresów adresów IP używanych na platformie Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) w ramach obwodu.

Po włączeniu publicznej komunikacji równorzędnej można nawiązać połączenie z większością usług platformy Azure. Firma Microsoft nie umożliwiają selektywne wybierz usługi, dla których firma Microsoft anonsować trasy do.

* Usługi, takie jak Azure Storage, SQL Database i websites, są oferowane na publicznych adresach IP.
* Za pomocą publicznej domeny routingu komunikacji równorzędnej można łączyć się prywatnie z usługami hostowanymi na publicznych adresach IP, w tym w odniesieniu do adresów VIP usług w chmurze.
* Można publicznej komunikacji równorzędnej domeny nawiązać połączenie z sieci Obwodowej i połączyć się z wszystkich usług platformy Azure dla swoich publicznych adresów IP z poziomu sieci WAN bez konieczności nawiązywania połączenia przez internet.

## <a name="services"></a>Services

W tej sekcji przedstawiono usługi dostępne za pośrednictwem publicznej komunikacji równorzędnej. Ponieważ publiczna Komunikacja równorzędna jest przestarzała, nie istnieje plan dodawania nowych lub dodatkowych usług do publicznej komunikacji równorzędnej. Jeśli używasz publicznej komunikacji równorzędnej, a usługa, której chcesz używać, jest obsługiwana tylko za pośrednictwem komunikacji równorzędnej firmy Microsoft, musisz przełączyć się do komunikacji równorzędnej firmy Microsoft. Listę obsługiwanych usług można znaleźć w temacie [Komunikacja równorzędna firmy Microsoft](expressroute-faqs.md#microsoft-peering) .

**Obsługiwał**

* Power BI
* Większość usług platformy Azure są obsługiwane. Bezpośrednio Sprawdź usługę, która ma zostać użyta do zweryfikowania pomocy technicznej.

**Nieobsługiwane:**
  * CDN
  * Azure Front Door
  * Serwer uwierzytelniania wieloskładnikowego (starsza wersja)
  * Traffic Manager

Aby sprawdzić dostępność określonej usługi, możesz sprawdzić dokumentację tej usługi, aby sprawdzić, czy istnieje zastrzeżony zakres opublikowany dla tej usługi. Następnie można wyszukać zakresy adresów IP usługi docelowej i porównać z zakresami wymienionymi w obszarze [zakresy adresów IP platformy Azure i Tagi usług — plik XML chmury publicznej](https://www.microsoft.com/download/details.aspx?id=56519). Możesz również otworzyć bilet pomocy technicznej dla omawianej usługi, aby uzyskać wyjaśnienie.

## <a name="compare"></a>Porównanie komunikacji równorzędnej

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Publiczna Komunikacja równorzędna Azure ma 1 adres IP translatora adresów sieciowych skojarzony z każdą sesją BGP. W przypadku więcej niż 2 adresów IP translatora adresów sieciowych Przenieś do komunikacji równorzędnej firmy Microsoft. Komunikacja równorzędna firmy Microsoft umożliwia skonfigurowanie własnych alokacji NAT, a także Używanie filtrów tras dla selektywnych anonsów prefiksów. Aby uzyskać więcej informacji, zobacz [przenoszenie do komunikacji równorzędnej firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Niestandardowe filtry tras

W ramach sieci, aby używać tylko trasy, czego potrzebujesz, możesz zdefiniować filtry tras niestandardowych. Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje na temat konfiguracji routingu.

## <a name="powershell"></a>Azure PowerShell kroki


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Ponieważ publiczna Komunikacja równorzędna jest przestarzała, nie można skonfigurować publicznej komunikacji równorzędnej na nowym obwodzie ExpressRoute.

1. Sprawdź, czy jest dostępny obwód ExpressRoute, który jest obsługiwany, a także włączony. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Upewnij się, że masz następujące informacje, zanim przejdziesz dalej.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Opcjonalnie:
   * Skrót MD5, jeśli zdecydujesz się go użyć.

   Z poniższego przykładu, aby skonfigurować publiczną komunikację równorzędną Azure dla obwodu

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Jeśli zdecydujesz się używać skrótu MD5, skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   >

### <a name="getpublic"></a>Aby dowiedzieć się Azure publicznej komunikacji równorzędnej

Można uzyskać szczegółów konfiguracji, za pomocą następującego polecenia cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Aby zaktualizować konfigurację komunikacji równorzędnej publicznej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="cli"></a>Kroki interfejsu wiersza polecenia platformy Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Sprawdź obwód usługi ExpressRoute, aby upewnić się, jest aprowizowana i włączona. Skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route list
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Upewnij się, że masz następujące informacje, zanim przejdziesz dalej.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * **Opcjonalnie —** Skrót MD5, jeśli zdecydujesz się go użyć.

   Uruchom poniższy przykład, aby skonfigurować publiczną komunikację równorzędną Azure dla obwodu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Jeśli zdecydujesz się używać skrótu MD5, skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.

### <a name="getpublic"></a>Aby wyświetlić Azure szczegóły dotyczące komunikacji równorzędnej publicznej

Można uzyskać szczegółów konfiguracji, korzystając z następującego przykładu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Dane wyjściowe są podobne do poniższego przykładu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Aby zaktualizować konfigurację komunikacji równorzędnej publicznej Azure

Możesz zaktualizować dowolną część konfiguracji, korzystając z następującego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, uruchamiając w poniższym przykładzie:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="portal"></a>Azure Portal kroki

Aby skonfigurować komunikację równorzędną, należy użyć kroków programu PowerShell lub interfejsu wiersza polecenia zawartych w tym artykule. Aby zarządzać komunikacją równorzędną, można użyć poniższych sekcji. W celu uzyskania informacji te kroki wyglądają podobnie do zarządzania [komunikacji równorzędnej firmy Microsoft w portalu](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="get"></a>Aby wyświetlić Azure szczegóły dotyczące komunikacji równorzędnej publicznej

Wyświetl właściwości publicznej komunikacji równorzędnej Azure, wybierając komunikację równorzędną w portalu.

### <a name="update"></a>Aby zaktualizować konfigurację komunikacji równorzędnej publicznej Azure

Wybierz wiersz dla komunikacji równorzędnej, a następnie zmodyfikuj właściwości komunikacji równorzędnej.

### <a name="delete"></a>Aby usunąć publiczną komunikację równorzędną Azure

Usuń konfigurację komunikacji równorzędnej, wybierając ikonę Usuń.

## <a name="next-steps"></a>Następne kroki

Następny krok, [Połącz sieć wirtualną z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).