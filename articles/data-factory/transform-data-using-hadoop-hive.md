---
title: Przekształcanie danych za pomocą działania programu Hive platformy Hadoop
description: Dowiedz się, jak za pomocą działania programu Hive w usłudze Azure Data Factory uruchamiać zapytania programu Hive na żądanie/własny klaster usługi HDInsight.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: b4af3f897a12c71d73962735d3fe68d95f138cef
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912908"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania programu Hive platformy Hadoop w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-hive-activity.md)
> * [Bieżąca wersja](transform-data-using-hadoop-hive.md)

Działanie programu Hive w usłudze HDInsight w [potoku](concepts-pipelines-activities.md) Data Factory wykonuje zapytania Hive w klastrze usługi HDInsight w [twoim własnym](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji.

Jeśli jesteś nowym do Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](introduction.md) i wykonaj [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Szczegóły składni
| Właściwość            | Opis                                                  | Wymagane |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nazwa działania                                         | Tak      |
| description         | Tekst opisujący działanie używanego działania                | Nie       |
| type                | W przypadku działania programu Hive typem działania jest HDinsightHive        | Tak      |
| linkedServiceName   | Odwołanie do klastra usługi HDInsight zarejestrowanego jako połączona usługa w Data Factory. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| scriptLinkedService | Odwołanie do połączonej usługi Azure Storage użytej do przechowania skryptu Hive do wykonania. Jeśli nie określisz tej połączonej usługi, zostanie użyta połączona usługa Azure Storage zdefiniowana w połączonej usłudze HDInsight. | Nie       |
| scriptPath          | Podaj ścieżkę do pliku skryptu przechowywanego w usłudze Azure Storage, która jest określona przez elementu scriptlinkedservice. W nazwie pliku rozróżniana jest wielkość liter. | Tak      |
| getDebugInfo        | Określa, kiedy pliki dziennika są kopiowane do usługi Azure Storage używanej przez klaster HDInsight (lub) określonej przez elementu scriptlinkedservice. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie       |
| arguments           | Określa tablicę argumentów zadania usługi Hadoop. Argumenty są przesyłane jako argumenty wiersza polecenia do każdego zadania. | Nie       |
| defines             | Określ parametry jako pary klucz/wartość dla odwołania w skrypcie Hive. | Nie       |
| queryTimeout        | Wartość limitu czasu zapytania (w minutach). Stosowane, gdy klaster usługi HDInsight ma pakiet Enterprise Security włączony. | Nie       |

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego Machine Learning](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
