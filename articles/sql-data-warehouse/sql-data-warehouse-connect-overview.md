---
title: Nawiązywanie połączenia z usługą Azure SQL Data Warehouse
description: Połącz się z Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a14b99753c9f06f2e0cf32dd8b5c7776cfdad89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685878"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Nawiązywanie połączenia z usługą Azure SQL Data Warehouse
Połącz się z Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Znajdowanie nazwy serwera
W poniższym przykładzie nazwa serwera to samplesvr.database.windows.net. Aby znaleźć w pełni kwalifikowaną nazwę serwera:

1. Przejdź do witryny [Azure Portal][Azure portal].
2. Kliknij pozycję **Magazyny danych SQL**.
3. Kliknij magazyn danych, z którym chcesz nawiązać połączenie.
4. Znajdź pełną nazwę serwera.
   
    ![Pełna nazwa serwera][1]

## <a name="supported-drivers-and-connection-strings"></a>Obsługiwane sterowniki i parametry połączenia
Usługa Azure SQL Data Warehouse obsługuje sterowniki [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] i [JDBC][JDBC]. Aby znaleźć najnowszą wersję i dokumentację, kliknij jeden z powyższych sterowników. Aby automatycznie wygenerować parametry połączenia dla sterownika, którego używasz z Azure Portal, kliknij pozycję **Pokaż parametry połączenia bazy danych** w poprzednim przykładzie. Poniżej przedstawiono również przykłady parametrów połączenia dla każdego sterownika.

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na wartość 300 sekund, aby połączenie nie zostało zakończone mimo krótkich okresów niedostępności.
> 
> 

### <a name="adonet-connection-string-example"></a>Przykład parametrów połączenia sterownika ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Przykład parametrów połączenia sterownika ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Przykład parametrów połączenia sterownika PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Przykład parametrów połączenia sterownika JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Ustawienia połączenia
Usługa SQL Data Warehouse standaryzuje niektóre ustawienia podczas tworzenia połączenia i obiektu. Tych ustawień nie można zastąpić i obejmują one:

| Ustawienia bazy danych | Wartość |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Następne kroki
Aby nawiązać połączenie i rozpocząć tworzenie zapytań przy użyciu programu Visual Studio, zobacz artykuł [Query with Visual Studio][Query with Visual Studio] (Wykonywanie zapytań przy użyciu programu Visual Studio). Aby dowiedzieć się więcej na temat opcji uwierzytelniania, zobacz [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] (Uwierzytelnianie w usłudze Azure SQL Data Warehouse).

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


