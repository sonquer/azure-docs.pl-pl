---
title: Prześlij dużą liczbę zadań — Azure Batch | Microsoft Docs
description: Jak efektywnie przesłać bardzo dużą liczbę zadań w pojedynczym Azure Batch zadania
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026051"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Przesyłanie dużej liczby zadań do zadania wsadowego

Po uruchomieniu obciążeń Azure Batch dużej skali można przesłać dziesiątki tysięcy, setki tysięcy lub jeszcze więcej zadań do jednego zadania. 

Ten artykuł zawiera wskazówki i przykłady kodu do przesyłania dużej liczby zadań o znacznie większej przepływności do jednego zadania wsadowego. Po przesłaniu zadań wprowadzają kolejkę wsadową do przetwarzania w puli określonej dla danego zadania.

## <a name="use-task-collections"></a>Korzystanie z kolekcji zadań

Interfejsy API zadań wsadowych zapewniają metody do wydajnego dodawania zadań do zadania jako *kolekcji*, a także do jednej naraz. Podczas dodawania dużej liczby zadań należy użyć odpowiednich metod lub przeciążeń, aby dodać zadania jako kolekcje. Ogólnie rzecz biorąc tworzysz kolekcję zadań przez Definiowanie zadań podczas iteracji nad zestawem plików wejściowych lub parametrów dla danego zadania.

Maksymalny rozmiar kolekcji zadań, którą można dodać w pojedynczym wywołaniu, zależy od używanego interfejsu API usługi Batch:

* Poniższe interfejsy API partii ograniczają kolekcję do **100 zadań**. Limit może być mniejszy w zależności od rozmiaru zadań — na przykład jeśli zadania zawierają dużą liczbę plików zasobów lub zmiennych środowiskowych.

    * [Interfejs API REST](/rest/api/batchservice/task/addcollection)
    * [Interfejs API języka Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Interfejs API środowiska Node. js](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Korzystając z tych interfejsów API, należy zapewnić logikę w celu podzielenia liczby zadań do osiągnięcia limitu kolekcji, a także obsłużyć błędy i ponawiania próby w przypadku niepowodzenia dodawania zadań. Jeśli kolekcja zadań jest zbyt duża, aby można ją było dodać, żądanie generuje błąd i należy ponownie ponowić próbę z mniejszą liczbą zadań.

* Poniższe interfejsy API obsługują znacznie większe kolekcje zadań — ograniczone tylko do dostępności pamięci RAM na kliencie przesyłania. Te interfejsy API w sposób przezroczysty obsługują dzielenie kolekcji zadań do "fragmentów" dla interfejsów API niższego poziomu i ponawiania prób w przypadku niepowodzenia dodawania zadań.

    * [Interfejs API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Interfejs API języka Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch rozszerzenia interfejsu wiersza](batch-cli-templates.md) polecenia z szablonami interfejsu wiersza polecenia w usłudze Batch
    * [Rozszerzenie zestawu SDK języka Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Zwiększenie przepływności przesyłania zadań

Dodanie dużej kolekcji zadań do zadania może zająć trochę czasu — na przykład do 1 minuty, aby dodać 20 000 zadań za pośrednictwem interfejsu API platformy .NET. W zależności od interfejsu API usługi Batch i obciążenia można zwiększyć przepływność zadania, modyfikując co najmniej jedną z następujących czynności:

* **Rozmiar zadania** — Dodawanie dużych zadań trwa dłużej niż Dodawanie mniejszych. Aby zmniejszyć rozmiar każdego zadania w kolekcji, można uprościć wiersz polecenia zadania, zmniejszyć liczbę zmiennych środowiskowych lub obsłużyć bardziej wydajne wymagania dotyczące wykonywania zadań. Na przykład zamiast używać dużej liczby plików zasobów, należy zainstalować zależności zadań przy użyciu [zadania uruchamiania](batch-api-basics.md#start-task) w puli lub użyć [pakietu aplikacji](batch-application-packages.md) lub [kontenera Docker](batch-docker-container-workloads.md).

* **Liczba operacji równoległych** — w zależności od interfejsu API usługi Batch zwiększenie przepływności przez zwiększenie maksymalnej liczby operacji współbieżnych przez klienta usługi Batch. Skonfiguruj to ustawienie za pomocą właściwości [BatchClientParallelOptions. MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) w interfejsie API platformy .NET lub parametru `threads` metod, takich jak [TaskOperations. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) w rozszerzeniu zestawu SDK języka Python dla usługi Batch. (Ta właściwość nie jest dostępna w natywnym zestawie SDK Python w usłudze Batch). Domyślnie ta właściwość jest ustawiona na 1, ale ustawiana jest wyższa, aby zwiększyć przepływność operacji. Zwiększa się przepływność poprzez zużywanie przepustowości sieci i pewnej wydajności procesora CPU. Przepływność zadań zwiększa się o nawet 100 razy `MaxDegreeOfParallelism` lub `threads`. W tym celu należy ustawić liczbę współbieżnych operacji poniżej 100. 
 
  Rozszerzenie interfejsu wiersza polecenia Azure Batch przy użyciu szablonów wsadowych zwiększa liczbę współbieżnych operacji automatycznie na podstawie liczby dostępnych rdzeni, ale tej właściwości nie można skonfigurować w interfejsie wiersza polecenia. 

* **Limity połączeń HTTP** — liczba współbieżnych połączeń HTTP może ograniczać wydajność klienta usługi Batch w przypadku dodawania dużej liczby zadań. Liczba połączeń HTTP jest ograniczona do określonych interfejsów API. Podczas tworzenia przy użyciu interfejsu API .NET na przykład właściwość [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) jest domyślnie ustawiona na 2. Zalecamy zwiększenie wartości do liczby zbliżonej do liczby operacji równoległych lub większej.

## <a name="example-batch-net"></a>Przykład: Batch .NET

Poniższe C# fragmenty kodu zawierają ustawienia do skonfigurowania podczas dodawania dużej liczby zadań przy użyciu interfejsu API usługi Batch platformy .NET.

Aby zwiększyć przepływność zadań, należy zwiększyć wartość właściwości [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Przykład:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Dodaj kolekcję zadań do zadania przy użyciu odpowiedniego przeciążenia metody [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) lub [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) . Przykład:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Przykład: rozszerzenie Batch CLI

Korzystając z rozszerzeń interfejsu wiersza polecenia Azure Batch z [szablonami interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md), Utwórz plik JSON szablonu zadania zawierający [fabrykę zadań](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Fabryka zadań konfiguruje kolekcję powiązanych zadań dla zadania z jednej definicji zadania.  

Poniżej przedstawiono przykładowy szablon zadania dla jednowymiarowego zadania odchylenia z dużą liczbą zadań — w tym przypadku 250 000. Wiersz polecenia zadania jest prostym poleceniem `echo`.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Aby uruchomić zadanie z szablonem, zobacz [Korzystanie z szablonów interfejsu wiersza polecenia i transferu plików Azure Batch](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Przykład: rozszerzenie Batch Python SDK

Aby użyć rozszerzenia Azure Batch Python SDK, najpierw zainstaluj zestaw SDK języka Python i rozszerzenie:

```
pip install azure-batch
pip install azure-batch-extensions
```

Skonfiguruj `BatchExtensionsClient`, który używa rozszerzenia zestawu SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Utwórz kolekcję zadań do dodania do zadania. Przykład:


```python
tasks = list()
# Populate the list with your tasks
...
```

Dodaj kolekcję zadań przy użyciu [zadania. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Ustaw parametr `threads`, aby zwiększyć liczbę operacji współbieżnych:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Rozszerzenie Batch Python SDK obsługuje także Dodawanie parametrów zadań do zadania przy użyciu specyfikacji JSON dla fabryki zadań. Na przykład skonfiguruj parametry zadania dla odchylenia parametrycznego podobnego do tego w poprzednim przykładzie szablonu interfejsu wiersza polecenia usługi Batch:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Dodaj parametry zadania do zadania. Ustaw parametr `threads`, aby zwiększyć liczbę operacji współbieżnych:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzystaniu z rozszerzenia interfejsu wiersza polecenia Azure Batch z [szablonami interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md).
* Dowiedz się więcej na temat [rozszerzenia Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
