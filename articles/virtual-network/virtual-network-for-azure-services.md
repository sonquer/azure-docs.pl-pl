---
title: Sieć wirtualna dla usług platformy Azure
titlesuffix: Azure Virtual Network
description: Poznaj zalety wdrażania zasobów w sieci wirtualnej. Zasoby w sieciach wirtualnych mogą komunikować się ze sobą i zasobami lokalnymi bez ruchu przechodzącego przez Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048825"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integracja sieci wirtualnej dla usług platformy Azure

Integracja usług platformy Azure z usługą Azure Virtual Network umożliwia prywatnym dostęp do usługi z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.
Usługi platformy Azure w sieci wirtualnej można zintegrować z następującymi opcjami:
- Wdrażanie dedykowanych wystąpień usługi w sieci wirtualnej. Usługi mogą być następnie dostępne do prywatnego dostępu w ramach sieci wirtualnej i z sieci lokalnych.
- Korzystanie z [prywatnego linku](../private-link/private-link-overview.md) w celu uzyskania dostępu do prywatnego określonego wystąpienia usługi z sieci wirtualnej i sieci lokalnych.

Możesz również uzyskać dostęp do usługi za pomocą publicznych punktów końcowych, rozszerzając sieć wirtualną do usługi za pośrednictwem [punktów końcowych usługi](virtual-network-service-endpoints-overview.md). Punkty końcowe usługi umożliwiają zabezpieczenie zasobów usługi w sieci wirtualnej.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Wdrażanie usług platformy Azure w sieciach wirtualnych

Podczas wdrażania dedykowanych usług platformy Azure w [sieci wirtualnej](virtual-networks-overview.md)można komunikować się z zasobami usługi prywatnie, za pomocą prywatnych adresów IP.

![Usługi wdrożone w sieci wirtualnej](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Wdrażanie usług w ramach sieci wirtualnej zapewnia następujące możliwości:

- Zasoby w ramach sieci wirtualnej mogą komunikować się ze sobą prywatnie, za pomocą prywatnych adresów IP. Przykładowo bezpośrednie przesyłanie danych między usługą HDInsight i SQL Server uruchomione na maszynie wirtualnej w sieci wirtualnej.
- Zasoby lokalne mogą uzyskiwać dostęp do zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP za pośrednictwem [sieci VPN typu lokacja-lokacja (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) lub [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sieci wirtualne mogą być połączone za pomocą [komunikacji równorzędnej](virtual-network-peering-overview.md) , aby umożliwić komunikację między sieciami wirtualnymi przy użyciu prywatnych adresów IP.
- Wystąpienia usługi w sieci wirtualnej są zwykle w pełni zarządzane przez usługę platformy Azure. Obejmuje to monitorowanie kondycji zasobów i skalowanie z obciążeniem.
- Wystąpienia usługi są wdrażane w podsieci w sieci wirtualnej. Przychodzący i wychodzący dostęp do sieci dla podsieci musi być otwarty za pomocą [sieciowych grup zabezpieczeń](security-overview.md#network-security-groups), zgodnie ze wskazówkami dostarczonymi przez usługę.
- Niektóre usługi również nakładają ograniczenia dotyczące podsieci, w których są wdrażane, ograniczają stosowanie zasad, trasy lub łączenie maszyn wirtualnych i zasobów usług w tej samej podsieci. Sprawdź każdą z usług zgodnie z określonymi ograniczeniami, ponieważ mogą one ulec zmianie w czasie. Przykładami takich usług są Azure NetApp Files, dedykowany moduł HSM Azure Container Instances, App Service. 
- Opcjonalnie usługi mogą wymagać [delegowanej podsieci](virtual-network-manage-subnet.md#add-a-subnet) jako jawnego identyfikatora, który może hostować określoną usługę w podsieci. Dzięki delegowaniu usługi uzyskują jawne uprawnienia do tworzenia zasobów specyficznych dla usługi w podsieci delegowanej.
- Zapoznaj się z przykładem odpowiedzi interfejsu API REST w [sieci wirtualnej z delegowaną podsiecią](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Kompleksowa Lista usług, które korzystają z delegowanego modelu podsieci, można uzyskać za pośrednictwem interfejsu API [dostępne delegacje](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) .

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Usługi, które można wdrożyć w sieci wirtualnej

|Kategoria|Usługa| Podsieć dedykowana
|-|-|-|
| Compute | Maszyny wirtualne: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Usługa w chmurze](https://msdn.microsoft.com/library/azure/jj156091): tylko sieć wirtualna (klasyczna)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nie <br/> Nie <br/> Nie <br/> No²
| Sieć | [Application Gateway — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Wirtualne urządzenia sieciowe](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Yes <br/> Yes <br/> Yes <br/> Nie
|Dane|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie zarządzane usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Yes <br/> Yes <br/> 
|Analiza | [Usługa Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Tożsamość | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nie <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Wystąpienie kontenera platformy Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Aparat Azure Container Service](https://github.com/Azure/acs-engine) z [dodatkiem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) platformy Azure Virtual Network CNI|No²<br/> Yes <br/><br/> Nie
| Sieć Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Środowisko usługi App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Yes <br/> Yes <br/> Yes
| Przez | [Dedykowany moduł HSM platformy Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Yes <br/> Yes <br/>
| | |

"dedykowany" oznacza, że w tej podsieci można wdrożyć tylko zasoby określone dla usługi i nie można ich łączyć z maszyną wirtualną/VMSSs klienta <br/> zaleca się, aby korzystać z tych usług w dedykowanej podsieci, ale nie do obowiązkowego wymagania narzuconego przez usługę.
