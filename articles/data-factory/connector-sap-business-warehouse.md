---
title: Kopiuj dane z SAP BW
description: Informacje o kopiowaniu danych z programu SAP Business Warehouse do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 0c37d77ca73ddbe8b79351f90275a1d639757633
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923733"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiowanie danych z programu SAP Business Warehouse przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Bieżąca wersja](connector-sap-business-warehouse.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu SAP Business Warehouse (BW). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik SAP Business Warehouse jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu SAP Business Warehouse można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W przypadku tego łącznika SAP Business Warehouse obsługiwane są następujące rozwiązania:

- SAP Business Warehouse w **wersji 7. x**.
- Kopiowanie danych z **InfoCubes i QueryCubes** (w tym zapytań BEX) przy użyciu zapytań MDX.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse, należy wykonać następujące czynności:

- Skonfiguruj samodzielny Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj **bibliotekę SAP NetWeaver** na maszynie Integration Runtime. Bibliotekę SAP NetWeaver można uzyskać od administratora SAP lub bezpośrednio z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukaj uwagę na oprogramowanie **SAP #1025361** , aby pobrać lokalizację pobierania dla najnowszej wersji. Upewnij się, że wybrano **64-bitową** bibliotekę SAP NetWeaver, która jest zgodna z instalacją Integration Runtime. Następnie zainstaluj wszystkie pliki zawarte w zestawie SDK SAP NetWeaver RFC zgodnie z uwagą SAP. Biblioteka SAP NetWeaver jest również dostępna w instalacji narzędzi klienckich SAP.

>[!TIP]
>Aby rozwiązać problem z łącznością SAP BW, upewnij się, że:
>- Wszystkie biblioteki zależności wyodrębnione z zestawu SDK NetWeaver RFC znajdują się w folderze%Windir%\System32. Zwykle jest to plik icudt34. dll, icuin34. dll, icuuc34. dll, libicudecnumber. dll, librfc32. dll, libsapucum. dll, sapcrypto. dll, sapcryto_old. dll, sapnwrfc. dll.
>- Porty używane do nawiązania połączenia z serwerem SAP są włączane na samoobsługowym komputerze IR, który zwykle jest port 3300 i 3201.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika SAP Business Warehouse.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla połączonej usługi SAP Business Warehouse (BW):

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **SAPBW** | Tak |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | Tak |
| systemNumber | Numer systemu SAP BW.<br/>Dozwolona wartość: dwucyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| clientId | Identyfikator klienta klienta w systemie SAP w.<br/>Dozwolona wartość: 3-cyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| hasło | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP BW.

Aby skopiować dane z SAP BW, ustaw właściwość Type zestawu danych na **SapBwCube**. Brak właściwości specyficznych dla typu, które są obsługiwane dla SAP BWgo zestawu danych typu relacyjnego.

**Przykład:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz korzystać z nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP BW źródło.

### <a name="sap-bw-as-source"></a>SAP BW jako źródło

Aby skopiować dane z SAP BW, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **SapBwSource** | Tak |
| query | Określa zapytanie MDX do odczytu danych z wystąpienia SAP BW. | Tak |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz źródła z wpisaną `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="data-type-mapping-for-sap-bw"></a>Mapowanie typu danych dla SAP BW

Podczas kopiowania danych z SAP BW następujące mapowania są używane z SAP BW typów danych do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych SAP BW | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ACCP | Int |
| CHAR | Ciąg |
| CLNT | Ciąg |
| CURR | Decimal |
| CUKY | Ciąg |
| GRUDZIEŃ | Decimal |
| FLTP | Double |
| INT1 | Bajtów |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Ciąg |
| LCHR | Ciąg |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Ciąg |
| JEDNOSTKA | Ciąg |
| DATS | Ciąg |
| NUMC | Ciąg |
| TIMS | Ciąg |


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
