---
title: Kopiuj dane do indeksu wyszukiwania
description: Informacje o sposobie wypychania lub kopiowania danych do indeksu usługi Azure Search przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 418026d5569cd7e4a7c5239f99650833b1b9514d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892936"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopiowanie danych do indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-search-connector.md)
> * [Bieżąca wersja](connector-azure-search.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do usługi Azure Wyszukiwanie poznawcze index. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych do indeksu wyszukiwania. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika usługi Azure Wyszukiwanie poznawcze.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi Azure Wyszukiwanie poznawcze są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **AzureSearch** | Tak |
| url | Adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora dla usługi wyszukiwania. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

> [!IMPORTANT]
> Podczas kopiowania danych z magazynu danych w chmurze do indeksu wyszukiwania w usłudze Azure Wyszukiwanie poznawcze połączonej usłudze należy odwołać się do Azure Integration Runtime z jawnym regionem w connactVia. Ustaw region jako lokalizację, w której znajduje się Twoja usługa wyszukiwania. Dowiedz się więcej z [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Przykład:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Wyszukiwanie poznawcze platformy Azure.

Aby skopiować dane do usługi Azure Wyszukiwanie poznawcze, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **AzureSearchIndex** | Tak |
| indexName | Nazwa indeksu wyszukiwania. Data Factory nie tworzy indeksu. Indeks musi istnieć na platformie Azure Wyszukiwanie poznawcze. | Tak |

**Przykład:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Wyszukiwanie poznawcze platformy Azure.

### <a name="azure-cognitive-search-as-sink"></a>Wyszukiwanie poznawcze platformy Azure jako ujścia

Aby skopiować dane do usługi Azure Wyszukiwanie poznawcze, ustaw typ źródła w działaniu Copy na **AzureSearchIndexSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **AzureSearchIndexSink** | Tak |
| writeBehavior | Określa, czy należy scalić lub zamienić, gdy dokument już istnieje w indeksie. Zobacz [Właściwość WriteBehavior](#writebehavior-property).<br/><br/>Dozwolone wartości to: **Scal** (wartość domyślna) i **Przekaż**. | Nie |
| writeBatchSize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. Aby uzyskać szczegółowe informacje, zobacz [Właściwość WriteBatchSize](#writebatchsize-property) .<br/><br/>Dozwolone wartości to: integer od 1 do 1 000; wartość domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior

AzureSearchSink upserts podczas zapisywania danych. Innymi słowy podczas pisania dokumentu, jeśli klucz dokumentu istnieje już w indeksie wyszukiwania, usługa Azure Wyszukiwanie poznawcze aktualizuje istniejący dokument zamiast zgłaszania wyjątku konfliktu.

AzureSearchSink udostępnia następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scalanie**: Połącz wszystkie kolumny w nowym dokumencie z istniejącym elementem. W przypadku kolumn o wartości null w nowym dokumencie jest zachowywana wartość w istniejącej.
- **Przekazywanie**: nowy dokument zastępuje istniejący. W przypadku kolumn nieokreślonych w nowym dokumencie wartość jest ustawiana na wartość null niezależnie od tego, czy w istniejącym dokumencie istnieje wartość inna niż null.

Zachowanie domyślne jest **scalane**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize

Usługa Azure Wyszukiwanie poznawcze obsługuje pisanie dokumentów jako partii. Zadanie wsadowe może zawierać od 1 do 1 000 akcji. Akcja obsługuje jeden dokument, aby wykonać operację przekazywania/scalania.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Obsługa typu danych

W poniższej tabeli określono, czy typ danych Wyszukiwanie poznawcze platformy Azure jest obsługiwany.

| Typ danych Wyszukiwanie poznawcze platformy Azure | Obsługiwane w usłudze Azure Wyszukiwanie poznawcze sink |
| ---------------------- | ------------------------------ |
| Ciąg | Tak |
| Int32 | Tak |
| Int64 | Tak |
| Double | Tak |
| Wartość logiczna | Tak |
| DataTimeOffset | Tak |
| String Array | N |
| GeographyPoint względem | N |

Obecnie inne typy danych (np. ComplexType) nie są obsługiwane. Aby uzyskać pełną listę obsługiwanych typów danych Wyszukiwanie poznawcze platformy Azure, zobacz [obsługiwane typy danych (Azure wyszukiwanie poznawcze)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
