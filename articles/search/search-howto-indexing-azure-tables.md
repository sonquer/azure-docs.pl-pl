---
title: Wyszukiwanie w usłudze Azure Table Storage
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować dane przechowywane w usłudze Azure Table Storage za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113002"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Jak indeksować tabele z usługi Azure Table Storage za pomocą usługi Azure Wyszukiwanie poznawcze

W tym artykule pokazano, jak używać usługi Azure Wyszukiwanie poznawcze do indeksowania danych przechowywanych w usłudze Azure Table Storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurowanie indeksowania usługi Azure Table Storage

Indeksator usługi Azure Table Storage można skonfigurować przy użyciu następujących zasobów:

* [Azure Portal](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) usługi Azure wyszukiwanie poznawcze
* Azure Wyszukiwanie poznawcze [.NET SDK](https://aka.ms/search-sdk)

W tym miejscu zademonstrowano przepływ przy użyciu interfejsu API REST. 

### <a name="step-1-create-a-datasource"></a>Krok 1. Tworzenie źródła danych

Źródło danych określa dane do indeksowania, poświadczenia potrzebne do uzyskania dostępu do danych oraz zasady, które umożliwiają usłudze Azure Wyszukiwanie poznawcze wydajne identyfikowanie zmian w danych.

W przypadku indeksowania tabeli źródło danych musi mieć następujące właściwości:

- **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
- **Typ** musi być `azuretable`.
- parametr **Credentials** zawiera parametry połączenia konta magazynu. Aby uzyskać szczegółowe informacje, zobacz sekcję [Określanie poświadczeń](#Credentials) .
- **kontener** ustawia nazwę tabeli i opcjonalne zapytanie.
    - Określ nazwę tabeli przy użyciu parametru `name`.
    - Opcjonalnie można określić zapytanie przy użyciu parametru `query`. 

> [!IMPORTANT] 
> Jeśli to możliwe, Użyj filtru na PartitionKey w celu uzyskania lepszej wydajności. Każde inne zapytanie wykonuje pełne skanowanie tabeli, co skutkuje niską wydajnością dużych tabel. Zobacz sekcję [zagadnienia dotyczące wydajności](#Performance) .


Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Create DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sposoby określania poświadczeń ####

Poświadczenia dla tabeli można podać w jeden z następujących sposobów: 

- **Pełny dostęp do parametrów połączenia konta magazynu**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` można uzyskać parametry połączenia z Azure Portal, przechodząc do **bloku konta magazynu** > **Ustawienia** > **klucze** (dla klasycznych kont magazynu) lub **Ustawienia** > **klucze dostępu** (dla Azure Resource Manager kont magazynu).
- **Parametry połączenia sygnatury dostępu współdzielonego konta magazynu**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` sygnatura dostępu współdzielonego powinna mieć uprawnienia listy i odczytu w kontenerach (w tym przypadku tabel w tym przypadku) i obiektach (wiersze tabeli).
-  **Sygnatura dostępu współdzielonego tabeli**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` sygnatura dostępu współdzielonego powinna mieć uprawnienia zapytania (odczyt) w tabeli.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> W przypadku korzystania z poświadczeń sygnatury dostępu współdzielonego należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator kończy się niepowodzeniem z komunikatem o błędzie podobnym do "poświadczenia podane w parametrach połączenia są nieprawidłowe lub wygasły".  

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Aby utworzyć indeks:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [create index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3. Tworzenie indeksatora
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram służący do automatyzowania odświeżania danych. 

Po utworzeniu indeksu i źródła danych możesz utworzyć indeksator:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator działa co dwie godziny. (Interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał wynosi pięć minut. Harmonogram jest opcjonalny; w przypadku pominięcia indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zobacz [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Postępowanie z różnymi nazwami pól
Czasami nazwy pól w istniejącym indeksie różnią się od nazw właściwości w tabeli. Mapowania pól można użyć do mapowania nazw właściwości z tabeli do nazw pól w indeksie wyszukiwania. Aby dowiedzieć się więcej na temat mapowania pól, zobacz [mapowania pól usługi Azure wyszukiwanie poznawcze Indexer — różnice między źródłami danych i indeksami wyszukiwania](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Obsługuj klucze dokumentów
W usłudze Azure Wyszukiwanie poznawcze klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza jest wymagane dla każdego dokumentu, który jest dodawany do indeksu. (W rzeczywistości jest to jedyne pole wymagane).

Ponieważ wiersze tabeli mają klucz złożony, usługa Azure Wyszukiwanie poznawcze generuje pole syntetyczne o nazwie `Key`, które jest połączeniem wartości klucza partycji i klucza wiersza. Na przykład jeśli PartitionKey wiersza jest `PK1` i RowKey jest `RK1`, wartość pola `Key` jest `PK1RK1`.

> [!NOTE]
> Wartość `Key` może zawierać znaki, które są nieprawidłowe w kluczach dokumentów, takich jak łączniki. Za pomocą [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode` można zajmować się nieprawidłowymi znakami. Wykonując tę czynność, pamiętaj również, aby użyć kodowania Base64 bezpiecznego dla adresu URL podczas przekazywania kluczy dokumentów w wywołaniach interfejsu API, na przykład podczas wyszukiwania.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie przyrostowe i usuwanie
Po skonfigurowaniu indeksatora tabeli do uruchamiania zgodnie z harmonogramem program ponownie indeksuje tylko nowe lub zaktualizowane wiersze, zgodnie z wartością `Timestamp` wiersza. Nie musisz określać zasad wykrywania zmian. Indeksowanie przyrostowe jest automatycznie włączone.

Aby wskazać, że niektóre dokumenty muszą zostać usunięte z indeksu, można użyć strategii usuwania nietrwałego. Zamiast usuwać wiersz, Dodaj właściwość, aby wskazać, że została usunięta, i skonfiguruj zasady wykrywania usuwania nietrwałego dla źródła danych. Na przykład następujące zasady uważają, że wiersz jest usuwany, jeśli wiersz ma właściwość `IsDeleted` z wartością `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Domyślnie usługa Azure Wyszukiwanie poznawcze używa następującego filtra zapytania: `Timestamp >= HighWaterMarkValue`. Ponieważ tabele platformy Azure nie mają dodatkowego indeksu w polu `Timestamp`, ten typ zapytania wymaga pełnego skanowania tabeli i w związku z tym jest wolny dla dużych tabel.


Poniżej przedstawiono dwie możliwe podejścia do usprawnienia działania indeksowania tabeli. Oba te podejścia polegają na użyciu partycji tabel: 

- Jeśli dane można w naturalny sposób podzielić na kilka zakresów partycji, Utwórz źródło danych i odpowiedni indeksator dla każdego zakresu partycji. Każdy indeksator musi teraz przetwarzać tylko określony zakres partycji, co zwiększa wydajność zapytań. Jeśli dane, które muszą być indeksowane, mają niewielką liczbę stałych partycji, jeszcze lepszy: każdy indeksator wykonuje tylko skanowanie partycji. Na przykład, aby utworzyć źródło danych do przetwarzania zakresu partycji z kluczami z `000` do `100`, należy użyć zapytania w następujący sposób: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Jeśli dane są partycjonowane według czasu (na przykład można utworzyć nową partycję każdego dnia lub tygodnia), należy wziąć pod uwagę następujące podejście: 
    - Użyj zapytania dotyczącego formularza: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitoruj postęp indeksatora przy użyciu [interfejsu Get indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)i okresowe aktualizowanie warunku `<TimeStamp>` zapytania w oparciu o ostatnią pomyślną wartość w znaczniku limitu górnego. 
    - W przypadku konieczności wyzwolenia kompletnego ponownego indeksowania należy zresetować zapytanie DataSource oprócz resetowania indeksatora. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam ulepszyć platformę Azure Wyszukiwanie poznawcze
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, prześlij je w naszej [witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
