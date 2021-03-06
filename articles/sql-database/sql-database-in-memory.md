---
title: Technologie w pamięci
description: Azure SQL Database technologie w pamięci znacznie poprawiają wydajność obciążeń transakcyjnych i analitycznych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810262"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optymalizowanie wydajności przy użyciu technologii w pamięci w SQL Database

Technologie w pamięci w Azure SQL Database umożliwiają zwiększenie wydajności aplikacji i potencjalnie zmniejszenie kosztów związanych z bazą danych. 

## <a name="when-to-use-in-memory-technologies"></a>Kiedy używać technologii w pamięci

Korzystając z technologii znajdujących się w pamięci w Azure SQL Database, można osiągnąć ulepszenia wydajności przy użyciu różnych obciążeń:

- **Transakcyjna** (przetwarzanie transakcyjne online (OLTP)), w których większość żądań odczyta lub aktualizuje mniejszy zestaw danych (na przykład CRUD operacji).
- Analiza **analityczna** (Online Analytical Processing (OLAP)), w której większość zapytań ma skomplikowane obliczenia dla celów raportowania, z określoną liczbą zapytań, które ładują i dołączają dane do istniejących tabel (nazywanych zbiorczo) lub usuwania danych z tabele. 
- **Mieszany** (transakcja hybrydowa/analityczna przetwarzania (HTAP)), w którym zapytania OLTP i OLAP są wykonywane w tym samym zestawie danych.

Technologie w pamięci mogą zwiększyć wydajność tych obciążeń, zachowując dane, które powinny być przetwarzane w pamięci, przy użyciu natywnej kompilacji zapytań lub przetwarzania zaawansowanego, takiego jak przetwarzanie wsadowe i instrukcje SIMD, które są dostępne w podstawowy sprzęt. 

## <a name="overview"></a>Omówienie

Azure SQL Database ma następujące technologie w pamięci:
- Przetwarzanie wsadowe *[w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* zwiększa liczbę transakcji na sekundę i zmniejsza opóźnienia w przetwarzaniu transakcji. Scenariusze, które korzystają z OLTP w pamięci, to: przetwarzanie transakcji o wysokiej przepływności, takie jak handel i granie, pozyskiwanie danych z zdarzeń lub urządzeń IoT, buforowanie, ładowanie danych i tymczasowe tabele i zmienne tabeli.
- *Klastrowane indeksy magazynu kolumn* zmniejszają rozmiar magazynu (do 10 razy) i zwiększają wydajność raportów i kwerend analitycznych. Można jej używać z tabelami faktów w składnic danych, aby zmieścić więcej danych w bazie danych i zwiększyć wydajność. Ponadto można użyć jej z danymi historycznymi w operacyjnej bazie danych, aby archiwizować i mieć możliwość wykonywania zapytań do 10 razy więcej danych.
- *Nieklastrowane indeksy magazynu kolumn* dla HTAP ułatwiają uzyskiwanie wglądu w dane biznesowe w czasie rzeczywistym za pomocą zapytań bezpośrednio do operacyjnej bazy danych, bez konieczności uruchamiania kosztownych procesów wyodrębniania, przekształcania i ładowania (ETL) i oczekiwania na dane Magazyn, który ma zostać wypełniony. Nieklastrowane indeksy magazynu kolumn umożliwiają szybkie wykonywanie zapytań analitycznych w bazie danych OLTP, jednocześnie zmniejszając wpływ na obciążenie operacyjne.
- *Klastrowane indeksy magazynu kolumn* dla usługi HTAP umożliwiają szybkie przetwarzanie transakcji i *współbieżne* uruchamianie zapytań analitycznych na tych samych danych.

Zarówno indeksy magazynu kolumn, jak i OLTP w pamięci są częścią produktu SQL Server od 2012 do 2014. Azure SQL Database i SQL Server mają tę samą implementację technologii w pamięci. Przechodzenie do przodu, nowe możliwości dla tych technologii są udostępniane w Azure SQL Database pierwsze przed ich udostępnieniem w SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Zalety technologii w pamięci

Ze względu na wydajniejsze przetwarzanie zapytań i transakcji, technologie w pamięci pomagają również obniżyć koszty. Zazwyczaj nie trzeba aktualizować warstwy cenowej bazy danych, aby uzyskać zyski z wydajności. W niektórych przypadkach może nawet być możliwe zmniejszenie warstwy cenowej, ale wciąż można zwiększyć wydajność przy użyciu technologii w pamięci.

Poniżej przedstawiono dwa przykłady sposobu, w jaki w przypadku przetwarzania w pamięci można znacznie poprawić wydajność:

- Korzystając z przetwarzania OLTP w pamięci, [rozwiązania biznesowe kworum mogły zwiększyć obciążenie przy jednoczesnym zwiększeniu DTU o 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - Jednostki DTU oznaczają *jednostkę transakcji bazy danych*i obejmują pomiar zużycia zasobów.
- Poniższy film wideo ilustruje znaczne zwiększenie zużycia zasobów przy użyciu przykładowego obciążenia: [OLTP w pamięci w Azure SQL Database wideo](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Aby uzyskać więcej informacji, zobacz wpis w blogu: przetwarzanie [OLTP w pamięci w Azure SQL Database wpis w blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie w pamięci są dostępne w warstwach Premium i Krytyczne dla działania firmy baz danych Azure SQL Database i elastycznych pul Premium.

Poniższy film wideo wyjaśnia potencjalne korzyści z wydajności dzięki technologiom pamięci w Azure SQL Database. Należy pamiętać, że uzyskany wzrost wydajności zawsze zależy od wielu czynników, takich jak charakter obciążenia i danych, wzorzec dostępu do bazy danych itd.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

W tym artykule opisano aspekty przetwarzania OLTP w pamięci i indeksów magazynu kolumn, które są specyficzne dla Azure SQL Database i zawierają także przykłady:

- Zobaczysz wpływ tych technologii na limity rozmiaru magazynu i danych.
- Zobaczysz, jak zarządzać przenoszeniem baz danych korzystających z tych technologii między różnymi warstwami cenowymi.
- Zobaczysz dwa przykłady ilustrujące użycie OLTP w pamięci, a także indeksów magazynu kolumn w Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie OLTP i scenariusze użycia w pamięci (w](https://msdn.microsoft.com/library/mt774593.aspx) tym informacje na temat analiz przypadków klientów i informacji na temat rozpoczynania pracy)
- [Dokumentacja przetwarzania OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe przetwarzanie transakcyjne/analityczne (HTAP), znane także jako [Analiza operacyjna w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Przetwarzanie OLTP w pamięci

Technologia OLTP w pamięci zapewnia bardzo szybkie operacje dostępu do danych, zachowując wszystkie dane w pamięci. Używa ona również wyspecjalizowanych indeksów, natywnej kompilacji zapytań i danych z niewolnego zamknięcia, aby zwiększyć wydajność obciążenia OLTP. Istnieją dwa sposoby organizowania danych OLTP w pamięci:

- **Zoptymalizowane pod kątem pamięci format magazynu wierszy** , w którym każdy wiersz jest oddzielnym obiektem pamięci. Jest to klasyczny format OLTP w pamięci zoptymalizowany pod kątem obciążeń OLTP o wysokiej wydajności. Istnieją dwa typy tabel zoptymalizowanych pod kątem pamięci, które mogą być używane w formacie magazynu wierszy zoptymalizowanym pod kątem pamięci:
  - *Tabele trwałe* (SCHEMA_AND_DATA), w których wiersze znajdujące się w pamięci są zachowywane po ponownym uruchomieniu serwera. Ten typ tabel zachowuje się jak tradycyjna tabela magazynu wierszy z dodatkowymi zaletami optymalizacji w pamięci.
  - *Tabele nietrwałe* (SCHEMA_ONLY), w których wiersze nie są zachowywane po ponownym uruchomieniu. Ten typ tabeli jest przeznaczony dla danych tymczasowych (na przykład zastępowanie tabel tymczasowych) lub tabel, w których należy szybko ładować dane przed przeniesieniem ich do pewnej utrwalonej tabeli (nazywanej tabelami przemieszczania).
- **Zoptymalizowane pod kątem pamięci format magazynu kolumn** , w którym dane są zorganizowane w formacie kolumnowym. Ta struktura jest przeznaczona dla scenariuszy HTAP, w których należy uruchamiać zapytania analityczne w tej samej strukturze danych, w której działa obciążenie OLTP.

> [!Note]
> Technologia OLTP w pamięci jest przeznaczona dla struktur danych, które mogą w pełni znajdować się w pamięci. Ponieważ dane znajdujące się w pamięci nie mogą zostać Odciążone na dysku, upewnij się, że korzystasz z bazy danych, która ma wystarczającą ilość pamięci. Aby uzyskać więcej informacji [, zobacz rozmiar danych i limit magazynu dla przetwarzania OLTP w pamięci](#data-size-and-storage-cap-for-in-memory-oltp) .

Krótki przewodnik po usłudze OLTP w pamięci: [Przewodnik Szybki Start 1: technologie OLTP w pamięci umożliwiające szybszą wydajność T-SQL](https://msdn.microsoft.com/library/mt694156.aspx) (inny artykuł, który pomoże Ci rozpocząć pracę)

Szczegółowe wideo dotyczące technologii:

- [OLTP w pamięci w Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (który zawiera demonstrację korzyści z wydajności i kroki, aby odtworzyć te wyniki ręcznie)
- [Wideo OLTP w pamięci: co to jest i kiedy go używać](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Istnieje programistyczny sposób, aby zrozumieć, czy dana baza danych obsługuje przetwarzanie OLTP w pamięci. Można wykonać następujące zapytanie w języku Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Jeśli zapytanie zwróci wartość **1**, funkcja OLTP w pamięci jest obsługiwana w tej bazie danych. Następujące zapytania identyfikują wszystkie obiekty, które należy usunąć przed obniżeniem poziomu bazy danych do warstwy Standardowa/podstawowa:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Rozmiar danych i limit pamięci dla przetwarzania OLTP w pamięci

Przetwarzanie OLTP w pamięci obejmuje tabele zoptymalizowane pod kątem pamięci, które są używane do przechowywania danych użytkownika. Te tabele są wymagane do dopasowania do pamięci. Ze względu na to, że zarządzasz pamięcią bezpośrednio w usłudze SQL Database, mamy koncepcję przydziału danych użytkownika. Ten pomysł jest określany mianem *magazynu OLTP w pamięci*.

Każda obsługiwana warstwa cenowa pojedynczej bazy danych i każda warstwa cenowa puli elastycznej zawiera pewną ilość magazynu OLTP w pamięci. Zobacz [limity zasobów opartych na jednostkach DTU — pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparte na jednostkach DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów rdzeń wirtualny — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów oparte na rdzeń wirtualny — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Następujące elementy są wliczane do limitu magazynu OLTP w pamięci:

- Aktywne wiersze danych użytkownika w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabeli. Należy zauważyć, że stare wersje wierszy nie są wliczane do limitu.
- Indeksy w tabelach zoptymalizowanych pod kątem pamięci.
- Narzuty eksploatacyjne operacji ALTER TABLE.

Po osiągnięciu limitu wystąpi błąd braku przydziału i nie można już wstawiać ani aktualizować danych. Aby wyeliminować ten błąd, Usuń dane lub Zwiększ warstwę cenową bazy danych lub puli.

Aby uzyskać szczegółowe informacje na temat monitorowania wykorzystania magazynu OLTP w pamięci i konfigurowania alertów po niemal osiągnięciu limitu, zobacz [monitorowanie magazynu w pamięci](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Pule elastyczne — informacje

W przypadku pul elastycznych magazyn OLTP w pamięci jest współużytkowany przez wszystkie bazy danych w puli. W związku z tym użycie w jednej bazie danych może mieć wpływ na inne bazy danych. Dwa środki zaradcze są następujące:

- Skonfiguruj `Max-eDTU` lub `MaxvCore` dla baz danych, które są mniejsze niż liczba jednostek eDTU lub rdzeń wirtualny dla puli jako całości. Pozwala to na maksymalne wykorzystanie magazynu OLTP w pamięci w dowolnej bazie danych w puli do rozmiaru odpowiadającego liczbie jednostek eDTU.
- Skonfiguruj `Min-eDTU` lub `MinvCore`, które są większe niż 0. Ta minimalna gwarantuje, że każda baza danych w puli ma ilość dostępnego magazynu OLTP w pamięci, który odpowiada skonfigurowanemu `Min-eDTU` lub `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Zmiana warstw usług baz danych korzystających z technologii OLTP w pamięci

Zawsze można uaktualnić bazę danych lub wystąpienie do wyższego poziomu, na przykład z Ogólnego przeznaczenia do Krytyczne dla działania firmy (lub standard do warstwy Premium). Tylko dostępne funkcje i zasoby zwiększają się.

Jednak obniżenie warstwy może mieć negatywny wpływ na bazę danych. Ten wpływ jest szczególnie oczywisty podczas obniżania poziomu Krytyczne dla działania firmy do Ogólnego przeznaczenia (lub Premium do wersji Standard lub Basic), gdy baza danych zawiera obiekty OLTP w pamięci. Tabele zoptymalizowane pod kątem pamięci są niedostępne po obniżeniu poziomu (nawet jeśli są widoczne). Te same zagadnienia mają zastosowanie w przypadku obniżenia poziomu cenowego puli elastycznej lub przeniesienia bazy danych z użyciem technologii znajdujących się w pamięci do puli elastycznej w warstwie Standardowa lub podstawowa.

> [!Important]
> Przetwarzanie OLTP w pamięci nie jest obsługiwane w warstwie Ogólnego przeznaczenia, standardowa lub podstawowa. W związku z tym nie można przenieść bazy danych, która ma wszystkie obiekty OLTP w pamięci do warstwy Standardowa lub podstawowa.

Przed obniżeniem poziomu bazy danych do warstwy Standardowa/podstawowa Usuń wszystkie tabele zoptymalizowane pod kątem pamięci i typy tabel, a także wszystkie natywnie skompilowane moduły T-SQL. 

*Skalowanie zasobów w warstwie krytyczne dla działania firmy*: dane w tabelach zoptymalizowanych pod kątem pamięci muszą mieścić się w magazynie OLTP w pamięci, który jest skojarzony z warstwą bazy danych lub wystąpienia zarządzanego, lub jest dostępny w puli elastycznej. W przypadku próby skalowania w dół warstwy lub przeniesienia bazy danych do puli, która nie ma wystarczającej ilości dostępnego magazynu OLTP w pamięci, operacja kończy się niepowodzeniem.

## <a name="in-memory-columnstore"></a>Magazyn kolumn w pamięci

Technologia magazynu kolumn w pamięci umożliwia przechowywanie dużych ilości danych w tabelach i wykonywanie na nich zapytań. Technologia magazynu kolumn korzysta z opartych na kolumnach formacie magazynu danych i przetwarzania zapytań wsadowych, aby uzyskać dostęp do 10 razy wydajności zapytań w obciążeniach OLAP w ramach tradycyjnego magazynu zorientowanego na wiersze. Możesz również uzyskać zyski do 10 razy kompresji danych przez rozmiar nieskompresowanych danych.
Istnieją dwa typy modeli magazynu kolumn, za pomocą których można organizować dane:

- **Klastrowana magazyn kolumn** , w której wszystkie dane w tabeli są zorganizowane w formacie kolumnowym. W tym modelu wszystkie wiersze w tabeli są umieszczane w formacie kolumnowy, który kompresuje dane i umożliwia wykonywanie szybkich zapytań analitycznych i raportów w tabeli. W zależności od charakteru danych rozmiar danych może ulec zmniejszeniu 10X-100x. Klastrowany model magazynu kolumn umożliwia również szybkie pozyskiwanie dużych ilości danych (ładowanie masowe), ponieważ duże partie danych przekraczają 100 000 wierszy, zanim są przechowywane na dysku. Ten model jest dobrym rozwiązaniem dla scenariuszy klasycznego magazynu danych. 
- **Nieklastrowana magazyn kolumn** , w której dane są przechowywane w tradycyjnych magazynu wierszy tabeli i istnieje indeks w formacie magazynu kolumn, który jest używany dla zapytań analitycznych. Ten model umożliwia hybrydowe przetwarzanie transakcyjne (HTAP): możliwość uruchamiania wykonywania analizy w czasie rzeczywistym w ramach obciążeń transakcyjnych. Zapytania OLTP są wykonywane w tabeli magazynu wierszy, która jest zoptymalizowana pod kątem dostępu do małego zestawu wierszy, podczas gdy zapytania OLAP są wykonywane w indeksie magazynu kolumn, który jest lepszym wyborem w przypadku skanowania i analizy. Azure SQL Database optymalizator zapytań dynamicznie wybiera format magazynu wierszy lub magazynu kolumn na podstawie zapytania. Nieklastrowane indeksy magazynu kolumn nie zmniejszają rozmiaru danych, ponieważ oryginalny zestaw danych jest przechowywany w oryginalnej tabeli magazynu wierszy bez żadnej zmiany. Jednak rozmiar dodatkowego indeksu magazynu kolumn powinien mieć wielkość mniejszą niż odpowiedni indeks B-drzewa.

> [!Note]
> Technologia magazynu kolumn w pamięci przechowuje tylko te dane, które są zbędne do przetwarzania w pamięci, podczas gdy dane, które nie mieszczą się w pamięci, są przechowywane na dysku. W związku z tym ilość danych w strukturach magazynu kolumn w pamięci może przekroczyć ilość dostępnej pamięci. 

Szczegółowy film dotyczący technologii:

- [Indeks magazynu kolumn: wideo analizy w pamięci z zapłonem 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Rozmiar danych i magazyn dla indeksów magazynu kolumn

Indeksy magazynu kolumn nie są wymagane do dopasowania do pamięci. W związku z tym jedynym limitem rozmiaru indeksów jest maksymalny całkowity rozmiar bazy danych, który jest udokumentowany w [modelu zakupu opartego na](sql-database-service-tiers-dtu.md) jednostkach DTU oraz w artykułach [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md) .

W przypadku korzystania z klastrowanych indeksów magazynu kolumn, kompresja kolumnowa jest używana dla magazynu tabeli podstawowej. Ta kompresja może znacznie zmniejszyć rozmiar przestrzeni dyskowej danych użytkownika, co oznacza, że można zmieścić więcej danych w bazie danych. Kompresję można również zwiększyć przy użyciu [kompresji archiwalnej](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Ilość kompresji, którą można osiągnąć, zależy od rodzaju danych, ale 10 razy kompresja nie jest nietypowa.

Na przykład jeśli masz bazę danych o maksymalnym rozmiarze wynoszącym 1 terabajt (TB), a następnie przeniesiesz 10 razy kompresję przy użyciu indeksów magazynu kolumn, możesz dopasować łącznie 10 TB danych użytkownika do bazy danych.

W przypadku korzystania z nieklastrowanych indeksów magazynu kolumn tabela podstawowa jest nadal przechowywana w tradycyjnym formacie magazynu wierszy. W związku z tym oszczędności magazynu nie są tak duże jak w przypadku klastrowanych indeksów magazynu kolumn. Jednak w przypadku wymiany wielu tradycyjnych indeksów nieklastrowanych z pojedynczym indeksem magazynu kolumn nadal można uzyskać ogólne oszczędności w odniesieniu do tabeli.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Zmiana warstw usługi baz danych zawierających indeksy magazynu kolumn

*W przypadku obniżenia poziomu jednej bazy danych do warstwy Podstawowa lub standardowa* może nie być możliwe, jeśli warstwa docelowa jest niższa niż S3. Indeksy magazynu kolumn są obsługiwane tylko w warstwie cenowej Krytyczne dla działania firmy/Premium i w warstwie Standardowa, S3 i wyższych, a nie w warstwie Podstawowa. W przypadku obniżenia poziomu bazy danych do nieobsługiwanej warstwy, indeks magazynu kolumn będzie niedostępny. System przechowuje indeks magazynu kolumn, ale nigdy nie korzysta z indeksu. Jeśli później przejdziesz uaktualnienie do obsługiwanej warstwy lub poziomu, indeks magazynu kolumn będzie natychmiast gotowy do ponownego wykorzystania.

Jeśli masz **klastrowany** indeks magazynu kolumn, cała tabela staną się niedostępna po obniżeniu poziomu. Dlatego zalecamy porzucenie wszystkich *klastrowanych* indeksów magazynu kolumn przed obniżeniem poziomu bazy danych na nieobsługiwaną warstwę lub poziom.

> [!Note]
> Wystąpienie zarządzane obsługuje indeksy magazynu kolumn we wszystkich warstwach.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Następne kroki

- [Szybki Start 1: technologie OLTP w pamięci do szybszej wydajności T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Używanie OLTP w pamięci w istniejącej aplikacji Azure SQL](sql-database-in-memory-oltp-migration.md)
- [Monitorowanie magazynu OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md) na potrzeby przetwarzania OLTP w pamięci
- [Wypróbuj funkcje znajdujące się w pamięci w programie Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

### <a name="deeper-information"></a>Dokładniejsze informacje

- [Dowiedz się, jak kworum podwaja obciążenie bazy danych przy jednoczesnym obniżeniu liczby jednostek DTU o 70% z użyciem OLTP w pamięci w SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Przetwarzanie OLTP w pamięci w Azure SQL Database wpis w blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Więcej informacji na temat przetwarzania OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Informacje o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- [Informacje o analizie operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)
- Zapoznaj się [z typowymi wzorcami obciążeń i zagadnieniami](https://msdn.microsoft.com/library/dn673538.aspx) dotyczącymi migracji (które opisują wzorce obciążeń, w których przetwarzanie OLTP w pamięci często zapewnia znaczny wzrost wydajności)

### <a name="application-design"></a>Projekt aplikacji

- [Przetwarzanie OLTP w pamięci (Optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Używanie OLTP w pamięci w istniejącej aplikacji Azure SQL](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Narzędzia

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
