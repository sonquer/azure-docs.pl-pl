---
title: 'Szybki Start: Tworzenie magazynu — Azure PowerShell'
description: Szybko Utwórz SQL Database serwer logiczny, regułę zapory na poziomie serwera i magazyn danych z Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 94dcc0dee5dd4fe81eb5ce067d7ace31edeca353
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461508"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Szybki Start: Tworzenie i wykonywanie zapytań do Azure SQL Data Warehouse za pomocą Azure PowerShell

Szybko Utwórz Azure SQL Data Warehouse przy użyciu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby sprawdzić, która subskrypcja jest używana, uruchom polecenie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom polecenie [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Tworzenie serwera logicznego

Utwórz [serwer logiczny Azure SQL](../sql-database/sql-database-logical-servers.md) przy użyciu polecenia [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) . Serwer logiczny zawiera grupę baz danych zarządzanych jako grupa. W poniższym przykładzie tworzony jest losowo nazwany serwer w grupie zasobów z użytkownikiem administracyjnym o nazwie `ServerAdmin` i hasłem `ChangeYourAdminPassword1`. Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera Azure SQL Server](../sql-database/sql-database-firewall-configure.md) przy użyciu polecenia [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) . Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak SQL Server Management Studio lub Narzędzie SQLCMD, połączyć się z SQL Data Warehouse za pomocą zapory usługi SQL Data Warehouse. W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database i SQL Data Warehouse komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. W takim przypadku nie będzie można nawiązać połączenia z serwerem Azure SQL, chyba że dział IT otworzy port 1433.
>


## <a name="create-a-data-warehouse"></a>Tworzenie magazynu danych
Ten przykład tworzy magazyn danych przy użyciu wcześniej zdefiniowanych zmiennych.  Określa cel usługi jako DW100c, który jest tańszym punktem wyjścia dla hurtowni danych. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Wymagane parametry:

* **RequestedServiceObjectiveName**: ilość żądanych [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) . Zwiększenie tej kwoty zwiększa koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
* **DatabaseName**: nazwa tworzonego SQL Data Warehouse.
* **Servername**: Nazwa serwera, który jest używany do tworzenia.
* **ResourceGroupName**: Grupa zasobów, której używasz. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Edition**: musi mieć wartość „DataWarehouse”, aby utworzyć magazyn SQL Data Warehouse.

Opcjonalne parametry:

- **CollationName**: sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowania w bazie danych.
- **MaxSizeBytes**: domyślny maksymalny rozmiar bazy danych to 240TB. Maksymalny rozmiar ogranicza dane magazynu wierszy. Istnieje nieograniczony magazyn dla danych kolumnowych.

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz polecenie [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start. 

> [!TIP]
> Jeśli planujesz kontynuować pracę z nowszymi samouczkami Szybki Start, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start w Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

Utworzono magazyn danych, utworzono regułę zapory, połączono z magazynem danych i uruchomiono kilka zapytań. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.
> [!div class="nextstepaction"]
>[Ładowanie danych do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
