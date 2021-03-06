---
title: Narzędzia do wprowadzania danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach do pozyskiwania danych i narzędziach, które są wstępnie zainstalowane na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612633"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia do wprowadzania danych maszyny wirtualnej do nauki o danych

Jako jeden z pierwszych kroków technicznych w projekcie analizy danych lub AI, należy zidentyfikować zestawy danych, które mają być używane, i umieścić je w środowisku analitycznym. Data Science Virtual Machine (DSVM) oferuje narzędzia i biblioteki do przenoszenia danych z różnych źródeł do magazynu danych analitycznych lokalnie na DSVM lub na platformę danych w chmurze lub lokalnie.

Oto kilka narzędzi do przenoszenia danych, które są dostępne w DSVM.

## <a name="adlcopy"></a>Narzędzia AdlCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do kopiowania danych z usługi Azure Blob Storage do Azure Data Lake Store. Ponadto można kopiować dane między dwa konta usługi Azure Data Lake Store.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob z usługi Azure Blob Storage do Azure Data Lake Store.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia i wpisz `adlcopy`, aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Korzystanie z narzędzia AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Narzędzia pokrewne na DSVM      | AzCopy, interfejs wiersza polecenia platformy Azure     |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do zarządzania dla platformy Azure. Zawiera również zlecenia poleceń służące do przenoszenia danych z platform danych platformy Azure, takich jak Azure Blob Storage i Azure Data Lake Store.     |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z usługi Azure Storage oraz do Azure Data Lake Store.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia i wpisz `az`, aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)     |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>Narzędzie AzCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do kopiowania danych do i z plików lokalnych, magazynu obiektów blob platformy Azure, plików i tabel.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do usługi Azure Blob Storage i kopiowanie obiektów BLOB między kontami.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia i wpisz `azcopy`, aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Narzędzie AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Narzędzia pokrewne na DSVM      | Narzędzia AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Narzędzie do migracji danych usługi Cosmos DB platformy Azure

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do importowania danych z różnych źródeł do Azure Cosmos DB bazy danych NoSQL w chmurze. Te źródła obejmują pliki JSON, pliki CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB i Azure Cosmos DB kolekcje interfejsów API SQL.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny wirtualnej do CosmosDB, importowanie danych z usługi Azure Table Storage do CosmosDB i importowanie danych z bazy danych Microsoft SQL Server do CosmosDB.     |
|  Jak używać / ją uruchomić?    |   Aby użyć wersji wiersza polecenia, Otwórz wiersz polecenia i wpisz `dt`. Aby użyć narzędzia graficznego interfejsu użytkownika, Otwórz wiersz polecenia i wpisz `dtui`.    |
| Zawiera linki do przykładów      | [CosmosDB Importuj dane](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

|    |           |
| ------------- | ------------- |
| Co to jest?   | Graficzny interfejs użytkownika służący do współpracy z plikami przechowywanymi w chmurze platformy Azure. |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie i eksportowanie danych z DSVM.    |
|  Jak używać / ją uruchomić?    | Wyszukaj ciąg "Eksplorator usługi Azure Storage" w menu Start. |
| Zawiera linki do przykładów      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzia programu SQL Server do skopiowania danych między programu SQL Server i plik danych.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli SQL Server i eksportowanie tabeli SQL Server do pliku.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia i wpisz `bcp`, aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Narzędzie bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Narzędzia pokrewne na DSVM      | SQL Server, narzędzia sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do instalowania kontenera magazynu obiektów blob platformy Azure w systemie plików Linux.      |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie obiektów BLOB w kontenerze.      |
|  Jak używać i uruchamiać je?    |   Uruchom _blobfuse_ w terminalu.    |
| Zawiera linki do przykładów      | [blobfuse w witrynie GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Narzędzia pokrewne na DSVM      | Interfejs wiersza polecenia platformy Azure      |
