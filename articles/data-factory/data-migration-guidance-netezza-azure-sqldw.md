---
title: Migrowanie danych z lokalnego serwera Netezza na platformę Azure
description: Użyj Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 80c9929f37b4890387a7625f04db6ce3e37f0cdd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922119"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Używanie Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure 

Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z lokalnego serwera Netezza do konta usługi Azure Storage lub bazy danych Azure SQL Data Warehouse Database. 

Ten artykuł zawiera następujące informacje dotyczące inżynierów danych i deweloperów:

> [!div class="checklist"]
> * Wydajność 
> * Odporność kopiowania
> * Bezpieczeństwo sieci
> * Architektura rozwiązania wysokiego poziomu 
> * Najlepsze rozwiązania w zakresie implementacji  

## <a name="performance"></a>Wydajność

Azure Data Factory oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach. Jeśli jesteś deweloperem, możesz utworzyć potoki, aby w pełni wykorzystać przepustowość sieci i bazy danych, aby zmaksymalizować przepływność przenoszenia danych w środowisku.

![Diagram wydajności](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Poprzedni diagram można interpretować w następujący sposób:

- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych. Gdy używasz Azure Integration Runtime, możesz określić [do 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. Przy użyciu własnego środowiska Integration Runtime (własne środowisko IR) można ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([do czterech węzłów](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), a działanie pojedynczej kopii dystrybuuje swoją partycję we wszystkich węzłach. 

- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu wielu wątków. 

- Przepływ sterowania Azure Data Factory można uruchomić równolegle wiele działań kopiowania. Na przykład można je uruchomić przy użyciu [dla każdej pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący wydajności i skalowalności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odporność

W ramach pojedynczego uruchomienia działania kopiowania Azure Data Factory ma wbudowany mechanizm ponawiania prób, który umożliwia obsługę określonego poziomu przejściowych błędów w magazynach danych lub w sieci źródłowej.

Przy kopiowaniu danych między źródłami i ujściami danych za pomocą Azure Data Factory działania kopiowania istnieją dwa sposoby obsługi niezgodnych wierszy. Możesz przerwać działanie kopiowania i niepowodzeniem lub kontynuować kopiowanie reszty danych, pomijając niezgodne wiersze danych. Ponadto, aby poznać przyczynę niepowodzenia, można zarejestrować niezgodne wiersze w usłudze Azure Blob Storage lub Azure Data Lake Store, naprawić dane w źródle danych, a następnie ponowić działanie kopiowania.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie Azure Data Factory transferu danych z lokalnego serwera Netezza do konta usługi Azure Storage lub bazy danych Azure SQL Data Warehouse przy użyciu szyfrowanego połączenia za pośrednictwem protokołu Hypertext Transfer Protocol Secure (HTTPS). Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i uniemożliwia podsłuchiwanie i ataki typu man-in-the-middle.

Alternatywnie, jeśli nie chcesz, aby dane były transferowane za pośrednictwem publicznej sieci Internet, możesz zwiększyć poziom bezpieczeństwa, przekazując dane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route. 

W następnej sekcji omówiono sposób osiągnięcia wyższego poziomu zabezpieczeń.

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji omówiono dwa sposoby migrowania danych.

### <a name="migrate-data-over-the-public-internet"></a>Migrowanie danych za pośrednictwem publicznego Internetu

![Migrowanie danych za pośrednictwem publicznego Internetu](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Poprzedni diagram można interpretować w następujący sposób:

- W tej architekturze dane są bezpiecznie transferowane za pośrednictwem protokołu HTTPS przez publiczny Internet.

- Aby osiągnąć tę architekturę, należy zainstalować Azure Data Factory Integration Runtime (własny) na komputerze z systemem Windows za zaporą firmową. Upewnij się, że to środowisko Integration Runtime może bezpośrednio uzyskać dostęp do serwera Netezza. Aby w pełni wykorzystać przepustowość sieci i magazyn danych do kopiowania danych, możesz ręcznie skalować maszynę lub skalować ją do wielu maszyn.

- Korzystając z tej architektury, można migrować zarówno dane pierwotnej migawki, jak i dane różnicowe.

### <a name="migrate-data-over-a-private-network"></a>Migrowanie danych za pośrednictwem sieci prywatnej 

![Migrowanie danych za pośrednictwem sieci prywatnej](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Poprzedni diagram można interpretować w następujący sposób:

- W tej architekturze można migrować dane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route, a dane nigdy nie przechodzą przez publiczny Internet. 

- Aby osiągnąć tę architekturę, należy zainstalować Azure Data Factory Integration Runtime (własny) na maszynie wirtualnej z systemem Windows w sieci wirtualnej platformy Azure. Aby w pełni wykorzystać przepustowość sieci i magazyn danych do kopiowania danych, można ręcznie skalować maszynę wirtualną lub skalować ją do wielu maszyn wirtualnych.

- Korzystając z tej architektury, można migrować zarówno dane pierwotnej migawki, jak i dane różnicowe.

## <a name="implement-best-practices"></a>Implementowanie najlepszych rozwiązań 

### <a name="manage-authentication-and-credentials"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby przeprowadzić uwierzytelnianie w Netezza, można użyć [uwierzytelniania ODBC za pośrednictwem parametrów połączenia](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Aby przeprowadzić uwierzytelnianie w usłudze Azure Blob Storage: 

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Tożsamość zarządzana na podstawie automatycznie zarządzanej tożsamości Azure Data Factory w usłudze Azure Active Directory (Azure AD) umożliwia konfigurowanie potoków bez konieczności podawania poświadczeń w definicji połączonej usługi.  

   - Alternatywnie można uwierzytelniać w usłudze Azure Blob Storage za pomocą [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 

- Aby uwierzytelnić się w Azure Data Lake Storage Gen2: 

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity).
   
   - Można również użyć jednostki [usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 

- Aby uwierzytelnić się w Azure SQL Data Warehouse:

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity).
   
   - Można również użyć jednostki [usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) lub [uwierzytelniania SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Jeśli nie korzystasz z tożsamości zarządzanych dla zasobów platformy Azure, zdecydowanie zalecamy [przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) , aby ułatwić centralne zarządzanie kluczami i ich obracanie bez konieczności modyfikowania Azure Data Factory połączonych usług. Jest to również jedno z [najlepszych rozwiązań dotyczących](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)ciągłej integracji/ciągłego wdrażania. 

### <a name="migrate-initial-snapshot-data"></a>Migrowanie danych początkowej migawki 

W przypadku małych tabel (czyli tabel o woluminie o rozmiarze mniejszym niż 100 GB lub migracji na platformę Azure w ciągu dwóch godzin) można wykonać każde zadanie kopiowania Załaduj dane na tabelę. Aby uzyskać większą przepływność, można uruchomić wiele zadań kopiowania Azure Data Factory w celu załadowania oddzielnych tabel współbieżnie. 

W ramach każdego zadania kopiowania, aby uruchamiać zapytania równoległe i kopiować dane przez partycje, można także uzyskać dostęp do pewnego poziomu równoległego przy użyciu [Ustawienia właściwości`parallelCopies`](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) z jedną z następujących opcji partycji danych:

- Aby ułatwić osiągnięcie większej wydajności, zachęcamy do rozpoczęcia od wycinka danych.  Upewnij się, że wartość w ustawieniu `parallelCopies` jest mniejsza niż całkowita liczba partycji wycinków danych w tabeli na serwerze Netezza.  

- Jeśli wolumin poszczególnych partycji wycinków danych jest nadal duży (na przykład 10 GB lub więcej), zachęcamy do przełączenia na dynamiczną partycję zakresu. Ta opcja zapewnia większą elastyczność definiowania liczby partycji i woluminu każdej partycji przez kolumnę partycji, górną granicę i dolną granicę.

W przypadku większych tabel (czyli tabel z woluminem 100 GB lub nowszym lub *nie* można migrować do platformy Azure w ciągu dwóch godzin) zaleca się Partycjonowanie danych według zapytania niestandardowego, a następnie każde kopiowanie jednej partycji w danym momencie. Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania Azure Data Factory współbieżnie. Dla każdego elementu docelowego zadania kopiowania jednej partycji za pomocą zapytania niestandardowego można zwiększyć przepływność, włączając równoległość za pomocą wycinka danych lub zakresu dynamicznego. 

Jeśli jakiekolwiek zadanie kopiowania zakończy się niepowodzeniem z powodu przejściowego problemu z siecią lub magazynem danych, można ponownie uruchomić zadanie kopiowania zakończonego niepowodzeniem, aby ponownie załadować tę konkretną partycję z tabeli. Nie ma to wpływu na inne zadania kopiowania, które ładują inne partycje.

Podczas ładowania danych do bazy danych Azure SQL Data Warehouse zalecamy włączenie wielopodstawau w ramach zadania kopiowania z magazynem obiektów blob platformy Azure jako przemieszczania.

### <a name="migrate-delta-data"></a>Migrowanie danych różnicowych 

Aby zidentyfikować nowe lub zaktualizowane wiersze z tabeli, użyj kolumny timestamp lub przyrostowego klucza w schemacie. Następnie można zapisać najnowszą wartość jako górny limit w tabeli zewnętrznej, a następnie użyć jej do filtrowania danych różnicowych przy następnym załadowaniu danych. 

Każda tabela może korzystać z innej kolumny limitu, aby identyfikować jej nowe lub zaktualizowane wiersze. Zalecamy utworzenie tabeli formantów zewnętrznych. W tabeli każdy wiersz reprezentuje jedną tabelę na serwerze Netezza z nazwą kolumny określonego znaku wodnego i wartością górnego limitu. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurowanie własnego środowiska Integration Runtime

W przypadku migrowania danych z serwera Netezza do platformy Azure, niezależnie od tego, czy serwer jest lokalnie używany przez zaporę firmy, czy w środowisku sieci wirtualnej, należy zainstalować własne środowisko IR na komputerze lub maszynie wirtualnej z systemem Windows, który jest aparatem używanym do Przenieś dane. Podczas instalowania własnego środowiska IR zalecamy następujące podejście:

- Dla każdej maszyny lub maszyny wirtualnej z systemem Windows należy rozpocząć od konfiguracji 32 vCPU i 128 GB pamięci. Można monitorować użycie procesora CPU i pamięci przez maszynę podczerwieni podczas migracji danych, aby sprawdzić, czy jest konieczne dalsze skalowanie maszyny w celu uzyskania lepszej wydajności lub skalowanie maszyny w dół w celu zaoszczędzenia kosztów.

- Możesz również skalować w poziomie, kojarząc do czterech węzłów za pomocą jednego samodzielnego środowiska IR. Pojedyncze zadanie kopiowania uruchomione dla samodzielnego środowiska IR automatycznie stosuje wszystkie węzły maszyny wirtualnej w celu równoległego kopiowania danych. Aby zapewnić wysoką dostępność, należy zacząć od czterech węzłów maszyn wirtualnych, aby uniknąć single point of failure podczas migracji danych.

### <a name="limit-your-partitions"></a>Ograniczanie partycji

Najlepszym rozwiązaniem jest przeprowadzenie oceny wydajności koncepcji z reprezentatywnym przykładowym zestawem danych, dzięki czemu można określić odpowiedni rozmiar partycji dla każdego działania kopiowania. Zalecamy załadowanie każdej partycji na platformę Azure w ciągu dwóch godzin.  

Aby skopiować tabelę, Zacznij od jednego działania kopiowania z pojedynczą obsługiwaną maszyną podczerwieni. Stopniowo Zwiększ `parallelCopies` ustawienie na podstawie liczby partycji wycinków danych w tabeli. Sprawdź, czy cała tabela może zostać załadowana do platformy Azure w ciągu dwóch godzin, zgodnie z przepływem pracy, który wynika z zadania kopiowania. 

Jeśli nie można go załadować do platformy Azure w ciągu dwóch godzin, a pojemność samodzielnego środowiska IR i magazynu danych nie jest w pełni używana, należy stopniowo zwiększać liczbę współbieżnych działań kopiowania do momentu osiągnięcia limitu sieci lub limitu przepustowości magazynu danych. wolumin. 

Monitoruj użycie procesora CPU i pamięci na samoobsługowej maszynie IR i przygotuj się do skalowania maszyny w górę lub w poziomie do wielu maszyn, gdy widzisz, że procesor i pamięć są w pełni używane. 

W przypadku wystąpienia błędów ograniczania, zgodnie z Azure Data Factory działania kopiowania, należy zmniejszyć ustawienia współbieżności lub `parallelCopies` w Azure Data Factory lub rozważyć zwiększenie przepustowości lub limitów operacji we/wy na sekundę (IOPS) sieci i magazynów danych. 


### <a name="estimate-your-pricing"></a>Oszacowanie cen 

Rozważmy następujący potok, który jest zbudowany w celu migrowania danych z lokalnego serwera Netezza do bazy danych Azure SQL Data Warehouse:

![Potoku cenowe](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Załóżmy, że następujące instrukcje są prawdziwe: 

- Całkowity wolumin danych to 50 terabajtów (TB). 

- Migrujemy dane przy użyciu architektury pierwszego rozwiązania (serwer Netezza działa lokalnie, za zaporą).

- Wolumin 50-TB jest podzielony na partycje 500, a każda czynność kopiowania przenosi jedną partycję.

- Każde działanie kopiowania jest skonfigurowane z użyciem jednego własnego środowiska IR w porównaniu z czterema maszynami i osiąga przepływność wynoszącą 20 megabajtów na sekundę (MB/s). (W ramach działania kopiowania `parallelCopies` jest ustawiony na 4, a każdy wątek ładowania danych z tabeli osiągnie przepływność 5 MB/s).

- Ustawienie współbieżności ForEach jest ustawione na 3, a przepustowość zagregowana to 60 MB/s.

- W sumie wykonanie migracji trwa 243 godzin.

W oparciu o powyższe założenia jest szacowana cena: 

![Tabela cen](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Cennik przedstawiony w powyższej tabeli jest hipotetyczny. Rzeczywiste ceny są uzależnione od rzeczywistej przepływności w danym środowisku. Cena maszyny z systemem Windows (z zainstalowanym własnym programem IR) nie jest uwzględniona. 

### <a name="additional-references"></a>Dodatkowa dokumentacja

Aby uzyskać więcej informacji, zobacz następujące artykuły i przewodniki:

- [Migrowanie danych z lokalnej bazy danych magazynu danych relacyjnych na platformę Azure przy użyciu Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Łącznik Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Łącznik ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Łącznik usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Łącznik Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Przewodnik dostrajania wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i konfigurowanie własnego środowiska Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Własne środowisko Integration Runtime HA i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia dotyczące zabezpieczeń przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Przyrostowe kopiowanie danych z jednej tabeli](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Przyrostowe kopiowanie danych z wielu tabel](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Strona cennika Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)
