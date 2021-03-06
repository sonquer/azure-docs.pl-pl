---
title: Konfigurowanie wydajności dla Azure-SSIS Integration Runtime
description: Dowiedz się, jak skonfigurować właściwości Azure-SSIS Integration Runtime w celu uzyskania wysokiej wydajności
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: 15aac35a7ebc505e76ddfd0c538c4fddb7b2d9ff
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930543"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności

W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime (IR) w celu uzyskania wysokiej wydajności. Azure-SSIS IR umożliwia wdrażanie i uruchamianie pakietów SQL Server Integration Services (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat Azure-SSIS IR, zobacz artykuł [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) . Aby uzyskać informacje na temat wdrażania i uruchamiania pakietów usług SSIS na platformie Azure, zobacz [Podnieś i przenieś SQL Server Integration Services obciążeń do chmury](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ten artykuł zawiera wyniki wydajności i obserwacje z testów wewnętrznych wykonanych przez członków zespołu programistycznego usług SSIS. Wyniki mogą się różnić. Wykonaj własne testy przed zakończeniem ustawień konfiguracji, które mają wpływ na koszt i wydajność.

## <a name="properties-to-configure"></a>Właściwości do skonfigurowania

W poniższej części skryptu konfiguracji przedstawiono właściwości, które można skonfigurować podczas tworzenia Azure-SSIS Integration Runtime. Aby uzyskać kompletny skrypt programu PowerShell i opis, zobacz [wdrażanie pakietów SQL Server Integration Services na platformie Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** jest lokalizacją dla węzła procesu roboczego Integration Runtime. Węzeł procesu roboczego utrzymuje stałe połączenie z bazą danych wykazu usług SSIS (SSISDB) w usłudze Azure SQL Database. Ustaw **AzureSSISLocation** w tej samej lokalizacji co serwer SQL Database, który hostuje SSISDB, co umożliwia wydajne działanie środowiska Integration Runtime.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, w tym Azure-SSIS IR, obsługuje następujące opcje:
-   Standardowa\_A4\_v2
-   Standardowa\_A8\_v2
-   Standardowa\_D1\_v2
-   Standardowa\_D2\_v2
-   Standardowa\_D3\_v2
-   Standardowa\_D4\_v2
-   Standardowa\_D2\_v3
-   Standardowa\_D4\_v3
-   Standardowa\_D8\_v3
-   Standardowa\_D16\_v3
-   Standardowa\_D32\_v3
-   Standardowa\_D64\_v3
-   Standardowa\_E2\_v3
-   Standardowa\_E4\_v3
-   Standardowa\_E8\_v3
-   Standardowa\_E16\_v3
-   Standardowa\_E32\_v3
-   Standardowa\_E64\_v3

W nieoficjalnym teście prowadzonym przez zespół inżynierów usług SSIS seria D wydaje się bardziej odpowiednia dla wykonywania pakietów SSIS niż w przypadku serii.

-   Stosunek wydajności/ceny serii D jest wyższy niż seria, a stosunek wydajności/ceny dla serii v3 jest wyższy niż seria v2.
-   Przepływność dla serii D jest wyższa niż seria w tej samej cenie, a przepływność dla serii v3 jest wyższa niż seria v2 w tej samej cenie.
-   Węzły serii v2 Azure-SSIS IR nie są odpowiednie dla konfiguracji niestandardowej, dlatego należy zamiast tego użyć węzłów serii v3. Jeśli korzystasz już z węzłów serii w wersji 2, przejdź do korzystania z węzłów serii v3 najszybciej, jak to możliwe.
-   Seria E ma rozmiary maszyn wirtualnych zoptymalizowanych pod kątem pamięci, które zapewniają większy stosunek pamięci do procesora CPU niż inne maszyny. Jeśli pakiet wymaga dużej ilości pamięci, można rozważyć wybranie maszyny wirtualnej serii E.

### <a name="configure-for-execution-speed"></a>Skonfiguruj na potrzeby szybkości wykonywania
Jeśli nie masz wielu pakietów do uruchomienia i chcesz szybko uruchamiać pakiety, użyj informacji z poniższej tabeli, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

Te dane przedstawiają wykonywanie pojedynczego pakietu na jednym węźle procesu roboczego. Pakiet ładuje 3 000 000 rekordów z imionami i kolumnami nazwiska z usługi Azure Blob Storage, generuje kolumnę pełna nazwa i zapisuje rekordy, które mają pełną nazwę o długości przekraczającej 20 znaków na platformie Azure Blob Storage.

![Szybkość wykonywania pakietu SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie na potrzeby ogólnej przepływności

Jeśli masz wiele pakietów do uruchomienia i masz największe znaczenie dla ogólnej przepływności, użyj informacji z poniższej tabeli, aby wybrać typ maszyny wirtualnej odpowiedni dla danego scenariusza.

![W Integration Runtime SSIS maksymalna przepływność](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność środowiska Integration Runtime. Przepływność środowiska Integration Runtime jest proporcjonalna do **AzureSSISNodeNumber**. W pierwszej kolejności ustaw **AzureSSISNodeNumber** na małą wartość, Monitoruj przepływność środowiska Integration Runtime, a następnie Dostosuj wartość dla danego scenariusza. Aby ponownie skonfigurować liczbę węzłów procesu roboczego, zobacz [Zarządzanie środowiskiem Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Gdy używasz już zaawansowanego węzła procesu roboczego do uruchamiania pakietów, zwiększenie **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepływność środowiska Integration Runtime. W przypadku węzłów Standard_D1_v2 obsługiwane są 1-4 wykonania równoległe na węzeł. Dla wszystkich innych typów węzłów, obsługiwane są maksymalnie 1-maksymalne (2 x liczba rdzeni, 8) wykonywanie równoległe na węzeł. Jeśli potrzebujesz **AzureSSISMaxParallelExecutionsPerNode** poza maksymalną obsługiwaną przez nas wartością, możesz otworzyć bilet pomocy technicznej i możemy zwiększyć maksymalną wartość dla Ciebie, a następnie użyć programu Azure PowerShell do aktualizacji **AzureSSISMaxParallelExecutionsPerNode**.
Można oszacować odpowiednią wartość w oparciu o koszt pakietu oraz następujące konfiguracje dla węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardowa\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standardowa\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standardowa\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standardowa\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standardowa\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standardowa\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standardowa\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Standardowa\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standardowa\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standardowa\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| Standardowa\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| Standardowa\_D64\_v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standardowa\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| Standardowa\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standardowa\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standardowa\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standardowa\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| Standardowa\_E64\_v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Poniżej przedstawiono wskazówki dotyczące ustawiania prawidłowej wartości właściwości **AzureSSISMaxParallelExecutionsPerNode** : 

1. Najpierw ustaw na małą wartość.
2. Zwiększ go o niewielką ilość, aby sprawdzić, czy ogólna przepływność jest ulepszona.
3. Zatrzymaj zwiększenie wartości, gdy ogólna przepływność osiągnie wartość maksymalną.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** to warstwa cenowa bazy danych wykazu usług SSIS (SSISDB) w usłudze Azure SQL Database. To ustawienie ma wpływ na maksymalną liczbę procesów roboczych w wystąpieniu środowiska IR, szybkość do kolejki wykonania pakietu oraz szybkość ładowania dziennika wykonania.

-   Jeśli nie masz opieki nad szybkością wykonywania pakietów w kolejce i załadowaniu dziennika wykonywania, możesz wybrać najniższą warstwę cenową bazy danych. Azure SQL Database z cenami podstawowymi obsługuje 8 procesów roboczych w wystąpieniu środowiska Integration Runtime.

-   Wybierz bardziej wydajną bazę danych niż podstawowa, jeśli liczba procesów roboczych jest większa niż 8, lub liczba rdzeni jest większa niż 50. W przeciwnym razie baza danych stanowi wąskie gardła wystąpienia środowiska Integration Runtime i ogólna wydajność ma negatywny wpływ.

-   Wybierz bardziej wydajną bazę danych, na przykład S3, jeśli poziom rejestrowania jest ustawiony na pełne. Zgodnie z naszymi nieoficjalnymi testami, warstwa cenowa S3 może obsługiwać wykonywanie pakietów SSIS z 2 węzłami, 128 liczbą równoległą i pełnym poziomem rejestrowania.

Warstwę cenową bazy danych można również dostosować na podstawie informacji o użyciu [jednostek transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (DTU) dostępnych na Azure Portal.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu usług SSIS do uruchamiania na platformie Azure różni się od projektowania pakietu do wykonania lokalnego. Zamiast łączyć wiele niezależnych zadań w tym samym pakiecie, rozdziel je na kilka pakietów, aby zwiększyć efektywność wykonywania w Azure-SSIS IR. Utwórz wykonanie pakietu dla każdego pakietu, aby nie musiały czekać na zakończenie każdego z nich. To podejście korzysta ze skalowalności środowiska Azure-SSIS Integration Runtime i zwiększa ogólną przepływność.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat Azure-SSIS Integration Runtime. Zobacz [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
