---
title: Kopiuj dane z MongoDB przy użyciu starszej wersji
description: Informacje o kopiowaniu danych z usługi Mongo DB do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 0bdd8d454b979250b57cf657d347309b99a86ede
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892569"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z MongoDB za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Bieżąca wersja](connector-mongodb.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!IMPORTANT]
>Moduł ADF zwolni nowy łącznik MongoDB, który zapewnia lepszą obsługę natywnych MongoDB w porównaniu do tej implementacji opartej na ODBC, zobacz artykuł dotyczący [łącznika MongoDB](connector-mongodb.md) , aby uzyskać szczegółowe informacje. Ten starszy łącznik MongoDB jest obsługiwany jako gotowy do zapewnienia zgodności z poprzednimi wersjami, a w przypadku każdego nowego obciążenia Użyj nowego łącznika.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z bazy danych MongoDB można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Ten łącznik MongoDB obsługuje:

- MongoDB **wersje 2,4, 2,6, 3,0, 3,2, 3,4 i 3,6**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **anonimowego** .

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik MongoDB, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika MongoDB.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi MongoDB są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość Type musi mieć wartość: **MongoDB** |Tak |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP, którego serwer MongoDB używa do nasłuchiwania połączeń klientów. |Nie (domyślnie 27017) |
| databaseName |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| authenticationType | Typ uwierzytelniania używany do łączenia się z bazą danych MongoDB.<br/>Dozwolone wartości to: **Basic**i **Anonymous**. |Tak |
| nazwa użytkownika |Konto użytkownika do uzyskiwania dostępu do MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, która ma zostać użyta do sprawdzenia poświadczeń w celu uwierzytelnienia. |Nie. W przypadku uwierzytelniania podstawowego wartość domyślna to użycie konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Dla zestawu danych MongoDB są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **MongoDbCollection** | Tak |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MongoDbSource** | Tak |
| query |Użyj niestandardowej kwerendy SQL-92 do odczytu danych. Na przykład: select * from MyTable. |Nie (Jeśli określono "CollectionName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> W przypadku określenia zapytania SQL należy zwrócić uwagę na format daty i godziny. Na przykład: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` lub aby użyć parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory

Azure Data Factory schemat wniosku o usługę z kolekcji MongoDB przy użyciu **najnowszych dokumentów 100** w kolekcji. Jeśli następujące dokumenty 100 nie zawierają pełnego schematu, niektóre kolumny mogą zostać zignorowane podczas operacji kopiowania.

## <a name="data-type-mapping-for-mongodb"></a>Mapowanie typu danych dla MongoDB

Podczas kopiowania danych z MongoDB następujące mapowania są używane z typów danych MongoDB do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| MongoDB — typ danych | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Binary |Byte[] |
| Wartość logiczna |Wartość logiczna |
| Data |Data i godzina |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Ciąg |
| Ciąg |Ciąg |
| UUID |Identyfikator GUID |
| Obiekt |Reznormalizowany do spłaszczonych kolumn z "_" jako separatorem zagnieżdżonym |

> [!NOTE]
> Aby dowiedzieć się więcej o obsłudze tablic przy użyciu tabel wirtualnych, zobacz sekcję [Obsługa typów złożonych przy użyciu tabel wirtualnych](#support-for-complex-types-using-virtual-tables) .
>
> Obecnie następujące typy danych MongoDB nie są obsługiwane: dbpointer, JavaScript, max/min Key, wyrażenie regularne, symbol, sygnatura czasowa, niezdefiniowane.

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa typów złożonych przy użyciu tabel wirtualnych

Azure Data Factory używa wbudowanego sterownika ODBC do nawiązywania połączenia i kopiowania danych z bazy danych MongoDB. W przypadku typów złożonych, takich jak tablice lub obiekty o różnych typach w dokumentach, sterownik renormalizuje dane do odpowiednich tabel wirtualnych. W przypadku, gdy tabela zawiera takie kolumny, sterownik generuje następujące tabele wirtualne:

* **Tabela podstawowa**, która zawiera te same dane, co rzeczywista tabela z wyjątkiem kolumn typu złożonego. Tabela podstawowa używa takiej samej nazwy jak rzeczywista tabela, która reprezentuje.
* **Tabela wirtualna** dla każdej kolumny typu złożonego, która rozszerza zagnieżdżone dane. Tabele wirtualne są nazwane przy użyciu nazwy rzeczywistej tabeli, separatora "_" oraz nazwy tablicy lub obiektu.

Tabele wirtualne odwołują się do danych w rzeczywistej tabeli, umożliwiając sterownikowi dostęp do nieznormalizowanych danych. Możesz uzyskać dostęp do zawartości tablic MongoDB, wykonując zapytania i dołączając do tabel wirtualnych.

### <a name="example"></a>Przykład

Na przykład przykładem jest tabela MongoDB, która zawiera jedną kolumnę z tablicą obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacje.

| _id | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Złoty |[1,2] |

Sterownik generuje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Pierwsza tabela wirtualna jest tabelą podstawową o nazwie "Przykładowe", pokazana w przykładzie. Tabela podstawowa zawiera wszystkie dane oryginalnej tabeli, ale dane z tablic zostały pominięte i rozwinięte w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono tabele wirtualne, które reprezentują oryginalne tablice w przykładzie. Te tabele zawierają następujące elementy:

* Odwołanie z powrotem do oryginalnej kolumny klucza podstawowego odpowiadającej wierszowi oryginalnej tablicy (za pośrednictwem kolumny _id)
* Wskazanie pozycji danych w oryginalnej tablicy
* Rozwinięte dane dla każdego elementu w tablicy

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |wyskakujący |456 |0.2 |
| 1111 |1 |124 |laboratoryjn |1235 |0.2 |
| 2222 |0 |135 |lodówki |12543 |0.0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
