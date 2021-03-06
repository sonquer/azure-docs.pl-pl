---
title: Kopiowanie danych z Impala za pomocą Azure Data Factory
description: Dowiedz się, jak skopiować dane z aparatu Impala do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: f465fe4bb69bc5ae81db6c78df51bf5133de1b60
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929304"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiowanie danych z Impala za pomocą Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z aparatu Impala. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Impala jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane z aparatu Impala, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Data Factory oferuje wbudowane sterowników, aby włączyć łączność. W związku z tym nie trzeba ręcznie zainstalować sterownik, aby użyć tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpocznij

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych łącznik Impala.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla aparatu Impala połączoną usługę.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **Impala**. | Tak |
| host | Adres IP lub hosta nazwę serwera Impala (czyli 192.168.222.160).  | Tak |
| port | Port TCP używany serwer Impala do nasłuchiwania połączeń klientów. Wartość domyślna to 21050.  | Nie |
| authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to **anonimowe**, **SASLUsername**, i **UsernameAndPassword**. | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do uzyskiwania dostępu do serwera Impala. Wartością domyślną jest anonimowe, gdy używasz SASLUsername.  | Nie |
| hasło | Hasło, które odnosi się do nazwy użytkownika, gdy używasz UsernameAndPassword. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to **false**.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM, który zawiera certyfikatów zaufanego urzędu certyfikacji służącego do weryfikowania serwera, po nawiązaniu połączenia za pośrednictwem protokołu SSL. Tę właściwość można ustawić tylko wtedy, gdy używasz protokołu SSL na środowiskiem Integration Runtime. Wartość domyślna to plik cacerts.pem zainstalowane za pomocą środowiska integration runtime.  | Nie |
| useSystemTrustStore | Określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z określonego pliku PEM. Wartość domyślna to **false**.  | Nie |
| allowHostNameCNMismatch | Określa, czy wymagają nazwy certyfikatów wystawionych przez urząd certyfikacji SSL Period z nazwą hosta serwera, po nawiązaniu połączenia za pośrednictwem protokołu SSL. Wartość domyślna to **false**.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to **false**.  | Nie |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych aparatu Impala.

Aby skopiować dane z aparatu Impala, należy ustawić właściwość typu zestawu danych na **ImpalaObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **ImpalaObject** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| table | Nazwa tabeli. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typ źródła Impala.

### <a name="impala-as-a-source-type"></a>Impala jako typ źródła

Aby skopiować dane z aparatu Impala, należy ustawić typ źródła w działaniu kopiowania, aby **ImpalaSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **ImpalaSource**. | Tak |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
