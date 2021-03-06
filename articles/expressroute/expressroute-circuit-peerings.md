---
title: 'Azure ExpressRoute: obwody i Komunikacja równorzędna'
description: Ta strona zawiera omówienie obwodów usługi ExpressRoute i routing domen/komunikacji równorzędnej.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647139"
---
# <a name="expressroute-circuits-and-peering"></a>Obwody usługi ExpressRoute i komunikacji równorzędnej

Obwody usługi ExpressRoute z infrastruktury lokalnej firmą Microsoft za pośrednictwem dostawcy łączności. Ten artykuł pomaga zrozumieć obwodów usługi ExpressRoute i routing domen/komunikacji równorzędnej. Na poniższej ilustracji przedstawiono logiczną reprezentację łączność sieci WAN, a firmy Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Publiczna Komunikacja równorzędna Azure jest przestarzała i nie jest dostępna dla nowych obwodów usługi ExpressRoute. Nowe obwody obsługują komunikację równorzędną firmy Microsoft i prywatną komunikację równorzędną.  
>

## <a name="circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje połączenie logiczne między lokalną infrastrukturą i usługami chmurowymi firmy Microsoft za pośrednictwem dostawcy łączności. Może zamówić łączność obejmującą wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tej samej lub różnych regionach i mogą być połączone z lokalnym za pośrednictwem połączenia różnych dostawców.

Obwody usługi ExpressRoute nie są mapowane do żadnych jednostek fizycznych. Obwód jest unikatowo identyfikowana przez standardowy identyfikator GUID wywołać jako klucz usługi (s-key). Klucz usługi jest tylko część informacji wymienianych między firmą Microsoft, dostawca połączenia, a. Klawisz s nie jest wpis tajny ze względów bezpieczeństwa. Istnieje mapowanie 1:1 między obwodu usługi ExpressRoute i klawisz s.

Obwodów usługi ExpressRoute może zawierać dwóch niezależnych komunikacji równorzędnej: prywatnej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft. Istniejące obwodów usługi ExpressRoute może zawierać trzech komunikacji równorzędnej: Azure publicznej, prywatnej platformy Azure i Microsoft. Każdego wystąpienia komunikacji równorzędnej jest parę niezależnych sesje protokołu BGP, każde z nich nadmiarowo skonfigurowany w celu zapewnienia wysokiej dostępności. Brak 1: n (1 < = N < = 3) mapowanie między obwodu usługi ExpressRoute i domeny routingu. Obwód usługi ExpressRoute może mieć jeden, dwa lub wszystkie trzy komunikacje równorzędne włączone na obwód usługi ExpressRoute.

Każdy obwód o stałej szerokości pasma (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) i jest mapowany do dostawcy łączności i lokalizacji komunikacji równorzędnej. Przepustowość, którą wybierzesz jest współużytkowany przez wszystkie komunikacja równorzędna obwodu

### <a name="quotas"></a>Przydziały, limity i ograniczenia

Domyślne limity przydziału i limity dotyczą każdego obwód usługi ExpressRoute. Zapoznaj się [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) aktualne informacje dotyczące limitów przydziału.

## <a name="routingdomains"></a>Komunikacji równorzędnej usługi ExpressRoute

Obwód usługi ExpressRoute ma wiele routingu domeny/komunikację równorzędną skojarzone z nią: Azure prywatnej publicznych, platformy Azure i firmy Microsoft. Każdego wystąpienia komunikacji równorzędnej jest skonfigurowane tak samo w pary routerów (aktywny / aktywny lub udostępnianie obciążenia konfiguracji) wysokiej dostępności. Usługi platformy Azure są klasyfikowane jako *publicznej platformy Azure* i *Azure prywatne* do reprezentowania schematów adresowania IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Prywatną komunikację równorzędną Azure

Usługi Azure compute usług, a mianowicie maszyn wirtualnych (IaaS) i usług w chmurze (PaaS), które są wdrażane w ramach sieci wirtualnej mogą być połączone za pośrednictwem prywatnej komunikacji równorzędnej domeny. Prywatnej komunikacji równorzędnej domeny jest uważana za zaufane rozszerzenie sieci podstawowej w Microsoft Azure. Można skonfigurować dwukierunkowej łączności między sieciami wirtualnymi platformy Azure (Vnet) i sieci podstawowej. Tę komunikację równorzędną pozwala nawiązać połączenie z maszynami wirtualnymi i usługami bezpośrednio na ich prywatnych adresów IP w chmurze.  

Możesz połączyć więcej niż jednej sieci wirtualnej do prywatnej komunikacji równorzędnej domeny. Przegląd [z często Zadawanymi pytaniami](expressroute-faqs.md) informacji na temat limity i ograniczenia. Możesz odwiedzić stronę [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) aktualne informacje dotyczące limitów.  Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje na temat konfiguracji routingu.

### <a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Połączenie z programem Microsoft Usługi online (pakietem Office 365 i usługami Azure PaaS Services) odbywa się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Włączyliśmy dwukierunkowej łączności między sieci WAN, a Microsoft usługi w chmurze za pośrednictwem domeny routingu komunikacji równorzędnej firmy Microsoft. Musisz połączyć się z usługami chmurowymi firmy Microsoft tylko za pośrednictwem publiczne adresy IP, które są własnością użytkownik lub jego dostawca łączności i muszą być zgodne ze wszystkimi zdefiniowanymi regułami. Aby uzyskać więcej informacji, zobacz [wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) strony.

Zobacz [z często Zadawanymi pytaniami](expressroute-faqs.md) Aby uzyskać więcej informacji na temat obsługiwanych usług, koszty i szczegółów konfiguracji. Zobacz [lokalizacji usługi ExpressRoute](expressroute-locations.md) informacje dotyczące listy dostawców łączności oferty pomocy technicznej komunikacji równorzędnej firmy Microsoft.

## <a name="peeringcompare"></a>Porównanie komunikacji równorzędnej

W poniższej tabeli porównano trzy komunikacji równorzędnej:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Możesz włączyć co najmniej jedną z domen routingu w ramach obwodu usługi ExpressRoute. Można mieć wszystkich domen routingu umieszczanie na tej samej sieci VPN, aby połączyć je w pojedynczej domeny routingu. Można również wprowadzić je w różnych domen routingu, podobne do diagramu. Zalecana konfiguracja to, że prywatnej komunikacji równorzędnej jest podłączony bezpośrednio do sieci podstawowej, oraz publicznej komunikacji równorzędnej oraz komunikacji równorzędnej firmy Microsoft, które są podłączone do sieci Obwodowej.

Każdego wystąpienia komunikacji równorzędnej wymaga oddzielnych sesji protokołu BGP (jedną parę dla każdego typu komunikacji równorzędnej). Pary sesji protokołu BGP, podaj link o wysokiej dostępności. Jeśli łączysz się za pośrednictwem dostawców łączności 2 warstwy, ponosisz odpowiedzialność za Konfigurowanie routingu oraz zarządzanie nim. Dowiedz się więcej, przeglądając [przepływy pracy](expressroute-workflows.md) dotyczące konfigurowania usługi ExpressRoute.

## <a name="health"></a>Kondycja usługi ExpressRoute

Obwody usługi ExpressRoute mogą być monitorowane, aby zapewnić dostępność, łączność z sieciami wirtualnymi i przy użyciu wykorzystanie przepustowości [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM monitoruje kondycję prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej firmy Microsoft. Zapoznaj się z naszym [wpis](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* Znajdź dostawcę usługi. Zobacz [dostawcy i lokalizacje usługi ExpressRoute](expressroute-locations.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodów usługi ExpressRoute i zarządzanie nimi](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie routingu (komunikacji równorzędnej) dla obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
