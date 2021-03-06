---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 31a9da0678f602afcc117e5b2f7927af379da668
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469451"
---
Usługa Azure Managed disks obecnie oferuje cztery typy dysków, a każdy typ jest skierowany do określonych scenariuszy klientów.

## <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie dysków twardych, dysków półprzewodnikowych (SSD), standardowego dysku SSD i standardowych dysków twardych na potrzeby dysków zarządzanych, które ułatwią podjęcie decyzji o tym, czego używać.

|   | Dysk w warstwie Ultra   | Premium, SSD   | Standardowa, SSD   | Standardowa, dysk twardy   |
|---------|---------|---------|---------|---------|
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążenia intensywnie korzystające z operacji we/wy, takie jak [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), baz danych najwyższej warstwy (na przykład SQL, Oracle) i innych obciążeń intensywnie korzystających z transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery internetowe, aplikacje dla przedsiębiorstw używane w niewielkim stopniu oraz tworzenie i testowanie rozwiązań   |Kopia zapasowa, rozwiązania niekrytyczne, rzadki dostęp   |
|Maksymalny rozmiar dysku   |65 536 gibibajtów (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maksymalna przepustowość   |2000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maks. liczba operacji we/wy na sekundę   |160 000    |20,000   |6,000   |2000   |

## <a name="ultra-disk"></a>Dysk w warstwie Ultra

Dyski platformy Azure w warstwie Ultra oferują magazyn danych na dysku z wysoką przepływnością, dużą liczbą operacji we/wy na sekundę i stałym małym opóźnieniem dla maszyn wirtualnych usługi Azure IaaS. Niektóre dodatkowe zalety funkcji Ultra disks to możliwość dynamicznego zmieniania wydajności dysku wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji. Dysków w warstwie Ultra można używać tylko jako dysków danych. Zalecamy używanie dysków SSD w warstwie Premium jako dysków systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizacji Ultra Disk można niezależnie skonfigurować pojemność i wydajność dysku. Niezwykle dyski są dostępne w kilku stałych rozmiarach, z przedziału od 4 GiB do 64 TiB i oferują elastyczny model konfiguracji wydajności, który pozwala niezależnie konfigurować operacje we/wy i przepływność.

Niektóre kluczowe możliwości Ultra disks to:

- Pojemność dysku: pojemność Ultra disks z zakresu od 4 GiB do 64 TiB.
- Liczba operacji we/wy na sekundę: niezwykle dyski obsługują limity IOPS 300 operacji we/wy na sekundę i maksymalnie 160 K operacji we/wy na dysku. Aby osiągnąć liczbę operacji we/wy, która została zainicjowana, upewnij się, że wybrane operacje we/wy na dysku są mniejsze niż limit liczby IOPS maszyny wirtualnej. Minimalna liczba operacji we/wy na dysk wynosi 2 IOPS/GiB, a ogólna podstawowa linia bazowa wynosi 100 operacji we/wy. Na przykład jeśli masz 4 GiB Ultra Disk, będziesz mieć co najmniej 100 operacji we/wy, a nie osiem operacji we/wy na sekundę.
- Przepływność dysku: przy użyciu Ultra dysków limit przepływności pojedynczego dysku to 256 KiB/s dla każdej z zainicjowanych operacji we/wy, maksymalnie 2000 megabajtów na sekundę (MB/s = 10 ^ 6 b/s). Minimalna przepływność na dysk to 4KiB/s dla każdej z zainicjowanych operacji we/wy z co najmniej 1 MB/s.
- Ultra disks obsługuje dostosowanie atrybutów wydajności dysku (IOPS i przepływności) w czasie wykonywania bez odłączania dysku od maszyny wirtualnej. Po wystawieniu operacji zmiany rozmiaru dysku na dysku może upłynąć do czasu, aż zmiana zacznie obowiązywać. Przekroczono limit czterech operacji zmiany rozmiaru wydajności w oknie 24-godzinnym. Istnieje możliwość, że operacja zmiany rozmiaru wydajności nie powiedzie się z powodu braku wydajności przepustowości.

### <a name="disk-size"></a>Rozmiar dysku

|Rozmiar dysku (GiB)  |Limit operacji we/wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9600         |2000         |
|64     |19 200         |2000         |
|128     |38 400         |2000         |
|256     |76 800         |2000         |
|512     |80,000         |2000         |
|1024-65536 (rozmiary w tym zakresie zwiększają się w przyrostach 1 TiB)     |160 000         |2000         |

### <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]