---
title: Poszerzenie skryptów U-SQL przy użyciu języka R w Azure Data Lake Analytics
description: Dowiedz się, jak uruchomić kod języka R w skryptach U-SQL przy użyciu Azure Data Lake Analytics. Osadź kod języka R wewnątrz lub odwołuje się do plików.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672700"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Poszerzenie skryptów U-SQL przy użyciu kodu języka R w Azure Data Lake Analytics

Poniższy przykład ilustruje podstawowe kroki wdrażania kodu języka R:
* Użyj instrukcji `REFERENCE ASSEMBLY`, aby włączyć rozszerzenia języka R dla skryptu U-SQL.
* Użyj operacji `REDUCE`, aby podzielić dane wejściowe klucza.
* Rozszerzenia języka R dla języka U-SQL zawierają wbudowanego rozwiązania do redukcji (`Extension.R.Reducer`), które uruchamia kod R na każdym wierzchołku przypisanym do tego elementu. 
* Użycie dedykowanych, nazwanych ramek danych o nazwie `inputFromUSQL` i `outputToUSQL` w celu przekazywania danych między literami U-SQL i R. nazwy identyfikatorów ramek danych wejściowych i wyjściowych są stałe (oznacza to, że użytkownicy nie mogą zmienić wstępnie zdefiniowanych nazw wejściowych i wyjściowych identyfikatorów ramek danych).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Osadzanie kodu języka R w skrypcie U-SQL

Możesz użyć wbudowanego kodu R skryptu U-SQL za pomocą parametru polecenia `Extension.R.Reducer`. Na przykład można zadeklarować skrypt języka R jako zmienną ciągu i przekazać go jako parametr do elementu zmniejszającego.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Przechowuj kod w języku R w osobnym pliku i odwołując się do niego skrypt U-SQL

Poniższy przykład ilustruje bardziej złożone użycie. W takim przypadku kod R jest wdrażany jako zasób, który jest skrypt U-SQL.

Zapisz ten kod R jako oddzielny plik.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Użyj skryptu U-SQL w celu wdrożenia tego skryptu języka R przy użyciu instrukcji DEPLOY RESOURCE.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Jak platforma R integruje się ze standardem U-SQL

### <a name="datatypes"></a>Typy danych
* Ciągi i kolumny liczbowe z języka U-SQL są konwertowane jako-między ramkami danych języka R i U-SQL [obsługiwane typy: `double`, `string`, `bool`, `integer`, `byte`].
* Typ danych `Factor` nie jest obsługiwany w języku U-SQL.
* `byte[]` musi być serializowana jako zakodowany w formacie base64 `string`.
* Ciągi u-SQL mogą być konwertowane na czynniki w kodzie R, po utworzeniu ramki danych wejściowych języka R-SQL lub przez ustawienie parametru zmniejszającego `stringsAsFactors: true`.

### <a name="schemas"></a>Schematy
* Zestawy danych U-SQL nie mogą mieć zduplikowanych nazw kolumn.
* Nazwy kolumn w zestawach danych U-SQL muszą być ciągami.
* Nazwy kolumn muszą być takie same w skryptach języka U-SQL i języka R.
* Kolumna ReadOnly nie może być częścią wyjściowego elementu danych. Ponieważ kolumny tylko do odczytu są automatycznie wprowadzane z powrotem w tabeli U-SQL, jeśli jest częścią schematu wyjściowego UDO.

### <a name="functional-limitations"></a>Ograniczenia funkcjonalności
* Nie można dwukrotnie utworzyć wystąpienia aparatu języka R w tym samym procesie. 
* Obecnie język U-SQL nie obsługuje funkcji Złączer udo do przewidywania przy użyciu modeli partycjonowanych generowanych za pomocą narzędzia zmniejszającego udo. Użytkownicy mogą deklarować partycjonowane modele jako zasoby i używać ich w skrypcie języka R (Zobacz przykładowy kod `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Wersje języka R
Obsługiwane są tylko R 3.2.2.

### <a name="standard-r-modules"></a>Standardowe moduły R

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Ograniczenia rozmiaru danych wejściowych i wyjściowych
Każdy wierzchołek ma przypisaną ograniczoną ilość pamięci. Ponieważ wejściowe i wyjściowe ramki danych muszą istnieć w pamięci w kodzie R, całkowity rozmiar danych wejściowych i wyjściowych nie może przekroczyć 500 MB.

### <a name="sample-code"></a>Przykładowy kod
Po zainstalowaniu rozszerzeń zaawansowanej analizy U-SQL jest dostępny więcej przykładowego kodu na koncie Data Lake Store. Ścieżka do większej ilości przykładowego kodu to: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Wdrażanie niestandardowych modułów R przy użyciu języka U-SQL

Najpierw Utwórz niestandardowy moduł R i Zapisz go, a następnie Przekaż plik spakowanego niestandardowego modułu R do magazynu ADL. W tym przykładzie przekażemy plik magittr_ 1.5. zip do katalogu głównego domyślnego konta ADLS dla konta ADLA, z którego korzystamy. Po przekazaniu modułu do magazynu ADL Zadeklaruj go jako use RESOURCE DEPLOY, aby udostępnić go w skrypcie U-SQL i wywołać `install.packages`, aby go zainstalować.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Korzystanie z funkcji okna języka U-SQL dla zadań Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
