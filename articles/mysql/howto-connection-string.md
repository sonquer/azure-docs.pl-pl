---
title: Parametry połączenia — Azure Database for MySQL
description: Ten dokument zawiera listę obecnie obsługiwanych parametrów połączenia dla aplikacji, które łączą się z Azure Database for MySQL,C#w tym ADO.NET (), JDBC, Node. js, ODBC, php, Python i Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: bee98accd8ac404eb223975571b082dae754571a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770496"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Jak połączyć aplikacje z Azure Database for MySQL
W tym temacie wymieniono typy parametrów połączenia, które są obsługiwane przez Azure Database for MySQL wraz z szablonami i przykładami. W parametrach połączenia mogą istnieć inne parametry i ustawienia.

- Aby uzyskać certyfikat, zobacz [jak skonfigurować protokół SSL](./howto-configure-ssl.md).
- {your_host} = \<ServerName >. MySQL. Database. Azure. com
- {your_user} @ {ServerName} = format identyfikatora użytkownika w celu poprawnego uwierzytelniania.  Jeśli używasz tylko identyfikatora użytkownika, uwierzytelnienie zakończy się niepowodzeniem.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

W tym przykładzie nazwa serwera to `mydemoserver`, nazwa bazy danych to `wpdb`, nazwa użytkownika to `WPAdmin`, a hasło to `mypassword!2`. W związku z tym parametry połączenia powinny być następujące:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Pobierz szczegóły parametrów połączenia z Azure Portal
W [Azure Portal](https://portal.azure.com)przejdź do serwera Azure Database for MySQL, a następnie kliknij pozycję **Parametry połączenia** , aby uzyskać listę ciągów dla swojego wystąpienia: ![okienku parametry połączenia w Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia z bazą danych. Zmodyfikuj te przykłady, aby użyć własnych parametrów, takich jak nazwa bazy danych, hasło i tak dalej. Tego ciągu można użyć do nawiązania połączenia z serwerem z poziomu kodu i aplikacji.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji o bibliotekach połączeń, zobacz temat [koncepcje — biblioteki połączeń](./concepts-connection-libraries.md).
