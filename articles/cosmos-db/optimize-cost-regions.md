---
title: Optymalizacja kosztów wdrożeń wieloregionowych w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrożeń wieloregionowych w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753318"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizuj koszt dla wieloregionu w Azure Cosmos DB

Regiony na koncie usługi Azure Cosmos można dodawać i usuwać w dowolnym momencie. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos jest zarezerwowana w każdym regionie skojarzonym z Twoim kontem. Jeśli przepływność jest zainicjowana na godzinę, czyli sumą jednostek RU/s skonfigurowanych dla wszystkich baz danych i kontenerów dla konta usługi Azure Cosmos, jest `T`, a liczba regionów platformy Azure skojarzonych z kontem bazy danych jest `N`, łączna zainicjowana Przepływność dla konta usługi Cosmos dla danej godziny jest równa:

1. `T x N RU/s`, jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z jednym regionem zapisu. 

1. `T x (N+1) RU/s`, jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane ze wszystkimi regionami, które mogą przetwarzać operacje zapisu. 

Elastyczna przepływność przy użyciu jednego regionu zapisu kosztów $0.008/godz. na 100 RU/s i elastyczna przepływność z wieloma regionami z możliwością zapisu — koszt $0,016;/za godzinę na 100 RU/s. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Koszt wielu regionów zapisu

W systemie z wieloma wzorcami dostępność sieci jednostek ru dla operacji zapisu zwiększa się `N` razy, gdy `N` jest liczbą regionów zapisu. W przeciwieństwie do zapisu w pojedynczym regionie, każdy region jest teraz zapisywalny i powinien obsługiwać rozwiązywanie konfliktów. Zwiększona ilość obciążeń dla autorów. W punkcie planowania kosztów w celu przeprowadzenia `M` RU/s zapisów na całym świecie należy udostępnić `RUs` M na poziomie kontenera lub bazy danych. Następnie można dodać tyle regionów, ile chcesz, i użyć ich do zapisu, aby wykonać `M` RU na całym świecie zapisów. 

### <a name="example"></a>Przykład

Rozważmy, że masz kontener w regionie zachodnie stany USA o przepływności 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony — Wschodnie stany USA, Europa Północna i Azja Wschodnia, z których każdy ma ten sam magazyn i przepływność, i chcesz mieć możliwość zapisu w kontenerach we wszystkich czterech regionach z aplikacji rozproszonej globalnie. Łączny rachunek miesięczny (przy założeniu 31 dni) w miesiącu jest następujący:

|**Element**|**Użycie (co miesiąc)**|**Transmisji**|**Koszt miesięczny**|
|----|----|----|----|
|Opłaty za przepływność dla kontenera w regionie zachodnie stany USA (wiele regionów zapisu) |10 000 jednostek RU/s * 24 * 31 |$0,016 za 100 RU/s na godzinę |$1 190,40 |
|Rachunek przepływności dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia (wiele regionów zapisu) |(3 + 1) * 10 000 jednostek RU/s * 24 * 31 |$0,016 za 100 RU/s na godzinę |$4 761,60 |
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA |1 TB (lub 1 024 GB) |$0,25/GB |$256 |
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia |3 * 1 TB (lub 3 072 GB) |$0,25/GB |$768 |
|**Ogólnego**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zwiększenie wykorzystania przepływności na podstawie poszczególnych regionów

Jeśli masz niewydajne użycie, na przykład co najmniej jeden obszar lub nadmiernie wykorzystywanych regionów, możesz wykonać następujące kroki, aby zwiększyć wykorzystanie przepływności:  

1. Upewnij się, że w regionie zapisu została najpierw zoptymalizowana przepływność (jednostek ru), a następnie ustaw maksymalne użycie jednostek RU w regionach odczytu przy użyciu źródła zmian z regionu do odczytu itp. 

2. Wiele regionów zapisu odczytuje i zapisuje dane można skalować we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. 

3. Monitoruj działanie w regionach i możesz dodawać i usuwać regiony na żądanie, aby skalować przepływność odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)

