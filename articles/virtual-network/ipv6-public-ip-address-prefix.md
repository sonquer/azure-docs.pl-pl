---
title: Prefiks publicznego adresu IPv6 w usłudze Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat prefiksu publicznego adresu IPv6 w usłudze Azure Virtual Network.
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
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965169"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Prefiks publicznego adresu IPv6 (wersja zapoznawcza)

Na platformie Azure sieci wirtualne (VNet) i maszyny wirtualne (wirtualne) stosują się domyślnie, ponieważ nie mają łączności z Internetem. Możesz łatwo dodać połączenie internetowe IPv6 do modułów równoważenia obciążenia i maszyn wirtualnych platformy Azure z publicznymi adresami IPv6 uzyskanymi z platformy Azure.

Wszystkie publiczne adresy IP, które można zarezerwować, są skojarzone z wybranym przez Ciebie regionem platformy Azure i z subskrypcją platformy Azure. Możesz przenieść zastrzeżony (statyczny) adres IP IPv6 między dowolnymi modułami równoważenia obciążenia platformy Azure lub maszynami wirtualnymi w ramach subskrypcji. Możesz całkowicie usunąć skojarzenie publicznego adresu IP protokołu IPv6 i będzie on dostępny do użycia, gdy wszystko jest gotowe.

> [!WARNING]
> Należy zachować ostrożność, aby nie usuwać Twoich publicznych adresów IP przypadkowo. Usunięcie publicznego adresu IP spowoduje usunięcie go z subskrypcji i nie będzie można go odzyskać (nawet z pomocą techniczną platformy Azure).

Oprócz rezerwowania pojedynczych adresów IPv6 można zarezerwować ciągłe zakresy adresów IPv6 platformy Azure (nazywane prefiksem IP).  Podobnie jak poszczególne adresy IP, zarezerwowane prefiksy są skojarzone z wybranym przez Ciebie regionem platformy Azure i z subskrypcją platformy Azure. Przewidywalna, ciągły zakres adresów ma wiele użycia. Na przykład można znacznie uprościć *listy dozwolonych* IP aplikacji hostowanych przez platformę Azure przez firmę i klientów, ponieważ statyczne zakresy adresów IP mogą być łatwo zaprogramowane w zaporach lokalnych.  W razie potrzeby można utworzyć poszczególne publiczne adresy IP z prefiksu IP, a po usunięciu tych indywidualnych publicznych adresów IP są one *zwracane* do zarezerwowanego zakresu, aby można było użyć ich później. Wszystkie adresy IP w prefiksie IP są zarezerwowane do użytku wyłącznego do momentu usunięcia prefiksu.

> [!Important]
> Protokół IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Rozmiary prefiksów IPv6
Dostępne są następujące publiczne rozmiary prefiksów adresów IP:

-  Minimalny rozmiar prefiksu IPv6:/127 = 2 adresy
-  Maksymalny rozmiar prefiksu IPv6:/124 = 16 adresów

Rozmiar prefiksu jest określony jako rozmiar maski routingu między domenami (CIDR). Na przykład maska/128 reprezentuje pojedynczy adres IPv6, ponieważ adresy IPv6 składają się z 128 bitów.

## <a name="pricing"></a>Cennik
 
W przypadku kosztów związanych z korzystaniem z publicznych adresów IP platformy Azure, zarówno indywidualnych adresów IP, jak i zakresów adresów IP, zobacz [Cennik publicznego adresu IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Ograniczenia
Protokół IPv6 jest obsługiwany przez podstawowe publiczne adresy IP tylko z alokacją dynamiczną, co oznacza, że adres IPv6 zostanie zmieniony po usunięciu i ponownym wdrożeniu aplikacji (modułach wirtualnych lub usług równoważenia obciążenia) na platformie Azure. Publiczny adres IP w warstwie Standardowa obsługuje wyłącznie statyczną (zarezerwowaną) alokację, chociaż standardowe wewnętrzne moduły równoważenia obciążenia mogą również obsługiwać dynamiczną alokację z poziomu podsieci, do której są przypisane.  

Najlepszym rozwiązaniem jest użycie standardowych publicznych adresów IP i standardowych modułów równoważenia obciążenia dla aplikacji IPv6.

## <a name="next-steps"></a>Następne kroki
- Zarezerwuj publiczny [prefiks adresu IPv6](ipv6-reserve-public-ip-address-prefix.md).
- Dowiedz się więcej o [adresach IPv6](ipv6-overview.md).
- Dowiedz się więcej na temat [tworzenia i używania publicznych adresów IP](virtual-network-public-ip-address.md) (protokołów IPv4 i IPv6) na platformie Azure.
