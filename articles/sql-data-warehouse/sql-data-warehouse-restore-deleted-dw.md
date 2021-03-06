---
title: Przywracanie usuniętego magazynu danych
description: Przewodnik dotyczący przywracania usuniętego Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e508eff3b322b49a6dc50d818c8bcccc3e924ff2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759675"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Przywracanie usuniętego Azure SQL Data Warehouse

W tym artykule dowiesz się, jak przywrócić usunięte SQL Data Warehouse przy użyciu Azure Portal i programu PowerShell:

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każdy SQL Data Warehouse jest obsługiwany przez program SQL Server (na przykład myserver.database.windows.net), który ma domyślny limit przydziału jednostek DTU.  Sprawdź, czy program SQL Server ma wystarczający limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Przywracanie usuniętego magazynu danych za pomocą programu PowerShell

Aby przywrócić usunięty SQL Data Warehouse, należy użyć polecenia cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) . Jeśli odpowiedni serwer logiczny został również usunięty, nie można przywrócić tego magazynu danych.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą usunięty magazyn danych, który ma zostać przywrócony.
5. Pobierz konkretny usunięty magazyn danych.
6. Przywracanie usuniętego magazynu danych
    1. Aby przywrócić usunięte SQL Data Warehouse na inny serwer logiczny, należy określić inną nazwę serwera logicznego.  Ten serwer logiczny może również znajdować się w innej grupie zasobów i regionie.
    1. Aby przywrócić do innej subskrypcji, użyj przycisku [Przenieś](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) , aby przenieść serwer logiczny do innej subskrypcji.
1. Sprawdź, czy przywrócony magazyn danych jest w trybie online.
1. Po zakończeniu przywracania można skonfigurować odzyskany magazyn danych, wykonując następujące czynności: [Skonfiguruj bazę danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Przejdź do programu SQL Server, w którym jest hostowany usunięty magazyn danych.
3. Wybierz ikonę **usunięte bazy danych** w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wybierz usunięte SQL Data Warehouse, które chcesz przywrócić.

    ![Wybieranie usuniętych baz danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Określ nową **nazwę bazy danych** i kliknij przycisk **OK** .

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Następne kroki
- [Przywracanie istniejącego magazynu danych](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie z magazynu danych z geograficzną kopią zapasową](sql-data-warehouse-restore-from-geo-backup.md)