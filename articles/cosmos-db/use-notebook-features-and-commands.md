---
title: Korzystanie z wbudowanych poleceń i funkcji notesu w Azure Cosmos DB (wersja zapoznawcza)
description: Dowiedz się, jak używać wbudowanych poleceń i funkcji, aby wykonywać typowe operacje przy użyciu wbudowanych notesów Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513403"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Korzystanie z wbudowanych poleceń i funkcji notesu w Azure Cosmos DB (wersja zapoznawcza)

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano sposób wykonywania typowych operacji przy użyciu wbudowanych funkcji i poleceń notesu.

## <a name="install-a-new-package"></a>Zainstaluj nowy pakiet
Po włączeniu obsługi notesu dla kont usługi Azure Cosmos można otworzyć nowy Notes i zainstalować pakiet.

W nowej komórce kodu Wstaw i uruchom następujący kod, zastępując ``PackageToBeInstalled`` z żądanym pakietem języka Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Ten pakiet będzie dostępny do użycia z dowolnego notesu w obszarze roboczym konta usługi Azure Cosmos. 

> [!TIP]
> Jeśli Notes wymaga pakietu niestandardowego, zalecamy dodanie w notesie komórki umożliwiającej zainstalowanie pakietu, ponieważ w przypadku [zresetowania obszaru roboczego](#reset-notebooks-workspace)zostaną usunięte pakiety.  

## <a name="run-a-sql-query"></a>Uruchamianie zapytania SQL

Możesz użyć ``%%sql`` Magic polecenia, aby uruchomić [zapytanie SQL](sql-query-getting-started.md) względem dowolnego kontenera na Twoim koncie. Użyj składni:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Zastąp ``{database_id}`` i ``{container_id}`` nazwą bazy danych i kontenerem na swoim koncie Cosmos. Jeśli argumenty ``--database`` i ``--container`` nie są podane, zapytanie zostanie wykonane w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Można uruchomić dowolne zapytanie SQL, które jest prawidłowe w Azure Cosmos DB. Tekst zapytania musi znajdować się w nowym wierszu.

Przykład: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Uruchom ```%%sql?``` w komórce, aby wyświetlić dokumentację pomocy dla polecenia SQL Magic w notesie.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Uruchamianie zapytania SQL i danych wyjściowych do Pandas Frame

Wyniki zapytania ``%%sql`` można wyprowadzać w [Pandas Dataframe](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Użyj składni: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Zastąp ``{database_id}`` i ``{container_id}`` nazwą bazy danych i kontenerem na swoim koncie Cosmos. Jeśli argumenty ``--database`` i ``--container`` nie są podane, zapytanie zostanie wykonane w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Zastąp ``{outputDataFrameVar}`` nazwą zmiennej Dataframe, która będzie zawierać wyniki.
- Można uruchomić dowolne zapytanie SQL, które jest prawidłowe w Azure Cosmos DB. Tekst zapytania musi znajdować się w nowym wierszu. 

Przykład:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>Przekazywanie elementów JSON do kontenera
Możesz użyć ``%%upload`` Magic polecenia, aby przekazać dane z pliku JSON do określonego kontenera usługi Azure Cosmos. Aby przekazać elementy, użyj następującego polecenia:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Zastąp ``{database_id}`` i ``{container_id}`` nazwą bazy danych i kontenerem na swoim koncie usługi Azure Cosmos. Jeśli argumenty ``--database`` i ``--container`` nie są podane, zapytanie zostanie wykonane w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Zastąp ``{url_location_of_file}`` lokalizacją pliku JSON. Plik musi być tablicą prawidłowych obiektów JSON i powinien być dostępny za pośrednictwem publicznego Internetu.

Przykład:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Statystyki wyjściowe umożliwiają obliczenie obowiązujących jednostek RU/s używanych do przekazywania elementów. Na przykład jeśli 25 000 jednostek ru zostały zużyte ponad 38 sekund, obowiązująca wartość RU/s to 25 000 jednostek ru/38 s = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Ustaw domyślną bazę danych dla zapytań
Dla notesu można ustawić domyślne polecenia ```%%sql``` bazy danych. Zamień ```{database_id}``` na nazwę bazy danych.

```bash
%database {database_id}
```
Uruchom ```%database?``` w komórce, aby wyświetlić dokumentację w notesie.

## <a name="set-default-container-for-queries"></a>Ustaw domyślny kontener dla zapytań
Można ustawić domyślny kontener ```%%sql``` polecenia będą używane dla notesu. Zastąp ```{container_id}``` nazwą kontenera.

```bash
%container {container_id}
```
Uruchom ```%container?``` w komórce, aby wyświetlić dokumentację w notesie.

## <a name="use-built-in-nteract-data-explorer"></a>Korzystanie z wbudowanego Eksploratora danych nteract
Można użyć wbudowanego [Eksploratora danych nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) do filtrowania i wizualizacji ramki Dataframe. Aby włączyć tę funkcję, należy ustawić opcję ``pd.options.display.html.table_schema`` na ``True`` i ``pd.options.display.max_rows`` na żądaną wartość (można ustawić ``pd.options.display.max_rows`` na ``None``, aby wyświetlić wszystkie wyniki).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![Eksplorator danych nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Korzystanie z wbudowanego zestawu SDK języka Python
Wersja 4 [Azure Cosmos DB zestawu SDK języka Python dla interfejsu API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) jest zainstalowana i dołączona do środowiska notesu dla konta usługi Azure Cosmos.

Użyj wbudowanego wystąpienia ``cosmos_client``, aby uruchomić dowolną operację zestawu SDK. 

Przykład:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Zobacz [przykłady zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Wbudowany zestaw SDK języka Python jest obsługiwany tylko w przypadku kont interfejsu API SQL (Core). W przypadku innych interfejsów API konieczne będzie [zainstalowanie odpowiedniego sterownika języka Python](#install-a-new-package) ODPOWIADAJĄCego interfejsowi API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Tworzenie niestandardowego wystąpienia ``cosmos_client``
Aby uzyskać większą elastyczność, można utworzyć niestandardowe wystąpienie ``cosmos_client`` w celu:

- Dostosowywanie [zasad połączenia](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Wykonywanie operacji na innym koncie usługi Azure Cosmos niż to, w którym znajduje się użytkownik

Możesz uzyskać dostęp do parametrów połączenia i klucza podstawowego bieżącego konta za pomocą [zmiennych środowiskowych](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Dostęp do punktów końcowych konta i zmiennych klucza podstawowego
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Zmienne środowiskowe ``COSMOS_ENDPOINT`` i ``COSMOS_KEY`` mają zastosowanie tylko do interfejsu API SQL. W przypadku innych interfejsów API Znajdź punkt końcowy i klucz w bloku **Parametry połączenia** lub **klucze** na koncie usługi Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Resetuj obszar roboczy notesów
Aby zresetować obszar roboczy notesy do ustawień domyślnych, wybierz pozycję **Zresetuj obszar roboczy** na pasku poleceń. Spowoduje to usunięcie wszystkich zainstalowanych pakietów niestandardowych i ponowne uruchomienie serwera Jupyter. Nie wpłynie to na Twoje notesy, pliki i zasoby platformy Azure Cosmos.  

![Resetuj obszar roboczy notesów](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Następne kroki

- Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
- Dowiedz się więcej o [interfejsie API usługi Azure Cosmos DB Python SDK dla języka SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
