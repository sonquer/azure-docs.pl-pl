---
title: Kopiuj dane z MongoDB
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
ms.openlocfilehash: a7bb74c09b45429a160a3ec481c23073575cfe3c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892503"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z MongoDB za pomocą Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!IMPORTANT]
>W usłudze ADF należy wydać nową wersję łącznika MongoDB, która zapewnia lepszą obsługę natywnych MongoDB. Jeśli używasz poprzedniego łącznika MongoDB w rozwiązaniu, które jest obsługiwane w przypadku zgodności z poprzednimi wersjami, zapoznaj się z artykułem [łącznika MongoDB (starsza wersja)](connector-mongodb-legacy.md) .

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z bazy danych MongoDB można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Ten łącznik MongoDB obsługuje wyłącznie **wersje do 3,4**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika MongoDB.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi MongoDB są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość Type musi mieć wartość: **MongoDbV2** |Tak |
| connectionString |Określ parametry połączenia MongoDB, np. `mongodb://[username:password@]host[:port][/[database][?options]]`. Więcej informacji można znaleźć w [podręczniku MongoDB w parametrach połączenia](https://docs.mongodb.com/manual/reference/connection-string/) . <br/><br /> Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć konfigurację   `password`z parametrów połączenia. Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md) . |Tak |
| baza danych | Nazwa bazy danych, do której chcesz uzyskać dostęp. | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
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
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **MongoDbV2Collection** | Tak |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
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
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MongoDbV2Source** | Tak |
| filter | Określa filtr wyboru przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, Pomiń ten parametr lub Przekaż pusty dokument ({}). | Nie |
| cursorMethods. Project | Określa pola do zwrócenia w dokumentach dla projekcji. Aby zwrócić wszystkie pola w pasujących dokumentach, Pomiń ten parametr. | Nie |
| cursorMethods. Sort | Określa kolejność, w której zapytanie zwraca pasujące dokumenty. Zapoznaj się z [kursorem. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| cursorMethods.limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Zapoznaj się z [kursorem. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie |
| cursorMethods.skip | Określa liczbę dokumentów do pominięcia oraz miejsce, z którego MongoDB zaczyna zwracać wyniki. Zapoznaj się z [kursorem. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów do zwrócenia w każdej partii odpowiedzi z wystąpienia MongoDB. W większości przypadków modyfikacja rozmiaru partii nie wpłynie na użytkownika ani aplikację. Cosmos DB ogranicza każda partia zadań nie może przekroczyć 40MB rozmiaru, która jest sumą rozmiaru batchSize dokumentów, dlatego Zmniejsz tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100**) |

>[!TIP]
>ADF obsługuje dokument BSON w **trybie Strict**. Upewnij się, że zapytanie filtru jest w trybie Strict zamiast w trybie powłoki. Więcej opisu można znaleźć w [podręczniku MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>Eksportowanie dokumentów JSON jako-is

Tego łącznika MongoDB można użyć do eksportowania dokumentów JSON jako-pochodzi z kolekcji MongoDB do różnych magazynów opartych na plikach lub do Azure Cosmos DB. Aby uzyskać taką kopię schematu niezależny od, Pomiń sekcję "struktura" (nazywaną również *schematem*) w temacie zestaw danych i schemat w działaniu kopiowania.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z MongoDB do tabelarycznego ujścia, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
