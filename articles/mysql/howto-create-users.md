---
title: Tworzenie użytkowników — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników w celu współdziałania z serwerem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770479"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Tworzenie użytkowników w Azure Database for MySQL serwerze 
W tym artykule opisano, jak można tworzyć użytkowników na serwerze Azure Database for MySQL.

Podczas pierwszego tworzenia Azure Database for MySQL należy podać nazwę użytkownika i hasło administratora serwera. Aby uzyskać więcej informacji, możesz skorzystać z [przewodnika Szybki Start](quickstart-create-mysql-server-database-using-azure-portal.md). Nazwę użytkownika nazwy logowania administratora serwera można zlokalizować z Azure Portal.

Administrator serwera otrzymuje pewne uprawnienia dla serwera, jak to zostało wymienione: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCEs, INDEX, ALTER, SHOW Databases, CREATE Tables Table, LOCK TABLES, EXECUTE, podrzędny replikacji, replikacja KLIENT, TWORZENIE WIDOKU, WYŚWIETLANIE WIDOKU, TWORZENIE PROCEDURY, ZMIENIANIE PROCEDURY, TWORZENIE UŻYTKOWNIKA, ZDARZENIE, WYZWALACZ

Po utworzeniu serwera Azure Database for MySQL można użyć pierwszego konta użytkownika administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administratora. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych schematów bazy danych.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak utworzyć dodatkowych użytkowników administracyjnych w Azure Database for MySQL
1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Korzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, MySQL. exe, HeidiSQL lub inne. 
   Jeśli nie wiesz, jak nawiązać połączenie, zobacz temat [Korzystanie z programu MySQL Workbench do nawiązywania połączeń i wysyłania zapytań dotyczących danych](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zamień nową nazwę użytkownika na wartość symbolu zastępczego `new_master_user`. Ta składnia przyznaje listę uprawnień dla wszystkich schematów bazy danych ( *.* ) do nazwy użytkownika (new_master_user w tym przykładzie). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Weryfikowanie dotacji 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Jak utworzyć użytkowników bazy danych w Azure Database for MySQL

1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Korzystaj z preferowanego narzędzia klienckiego, takiego jak MySQL Workbench, MySQL. exe, HeidiSQL lub inne. 
   Jeśli nie wiesz, jak nawiązać połączenie, zobacz temat [Korzystanie z programu MySQL Workbench do nawiązywania połączeń i wysyłania zapytań dotyczących danych](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `db_user` nową nazwą użytkownika i symbolem zastępczym `testdb` nazwą swojej bazy danych.

   Ta składnia kodu SQL tworzy na przykład nową bazę danych o nazwie TestDB. Następnie tworzy nowego użytkownika w usłudze MySQL i przyznaje wszystkie uprawnienia nowemu schematowi bazy danych (TestDB.\*) dla tego użytkownika. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Sprawdź dotacje w ramach bazy danych.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Zaloguj się na serwerze, określając wydaną bazę danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład pokazuje wiersz polecenia MySQL. Za pomocą tego polecenia zostanie wyświetlony monit o podanie hasła dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Następne kroki
Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im nawiązanie połączenia: [Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą Azure Portal](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zobacz dokumentację produktu MySQL, aby [zarządzać kontami użytkowników](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), uzyskać [składnię](https://dev.mysql.com/doc/refman/5.7/en/grant.html)i [uprawnienia](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
