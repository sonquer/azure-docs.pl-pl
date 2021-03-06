---
title: Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks
description: Typowe polecenia programu PowerShell umożliwiające rozpoczęcie tworzenia sieci wirtualnej i skojarzonych z nią zasobów maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 3abde706ddff297094c7fbb1579b534894b349d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032919"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks

Jeśli chcesz utworzyć maszynę wirtualną, musisz utworzyć [sieć wirtualną](../../virtual-network/virtual-networks-overview.md) lub zapoznać się z istniejącą siecią wirtualną, w której można dodać maszynę wirtualną. Zazwyczaj podczas tworzenia maszyny wirtualnej należy rozważyć utworzenie zasobów opisanych w tym artykule.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Niektóre zmienne mogą być przydatne w przypadku uruchamiania więcej niż jednego polecenia w tym artykule:

- $location — lokalizacja zasobów sieciowych. Możesz użyć [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) , aby znaleźć [region geograficzny](https://azure.microsoft.com/regions/) , który działa dla Ciebie.
- $myResourceGroup — nazwa grupy zasobów, w której znajdują się zasoby sieciowe.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji podsieci |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix XX. X. X. X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typowa sieć może mieć podsieć dla [modułu równoważenia obciążenia](../../load-balancer/load-balancer-internet-overview.md) połączonego z Internetem oraz oddzielną podsieć dla [wewnętrznego modułu równoważenia obciążenia](../../load-balancer/load-balancer-internal-overview.md). |
| Tworzenie sieci wirtualnej |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $myResourceGroup lokalizacji $Location-AddressPrefix XX. X. X. X/XX-Subnet $subnet 1, $subnet 2 |
| Testowanie dla unikatowej nazwy domeny |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -wartość domainnamelabel "myDNS" — Lokalizacja $Location<BR><BR>Możesz określić nazwę domeny DNS dla [zasobu publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), co spowoduje utworzenie mapowania domainname.Location.cloudapp.Azure.com na publiczny adres IP na serwerach DNS zarządzanych przez platformę Azure. Nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak musi być literą lub cyfrą, a nazwa domeny musi być unikatowa w ramach swojej lokalizacji platformy Azure. Jeśli zwracana jest **wartość true** , proponowana nazwa jest globalnie unikatowa. |
| Tworzenie publicznego adresu IP |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Publiczny adres IP używa wcześniej przetestowanej nazwy domeny i jest używany przez konfigurację frontonu modułu równoważenia obciążenia. |
| Tworzenie konfiguracji adresu IP frontonu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $PIP<BR><BR>Konfiguracja frontonu obejmuje publiczny adres IP, który został wcześniej utworzony dla przychodzącego ruchu sieciowego. |
| Tworzenie puli adresów zaplecza |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Zapewnia wewnętrzne adresy wewnętrznej bazy danych modułu równoważenia obciążenia, do których dostęp uzyskuje się za pomocą interfejsu sieciowego. |
| Tworzenie sondy |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "RequestPath" healthProbe. aspx "-Protocol HTTP-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Zawiera sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza. |
| Tworzenie reguły równoważenia obciążenia |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Zawiera reguły przypisujące port publiczny modułu równoważenia obciążenia do portu w puli adresów zaplecza. |
| Tworzenie reguły NAT dla ruchu przychodzącego |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-elementu frontendipconfiguration $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Zawiera reguły mapowania portu publicznego modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza. |
| Tworzenie modułu równoważenia obciążenia |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer" $location-elementu frontendipconfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-probe $healthProbe |
| Tworzenie interfejsu sieciowego |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" — Lokalizacja $Location-PrivateIpAddress XX. X. X. X-Subnet $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Utwórz interfejs sieciowy przy użyciu publicznego adresu IP i podsieci sieci wirtualnej, która została wcześniej utworzona. |

## <a name="get-information-about-network-resources"></a>Uzyskaj informacje o zasobach sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetl listę sieci wirtualnych |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich sieci wirtualnych w grupie zasobów. |
| Uzyskaj informacje o sieci wirtualnej |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Wyświetlanie listy podsieci w sieci wirtualnej |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Pobierz informacje o podsieci |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $VNET<BR><BR>Pobiera informacje o podsieci w określonej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Wyświetlanie listy adresów IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę publicznych adresów IP w grupie zasobów. |
| Wyświetlanie listy modułów równoważenia obciążenia |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich modułów równoważenia obciążenia w grupie zasobów. |
| Wyświetl listę interfejsów sieciowych |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla wszystkie interfejsy sieciowe w grupie zasobów. |
| Uzyskaj informacje o interfejsie sieciowym |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Pobiera informacje o konkretnym interfejsie sieciowym. |
| Pobieranie konfiguracji adresu IP interfejsu sieciowego |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-interfejsu sieciowego $nic<BR><BR>Pobiera informacje o konfiguracji protokołu IP określonego interfejsu sieciowego. Wartość $nic reprezentuje obiekt zwrócony przez Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Zarządzanie zasobami sieciowymi

| Zadanie | Polecenie |
| ---- | ------- |
| Dodawanie podsieci do sieci wirtualnej |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Dodaje podsieć do istniejącej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Usuwanie sieci wirtualnej |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określoną sieć wirtualną z grupy zasobów. |
| Usuwanie interfejsu sieciowego |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony interfejs sieciowy z grupy zasobów. |
| Usuwanie modułu równoważenia obciążenia |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony moduł równoważenia obciążenia z grupy zasobów. |
| Usuwanie publicznego adresu IP |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony publiczny adres IP z grupy zasobów. |

## <a name="next-steps"></a>Następne kroki
* Użyj interfejsu sieciowego, który został właśnie utworzony podczas [tworzenia maszyny wirtualnej](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dowiedz się, jak [utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

