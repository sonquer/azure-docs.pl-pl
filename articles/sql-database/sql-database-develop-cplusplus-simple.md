---
title: Nawiązywanie połączenia z SQL Database przy użyciu języka C iC++
description: Użyj przykładowego kodu w tym przewodniku Szybki Start, aby skompilować nowoczesne C++ aplikacje i utworzyć w niej kopię relacyjnej bazy danych w chmurze z Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: fb6094ec418d2b212759bddd2c4d49c7e6193849
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690707"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Nawiązywanie połączenia z SQL Database przy użyciu języka C iC++

Ten wpis jest przeznaczony dla C i C++ deweloperów próbujących nawiązać połączenie z usługą Azure SQL DB. Jest on podzielony na sekcje, dzięki czemu możesz przejść do sekcji, która najlepiej przechwytuje zainteresowania.

## <a name="prerequisites-for-the-cc-tutorial"></a>Wymagania wstępne dotyczące samouczkaC++ C/

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Program [Visual Studio](https://www.visualstudio.com/downloads/). Aby skompilować i uruchomić C++ ten przykład, należy zainstalować składniki językowe.
* [Programowanie dla systemu Linux w programie Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). W przypadku tworzenia aplikacji w systemie Linux należy również zainstalować rozszerzenie programu Visual Studio Linux.

## <a id="AzureSQL"></a>Azure SQL Database i SQL Server na maszynach wirtualnych
Usługa Azure SQL jest oparta na Microsoft SQL Server i została zaprojektowana w celu zapewnienia wysokiej dostępności, wydajnej i skalowalnej usługi. Korzystanie z usługi SQL Azure w środowisku lokalnym jest wiele korzyści. Korzystając z usługi SQL Azure, nie musisz instalować, konfigurować i obsługiwać bazy danych ani zarządzać nią, ale tylko zawartością i strukturą bazy danych. Typowe rzeczy, z którymi obawiamy się z bazami danych, takie jak odporność na uszkodzenia i nadmiarowość, są wbudowane.

Platforma Azure ma obecnie dwie opcje hostingu obciążeń programu SQL Server: Azure SQL Database, Database as Service i SQL Server w Virtual Machines (VM). Nie znajdziesz szczegółowych informacji o różnicach między tymi dwoma, z wyjątkiem tego, że usługa Azure SQL Database jest najlepszym trafieniem dla nowych aplikacji opartych na chmurze, aby korzystać z zalet oszczędności kosztów i optymalizacji wydajności, które zapewniają usługi w chmurze. Jeśli rozważasz Migrowanie lub rozszerzanie aplikacji lokalnych do chmury, program SQL Server na maszynie wirtualnej platformy Azure może jeszcze bardziej usprawnić działanie. Aby zachować łatwość działania w tym artykule, Utwórzmy bazę danych SQL Azure.

## <a id="ODBC"></a>Technologie dostępu do danych: ODBC i OLE DB
Łączenie z bazą danych SQL Azure nie jest inne, a obecnie istnieją dwa sposoby łączenia się z bazami danych: ODBC (Open Database Connectivity) i OLE DB (łączenie obiektów i osadzanie bazy danych). W ostatnich latach firma Microsoft połączyła się z [ODBC na potrzeby natywnego dostępu do danych relacyjnych](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC jest stosunkowo prosta, a także znacznie szybszy niż OLE DB. Jedynym zastrzeżeniem jest to, że ODBC używa starego interfejsu API w stylu C.

## <a id="Create"></a>Krok 1. Tworzenie Azure SQL Database
Zobacz [stronę wprowadzenie](sql-database-single-database-get-started.md) , aby dowiedzieć się, jak utworzyć przykładową bazę danych.  Alternatywnie możesz wykonać to [krótkie dwuminutowe wideo](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) , aby utworzyć bazę danych Azure SQL Database przy użyciu Azure Portal.

## <a id="ConnectionString"></a>Krok 2. Uzyskiwanie parametrów połączenia
Po zainicjowaniu obsługi administracyjnej bazy danych Azure SQL należy wykonać następujące czynności, aby określić informacje o połączeniu i dodać adres IP klienta na potrzeby dostępu do zapory.

W [Azure Portal](https://portal.azure.com/)przejdź do parametrów połączenia ODBC usługi Azure SQL Database, używając parametrów **połączenia z bazą danych** , które wymieniono jako część sekcji Przegląd dla bazy danych:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Skopiuj zawartość **ODBC (obejmuje Node. js) [SQL Authentication]** . Ten ciąg jest używany później do łączenia się z C++ naszym interpreterem wiersza polecenia ODBC. Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia z bazą danych.

## <a id="Firewall"></a>Krok 3. Dodawanie adresu IP do zapory
Przejdź do sekcji Zapora serwera bazy danych i Dodaj [adres IP klienta do zapory, wykonując następujące kroki](sql-database-configure-firewall-settings.md) , aby upewnić się, że możemy nawiązać połączenie:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

W tym momencie skonfigurowano bazę danych SQL Azure i wszystko jest gotowe do łączenia się z C++ kodem.

## <a id="Windows"></a>Krok 4. Łączenie z aplikacji systemu Windows CC++ /
Możesz łatwo połączyć się z [bazą danych Azure SQL Database przy użyciu interfejsu ODBC w systemie Windows przy użyciu tego przykładu](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) , który kompiluje się z programem Visual Studio. Przykład implementuje interpreter wiersza polecenia ODBC, którego można użyć do nawiązania połączenia z bazą danych SQL Azure. Ten przykład pobiera plik nazwy źródła bazy danych (DSN) jako argument wiersza polecenia lub pełne parametry połączenia, które zostały wcześniej skopiowane z Azure Portal. Wywołaj stronę właściwości dla tego projektu i wklej parametry połączenia jako argument polecenia, jak pokazano poniżej:

![Propsfile DSN](./media/sql-database-develop-cplusplus-simple/props.png)

Upewnij się, że podajesz odpowiednie szczegóły uwierzytelniania dla bazy danych w ramach tego ciągu połączenia z bazą danych.

Uruchom aplikację, aby ją skompilować. Powinno zostać wyświetlone następujące okno z walidacją pomyślnego nawiązania połączenia. Możesz nawet uruchamiać podstawowe polecenia SQL, takie jak **Tworzenie tabeli** , aby sprawdzić poprawność łączności z bazą danych:

![Polecenia SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternatywnie można utworzyć plik DSN przy użyciu kreatora, który jest uruchamiany, gdy nie podano żadnych argumentów polecenia. Zalecamy również wypróbowanie tej opcji. Tego pliku DSN można użyć do automatyzacji i ochrony ustawień uwierzytelniania:

![Utwórz plik DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulacje! Pomyślnie nawiązano połączenie z usługą Azure SQL przy użyciu C++ technologii i ODBC w systemie Windows. Można kontynuować odczytywanie w taki sam sposób dla platformy Linux.

## <a id="Linux"></a>Krok 5. Nawiązywanie połączenia z aplikacji systemuC++ Linux C/
Jeśli nie zostały jeszcze wysłuchane wiadomości, program Visual Studio umożliwia teraz również opracowywanie C++ aplikacji systemu Linux. Informacje o tym nowym scenariuszu można znaleźć w [blogu C++ dotyczącym programowania w programie Visual for Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . Aby można było skompilować system Linux, potrzebna jest maszyna zdalna, w której działa system Linux dystrybucji. Jeśli nie masz takiego dostępu, możesz je skonfigurować szybko przy użyciu [maszyn wirtualnych systemu Linux Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym samouczku Przypuśćmy, że masz konfigurację dystrybucji systemu Linux Ubuntu 16,04. Kroki opisane w tym miejscu należy również stosować do Ubuntu 15,10, Red Hat 6 i Red Hat 7.

Poniższe kroki instalują biblioteki wymagane przez SQL i ODBC dla dystrybucji:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Uruchom program Visual Studio. W obszarze Narzędzia — Opcje > — > wielu platform — > Menedżer połączeń, Dodaj połączenie do pola systemu Linux:

![Opcje narzędzi](./media/sql-database-develop-cplusplus-simple/tools.png)

Po ustanowieniu połączenia za pośrednictwem protokołu SSH Utwórz pusty szablon projektu (Linux):

![Nowy szablon projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Następnie można dodać [nowy plik źródłowy C i zastąpić go tą zawartością](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Korzystając z interfejsów API ODBC SQLAllocHandle, SQLSetConnectAttr i SQLDriverConnect, powinno być możliwe zainicjowanie i nawiązanie połączenia z bazą danych.
Podobnie jak w przypadku przykładu ODBC dla systemu Windows, należy zastąpić Wywołanie SQLDriverConnect danymi z parametrów połączenia bazy danych skopiowanych z Azure Portal wcześniej.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Ostatnim zadaniem do wykonania przed kompilacją jest dodanie **ODBC** jako zależności biblioteki:

![Dodawanie ODBC jako biblioteki wejściowej](./media/sql-database-develop-cplusplus-simple/lib.png)

Aby uruchomić aplikację, wywołaj konsolę systemu Linux z menu **Debuguj** :

![Konsola systemu Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Jeśli połączenie zakończyło się pomyślnie, w konsoli systemu Linux powinna zostać wyświetlona nazwa bieżącej bazy danych:

![Dane wyjściowe okna konsoli systemu Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulacje! Samouczek został pomyślnie ukończony i teraz można połączyć się z usługą Azure SQL DB z poziomu C++ platformy Windows i systemu Linux.

## <a id="GetSolution"></a>Pobierz kompletne rozwiązanie C/C++ samouczka
Można znaleźć rozwiązanie getstarted, które zawiera wszystkie przykłady w tym artykule w witrynie GitHub:

* [Próbka ODBC C++ systemu Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), pobieranie C++ przykładu ODBC systemu Windows w celu nawiązania połączenia z usługą Azure SQL
* [Przykład C++ ODBC z systemem](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)Linux, pobieranie C++ przykładu ODBC z systemem Linux w celu nawiązania połączenia z usługą Azure SQL

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [omówieniem opracowywania SQL Database](sql-database-develop-overview.md)
* Więcej informacji na temat [dokumentacji interfejsu API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Poznaj wszystkie [możliwości usługi SQL Database](https://azure.microsoft.com/services/sql-database/)

