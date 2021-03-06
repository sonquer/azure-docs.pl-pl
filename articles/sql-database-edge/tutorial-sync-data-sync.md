---
title: Synchronizuj dane z Azure SQL Database Edge przy użyciu SQL Data Sync | Microsoft Docs
description: Informacje o synchronizowaniu danych z Azure SQL Database Edge przy użyciu usługi Azure SQL Data Sync
keywords: Baza danych SQL Database, synchronizacja danych z usługi SQL Database Edge, synchronizacja danych programu SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384181"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Samouczek: synchronizacja danych z SQL Database Edge do Azure SQL Database przy użyciu SQL Data Sync

W ramach tego samouczka nauczysz się używać *grupy synchronizacji* usługi Azure SQL Data Sync do przyrostowej synchronizacji danych z Azure SQL Database Edge do Azure SQL Database. SQL Data Sync to usługa oparta na usłudze Azure SQL Database, która umożliwia synchronizowanie danych, wybrana dwukierunkowo między wieloma bazami danych SQL i wystąpienia programu SQL Server. Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Ponieważ SQL Database Edge jest oparta na najnowszych wersjach [aparatu bazy danych SQL Server](/sql/sql-server/sql-server-technical-documentation/), każdy mechanizm synchronizacji danych, który jest stosowany do lokalnego wystąpienia SQL Server, może również służyć do synchronizowania danych z lub z SQL Database wystąpienia brzegowego uruchomionego na urządzeniu brzegowym.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga komputera z systemem Windows, który został skonfigurowany przy użyciu [agenta synchronizacji danych dla platformy Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Utworzenie bazy danych Azure SQL Database. Aby uzyskać informacje na temat sposobu tworzenia bazy danych Azure SQL Database przy użyciu Azure Portal, zobacz [Tworzenie pojedynczej bazy danych w programie Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Utwórz tabele i inne niezbędne obiekty we wdrożeniu Azure SQL Database.

* Utwórz niezbędne tabele i obiekty w rozmieszczeniu Azure SQL Database Edge. Aby uzyskać więcej informacji, zobacz [używanie SQL Database pakietów DAC z SQL Database Edge](stream-analytics.md).

* Zarejestruj wystąpienie Azure SQL Database Edge przy użyciu agenta synchronizacji danych dla usługi Azure SQL Data Sync. Aby uzyskać więcej informacji, zobacz [Dodawanie lokalnej bazy danych SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizuj dane między bazą danych SQL Azure a SQL Database Edge

Konfigurowanie synchronizacji między bazą danych Azure SQL Database a wystąpieniem SQL Database Edge przy użyciu SQL Data Sync obejmuje trzy kluczowe kroki:  

1. Użyj Azure Portal, aby utworzyć grupę synchronizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pojedynczej bazy danych można użyć do utworzenia wielu grup synchronizacji w celu synchronizowania danych z różnych SQL Database wystąpieniami krawędzi z co najmniej jedną *bazą danych SQL* na platformie Azure.

2. Dodaj elementy członkowskie synchronizacji do grupy synchronizacji. Aby uzyskać więcej informacji, zobacz [Dodawanie elementów członkowskich synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Skonfiguruj grupę synchronizacji, aby wybrać tabele, które będą częścią synchronizacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po wykonaniu powyższych kroków będziesz mieć grupę synchronizacji obejmującą bazę danych Azure SQL Database i wystąpienie SQL Database Edge.

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz następujące artykuły:

* [Agent synchronizacji danych dla platformy Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Najlepsze rozwiązania](../sql-database/sql-database-best-practices-data-sync.md) i [Rozwiązywanie problemów z usługą Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizowanie schematu synchronizacji przy użyciu języka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) lub [programu PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Synchronizuj między Azure SQL Database i Azure SQL Database Edge przy użyciu programu PowerShell](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). W tym samouczku Zastąp szczegóły `OnPremiseServer` bazy danych Azure SQL Databaseą szczegóły krawędzi.
