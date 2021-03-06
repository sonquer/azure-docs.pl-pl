---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 28724f85ada989cbe3ce754418fb781bb0468de4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77466049"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limity sieci — Azure Resource Manager następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych za pomocą **Azure Resource Manager** na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Ostatnio zwiększono wszystkie domyślne limity do ich maksymalnych limitów. Jeśli nie ma żadnej kolumny maksymalnego limitu, zasób nie ma dopuszczalnych limitów. Jeśli te limity zostały zwiększone przez pomoc techniczną w przeszłości i nie widzisz zaktualizowanych limitów w poniższych tabelach, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../articles/azure-resource-manager/templates/error-resource-quota.md) .

| Zasób | Domyślny/maksymalny limit | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3000 |
| Wirtualne sieci równorzędne dla sieci wirtualnej |500 |
| [Bramy sieci wirtualnej (bramy sieci VPN) na sieć wirtualną](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Bramy sieci wirtualnej (ExpressRoute Gateway) na sieć wirtualną](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Serwery DNS na sieć wirtualną |20 |
| Prywatne adresy IP na sieć wirtualną |65 536 |
| Prywatne adresy IP na interfejs sieciowy |256 |
| Prywatne adresy IP na maszynę wirtualną |256 |
| Publiczne adresy IP na interfejs sieciowy |256 |
| Publiczne adresy IP na maszynę wirtualną |256 |
| [Współbieżne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Karty interfejsu sieciowego |65 536 |
| Grupy zabezpieczeń sieci |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| Adresy IP i zakresy określone dla źródła lub miejsca docelowego w grupie zabezpieczeń |4,000 |
| Grupy zabezpieczeń aplikacji |3000 |
| Grupy zabezpieczeń aplikacji na konfigurację IP, na kartę sieciową |20 |
| Konfiguracje protokołu IP na grupę zabezpieczeń aplikacji |4,000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń sieciowej grupy zabezpieczeń |100 |
| Tabele tras zdefiniowane przez użytkownika |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |
| Certyfikaty główne punkt-lokacja na platformie Azure VPN Gateway |20 |
| Kurki sieci wirtualnej |100 |
| Naciśnij KOLEJno pozycje konfiguracje interfejsu sieciowego dla sieci wirtualnej |100 |

#### <a name="publicip-address"></a>Limity publicznych adresów IP
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP<sup>1</sup> | 10 dla warstwy Podstawowa. | Skontaktuj się z pomocą techniczną. |
| Statyczne publiczne adresy IP<sup>1</sup> | 10 dla warstwy Podstawowa. | Skontaktuj się z pomocą techniczną. |
| Standardowe publiczne adresy IP<sup>1</sup> | 10 | Skontaktuj się z pomocą techniczną. |
| Prefiksy publicznego adresu IP | ograniczone przez liczbę standardowych publicznych adresów IP w ramach subskrypcji | Skontaktuj się z pomocą techniczną. |
| Długość prefiksu publicznego adresu IP | /28 | Skontaktuj się z pomocą techniczną. |

<sup>1</sup> Domyślne limity dla publicznych adresów IP różnią się w zależności od typu kategorii oferty, takiego jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistym użyciem, dostawca usług kryptograficznych. Na przykład domyślna wartość dla subskrypcji Umowa Enterprise to 1000.

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

**usługa Load Balancer w warstwie Standardowa**

| Zasób                                | Domyślny/maksymalny limit         |
|-----------------------------------------|-------------------------------|
| Moduły równoważenia obciążenia                          | 1000                         |
| Reguły na zasób                      | 1,500                         |
| Reguły na kartę sieciową (między wszystkimi adresami IP na karcie sieciowej) | 300                           |
| Konfiguracje adresów IP frontonu              | 600                           |
| Rozmiar puli zaplecza                       | Konfiguracje protokołu IP 1 000, pojedynczej sieci wirtualnej |
| Porty wysokiej dostępności                 | 1 na wewnętrzny fronton       |
| Reguły ruchu wychodzącego na Load Balancer         | 20                            |


**Load Balancer podstawowa**

| Zasób                                | Domyślny/maksymalny limit        |
|-----------------------------------------|------------------------------|
| Moduły równoważenia obciążenia                          | 1000                        |
| Reguły na zasób                      | 250                          |
| Reguły na kartę sieciową (między wszystkimi adresami IP na karcie sieciowej) | 300                          |
| Konfiguracje adresów IP frontonu              | 200                          |
| Rozmiar puli zaplecza                       | Konfiguracje protokołu IP 300, zestaw o pojedynczej dostępności |
| Zestawy dostępności na Load Balancer     | 150                          |

#### <a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu **klasycznego** modelu wdrażania na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |100 |100 |
| Lokalne lokacje sieciowe |20 |50 |
| Serwery DNS na sieć wirtualną |20 |20 |
| Prywatne adresy IP na sieć wirtualną |4 096 |4 096 |
| Współbieżne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli |500 000, do 1 000 000 w przypadku co najmniej dwóch kart sieciowych. |500 000, do 1 000 000 w przypadku co najmniej dwóch kart sieciowych. |
| Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) |200 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |1000 |
| Tabele tras zdefiniowane przez użytkownika |200 |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |400 |
| Publiczne adresy IP (dynamiczne) |500 |500 |
| Zastrzeżone publiczne adresy IP |500 |500 |
| Publiczne adresy VIP na wdrożenie |5 |Kontakt z pomocą techniczną |
| Prywatne wirtualne adresy IP (wewnętrzne równoważenie obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu (ACL) punktu końcowego |50 |50 |
