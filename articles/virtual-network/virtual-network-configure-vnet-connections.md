---
title: Konfigurowanie i weryfikowanie połączeń sieci wirtualnej lub sieci VPN
description: Wskazówki krok po kroku dotyczące konfigurowania i weryfikowania różnorodnych wdrożeń sieci VPN i sieci wirtualnych platformy Azure
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099056"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurowanie i weryfikowanie połączeń sieci wirtualnej lub sieci VPN

Ten przewodnik zawiera wskazówki krok po kroku dotyczące konfigurowania i weryfikowania różnych wdrożeń sieci VPN i wirtualnych platformy Azure. Scenariusze obejmują Routing tranzytowy, połączenia sieciowe do sieci, Border Gateway Protocol (BGP), połączenia w wielu lokacjach i połączenia punkt-lokacja.

Bramy sieci VPN platformy Azure umożliwiają elastyczność w rozmieszczaniu niemal dowolnego rodzaju topologii połączonej sieci wirtualnej na platformie Azure. Można na przykład połączyć sieci wirtualne:

- Między regionami.
- Między typami sieci wirtualnych (Azure Resource Manager a klasyczny).
- Na platformie Azure lub w lokalnym środowisku hybrydowym.
- W różnych subskrypcjach. 

## <a name="network-to-network-vpn-connection"></a>Połączenie sieci VPN typu sieć-sieć

Łączenie sieci wirtualnej z inną siecią wirtualną (sieć-sieć) za pośrednictwem sieci VPN jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel za pośrednictwem protokołu IPsec i IKE. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji.

![Połączenie sieciowe-sieciowe z protokołem IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Jeśli sieci wirtualne znajdują się w tym samym regionie, warto rozważyć połączenie ich za pomocą komunikacji równorzędnej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnych nie korzysta z bramy sieci VPN. Zwiększa przepływność i zmniejsza opóźnienia. Aby skonfigurować połączenie komunikacji równorzędnej sieci wirtualnej, wybierz opcję **Konfiguruj i Weryfikuj wirtualne sieci równorzędne**.

Jeśli sieci wirtualne zostały utworzone za pomocą modelu wdrażania usługi Azure Resource Manager, wybierz pozycję **Konfiguruj i sprawdź poprawność Menedżer zasobów sieci wirtualnej do połączenia** sieci wirtualnej Menedżer zasobów, aby skonfigurować połączenie VPN.

Jeśli jedna z sieci wirtualnych została utworzona za pośrednictwem klasycznego modelu wdrażania platformy Azure, a druga została utworzona za pośrednictwem Menedżer zasobów, wybierz pozycję **Konfiguruj i sprawdź poprawność klasycznej sieci wirtualnej do Menedżer zasobów połączenie** z siecią wirtualną, aby skonfigurować połączenie sieci VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Skonfiguruj komunikację równorzędną sieci wirtualnych dla dwóch sieci wirtualnych w tym samym regionie

Przed rozpoczęciem wdrażania i konfigurowania komunikacji równorzędnej sieci wirtualnej platformy Azure upewnij się, że spełniono następujące wymagania wstępne:

* Wirtualne sieci równorzędne muszą znajdować się w tym samym regionie platformy Azure.
* Równorzędne sieci wirtualne muszą zawierać przestrzenie adresów IP, które nie nakładają się na siebie.
* Wirtualne sieci równorzędne obejmują dwie sieci wirtualne. Nie istnieje żadna pochodna relacja przechodnia między komunikacjami równorzędnymi. Na przykład, jeśli VNetA jest połączona z Vnetc, a Vnetc jest połączona z VNetC, VNetA *nie* jest równorzędna z VNetC.

Gdy spełnisz wymagania, możesz wykonać czynności opisane [w samouczku: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) Azure Portal do tworzenia i konfigurowania komunikacji równorzędnej.

Aby sprawdzić konfigurację komunikacji równorzędnej, użyj następującej metody:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. W polu zawierającym **zasoby wyszukiwania** tekstu w górnej części portalu wpisz **sieci wirtualne**. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
3. W wyświetlonym bloku **sieci wirtualne** wybierz sieć wirtualną, dla której chcesz utworzyć komunikację równorzędną.
4. W okienku, które pojawia się dla sieci wirtualnej, wybierz pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
5. Wybierz komunikację równorzędną i Wyświetl wyniki konfiguracji.

![Opcje sprawdzania konfiguracji komunikacji równorzędnej sieci wirtualnej](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Aby uzyskać Azure PowerShell, uruchom polecenie [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) w celu uzyskania komunikacji równorzędnej sieci wirtualnej. Oto przykład:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Łączenie Menedżer zasobów sieci wirtualnej z inną siecią wirtualną Menedżer zasobów

Połączenie można skonfigurować z jednej Menedżer zasobów sieci wirtualnej bezpośrednio do innej Menedżer zasobów sieci wirtualnej. Można też skonfigurować połączenie przy użyciu protokołu IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Skonfiguruj połączenie sieci VPN między Menedżer zasobów sieciami wirtualnymi

Aby skonfigurować połączenie między sieciami wirtualnymi Menedżer zasobów bez użycia protokołu IPsec, zobacz [Konfigurowanie połączenia sieci VPN między siecią i siecią przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Aby skonfigurować połączenie z protokołem IPsec między dwiema Menedżer zasobów sieciami wirtualnymi, wykonaj kroki od 1 do 5 w obszarze [Utwórz połączenie lokacja-lokacja w Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) dla każdej sieci wirtualnej.

> [!Note]
> Te kroki działają tylko w przypadku sieci wirtualnych w tej samej subskrypcji. Jeśli sieci wirtualne znajdują się w różnych subskrypcjach, musisz użyć programu PowerShell, aby nawiązać połączenie. Zobacz artykuł [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Weryfikowanie połączenia sieci VPN między sieciami wirtualnymi Menedżer zasobów

![Klasyczne połączenie sieci wirtualnej z siecią wirtualną Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Aby sprawdzić, czy połączenie sieci VPN jest prawidłowo skonfigurowane, wykonaj te instrukcje.

> [!Note] 
> Liczby po składnikach sieci wirtualnej w tych krokach odpowiadają liczbom na powyższym diagramie.

1. Upewnij się, że w połączonych sieciach wirtualnych nie ma nakładających się przestrzeni adresowych.
2. Sprawdź, czy zakres adresów dla Azure Resource Manager sieci wirtualnej (1) jest prawidłowo zdefiniowany w wystąpieniu **obiektu połączenia** (4).
3. Sprawdź, czy zakres adresów dla Azure Resource Manager sieci wirtualnej (6) jest prawidłowo zdefiniowany w wystąpieniu **obiektu połączenia** (3).
4. Sprawdź, czy wstępnie udostępnione klucze są zgodne z obiektami połączeń.
5. Sprawdź, czy adres VIP bramy sieci wirtualnej Azure Resource Manager (2) jest prawidłowo zdefiniowany w wystąpieniu **obiektu połączenia** (4).
6. Sprawdź, czy adres VIP bramy sieci wirtualnej Azure Resource Manager (5) jest prawidłowo zdefiniowany w wystąpieniu **obiektu połączenia** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Łączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżer zasobów

Można utworzyć połączenie między sieciami wirtualnymi znajdującymi się w różnych subskrypcjach i w różnych regionach. Można również połączyć sieci wirtualne, które mają już połączenia z sieciami lokalnymi, o ile typ bramy został skonfigurowany jako oparty na trasach.

Aby skonfigurować połączenie między klasyczną siecią wirtualną a siecią wirtualną Menedżer zasobów, zobacz [łączenie sieci wirtualnych z różnych modeli wdrażania przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Klasyczne połączenie sieci wirtualnej z siecią wirtualną Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Aby sprawdzić konfigurację podczas łączenia klasycznej sieci wirtualnej z siecią wirtualną Azure Resource Manager, wykonaj te instrukcje.

> [!Note] 
> Liczby po składnikach sieci wirtualnej w tych krokach odpowiadają liczbom na powyższym diagramie. 

1. Upewnij się, że w połączonych sieciach wirtualnych nie ma nakładających się przestrzeni adresowych.
2. Sprawdź, czy zakres adresów dla Azure Resource Manager sieci wirtualnej (6) jest zdefiniowany dokładnie w klasycznej definicji sieci lokalnej (3).
3. Sprawdź, czy zakres adresów dla klasycznej sieci wirtualnej (1) jest prawidłowo zdefiniowany w wystąpieniu Azure Resource Manager **obiektu połączenia** (4).
4. Upewnij się, że wirtualny adres IP bramy sieci wirtualnej (2) jest prawidłowo zdefiniowany w wystąpieniu Azure Resource Manager **obiektu połączenia** (4).
5. Sprawdź, czy Azure Resource Manager Brama sieci wirtualnej (5) jest zdefiniowana dokładnie w klasycznym wystąpieniu **definicji sieci lokalnej** (3).
6. Sprawdź, czy klucze wstępne są zgodne z obydwoma połączonymi sieciami wirtualnymi:
   - Klasyczna Sieć wirtualna: **Definicja sieci lokalnej** r.3
   - Azure Resource Manager sieci wirtualnej: **Obiekt połączenia** czwart

## <a name="create-a-point-to-site-vpn-connection"></a>Tworzenie połączenia sieci VPN typu punkt-lokacja

Konfiguracja typu punkt-lokacja (*P2S* na poniższym diagramie) umożliwia utworzenie bezpiecznego połączenia z pojedynczego komputera klienckiego do sieci wirtualnej. Połączenia typu punkt-lokacja są przydatne, gdy chcesz nawiązać połączenie z siecią wirtualną z lokalizacji zdalnej, np. z domu lub z konferencji. Są one również przydatne, gdy masz tylko kilku klientów, którzy muszą łączyć się z siecią wirtualną. 

Połączenie sieci VPN punkt-lokacja jest inicjowane z komputera klienckiego za pośrednictwem natywnego klienta sieci VPN systemu Windows. Łączący się klienci używają certyfikatów do uwierzytelniania.

![Połączenie typu punkt-lokacja](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Połączenia punkt-lokacja nie wymagają urządzenia sieci VPN. Tworzą one połączenie sieci VPN za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Połączenie typu punkt-lokacja z siecią wirtualną można połączyć przy użyciu różnych narzędzi wdrażania i modeli wdrażania:

* [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu Azure Portal (klasyczny)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Weryfikowanie połączenia punkt-lokacja

Artykuł [Rozwiązywanie problemów: Problemy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) z połączeniem punkt-lokacja na platformie Azure przeprowadzą między typowymi problemami dotyczącymi połączeń punkt-lokacja.

## <a name="create-a-multisite-vpn-connection"></a>Tworzenie wielooddziałowego połączenia sieci VPN

Można dodać połączenie typu lokacja-lokacja (*S2S* na poniższym diagramie) do sieci wirtualnej, która ma już połączenie lokacja-lokacja, połączenie punkt-lokacja lub połączenie sieciowe-sieciowe. Ten rodzaj połączenia jest często nazywany konfiguracją *wielu lokacji* . 

![Połączenie z wieloma lokacjami](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Obecnie platforma Azure współpracuje z dwoma modelami wdrażania: Menedżer zasobów i klasyczny. Dwa modele nie są w pełni zgodne ze sobą. Aby skonfigurować połączenie w wielu lokacjach z różnymi modelami, zobacz następujące artykuły:

* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN (klasyczne)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Kroki opisane w tych artykułach nie dotyczą usługi Azure ExpressRoute i istniejących konfiguracji połączeń między lokacjami. Aby uzyskać więcej informacji, zobacz [współistniejące połączenia ExpressRoute i lokacja-lokacja](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Konfigurowanie routingu tranzytowego

Routing tranzytowy to konkretny Scenariusz routingu, w którym można połączyć wiele sieci w topologii łańcucha łańcuchowego. Ten Routing umożliwia zasobom w sieciach wirtualnych z dowolnego końca łańcucha komunikowanie się ze sobą za pomocą sieci wirtualnych między. Bez routingu tranzytowego sieci ani urządzenia połączone za pomocą koncentratora nie mogą nawiązać połączenia ze sobą.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurowanie routingu tranzytowego w ramach połączenia typu punkt-lokacja

Wyobraź sobie scenariusz, w którym chcesz skonfigurować połączenie sieci VPN typu lokacja-lokacja między VNetA i Vnetc. Należy również skonfigurować sieć VPN typu punkt-lokacja, aby klient mógł nawiązać połączenie z bramą VNetA. Następnie chcesz włączyć routing tranzytowy dla klientów punkt-lokacja, aby połączyć się z usługą Vnetc, który przechodzi przez VNetA. 

Ten scenariusz jest obsługiwany, gdy protokół BGP jest włączony w sieci VPN typu lokacja-lokacja między VNetA i Vnetc. Aby uzyskać więcej informacji, zobacz [Informacje o routingu sieci VPN typu punkt-lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurowanie routingu tranzytowego w ramach połączenia ExpressRoute

Usługa Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365. Aby uzyskać więcej informacji, zobacz [ExpressRoute Overview (przegląd](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)).

![Połączenie prywatnej komunikacji równorzędnej ExpressRoute z sieciami wirtualnymi platformy Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Firma Microsoft zaleca, aby Jeśli VNetA i Vnetc znajdują się w tym samym regionie geopolitycznym, należy [połączyć obie sieci wirtualne z obwodem ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) zamiast konfigurować Routing tranzytowy. Jeśli sieci wirtualne znajdują się w różnych regionach geopolitycznych, możesz je również połączyć bezpośrednio z własnym obwodem, jeśli masz [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Jeśli masz ExpressRoute i współistnienie lokacja-lokacja, routing tranzytowy nie jest obsługiwany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ExpressRoute i lokacja-lokacja przy użyciu programu PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Jeśli włączono ExpressRoute do łączenia sieci lokalnych z siecią wirtualną platformy Azure, możesz włączyć komunikację równorzędną między sieciami wirtualnymi, w których chcesz mieć Routing tranzytowy. Aby umożliwić sieciom lokalnym łączenie się ze zdalną siecią wirtualną, należy skonfigurować [komunikację równorzędną sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Wirtualne sieci równorzędne są dostępne tylko dla sieci wirtualnych w tym samym regionie.

Aby sprawdzić, czy skonfigurowano Routing tranzytowy dla komunikacji równorzędnej sieci wirtualnej, wykonaj następujące instrukcje:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. [Utwórz komunikację równorzędną między VNetA i vnetc](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) , jak pokazano na wcześniejszym diagramie. 
3. W okienku, które pojawia się dla sieci wirtualnej, wybierz pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
4. Wybierz komunikację równorzędną, którą chcesz wyświetlić. Następnie wybierz pozycję **Konfiguracja** , aby sprawdzić, czy włączono opcję **Zezwalaj na tranzyt bramy** w sieci VNetA połączonej z obwodem ExpressRoute, i **Użyj bramy zdalnej** w zdalnej sieci vnetc, która nie jest połączona z ExpressRoute zwarci.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu komunikacji równorzędnej sieci wirtualnej

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, użytkownicy mogą również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do infrastruktury lokalnej. Aby skonfigurować trasę tranzytową w komunikacji równorzędnej sieci wirtualnych, zobacz [połączenia sieciowe-sieciowe](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Tranzyt bramy nie jest obsługiwany w relacji komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Obie sieci wirtualne w relacji komunikacji równorzędnej muszą zostać utworzone za pomocą Menedżer zasobów do działania przesyłania bramy.

Aby sprawdzić, czy skonfigurowano trasę tranzytową dla komunikacji równorzędnej sieci wirtualnej, wykonaj następujące instrukcje:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. W polu zawierającym **zasoby wyszukiwania** tekstu w górnej części portalu wpisz **sieci wirtualne**. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
3. W wyświetlonym bloku **sieci wirtualne** wybierz sieć wirtualną, dla której chcesz sprawdzić ustawienie komunikacji równorzędnej.
4. W okienku, które zostanie wyświetlone dla wybranej sieci wirtualnej, wybierz pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
5. Wybierz komunikację równorzędną, którą chcesz wyświetlić. Sprawdź, czy włączono opcję **Zezwalaj na tranzyt bramy** i **Użyj bram zdalnych** w obszarze **Konfiguracja**.

![Wybory do sprawdzenia, czy skonfigurowano trasę tranzytową dla komunikacji równorzędnej sieci wirtualnej](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurowanie routingu tranzytowego w ramach połączenia typu sieć-sieć

Aby skonfigurować Routing tranzytowy między sieciami wirtualnymi, należy włączyć protokół BGP dla wszystkich pośrednich połączeń sieci między sieciami przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell. Aby uzyskać instrukcje, zobacz [jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Ruch tranzytowy za pośrednictwem bram sieci VPN platformy Azure jest możliwy za pośrednictwem klasycznego modelu wdrażania, ale zależy od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracyjnym sieci. Protokół BGP nie jest jeszcze obsługiwany w połączeniu z sieciami wirtualnymi platformy Azure i bramami sieci VPN za pośrednictwem klasycznego modelu wdrażania. Bez protokołu BGP ręczne Definiowanie przestrzeni adresowych tranzytu jest podatne na błędy i nie jest to zalecane.

> [!Note]
> Można skonfigurować klasyczne połączenia sieciowe do sieci przy użyciu klasycznego portalu Azure lub za pomocą pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania Azure Resource Manager ani Azure Portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznych sieci wirtualnych, zobacz [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurowanie routingu tranzytowego w ramach połączenia typu lokacja-lokacja

Aby skonfigurować Routing tranzytowy między siecią lokalną a siecią wirtualną z połączeniem lokacja-lokacja, należy włączyć protokół BGP dla wszystkich pośrednich połączeń lokacja-lokacja przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell. Instrukcje [można znaleźć w temacie Jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) .

Ruch tranzytowy za pośrednictwem bram sieci VPN platformy Azure jest możliwy za pośrednictwem klasycznego modelu wdrażania, ale zależy od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracyjnym sieci. Protokół BGP nie jest jeszcze obsługiwany w połączeniu z sieciami wirtualnymi platformy Azure i bramami sieci VPN za pośrednictwem klasycznego modelu wdrażania. Bez protokołu BGP ręczne Definiowanie przestrzeni adresowych tranzytu jest podatne na błędy i nie jest to zalecane.

> [!Note]
> Można skonfigurować klasyczne połączenia typu lokacja-lokacja przy użyciu klasycznego portalu Azure lub za pomocą pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania Azure Resource Manager ani Azure Portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznych sieci wirtualnych, zobacz [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurowanie protokołu BGP dla bramy sieci VPN

Protokół BGP jest standardowym protokołem routingu używanym w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Gdy protokół BGP jest używany w kontekście sieci wirtualnych platformy Azure, włącza bramy sieci VPN platformy Azure i lokalne urządzenia sieci VPN, znane jako elementy równorzędne protokołu BGP lub sąsiedzi. Wymienia "trasy", które będą informować bramę o dostępności i możliwości uzyskania dostępu do tych prefiksów, aby przejść przez bramę lub routery. 

Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami przez propagowanie tras, które Brama BGP uzyskuje od jednego elementu równorzędnego BGP do wszystkich innych elementów równorzędnych protokołu BGP. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu BGP za pomocą platformy Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurowanie protokołu BGP dla połączenia sieci VPN

Aby skonfigurować połączenie sieci VPN korzystające z protokołu BGP, zobacz [jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Włącz protokół BGP w bramie sieci wirtualnej przez utworzenie dla niego systemu autonomicznego (AS). Bramy podstawowe nie obsługują protokołu BGP. Aby sprawdzić jednostkę SKU bramy, przejdź do sekcji **Przegląd** w bloku **VPN Gateway** w Azure Portal. Jeśli jednostka SKU jest **podstawowa**, należy zmienić jednostkę SKU (zobacz Zmiana [rozmiarów bramy](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) na **VpnGw1**. 

Sprawdzenie jednostki SKU spowoduje przestoje od 20 do 30 minut. Gdy tylko brama ma poprawną jednostkę SKU, można dodać liczbę AS przy użyciu polecenia [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) programu PowerShell polecenia cmdlet. Po skonfigurowaniu numeru AS adres IP elementu równorzędnego protokołu BGP dla bramy zostanie udostępniony automatycznie.

Należy ręcznie podać `LocalNetworkGateway` numer AS i adres równorzędny protokołu BGP. Wartości `ASN` i`-BgpPeeringAddress` można ustawić przy użyciu polecenia [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) lub [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) programu PowerShell polecenia cmdlet. Niektóre z nich są zarezerwowane na platformę Azure i nie można ich używać zgodnie [z opisem w temacie Informacje o protokole BGP z platformą azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Obiekt połączenia musi mieć włączony protokół BGP. `-EnableBGP` Wartość można ustawić na `$True` za pomocą opcji [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) lub [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Weryfikowanie konfiguracji protokołu BGP

Aby sprawdzić, czy protokół BGP jest skonfigurowany prawidłowo, można uruchomić `get-AzureRmVirtualNetworkGateway` i `get-AzureRmLocalNetworkGateway` polecenia cmdlet. Następnie zobaczysz dane wyjściowe powiązane z protokołem BGP `BgpSettingsText` w części. Na przykład:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Utwórz aktywne/aktywne połączenie sieci VPN o wysokiej dostępności

Kluczowe różnice między bramami Active/Active i Active/standby są następujące:

* Należy utworzyć dwie konfiguracje IP bramy z dwoma publicznymi adresami IP.
* Należy ustawić flagę **EnableActiveActiveFeature** .
* Jednostka SKU bramy musi mieć wartość **VpnGw1**, **VpnGw2**lub **VpnGw3**.

Aby zapewnić wysoką dostępność połączeń między sieciami i między sieciami, należy wdrożyć wiele bram sieci VPN i ustanowić wiele połączeń równoległych między sieciami i platformą Azure. Aby zapoznać się z omówieniem opcji łączności i topologii, zobacz [wiele lokalizacji i połączenia między sieciami (o wysokiej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)dostępności).

Aby utworzyć aktywne/aktywne połączenia między sieciami i między sieciami, postępuj zgodnie z instrukcjami w temacie [Konfigurowanie aktywnych/aktywnych połączeń sieci VPN S2S z bramami sieci VPN platformy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) Azure, aby skonfigurować bramę sieci VPN platformy Azure w trybie aktywny/aktywny.

> [!Note]  
> * W przypadku dodawania adresów do bramy sieci lokalnej w trybie aktywny/aktywny z włączonym protokołem BGP należy *dodać tylko adresy/32 elementów równorzędnych protokołu BGP*. W przypadku dodania kolejnych adresów będą one uznawane za trasy statyczne i mają pierwszeństwo przed trasami protokołu BGP.
> * W przypadku sieci lokalnych, które nawiązują połączenie z platformą Azure, należy używać innych numerów protokołu BGP. (Jeśli są takie same, należy zmienić sieć wirtualną jako liczbę, jeśli lokalne urządzenie sieci VPN korzysta już z protokołu ASN do komunikacji równorzędnej z innymi sąsiadami BGP).

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Zmiana typu bramy sieci VPN platformy Azure po wdrożeniu

Nie można zmienić typu bramy sieci wirtualnej platformy Azure z zasad opartych na trasach ani w inny sposób. Należy najpierw usunąć bramę. Po tym okresie adres IP i klucz wstępny nie będą zachowywane. Następnie można utworzyć nową bramę żądanego typu. 

Aby usunąć i utworzyć bramę, wykonaj następujące kroki:

1. Usuń wszystkie połączenia skojarzone z oryginalną bramą.
2. Usuń bramę przy użyciu Azure Portal, programu PowerShell lub klasycznego programu PowerShell: 
   * [Usuń bramę sieci wirtualnej za pomocą Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (wersja klasyczna)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Wykonaj kroki opisane w temacie [Tworzenie bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) w celu utworzenia nowej bramy żądanego typu i dokończ konfigurację sieci VPN.

> [!Note]
> Ten proces zajmie około 60 minut.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

