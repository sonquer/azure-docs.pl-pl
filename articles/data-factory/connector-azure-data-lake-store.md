---
title: Kopiuj dane do lub z Azure Data Lake Storage Gen1
description: Informacje o kopiowaniu danych z obsługiwanych magazynów danych źródłowych do Azure Data Lake Store lub z Data Lake Store do obsługiwanych magazynów ujścia przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4dae0d10f103710a0e6039127c5c1cacb63c03c4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893104"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję Azure Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

W tym artykule opisano sposób kopiowania danych do i z Azure Data Lake Storage Gen1. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Data Lake Storage Gen1 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md) 
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W przypadku tego łącznika możesz:

- Skopiuj pliki przy użyciu jednej z następujących metod uwierzytelniania: nazwy głównej usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Kopiuj pliki jako lub Analizuj lub generuj pliki z [obsługiwanymi formatami plików i koderami-dekoder kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowaj listy ACL](#preserve-acls-to-data-lake-storage-gen2) podczas kopiowania do Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu własnego środowiska Integration Runtime Skonfiguruj zaporę firmową tak, aby zezwalała na ruch wychodzący do `<ADLS account name>.azuredatalakestore.net` i `login.microsoftonline.com/<tenant>/oauth2/token` na porcie 443. Jest to usługa tokenu zabezpieczającego platformy Azure, z którą musi się komunikować środowisko Integration Runtime w celu uzyskania tokenu dostępu.

## <a name="get-started"></a>Rozpocznij

> [!TIP]
> Aby zapoznać się z przewodnikiem dotyczącym korzystania z łącznika Azure Data Lake Store, zobacz [ładowanie danych do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla Azure Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla Azure Data Lake Store połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość `type` musi mieć wartość **AzureDataLakeStore**. | Tak |
| dataLakeStoreUri | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jedną z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli ta właściwość nie jest określona, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

### <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania nazwy głównej usługi

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki.

1. Zarejestruj jednostkę aplikacji w Azure Active Directory i Udziel jej dostępu do Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia. Zapoznaj się z przykładami dotyczącymi działania uprawnień w Data Lake Storage Gen1 z poziomu [kontroli dostępu w programie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako źródło**: w **Eksploratorze danych** > **dostęp**, udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **odczytu** plików do skopiowania. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Kontrola dostępu na poziomie konta (IAM) nie jest wymagana.
    - **Jako ujścia**: w **Eksploratorze danych** > **dostęp**, udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **zapisu** dla folderu ujścia. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Jeśli używasz środowiska Azure Integration Runtime do kopiowania (źródła i ujścia znajdują się w chmurze), w obszarze IAM Przydziel co najmniej rolę **czytnika** , aby umożliwić Data Factory wykryć region Data Lake Store. Jeśli chcesz uniknąć tej roli mapy IAM, jawnie [Utwórz środowisko Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) przy użyciu lokalizacji Data Lake Store. Na przykład jeśli Data Lake Store znajduje się w Europie Zachodniej, Utwórz środowisko Azure Integration Runtime z lokalizacją "Europa Zachodnia". Skojarz je w połączonej usłudze Data Lake Store, jak pokazano w poniższym przykładzie.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako `SecureString`, aby bezpiecznie je przechowywać w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| tenant | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w której znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Tak |

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Korzystanie z tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania Data Lake Store, podobnie jak w przypadku korzystania z własnej nazwy głównej usługi. Umożliwia to wyznaczeniu fabryki dostęp do danych i ich kopiowanie do lub z Data Lake Store.

Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobierz informacje o tożsamości zarządzanej fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi", która jest generowana wraz z fabryką.

2. Przyznaj zarządzanej tożsamości dostęp do Data Lake Store. Zapoznaj się z przykładami dotyczącymi działania uprawnień w Data Lake Storage Gen1 z poziomu [kontroli dostępu w programie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Jako źródło**: w **Eksploratorze danych** > **dostęp**, udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **odczytu** plików do skopiowania. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Kontrola dostępu na poziomie konta (IAM) nie jest wymagana.
    - **Jako ujścia**: w **Eksploratorze danych** > **dostęp**, udziel co najmniej uprawnień **Execute** dla wszystkich folderów nadrzędnych, łącznie z uprawnieniami do **zapisu** dla folderu ujścia. Można wybrać opcję dodania do **tego folderu i wszystkich elementów podrzędnych** dla opcji cykliczne oraz dodać jako **uprawnienie dostępu i domyślny wpis uprawnienia**. Jeśli używasz środowiska Azure Integration Runtime do kopiowania (źródła i ujścia znajdują się w chmurze), w obszarze IAM Przydziel co najmniej rolę **czytnika** , aby umożliwić Data Factory wykryć region Data Lake Store. Jeśli chcesz uniknąć tej roli mapy IAM, jawnie [Utwórz środowisko Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) przy użyciu lokalizacji Data Lake Store. Skojarz je w połączonej usłudze Data Lake Store, jak pokazano w poniższym przykładzie.

W Azure Data Factory nie trzeba określać żadnych właściwości oprócz ogólnych informacji Data Lake Store w połączonej usłudze.

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość Type w obszarze `location` w zestawie danych musi być ustawiona na wartość **AzureDataLakeStoreLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez Azure Data Lake Store źródła i ujścia.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze Ustawienia `storeSettings` w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureDataLakeStoreReadSettings**. | Tak                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi pod daną folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli nie określono `fileName` w zestawie danych |
| modifiedDatetimeStart    | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń do współbieżnego połączenia z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako ujście

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla Azure Data Lake Store Gen1 w obszarze Ustawienia `storeSettings` w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureDataLakeStoreWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | recursive | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Puste, Użyj domyślnego) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Puste, Użyj domyślnego) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Przykłady zachowania operacji kopiowania

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości `recursive` i `copyBehavior`.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 + file3 + File4 + File5 zawartość jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 zawartości są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa File1<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachowaj listy ACL do Data Lake Storage Gen2

>[!TIP]
>Aby w ogólny sposób skopiować dane z Azure Data Lake Storage Gen1 do Gen2, zobacz artykuł [Kopiowanie danych z Azure Data Lake Storage Gen1 do Gen2 z Azure Data Factoryami](load-azure-data-lake-storage-gen2-from-gen1.md) w celu uzyskania wskazówek i najlepszych rozwiązań.

Jeśli chcesz replikować listy kontroli dostępu (ACL) wraz z plikami danych podczas uaktualniania z Data Lake Storage Gen1 do Data Lake Storage Gen2, zobacz temat [zachowywanie list ACL z Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać pliki z Azure Data Lake Storage Gen1 w formacie JSON, Avro, rozdzielony tekst lub Parquet. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [transformacja ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródłowej można odczytywać z kontenera, folderu lub pojedynczego pliku w Azure Data Lake Storage Gen1. Karta **Opcje źródła** umożliwia zarządzanie sposobem odczytywania plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżka symboli wieloznacznych:** Użycie wzorca wieloznacznego spowoduje, że ADF będzie przełączać pętlę do każdego pasującego folderu i pliku w ramach pojedynczego przekształcenia źródła. Jest to efektywny sposób przetwarzania wielu plików w ramach pojedynczego przepływu. Dodaj wiele symboli wieloznacznych wzorców ze znakiem +, który pojawia się po umieszczeniu wskaźnika myszy na istniejącym wzorcu symboli wieloznacznych.

Z kontenera źródłowego wybierz serię plików, które pasują do wzorca. W zestawie danych można określić tylko kontener. Ścieżka symbolu wieloznacznego musi zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*``` reprezentuje dowolny zestaw znaków
* ```**``` reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?``` zastępuje jeden znak
* ```[]``` dopasowuje jeden z więcej znaków w nawiasach

* ```/data/sales/**/*.csv``` pobiera wszystkie pliki CSV w obszarze/Data/Sales
* ```/data/sales/20??/**``` pobiera wszystkie pliki w 20-wieku
* ```/data/sales/2004/*/12/[XY]1?.csv``` pobiera wszystkie pliki CSV w 2004 w grudniu, zaczynając od X lub Y poprzedzone przez dwucyfrowy numer

**Ścieżka katalogu głównego partycji:** Jeśli masz partycjonowane foldery w źródle plików o formacie ```key=value``` (na przykład Year = 2019), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami partycjonowanymi oraz pliki liści, które chcesz odczytać.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia ścieżka katalogu głównego partycji, aby określić, jaki jest najwyższy poziom struktury folderów. Gdy przeglądasz zawartość danych za pośrednictwem wersji zapoznawczej, zobaczysz, że na AUTOMATYCZNYm ekranie zostaną dodane rozpoznane partycje znalezione na każdym z poziomów folderów.

![Ścieżka katalogu głównego partycji](media/data-flow/partfile1.png "Podgląd ścieżki katalogu głównego partycji")

**Lista plików:** To jest zestaw plików. Utwórz plik tekstowy, który zawiera listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Zapisz nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nazwę nowej kolumny, aby zapisać ciąg nazw plików.

**Po zakończeniu:** Wybierz, aby nic nie robić z plikiem źródłowym po uruchomieniu przepływu danych, usuń plik źródłowy lub Przenieś plik źródłowy. Ścieżki do przenoszenia są względne.

Aby przenieść pliki źródłowe do innej lokalizacji po przetworzeniu, najpierw wybierz pozycję "Przenieś" dla operacji na pliku. Następnie ustaw katalog "z". Jeśli nie używasz symboli wieloznacznych dla ścieżki, ustawienie "od" będzie takie samo jak folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolem wieloznacznym, składnia będzie wyglądać następująco:

```/data/sales/20??/**/*.csv```

Możesz określić wartość "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie pliki, które zostały objęte usługą/Data/Sales, są przenoszone do/backup/priorSales.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko wtedy, gdy rozpoczyna się przepływ danych z uruchomienia potoku (debugowania lub przebiegu wykonywania) używającego działania wykonywania przepływu danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatniej modyfikacji:** Można filtrować, które pliki są przetwarzane, określając zakres dat, po którym były ostatnio modyfikowane. Wszystkie daty i godziny są w formacie UTC. 

### <a name="sink-properties"></a>Właściwości ujścia

W transformacji ujścia można pisać do kontenera lub folderu w Azure Data Lake Storage Gen1. Karta **Ustawienia** umożliwia zarządzanie sposobem pisania plików.

![Opcje ujścia](media/data-flow/file-sink-settings.png "Opcje ujścia")

**Wyczyść folder:** Określa, czy folder docelowy jest usuwany przed zapisaniem danych.

**Opcja nazwy pliku:** Określa sposób nazywania plików docelowych w folderze docelowym. Nazwy plików są następujące:
   * **Domyślnie**: Zezwalaj platformie Spark na nazwy plików na podstawie wartości domyślnych części.
   * **Wzorzec**: wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład **pożyczki [n]. csv** utworzy loans1. csv, loans2. csv i tak dalej.
   * **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny. Ścieżka jest względna wobec kontenera DataSet, a nie folderu docelowego.
   * **Dane wyjściowe do pojedynczego pliku**: Połącz podzielone na partycje pliki wyjściowe w jeden nazwany plik. Ścieżka jest określana względem folderu DataSet. Należy pamiętać, że operacja scalania może się nie powieść w zależności od rozmiaru węzła. Ta opcja nie jest zalecana w przypadku dużych zestawów danych.

**Wszystkie oferty:** Określa, czy wszystkie wartości mają być ujęte w cudzysłowy

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureDataLakeStoreFile**. |Tak |
| folderPath | Ścieżka do folderu w Data Lake Store. Jeśli nie zostanie określony, wskazuje katalog główny. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany. Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Na przykład: RootFolder/subfolder/. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Nie |
| fileName | Nazwa lub filtr symboli wieloznacznych dla plików pod określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru, dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^`, aby wyjść, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]* ", na przykład" Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to " *[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]* ", na przykład" MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli ich czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu są obsługiwane następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format), i [formatu Parquet ](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z częścią **folderu i nazwą pliku o** nazwie.<br>Aby skopiować podzestaw plików w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Model źródłowy starszego działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość `type` źródła działania kopiowania musi być ustawiona na wartość **AzureDataLakeStoreSource**. |Tak |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Gdy `recursive` ma wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Model ujścia starszej aktywności kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość `type` ujścia działania kopiowania musi być ustawiona na wartość **AzureDataLakeStoreSink**. |Tak |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie nazwa pliku jest generowana automatycznie. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
