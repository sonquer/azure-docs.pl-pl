---
title: Przenoszenie danych za pomocą działania kopiowania
description: 'Dowiedz się więcej na temat przenoszenia danych w potokach Data Factory: migracja danych między magazynami w chmurze a magazynem lokalnym i magazynem w chmurze. Użyj działania kopiowania.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931918"
---
# <a name="move-data-by-using-copy-activity"></a>Przenoszenie danych za pomocą działania kopiowania
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-data-movement-activities.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-overview.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [działanie Copy in v2](../copy-activity-overview.md).

## <a name="overview"></a>Przegląd
W Azure Data Factory można użyć działania kopiowania do kopiowania danych między lokalnymi i magazynami danych w chmurze. Po skopiowaniu danych można je dodatkowo przekształcane i analizowane. Działanie kopiowania umożliwia również publikować wyniki analizy do analizy biznesowej (BI) i użycie aplikacji i przekształcania.

![Rola działania kopiowania](media/data-factory-data-movement-activities/copy-activity.png)

Działanie kopiowania jest obsługiwane przez bezpieczną, niezawodną, skalowalną i [globalnie dostępną usługę](#global). Ten artykuł zawiera szczegółowe informacje na temat przenoszenia danych w ramach działania Data Factory i kopiowania.

Najpierw zobaczmy, jak migracja danych odbywa się między dwoma magazynami danych w chmurze, a między lokalnym magazynem danych i magazynem danych w chmurze.

> [!NOTE]
> Aby uzyskać ogólne informacje na temat działań, zobacz [Opis potoków i działań](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiowanie danych między dwoma magazynami danych w chmurze
Gdy magazyny danych źródła i ujścia znajdują się w chmurze, działania kopiowania przechodzą przez następujące etapy w celu skopiowania danych ze źródła do ujścia. Usługa, która obsługuje działania kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresję/dekompresowanie, Mapowanie kolumn i konwersję typu. Robi te operacje na podstawie konfiguracji zestaw wejściowy, wyjściowy zestaw danych i działania kopiowania.
3. Zapisuje dane w docelowym magazynie danych.

Usługa automatycznie wybiera optymalny region do przeprowadzenia przenoszenia danych. Ten region jest zwykle najbliżej magazynu danych ujścia.

![Kopiowanie z chmury do chmury](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych i magazynem danych w chmurze
Aby bezpiecznie przenosić dane między lokalnym magazynem danych i magazynem danych w chmurze, należy zainstalować bramę Zarządzanie danymi na maszynie lokalnej. Brama Zarządzanie danymi to Agent, który umożliwia przenoszenie i przetwarzanie danych hybrydowych. Można go zainstalować na tym samym komputerze co magazyn danych lub na oddzielnym komputerze, który ma dostęp do magazynu danych.

W tym scenariuszu Brama Zarządzanie danymi wykonuje operacje serializacji/deserializacji, kompresję/dekompresor, Mapowanie kolumn i konwersję typów. Dane nie przepływają przez usługę Azure Data Factory. Zamiast tego Zarządzanie danymi Brama bezpośrednio zapisuje dane w magazynie docelowym.

![Kopiowanie z lokalizacji lokalnej do chmury](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zobacz [przenoszenie danych między lokalnymi i magazynami danych w chmurze,](data-factory-move-data-between-onprem-and-cloud.md) aby zapoznać się z wprowadzeniem i instruktażem. Aby uzyskać szczegółowe informacje o tym agencie, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) .

Możesz również przenieść dane z/do obsługiwanych magazynów danych, które są hostowane na maszynach wirtualnych IaaS platformy Azure za pomocą bramy Zarządzanie danymi. W takim przypadku można zainstalować bramę Zarządzanie danymi na tej samej maszynie wirtualnej, w której znajduje się sam magazyn danych, lub na oddzielnej maszynie wirtualnej, która ma dostęp do magazynu danych.

## <a name="supported-data-stores-and-formats"></a>Formaty i obsługiwane magazyny danych
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

> [!NOTE] 
> Jeśli musisz przenieść dane do/z magazynu danych nieobsługiwanego przez działanie kopiowania, użyj **niestandardowego działania** w usłudze Data Factory z własną logiką do obsługi kopiowania/przenoszenia danych. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików
Możesz użyć działania kopiowania, aby **skopiować pliki** między dwoma magazynami danych opartymi na plikach, można pominąć [sekcję format](data-factory-create-datasets.md) w definicjach zestawu danych wejściowych i wyjściowych. Dane są kopiowane efektywnie bez serializacji/deserializacji.

Działanie Copy odczytuje również dane z i zapisuje je w plikach w określonych formatach: Avro **Text, JSON,, Orc i Parquet**oraz kompresji kodery-dekoder w formacie **gzip, Wklęśnięcie, BZip2 i ZipDeflate** . Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółowymi informacjami.

Na przykład należy wykonać następujące działania kopiowania:

* Kopiowanie danych na lokalnym serwerze SQL i zapisywać do usługi Azure Data Lake Store w formacie ORC.
* Skopiuj pliki w formacie tekstowym (CSV) w systemie plików w środowisku lokalnym i zapisywać do obiektów Blob platformy Azure, w formacie Avro.
* Skopiuj pliki zip z systemu plików w środowisku lokalnym i następnie Dekompresuj ziemi do usługi Azure Data Lake Store.
* Kopiowanie danych w formacie tekstowym skompresowany (CSV) GZip z obiektów Blob platformy Azure i zapisu do usługi Azure SQL Database.

## <a name="global"></a>Dostępne globalnie przenoszenie danych
Azure Data Factory jest dostępna tylko w regionach zachodnie stany USA, Wschodnie stany USA i Europa Północna. Jednak usługa, która ma uprawnienia do kopiowania, jest dostępna globalnie w następujących regionach i lokalizacje geograficzne. Dostępnie topologii zapewnia efektywne przenoszenia, zazwyczaj pozwala uniknąć przeskoków między regionami. Zobacz [usługi według regionów](https://azure.microsoft.com/regions/#services) dostępność usługi Data Factory i przenoszenia danych w regionie.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiowanie danych między magazynami danych w chmurze
Gdy magazyny danych źródła i ujścia znajdują się w chmurze, Data Factory używa wdrożenia usługi w regionie, który znajduje się najbliżej ujścia w tej samej lokalizacji geograficznej, aby przenieść dane. Mapę można znaleźć w poniższej tabeli:

| Lokalizacja docelowa docelowych magazynów danych | Region docelowego magazynu danych | Region używany do przenoszenia danych |
|:--- |:--- |:--- |
| Stany Zjednoczone | Wschodnie stany USA | Wschodnie stany USA |
| &nbsp; | Wschodnie stany USA 2 | Wschodnie stany USA 2 |
| &nbsp; | Środkowe stany USA | Środkowe stany USA |
| &nbsp; | Północno-środkowe stany USA | Północno-środkowe stany USA |
| &nbsp; | Południowo-środkowe stany USA | Południowo-środkowe stany USA |
| &nbsp; | Zachodnio-środkowe stany USA | Zachodnio-środkowe stany USA |
| &nbsp; | Zachodnie stany USA | Zachodnie stany USA |
| &nbsp; | Zachodnie stany USA 2 | Zachodnie stany USA 2 |
| Kanada | Kanada Wschodnia | Kanada Środkowa |
| &nbsp; | Kanada Środkowa | Kanada Środkowa |
| Brazylia | Brazylia Południowa | Brazylia Południowa |
| Europa | Europa Północna | Europa Północna |
| &nbsp; | Europa Zachodnia | Europa Zachodnia |
| Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | Południowe Zjednoczone Królestwo |
| &nbsp; | Południowe Zjednoczone Królestwo | Południowe Zjednoczone Królestwo |
| Azja i Pacyfik | Azja Południowo-Wschodnia | Azja Południowo-Wschodnia |
| &nbsp; | Azja Wschodnia | Azja Południowo-Wschodnia |
| Australia | Australia Wschodnia | Australia Wschodnia |
| &nbsp; | Australia Południowo-Wschodnia | Australia Południowo-Wschodnia |
| Indie | Indie Środkowe | Indie Środkowe |
| &nbsp; | Indie Zachodnie | Indie Środkowe |
| &nbsp; | Indie Południowe | Indie Środkowe |
| Japonia | Japonia Wschodnia | Japonia Wschodnia |
| &nbsp; | Japonia Zachodnia | Japonia Wschodnia |
| Korea Południowa | Korea Środkowa | Korea Środkowa |
| &nbsp; | Korea Południowa | Korea Środkowa |

Alternatywnie można jawnie wskazać region usługi Data Factory, który ma być używany do wykonywania kopii, określając właściwość `executionLocation` w obszarze `typeProperties`działania kopiowania. Obsługiwane wartości dla tej właściwości są wymienione w powyższym **regionie używanym do przenoszenia danych** . Zwróć uwagę na to, że dane przechodzą przez ten region przez sieć podczas kopiowania. Na przykład, aby skopiować między sklepami systemu Azure w Korei, można określić, `"executionLocation": "Japan East"` mają być kierowane przez region Japonia (zobacz [przykładowe dane JSON](#by-using-json-scripts) jako odwołanie).

> [!NOTE]
> Jeśli region docelowego magazynu danych nie znajduje się na liście wcześniejszych lub nie można go wykryć, domyślnie działanie kopiowania kończy się niepowodzeniem zamiast przechodzić przez alternatywny region, chyba że `executionLocation`. Lista obsługiwanych regionów zostanie rozszerzona z upływem czasu.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych i magazynem danych w chmurze
Gdy dane są kopiowane między środowiskiem lokalnym (lub maszynami wirtualnymi platformy Azure/IaaS) i magazynami w chmurze, usługa [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) wykonuje przenoszenie danych na maszynie lokalnej lub maszynie wirtualnej. Dane nie przepływają przez usługę w chmurze, chyba że używana jest możliwość [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) . W takim przypadku dane są przesyłane przez przemieszczanie magazynu obiektów blob platformy Azure przed jego zapisaniem w magazynie danych ujścia.

## <a name="create-a-pipeline-with-copy-activity"></a>Tworzenie potoku za pomocą działania kopiowania
Potok z działaniem kopiowania można utworzyć na kilka sposobów:

### <a name="by-using-the-copy-wizard"></a>Za pomocą Kreatora kopiowania
Kreator kopiowania Data Factory ułatwia utworzenie potoku za pomocą działania kopiowania. Ten potok umożliwia kopiowanie danych z obsługiwanych źródeł do miejsc docelowych *bez zapisywania definicji JSON* dla połączonych usług, zestawów danych i potoków. Szczegółowe informacje na temat Kreatora można znaleźć w temacie [Data Factory Copy Wizard](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>Za pomocą skryptów JSON
Możesz użyć edytora Data Factory w programie Visual Studio lub Azure PowerShell, aby utworzyć definicję JSON dla potoku (za pomocą działania kopiowania). Następnie można wdrożyć go w celu utworzenia potoku w Data Factory. Zobacz Samouczek: Aby zapoznać się z instrukcjami krok po kroku, [Użyj działania kopiowania w potoku Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .    

Właściwości JSON (takie jak nazwa, opis, tabele wejściowe i wyjściowe oraz zasady) są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji `typeProperties` działania, różnią się w zależności od typu działania.

W przypadku działania kopiowania sekcja `typeProperties` różni się w zależności od typów źródeł i ujścia. Kliknij element Źródło/ujścia w sekcji [obsługiwane źródła i ujścia](#supported-data-stores-and-formats) , aby poznać właściwości typu obsługiwane przez działanie kopiowania dla tego magazynu danych.

Oto przykładowa definicja JSON:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Harmonogram zdefiniowany w wyjściowym zestawie danych określa, kiedy działa działanie (na przykład: **codziennie**, częstotliwość jako **dzień**i interwał jako **1**). Działanie kopiuje dane z wejściowego zestawu danych (**Źródło**) do wyjściowego zestawu danych (**ujścia**).

Można określić więcej niż jeden wejściowy zestaw danych do działania kopiowania. Są one używane do weryfikowania zależności przed uruchomieniem działania. Jednak tylko dane z pierwszego zestawu danych są kopiowane do docelowego zestawu danych. Aby uzyskać więcej informacji, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md), która opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Ponadto Wyświetla zaobserwowanego podczas testowania wewnętrznego i w tym artykule omówiono różne sposoby, aby zoptymalizować wydajność działania kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia
Domyślnie działanie kopiowania spowoduje zatrzymanie kopiowania danych i zwrócenie błędu w przypadku wystąpienia niezgodnych danych między źródłem i ujściam; Mimo że można jawnie skonfigurować program, aby pominąć i zarejestrować niezgodne wiersze i skopiować te zgodne dane, aby kopie powiodło się. Zobacz [działania kopiowania odporności na uszkodzenia](data-factory-copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Zapoznaj się z [zagadnieniami](data-factory-data-movement-security-considerations.md)dotyczącymi zabezpieczeń, które opisują infrastrukturę zabezpieczeń, która w Azure Data Factory używać usług przenoszenia danych w celu zabezpieczania danych.

## <a name="scheduling-and-sequential-copy"></a>Planowanie i kopiowanie sekwencyjne
Zapoznaj się z tematem [Planowanie i wykonywanie,](data-factory-scheduling-and-execution.md) Aby uzyskać szczegółowe informacje o tym, jak planowanie i wykonywanie działa w Data Factory. Istnieje możliwość uruchomienia wielu operacji kopiowania jeden po drugim w sekwencyjny/uporządkowany sposób. Zapoznaj się z sekcją [Kopiuj sekwencyjnie](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) .

## <a name="type-conversions"></a>Konwersje typu
Różne magazyny danych mają różne systemy typów natywnych. Działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia z następującymi krokami:

1. Konwertuj z natywnych typów źródła na typ .NET.
2. Konwertuj z typu .NET na natywny typ ujścia.

Mapowanie z systemu typu natywnego na typ .NET dla magazynu danych znajduje się w odpowiednim artykule magazynu danych. (Kliknij określone łącze w tabeli obsługiwane magazyny danych). Można użyć tych mapowań do określenia odpowiednich typów podczas tworzenia tabel, tak że działanie Copy wykonuje odpowiednie konwersje.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o działaniu kopiowania, zobacz [Kopiowanie danych z usługi Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Aby dowiedzieć się więcej na temat przenoszenia danych z lokalnego magazynu danych do magazynu danych w chmurze, zobacz [przenoszenie danych z lokalnych do magazynów danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md).
