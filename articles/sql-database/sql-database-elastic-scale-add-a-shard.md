---
title: Dodawanie fragmentu za pomocą narzędzi elastycznych baz danych
description: Jak używać interfejsów API skalowania elastycznego do dodawania nowych fragmentów do zestawu fragmentu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823710"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Dodawanie fragmentu przy użyciu narzędzi Elastic Database

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Aby dodać fragmentu dla nowego zakresu lub klucza

Aplikacje często muszą dodać nowe fragmentów do obsługi danych, które są oczekiwane z nowych kluczy lub zakresów kluczy, dla mapy fragmentu, która już istnieje. Na przykład aplikacja podzielonej na fragmenty według identyfikatora dzierżawy może potrzebować nowego fragmentu dla nowej dzierżawy, a dane podzielonej na fragmenty miesięcznie mogą potrzebować nowego fragmentu, który został udostępniony przed rozpoczęciem każdego nowego miesiąca.

Jeśli nowy zakres wartości klucza nie jest już częścią istniejącego mapowania, można łatwo dodać nowy fragmentu i skojarzyć nowy klucz lub zakres z tym fragmentu.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Przykład: Dodawanie elementu fragmentu i jego zakresu do istniejącej mapy fragmentu

Ten przykład używa TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) Methods i tworzy wystąpienie ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) określonej. W poniższym przykładzie baza danych o nazwie **sample_shard_2** i wszystkie niezbędne obiekty schematu wewnątrz niej zostały utworzone w celu przechowania zakresu [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

W przypadku wersji .NET można także użyć programu PowerShell jako alternatywy do utworzenia nowego menedżera mapy fragmentu. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Aby dodać fragmentu dla pustej części istniejącego zakresu

W pewnych okolicznościach można już zmapować zakres na fragmentu i częściowo wypełnił go danymi, ale teraz chcesz, aby nadchodzące dane były kierowane do innego fragmentu. Na przykład fragmentu według zakresu dni i przydzielono już 50 dni do fragmentu, ale dnia 24, chcesz, aby przyszłe dane były umieszczane w innym fragmentu. [Narzędzie do dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md) Elastic Database może wykonać tę operację, ale jeśli przenoszenie danych nie jest konieczne (na przykład dane dotyczące zakresu dni [25, 50), które jest, dnia 25 włącznie z wyłącznym zakresem 50, jeszcze nie istnieją), można wykonać je w całości przy użyciu Interfejsy API zarządzania mapami fragmentu bezpośrednio.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Przykład: dzielenie zakresu i przypisywanie pustej części do nowo dodanej fragmentu

Utworzono bazę danych o nazwie "sample_shard_2" i wszystkie niezbędne obiekty schematu wewnątrz niej.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Ważne**: Użyj tej techniki tylko wtedy, gdy masz pewność, że zakres zaktualizowanego mapowania jest pusty.  Powyższe metody nie sprawdzają danych dla przenoszonego przedziału, dlatego najlepiej dołączyć sprawdzenia w kodzie.  Jeśli istnieją wiersze w przenoszeniu przedziału, rzeczywista Dystrybucja danych nie będzie zgodna ze zaktualizowaną mapą fragmentu. Użyj [Narzędzia Split-Merge](sql-database-elastic-scale-overview-split-and-merge.md) do wykonania operacji zamiast tego w takich przypadkach.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
