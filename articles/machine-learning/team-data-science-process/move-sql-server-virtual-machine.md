---
title: Przenoszenie danych do maszyny wirtualnej programu SQL Server — zespołu danych dla celów naukowych
description: Przenoszenie danych z plików prostych lub na lokalnym serwerze SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure.
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
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721374"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure

W tym artykule opisano opcje przenoszenia danych z plików prostych (formaty plików CSV lub TSV) lub z lokalnego programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure. Te zadania dotyczące przenoszenia danych do chmury są częścią zespołu danych dla celów naukowych.

W przypadku tematu, który zawiera opcje przenoszenia danych do Azure SQL Database Machine Learning, zobacz [przenoszenie danych do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

Poniższa tabela podsumowuje Opcje przenoszenia danych do programu SQL Server na maszynie wirtualnej platformy Azure.

| <b>ZEWNĘTRZ</b> | <b>MIEJSCE docelowe: SQL Server na maszynie wirtualnej platformy Azure</b> |
| --- | --- |
| <b>Plik prosty</b> |1. <a href="#insert-tables-bcp">Narzędzie do kopiowania zbiorczego wiersza polecenia (bcp)</a><br> 2. <a href="#insert-tables-bulkquery">zbiorczo Wstaw zapytanie SQL</a><br> 3. <a href="#sql-builtin-utilities">graficzne wbudowane narzędzia w SQL Server</a> |
| <b>SQL Server lokalne</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Wdróż bazę danych SQL Server w kreatorze Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">wyeksportuj do pliku prostego</a><br> 3. <a href="#sql-migration">Kreator migracji SQL Database</a> <br> 4. <a href="#sql-backup">Tworzenie kopii zapasowej i przywracanie bazy danych</a><br> |

W tym dokumencie przyjęto założenie, że polecenia SQL są wykonywane z programu SQL Server Management Studio lub Eksplorator bazy danych programu Visual Studio.

> [!TIP]
> Alternatywnie możesz użyć [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) , aby utworzyć i zaplanować potok, który będzie przenosić dane do SQL Server maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych za pomocą Azure Data Factory (działanie kopiowania)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Użyjesz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Zainicjowano **SQL Server na maszynie wirtualnej platformy Azure**. Aby uzyskać instrukcje, zobacz [Konfigurowanie maszyny wirtualnej platformy Azure SQL Server jako serwera notesu IPython na potrzeby zaawansowanej analizy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Przeniesienie danych z prostego źródła pliku do SQL Server na maszynie wirtualnej platformy Azure
Jeśli dane pochodzą z pliku prostego (ułożone w formacie wiersza/kolumny), można ją przenosić do maszyny Wirtualnej programu SQL Server na platformie Azure za pomocą następujących metod:

1. [Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)](#insert-tables-bcp)
2. [Wstaw zbiorczo zapytanie SQL](#insert-tables-bulkquery)
3. [Graficzne wbudowane narzędzia w SQL Server (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)
Narzędzie BCP jest narzędziem wiersza polecenia, zainstalowane z programem SQL Server i najszybszy sposób przenoszenia danych. Działa on w ramach wszystkich trzech SQL Server wariantów (lokalnych SQL Server, SQL Azure i SQL Server maszyny wirtualnej na platformie Azure).

> [!NOTE]
> **Gdzie mają być moje dane dla narzędzia BCP?**  
> Mimo że nie jest wymagany, o pliki zawierające źródło danych znajduje się na tym samym komputerze co docelowego programu SQL Server umożliwia szybszy transfer (sieci szybkość vs lokalny dysk szybkości operacji We/Wy). Pliki proste zawierające dane można przenieść na maszynę, na której zainstalowano SQL Server przy użyciu różnych narzędzi do kopiowania plików, takich jak [AzCopy](../../storage/common/storage-use-azcopy.md), [Eksplorator usługi Azure Storage](https://storageexplorer.com/) lub kopiowania/wklejania systemu windows za pośrednictwem Remote Desktop Protocol (RDP).
>
>

1. Upewnij się, że bazy danych i tabele są tworzone w docelowej bazie danych programu SQL Server. Oto przykład, jak to zrobić za pomocą poleceń `Create Database` i `Create Table`:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Wygeneruj plik formatu, który opisuje schemat tabeli, wydając następujące polecenie z wiersza polecenia na komputerze, na którym zainstalowano Narzędzie bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Wstaw dane do bazy danych przy użyciu polecenia BCP, które powinny być wykonywane z poziomu wiersza polecenia, gdy SQL Server jest zainstalowana na tym samym komputerze:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optymalizacja wstawek narzędzia bcp** Zapoznaj się z poniższym artykułem [wskazówki dotyczące optymalizacji importu zbiorczego "](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) aby zoptymalizować takie wstawki.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Przekształcają wstawia do szybszego przenoszenia danych
Jeśli przenoszone dane są duże, można przyspieszyć wykonywanie wielu poleceń BCP równolegle w skrypcie programu PowerShell.

> [!NOTE]
> Pozyskiwanie **danych Big Data** Aby zoptymalizować ładowanie danych pod kątem dużych i bardzo dużych zestawów danych, Podziel swoje logiczne i fizyczne tabele bazy z wieloma grupami plików i tabelami partycji. Aby uzyskać więcej informacji na temat tworzenia i ładowania danych do tabel partycji, zobacz sekcję [ładowanie równoległe tabel partycji SQL](parallel-load-sql-partitioned-tables.md).
>
>

Poniższy przykładowy skrypt programu PowerShell demonstruje równoległe wstawienia przy użyciu narzędzia bcp:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="insert-tables-bulkquery"></a>Wstaw zbiorczo zapytanie SQL
[Zbiorcze zapytanie SQL](https://msdn.microsoft.com/library/ms188365) można użyć do zaimportowania danych do bazy danych z plików opartych na wierszach/kolumnach (obsługiwane typy są omówione w temacie[przygotowanie danych do eksportu zbiorczego lub importu (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Poniżej przedstawiono niektóre przykładowe polecenia dla Bulk Insert są tak jak pokazano poniżej:  

1. Analizowanie danych i ustaw opcje niestandardowych przed zaimportowaniem, aby upewnić się, że bazy danych programu SQL Server przyjęto założenie, tego samego formatu dla wszystkich pól specjalnych, takich jak daty. Poniżej przedstawiono przykładowy sposób ustawić format daty jako rok, miesiąc, dzień, (Jeśli dane zawierają datę w formacie rok, miesiąc, dzień):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importowanie danych przy użyciu instrukcji importowania zbiorczego:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="sql-builtin-utilities"></a>Wbudowane narzędzia w SQL Server
Do zaimportowania danych do maszyny wirtualnej SQL Server na platformie Azure z poziomu prostego pliku można użyć SQL Server Integration Services (SSIS).
SSIS jest dostępna w dwóch środowiskach studio. Aby uzyskać szczegółowe informacje, zobacz [środowiska usług Integration Services (SSIS) i Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Aby uzyskać szczegółowe informacje na temat SQL Server narzędzi danych, zobacz [Microsoft SQL Server narzędzia danych](https://msdn.microsoft.com/data/tools.aspx)  
* Aby uzyskać szczegółowe informacje na temat Kreatora importu/eksportu, zobacz [SQL Server Kreatora importu i eksportu](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Przeniesienie danych z SQL Server lokalnych do SQL Server na maszynie wirtualnej platformy Azure
Można również użyć następujących strategii migracji:

1. [Wdrażanie bazy danych SQL Server w Kreatorze Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Eksportuj do pliku prostego](#export-flat-file)
3. [Kreator migracji SQL Database](#sql-migration)
4. [Tworzenie kopii zapasowej i przywracanie bazy danych](#sql-backup)

Opiszemy każdą z poniższych opcji poniżej:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Wdrażanie bazy danych programu SQL Server do kreatora Microsoft Azure VM
**Kreator wdrażania bazy danych SQL Server do Microsoft Azure maszyny wirtualnej** jest prostym i zalecanym sposobem przenoszenia danych z lokalnego wystąpienia SQL Server do SQL Server na maszynie wirtualnej platformy Azure. Aby zapoznać się ze szczegółowymi krokami, a także w omówieniu innych alternatyw, zobacz [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Eksportuj do pliku prostego
Różne metody mogą służyć do zbiorczego eksportowania danych z lokalnego SQL Server, zgodnie z opisem w temacie [import zbiorczy i eksport danych (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . W tym dokumencie opisano Program kopiowania zbiorczego (BCP) jako przykład. Gdy dane zostaną wyeksportowane do pliku prostego, można go było zaimportować do innego serwera SQL za pomocą importowania zbiorczego.

1. Wyeksportować dane z lokalnego programu SQL Server w pliku w następujący sposób za pomocą narzędzia bcp

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Utwórz bazę danych i tabelę na SQL Server maszynie wirtualnej na platformie Azure przy użyciu `create database` i `create table` dla schematu tabeli wyeksportowanego w kroku 1.
3. Tworzenie pliku formatu do opisywania schematu tabeli ilość, eksportu/importu danych. Szczegóły pliku formatu opisano w temacie [Tworzenie pliku formatu (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP z komputera programu SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP zdalnie względem programu SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Użyj dowolnej z metod opisanych w sekcji [przenoszenie danych ze źródła plików](#filesource_to_sqlonazurevm) , aby przenieść dane do SQL Server.

### <a name="sql-migration"></a>Kreator migracji SQL Database
[Kreator migracji bazy danych SQL Server](https://sqlazuremw.codeplex.com/) zapewnia przyjazny sposób przenoszenia danych między dwoma WYSTĄPIENIAMI programu SQL Server. Umożliwia użytkownikowi do mapy schematu danych między źródłami a tabel docelowych, wybierz typy kolumn i różnych innych funkcji. Używa ona kopiowania masowego (BCP) w sposób niewidoczny. Poniżej przedstawiono zrzut ekranu przedstawiający ekran powitalny Kreatora migracji bazy danych SQL.  

![Kreator migracji programu SQL Server][2]

### <a name="sql-backup"></a>Tworzenie kopii zapasowej i przywracanie bazy danych
Obsługa programu SQL Server:

1. [Tworzenie kopii zapasowej i przywracanie bazy danych](https://msdn.microsoft.com/library/ms187048.aspx) (zarówno do pliku lokalnego, jak i do BACPAC eksportu do obiektów BLOB) oraz [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx) (przy użyciu BACPAC).
2. Możliwość bezpośrednio utworzyć maszyny wirtualne SQL Server na platformie Azure za pomocą skopiowanego bazy danych lub Kopiuj do istniejącej bazy danych SQL Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z Kreatora kopiowania bazy danych](https://msdn.microsoft.com/library/ms188664.aspx).

Zrzut ekranu przedstawiający kopii bazy danych w górę/przywracania opcji dostępnych w programie SQL Server Management Studio znajdują się poniżej.

![Narzędzie importu programu SQL Server][1]

## <a name="resources"></a>Zasoby
[Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Omówienie programu SQL Server w usłudze Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
