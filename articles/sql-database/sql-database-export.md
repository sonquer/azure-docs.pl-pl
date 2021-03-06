---
title: Eksportowanie pojedynczej bazy danych lub puli do pliku BACPAC
description: Eksportowanie bazy danych Azure SQL Database do pliku BACPAC przy użyciu Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b96d2e69b757774afe177341caffe2f3cbf00e2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823449"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Eksportowanie bazy danych Azure SQL Database do pliku BACPAC

W przypadku konieczności eksportowania bazy danych na potrzeby archiwizacji lub przechodzenia do innej platformy można wyeksportować schemat bazy danych i dane do pliku [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Plik BACPAC jest plikiem ZIP z rozszerzeniem BACPAC zawierającym metadane i dane z bazy danych SQL Server. Plik BACPAC może być przechowywany w usłudze Azure Blob Storage lub w magazynie lokalnym w lokalizacji lokalnej, a później zaimportowany z powrotem do Azure SQL Database lub do SQL Server instalacji lokalnej.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Zagadnienia dotyczące eksportowania bazy danych Azure SQL Database

- Aby eksport był spójnie sprzeczny, należy upewnić się, że nie ma żadnych działań zapisu podczas eksportowania lub że eksportuje się z [sprzecznej ze sobą spójnej kopii](sql-database-copy.md) bazy danych Azure SQL Database.
- Jeśli eksportujesz do magazynu obiektów blob, maksymalny rozmiar pliku BACPAC wynosi 200 GB. Aby zarchiwizować większy plik BACPAC, należy wyeksportować do magazynu lokalnego.
- Eksportowanie pliku BACPAC do usługi Azure Premium Storage przy użyciu metod omówionych w tym artykule nie jest obsługiwane.
- Magazyn za zaporą nie jest obecnie obsługiwany.
- Jeśli operacja eksportu z Azure SQL Database przekroczy 20 godzin, może zostać anulowana. Aby zwiększyć wydajność podczas eksportowania, możesz:

  - Tymczasowe zwiększenie rozmiaru obliczeń.
  - Zastąp wszystkie działania odczytu i zapisu podczas eksportowania.
  - Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) z wartościami innymi niż null dla wszystkich dużych tabel. Bez indeksów klastrowanych eksport może zakończyć się niepowodzeniem, jeśli trwa dłużej niż 6-12 godzin. Wynika to z faktu, że usługa Export musi ukończyć skanowanie tabeli, aby spróbować wyeksportować całą tabelę. Dobrym sposobem na ustalenie, czy tabele są zoptymalizowane pod kątem eksportu, jest uruchomienie **polecenia DBCC SHOW_STATISTICS** i upewnienie się, że *RANGE_HI_KEY* nie ma wartości null i jej wartość ma dobry rozkład. Aby uzyskać szczegółowe informacje, zobacz [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nie są przeznaczone do użycia na potrzeby operacji tworzenia kopii zapasowych i przywracania. Azure SQL Database automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkownika. Aby uzyskać szczegółowe informacje, zobacz [Omówienie ciągłości](sql-database-business-continuity.md) działania i [SQL Database kopii zapasowych](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eksportuj do pliku BACPAC przy użyciu Azure Portal

Eksportowanie BACPAC bazy danych z [wystąpienia zarządzanego](sql-database-managed-instance.md) przy użyciu Azure PowerShell nie jest obecnie obsługiwane. Zamiast tego użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą Azure Portal lub PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się w różnych bazach danych o takim samym rozmiarze i może wymagać wolnego miejsca na dysku do 3 razy większym niż rozmiar bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W związku z tym niektóre żądania mogą zakończyć się niepowodzeniem z błędem `There is not enough space on the disk`. W takim przypadku obejście ma na celu uruchomienie programu sqlpackage. exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy do używania [sqlpackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby wyeksportować bazę danych przy użyciu [Azure Portal](https://portal.azure.com), Otwórz stronę bazy danych i kliknij przycisk **Eksportuj** na pasku narzędzi.

   ![eksport bazy danych](./media/sql-database-export/database-export1.png)

2. Określ nazwę pliku BACPAC, wybierz istniejące konto usługi Azure Storage i kontener do wyeksportowania, a następnie podaj odpowiednie poświadczenia, aby uzyskać dostęp do źródłowej bazy danych. W tym miejscu jest wymagana **Nazwa logowania administratora** programu SQL Server, nawet jeśli jesteś administratorem platformy Azure, ponieważ administrator platformy Azure nie ma uprawnień SQL Server uprawnienia administratora.

    ![eksport bazy danych](./media/sql-database-export/database-export2.png)

3. Kliknij przycisk **OK**.

4. Aby monitorować postęp operacji eksportowania, Otwórz stronę dla serwera SQL Database zawierającego wyeksportowaną bazę danych. W obszarze do **Ustawienia** , a następnie kliknij pozycję **Importuj/Eksportuj historię**.

   ![Eksportuj historię](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eksportowanie do pliku BACPAC przy użyciu narzędzia sqlpackage

Aby wyeksportować bazę danych SQL przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Eksportowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzie sqlpackage jest dostarczane z najnowszymi wersjami [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Możesz też pobrać najnowszą wersję elementu [sqlpackage](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z centrum pobierania Microsoft.

Zalecamy używanie narzędzia sqlpackage do skalowania i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Ten przykład przedstawia sposób eksportowania bazy danych przy użyciu narzędzia sqlpackage. exe z Active Directory uniwersalnym uwierzytelnianiem:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eksportowanie do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)

Najnowsza wersja SQL Server Management Studio zawiera kreatora do eksportowania bazy danych Azure SQL Database do pliku BACPAC. Zapoznaj się z tematem [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eksportowanie do pliku BACPAC przy użyciu programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie eksportowania bazy danych do pliku BACPAC przy użyciu Azure PowerShell. Aby wyeksportować wystąpienie zarządzane do pliku BACPAC, użyj SQL Server Management Studio lub sqlpackage.

Użyj polecenia cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) , aby przesłać żądanie eksportu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacja eksportowania może zająć trochę czasu.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Aby sprawdzić stan żądania eksportu, należy użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Uruchomienie to natychmiast po żądaniu zwykle zwraca **stan: w toku**. Gdy widzisz **stan: powodzenie** eksportu zostało zakończone.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat długoterminowego przechowywania kopii zapasowych pojedynczych baz danych i baz danych w puli jako alternatywy dla eksportowania bazy danych do celów archiwalnych, zobacz [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md). Zadań programu SQL Agent można użyć do zaplanowania kopii [zapasowych bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternatywy dla długoterminowego przechowywania kopii zapasowych.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby dowiedzieć się więcej o importowaniu BACPAC do bazy danych SQL Server, zobacz [Importowanie BACPAC do bazy danych SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Aby dowiedzieć się więcej na temat eksportowania BACPAC z bazy danych SQL Server, zobacz [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Aby dowiedzieć się więcej o używaniu usługi migracji danych do migracji bazy danych, zobacz [migrowanie SQL Server do Azure SQL Database offline przy użyciu narzędzia DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Jeśli eksportujesz z SQL Server jako preludium do migracji do Azure SQL Database, zobacz [Migrowanie bazy danych SQL Server do Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać i udostępniać klucze magazynu i sygnatury dostępu współdzielonego, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
