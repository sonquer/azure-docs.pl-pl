---
title: Wywołaj pakiet SSIS za pomocą działania procedury składowanej Azure Data Factory
description: W tym artykule opisano sposób wywoływania pakietu SQL Server Integration Services (SSIS) z potoku Azure Data Factory za pomocą działania procedury składowanej.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438784"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Wywołaj pakiet SSIS za pomocą działania procedury składowanej w Azure Data Factory
W tym artykule opisano sposób wywoływania pakietu SSIS z potoku Azure Data Factory przy użyciu działania procedury składowanej. 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [wywoływanie pakietów SSIS za pomocą działania procedury składowanej w](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Baza danych SQL Azure 
W przewodniku w tym artykule jest stosowana baza danych Azure SQL Database, która hostuje wykaz usług SSIS. Można również użyć Azure SQL Database wystąpienia zarządzanego.

### <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Utwórz środowisko Azure-SSIS Integration Runtime, jeśli go nie masz, postępując zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażanie pakietów usług SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Nie można użyć Data Factory w wersji 1 do utworzenia środowiska Azure-SSIS Integration Runtime. 

## <a name="azure-powershell"></a>Program Azure PowerShell
W tej sekcji użyjesz Azure PowerShell, aby utworzyć potok Data Factory za pomocą działania procedury składowanej, które wywołuje pakiet usług SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Poniższa procedura zawiera procedurę tworzenia fabryki danych. Tworzysz potok za pomocą działania procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę przechowywaną w bazie danych SSISDB w celu uruchomienia pakietu usług SSIS.

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.
2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie. 
3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **New-AzDataFactory** , używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Utwórz połączoną usługę w celu połączenia bazy danych Azure SQL Database, która hostuje katalog SSIS w fabryce danych. Data Factory używa informacji w tej połączonej usłudze, aby nawiązać połączenie z bazą danych SSISDB i wykonać procedurę przechowywaną w celu uruchomienia pakietu usług SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService. JSON** w folderze **C:\ADF\RunSSISPackage** o następującej zawartości: 

    > [!IMPORTANT]
    > Przed zapisaniem pliku Zastąp ciąg &lt;ServerName&gt;&lt;username&gt;@&lt;ServerName&gt; i &lt;hasło&gt; z wartościami Azure SQL Database.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. W **Azure PowerShell**przejdź do folderu **C:\ADF\RunSSISPackage** .
3. Uruchom polecenie cmdlet **New-AzDataFactoryLinkedService** , aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Ten wyjściowy zestaw danych jest fikcyjnym zestawem danych, który jest dyskiem z harmonogramem potoku. Należy zauważyć, że częstotliwość jest ustawiona na wartość Godzina, a interwał jest ustawiony na 1. W związku z tym potok jest uruchamiany raz na godzinę w czasie rozpoczęcia i zakończenia potoku. 

1. Utwórz plik OutputDataset. JSON o następującej zawartości: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Uruchom polecenie cmdlet **New-AzDataFactoryDataset** , aby utworzyć zestaw danych. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej 
W tym kroku utworzysz potok z działaniem procedury składowanej. Działanie wywołuje sp_executesql procedury składowanej w celu uruchomienia pakietu usług SSIS. 

1. W folderze **C:\ADF\RunSSISPackage** Utwórz plik JSON o nazwie Moje **potok. JSON** o następującej zawartości:

    > [!IMPORTANT]
    > Zastąp &lt;nazwę folderu&gt;, &lt;nazwę projektu&gt;, &lt;nazwa pakietu&gt; z nazwami folderu, projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Aby utworzyć potok: **RunSSISPackagePipeline**, uruchom polecenie cmdlet **New-AzDataFactoryPipeline** .

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Uruchom polecenie **Get-AzDataFactorySlice** , aby uzyskać szczegółowe informacje na temat wszystkich wycinków wyjściowego zestawu danych * *, który jest tabelą wyjściową potoku.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Zauważ, że określana tutaj właściwość StartDateTime oznacza taki sam czas rozpoczęcia jak określony w potoku JSON. 
1. Uruchom **Get-AzDataFactoryRun** , aby uzyskać szczegółowe informacje o uruchomieniach działania dla określonego wycinka.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Możesz kontynuować uruchamianie tego polecenia cmdlet do momentu, gdy wycinek będzie widoczny w stanie **Gotowe** lub **Niepowodzenie**. 

    Aby sprawdzić, czy pakiet został uruchomiony, możesz uruchomić następujące zapytanie względem bazy danych SSISDB na serwerze Azure SQL. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje o działaniu procedury składowanej, zobacz artykuł dotyczący [działania procedury składowanej](data-factory-stored-proc-activity.md) .

