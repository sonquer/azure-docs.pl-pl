---
title: Przenoszenie danych do usługi Azure SQL Database — zespołu danych dla celów naukowych
description: Przenoszenie danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server lokalnym do Azure SQL Database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722462"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning

W tym artykule opisano opcje przeniesienia danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server lokalnym do Azure SQL Database. Te zadania dotyczące przenoszenia danych do chmury są częścią zespołu danych dla celów naukowych.

Temat przedstawiający opcje przenoszenia danych do SQL Server lokalnych dla Machine Learning można znaleźć w temacie [przenoszenie danych do SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md).

Poniższa tabela podsumowuje Opcje przenoszenia danych do usługi Azure SQL Database.

| <b>ZEWNĘTRZ</b> | <b>MIEJSCE docelowe: Azure SQL Database</b> |
| --- | --- |
| <b>Plik prosty (format CSV lub TSV)</b> |[Wstaw zbiorczo zapytanie SQL](#bulk-insert-sql-query) |
| <b>SQL Server lokalne</b> |1.[Eksportuj do pliku prostego](#export-flat-file)<br> 2. [Kreator migracji SQL Database](#insert-tables-bcp)<br> 3. [Tworzenie kopii zapasowej i przywracanie bazy danych](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Wymagania wstępne
Procedury opisane w tym miejscu wymagają, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Używasz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **Azure SQL Database**. Jeśli konieczne jest skonfigurowanie Azure SQL Database, [wprowadzenie z Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) zawiera informacje na temat udostępniania nowego wystąpienia Azure SQL Database.
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/overview).

**Dane**: procesy migracji są przedstawiane za pomocą [zestawu danych NYC taksówki](https://chriswhong.com/open-data/foil_nyc_taxi/). Zestaw danych NYC taksówki zawiera informacje na temat danych o podróży i ich Targi i jest dostępny w usłudze Azure Blob Storage: [NYCe dane z taksówką](https://www.andresmh.com/nyctaxitrips/). Przykład i opis tych plików znajdują się w [opisie zestawu danych NYC taksówki](sql-walkthrough.md#dataset).

Możesz dostosować procedury opisane w tym miejscu do zestawu danych użytkownika lub zgodnie z opisem przy użyciu zestawu danych taksówek NYC postępuj zgodnie z instrukcjami. Aby przekazać zestaw danych NYC taksówki do lokalnej bazy danych SQL Server, postępuj zgodnie z procedurą opisaną w temacie [zbiorcze Importowanie danych do SQL Server Database](sql-walkthrough.md#dbload). Te instrukcje są przeznaczone dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="file-to-azure-sql-database"></a>Przeniesienie danych z prostego źródła pliku do Azure SQL Database
Dane w plikach prostych (CSV lub TSV) można przenieść do Azure SQL Database przy użyciu zbiorczego zapytania SQL.

### <a name="bulk-insert-sql-query"></a>Wstaw zbiorczo zapytanie SQL
Kroki procedury korzystającej z zapytania BULK INSERT SQL są podobne do wskazówek dotyczących przeniesienia danych z prostego źródła pliku do SQL Server na maszynie wirtualnej platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bulk Wstaw zapytanie SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Przeniesienie danych z SQL Server lokalnego do Azure SQL Database
Jeśli dane źródłowe są przechowywane w SQL Server lokalnym, istnieją różne możliwości przeniesienia danych do Azure SQL Database:

1. [Eksportuj do pliku prostego](#export-flat-file)
2. [Kreator migracji SQL Database](#insert-tables-bcp)
3. [Tworzenie kopii zapasowej i przywracanie bazy danych](#db-migration)
4. [Azure Data Factory](#adf)

Kroki dla pierwszych trzech są podobne do tych w sekcjach [Przenieś dane do SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md) , która obejmuje te same procedury. Linki do odpowiednich fragmentów w tym temacie zostały podane w poniższych instrukcjach.

### <a name="export-flat-file"></a>Eksportuj do pliku prostego
Kroki związane z tym eksportowaniem do pliku prostego są podobne do tych wskazówek objętych [eksportowaniem do pliku prostego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Kreator migracji SQL Database
Kroki opisane w Kreatorze migracji SQL Database są podobne do tych, które opisano w [Kreatorze migracji SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Tworzenie kopii zapasowej i przywracanie bazy danych
Procedura tworzenia kopii zapasowej i przywracania bazy danych przypomina te wskazówki wymienione w temacie [Tworzenie kopii zapasowej i przywracanie bazy danych](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Dowiedz się, jak przenieść dane do Azure SQL Database z Azure Data Factory (ADF) w tym temacie. [Przenieś dane z lokalnego programu SQL Server do usługi SQL Azure za pomocą Azure Data Factory](move-sql-azure-adf.md). W tym temacie pokazano, jak za pomocą funkcji ADF przenieść dane z lokalnej bazy danych SQL Server do Azure SQL Database za pośrednictwem usługi Azure Blob Storage.

Należy rozważyć użycie funkcji ADF, gdy dane wymagają ciągłego migrowania przy użyciu hybrydowych źródeł lokalnych i chmurowych.  Moduł ADF również pomaga, gdy dane wymagają przekształceń lub wymagają nowej logiki biznesowej podczas migracji. ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przenoszenia danych w regularnych odstępach czasu. ADF ma także inne możliwości, takie jak obsługa złożonych operacji.
