---
title: Obsługiwane magazyny danych w udziale danych platformy Azure
description: Dowiedz się więcej na temat magazynów danych, które są obsługiwane w przypadku używania udziału danych platformy Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964213"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Obsługiwane magazyny danych w udziale danych platformy Azure

Udział danych platformy Azure zapewnia otwarte i elastyczne udostępnianie danych, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a ich konsumenci danych mogą wybrać magazyn danych, do którego mają być wysyłane dane. 

Ten artykuł zawiera informacje o rozbudowanym zestawie magazynów danych systemu Azure, które są obsługiwane w udziale danych platformy Azure. Możesz również znaleźć informacje dotyczące kombinacji magazynów danych, które mogą być wykorzystywane przez dostawców danych i użytkowników danych. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jakie magazyny danych są obsługiwane w udziale danych platformy Azure? 

Poniższa tabela zawiera szczegółowe informacje o obsługiwanych źródłach danych dla udziału danych platformy Azure. 

| Magazyn danych | Udostępnianie oparte na migawce | Udostępnianie w miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage 1. generacji |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ ||
| Baza danych SQL Azure |Publiczna wersja zapoznawcza | |
| Azure Synapse Analytics (dawniej: Azure SQL DW) |Publiczna wersja zapoznawcza | |
| Azure Data Explorer | |Publiczna wersja zapoznawcza |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Udział danych platformy Azure oferuje klientom danych elastyczność podczas wybierania magazynu danych w celu akceptowania danych do programu. Na przykład dane udostępniane przez Azure SQL Database można odbierać do Azure Data Lake Store Gen2, Azure SQL Database lub Azure Synapse Analytics. Klienci mogą wybrać format, w którym będą wysyłane dane podczas konfigurowania otrzymanego udziału danych. 

Poniższa tabela zawiera szczegółowe informacje o różnych kombinacjach i wyborach, które użytkownicy danych mają podczas akceptowania i konfigurowania udziału danych. Aby uzyskać więcej informacji na temat konfigurowania mapowań zestawu danych, zobacz [jak skonfigurować mapowania zestawu danych](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure Data Lake Storage 1. generacji | Usługa Azure Data Lake Storage 2. generacji | Baza danych SQL Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage 1. generacji | ✓ | | ✓|
| Usługa Azure Data Lake Storage 2. generacji | ✓ | | ✓|
| Baza danych SQL Azure | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (dawniej: Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu
Udział danych platformy Azure obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje także udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Jeśli foldery są udostępniane w ramach udostępniania opartego na migawce, odbiorca danych może utworzyć pełną kopię danych udziału lub wykorzystać funkcję migawki przyrostowej do kopiowania tylko nowych lub zaktualizowanych plików. Istniejące pliki o tej samej nazwie zostaną zastąpione.

## <a name="share-from-a-sql-based-source"></a>Udostępnianie z poziomu źródła opartego na języku SQL
Udział danych platformy Azure obsługuje udostępnianie tabel lub widoków z usług Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL DW). Konsument danych może zdecydować się na zaakceptowanie danych do Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure jako pliku CSV lub Parquet. Pełna migawka zastępuje zawartość pliku docelowego. Alternatywnie konsument danych może zaakceptować dane w tabeli SQL. Jeśli docelowa tabela SQL nie jest dostępna po stronie klienta danych, usługa Azure Data Share tworzy tabelę SQL ze schematem źródłowym. Pełna migawka dołącza zawartość tabeli źródłowej do docelowej tabeli SQL. Migawka przyrostowa nie jest obecnie obsługiwana.

## <a name="share-from-azure-data-explorer"></a>Udostępnianie z usługi Azure Eksplorator danych
Udział danych platformy Azure umożliwia udostępnianie baz danych w miejscu z klastrów Eksplorator danych platformy Azure. Dostawca danych może współdzielić na poziomie bazy danych lub klastra. Po udostępnieniu na poziomie bazy danych konsument danych będzie mógł uzyskać dostęp do określonych baz danych udostępnionych przez dostawcę danych. Po udostępnieniu na poziomie klastra konsument danych może uzyskać dostęp do wszystkich baz danych z klastra dostawcy, w tym wszystkich przyszłych baz danych utworzonych przez dostawcę danych.

Aby uzyskać dostęp do udostępnionych baz danych, konsument danych musi mieć własny klaster Eksplorator danych platformy Azure. Klaster danych platformy Azure dla użytkowników Eksplorator danych musi znajdować się w tym samym centrum danych platformy Azure co klaster usługi Azure Eksplorator danych dostawcy danych. Po ustanowieniu relacji udostępniania udział danych platformy Azure tworzy link symboliczny między klastrami Eksplorator danych platformy Azure dostawcy i klientem.

Usługa Azure Eksplorator danych obsługuje dwa tryby pozyskiwania danych: wsadowe i strumieniowe. Dane odebrane z usługi Batch w udostępnionej bazie danych pojawią się od kilku sekund do kilku minut po stronie klienta danych. Dane odebrane z przesyłania strumieniowego mogą potrwać do 24 godzin po stronie konsumenta danych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
