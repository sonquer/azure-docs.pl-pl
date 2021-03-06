---
title: Problemy z łączeniem źródłowych baz danych
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej na temat rozwiązywania znanych problemów/błędów związanych z łączeniem Azure Database Migration Service ze źródłowymi bazami danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b697faeded4177381f70ebb9d1f93d928b25d7b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437790"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Rozwiązywanie problemów z błędami DMS podczas łączenia ze źródłowymi bazami danych

Poniższy artykuł zawiera szczegółowe informacje dotyczące sposobu rozwiązywania potencjalnych problemów, które mogą wystąpić podczas łączenia Azure Database Migration Service (DMS) ze źródłową bazą danych. Każda sekcja poniżej odnosi się do określonego typu źródłowej bazy danych, zawierającej błąd, który może wystąpić wraz ze szczegółami i linki do informacji o sposobach rozwiązywania problemów z łącznością.

## <a name="sql-server"></a>SQL Server

Potencjalne problemy związane z nawiązywaniem połączenia z źródłową bazą danych SQL Server i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły przyczyny i rozwiązywania problemów |
| ------------- | ------------- |
| Połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany tak, aby zezwalał na połączenia zdalne.<br> | Ten błąd występuje, gdy usługa nie może zlokalizować serwera źródłowego. Aby rozwiązać ten problem, zobacz artykuł [błąd podczas łączenia się z SQL Server źródłowym w przypadku używania portu dynamicznego lub nazwanego wystąpienia](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Błąd 53** — połączenie SQL nie powiodło się. (Również w przypadku kodów błędów 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Ten błąd występuje, gdy usługa nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z poniższymi zasobami, a następnie spróbuj ponownie. <br><br>  [Interaktywny przewodnik użytkownika dotyczący rozwiązywania problemów z łącznością](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Wymagania wstępne dotyczące migrowania SQL Server do Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Wymagania wstępne dotyczące migrowania SQL Server do wystąpienia zarządzanego Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Błąd 18456** — logowanie nie powiodło się.<br> | Ten błąd występuje, gdy usługa nie może nawiązać połączenia ze źródłową bazą danych przy użyciu podanych poświadczeń T-SQL. Aby rozwiązać ten problem, sprawdź wprowadzone poświadczenia. Możesz również odwołać się do [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub do dokumentów rozwiązywania problemów wymienionych w uwadze poniżej tej tabeli, a następnie spróbować ponownie. |
| Podano źle sformułowaną wartość AccountName "{0}". Oczekiwany format Kontaname to DomainName\UserName<br> | Ten błąd występuje, gdy użytkownik wybierze opcję uwierzytelnianie systemu Windows, ale udostępnia nazwę użytkownika w nieprawidłowym formacie. Aby rozwiązać ten problem, należy podać nazwę użytkownika w prawidłowym formacie uwierzytelniania systemu Windows lub wybrać opcję **uwierzytelnianie SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potencjalne problemy związane z nawiązywaniem połączenia z źródłową bazą danych MySQL AWS RDS oraz sposób ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły przyczyny i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd [2003]** [HY000] — połączenie nie powiodło się. Błąd [HY000] [MySQL] [ODBC x. x (w) Driver] nie można nawiązać połączenia z serwerem MySQL na serwerze "{Server}" (10060) | Ten błąd występuje, gdy sterownik MySQL ODBC nie może nawiązać połączenia z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [2005]** [HY000] — połączenie nie powiodło się. Błąd [HY000] [MySQL] [ODBC x. x (w) Driver] nieznany Host serwera MySQL "{Server}" | Ten błąd występuje, gdy usługa nie może znaleźć hosta źródłowego na serwerze RDS. Problem może być spowodowany tym, że wymienione źródło nie istnieje lub występuje problem z infrastrukturą usług pulpitu zdalnego. Aby rozwiązać ten problem, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [1045]** [HY000] — połączenie nie powiodło się. Błąd [HY000] [MySQL] [ODBC x. x (w) Driver] Odmowa dostępu dla użytkownika "{User}" @ "{Server}" (przy użyciu hasła: tak) | Ten błąd występuje, gdy sterownik MySQL ODBC nie może nawiązać połączenia z serwerem źródłowym z powodu nieprawidłowych poświadczeń. Sprawdź wprowadzone poświadczenia. Jeśli problem będzie się powtarzał, sprawdź, czy na komputerze źródłowym znajdują się poprawne poświadczenia. Może być konieczne zresetowanie hasła w konsoli programu. Jeśli problem nadal występuje, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [9002]** [HY000] — połączenie nie powiodło się. Błąd [HY000] [MySQL] [ODBC x. x (w) Driver] parametry połączenia mogą nie być odpowiednie. Odwiedź portal, aby uzyskać odwołania.| Ten błąd występuje, gdy połączenie kończy się niepowodzeniem z powodu problemu z parametrami połączenia. Sprawdź, czy podane parametry połączenia są prawidłowe. Aby rozwiązać ten problem, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd podczas rejestrowania danych binarnych. Zmienna binlog_format ma wartość "{value}". Zmień ją na "wiersz".** | Ten błąd występuje, jeśli wystąpił błąd podczas rejestrowania binarnego; Zmienna binlog_format ma nieprawidłową wartość. Aby rozwiązać ten problem, Zmień binlog_format w grupie parametrów na "wiersz", a następnie uruchom ponownie wystąpienie. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [opcji rejestrowania binarnego i zmiennych](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) lub [Dokumentacja plików dziennika bazy danych programu RDS MySQL AWS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z nawiązywaniem połączenia z bazą danych MySQL programu AWS RDS, zobacz następujące zasoby:
> * [Rozwiązywanie problemów z łącznością Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak mogę rozwiązać problemy związane z nawiązywaniem połączenia z moim wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potencjalne problemy związane z nawiązywaniem połączenia ze źródłową bazą danych AWS RDS PostgreSQL i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły przyczyny i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd [101]** [08001] — połączenie nie powiodło się. Błąd [08001] upłynął limit czasu. | Ten błąd występuje, gdy sterownik Postgres nie może nawiązać połączenia z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd: wal_level parametru ma wartość "{value}". Zmień go na "Logical", aby umożliwić replikację.** | Ten błąd występuje, jeśli parametr wal_level ma nieprawidłową wartość. Aby rozwiązać ten problem, Zmień wartość RDS. logical_replication w grupie parametrów na 1, a następnie uruchom ponownie wystąpienie. Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące migracji do usługi Azure PostgreSQL za pomocą systemu DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) lub [PostgreSQL w usłudze Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z nawiązywaniem połączenia z bazą danych AWS RDS PostgreSQL, zobacz następujące zasoby:
> * [Rozwiązywanie problemów z łącznością Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak mogę rozwiązać problemy związane z nawiązywaniem połączenia z moim wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potencjalne problemy związane z nawiązywaniem połączenia z bazą danych AWS RDS SQL Server i sposób ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły przyczyny i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd 53** — połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został odnaleziony lub nie jest dostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany tak, aby zezwalał na połączenia zdalne. (Dostawca: Dostawca nazwanych potoków, błąd: 40 — nie można otworzyć połączenia z SQL Server | Ten błąd występuje, gdy usługa nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd 18456** — logowanie nie powiodło się. Logowanie użytkownika "{User}" nie powiodło się | Ten błąd występuje, gdy usługa nie może nawiązać połączenia ze źródłową bazą danych przy użyciu podanych poświadczeń T-SQL. Aby rozwiązać ten problem, sprawdź wprowadzone poświadczenia. Możesz również odwołać się do [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub do dokumentów rozwiązywania problemów wymienionych w uwadze poniżej tej tabeli i spróbować ponownie. |
| **Błąd 87** — parametry połączenia są nieprawidłowe. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany tak, aby zezwalał na połączenia zdalne. (Dostawca: interfejsy sieciowe SQL, błąd: 25 — parametry połączenia są nieprawidłowe) | Ten błąd występuje, gdy usługa nie może połączyć się z serwerem źródłowym z powodu nieprawidłowych parametrów połączenia. Aby rozwiązać ten problem, sprawdź podane parametry połączenia. Jeśli problem będzie się powtarzać, zapoznaj się z dokumentem rozwiązywania problemów wymienionym w uwadze poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd — certyfikat serwera nie jest zaufany.** Połączenie z serwerem zostało pomyślnie ustanowione, ale wystąpił błąd podczas procesu logowania. (Dostawca: dostawca SSL, błąd: 0 — łańcuch certyfikatów został wystawiony przez urząd, który nie jest zaufany.) | Ten błąd występuje, jeśli używany certyfikat nie jest zaufany. Aby rozwiązać ten problem, należy znaleźć certyfikat, który może być zaufany, a następnie włączyć go na serwerze. Alternatywnie można wybrać opcję certyfikatu zaufania podczas nawiązywania połączenia. Wykonaj tę czynność tylko wtedy, gdy znasz certyfikat używany i ufasz. <br> Połączenia SSL szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń — są one podatne na ataki typu man-in-the-middle. Nie należy polegać na protokole SSL przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem. <br> Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSL z wystąpieniem usługi Microsoft SQL Server DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) lub [samouczkiem: Migrowanie SQL Server RDS na platformę Azure za pomocą usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Błąd 300** — użytkownik nie ma wymaganych uprawnień. Nie można wyświetlić uprawnienia stanu serwera w obiekcie "{Server}" w bazie danych "{Database}" | Ten błąd występuje, gdy użytkownik nie ma uprawnień do przeprowadzenia migracji. Aby rozwiązać ten problem, zapoznaj się z tematem [Granting Server Permissions-Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) lub [samouczek: Migruj SQL Server RDS na platformę Azure za pomocą usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) , aby uzyskać więcej informacji. |

> [!NOTE]
> Aby uzyskać więcej informacji dotyczących rozwiązywania problemów związanych z nawiązywaniem połączenia ze źródłem AWS RDS SQL Server, zobacz następujące zasoby:
>
> * [Usuwanie błędów łączności z programem SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Jak mogę rozwiązać problemy związane z nawiązywaniem połączenia z moim wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Znane problemy

* [Znane problemy/ograniczenia migracji z migracją online do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zapoznaj się z artykułem [jak skonfigurować parametry serwera w Azure Database for MySQL przy użyciu Azure Portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Zapoznaj się z artykułem [Przegląd wymagań wstępnych dotyczących korzystania z Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [często zadawane pytania dotyczące korzystania z Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
