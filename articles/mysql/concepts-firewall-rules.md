---
title: Reguły zapory — Azure Database for MySQL
description: Dowiedz się więcej na temat używania reguł zapory do włączania połączeń z serwerem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155200"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Reguły zapory serwera Azure Database for MySQL
Zapory uniemożliwiają dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania.

Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy akceptowalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

**Reguły zapory:** Te reguły umożliwiają klientom dostęp do całego serwera Azure Database for MySQL, czyli wszystkich baz danych na tym samym serwerze logicznym. Reguły zapory na poziomie serwera można skonfigurować za pomocą poleceń Azure Portal lub interfejsu wiersza polecenia platformy Azure. Aby utworzyć reguły zapory na poziomie serwera, musisz być właścicielem subskrypcji lub współautorem subskrypcji.

## <a name="firewall-overview"></a>Omówienie zapory
Dostęp wszystkich baz danych do serwera Azure Database for MySQL jest blokowany domyślnie przez zaporę. Aby rozpocząć korzystanie z serwera z innego komputera, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera. Użyj reguł zapory, aby określić, które zakresy adresów IP z Internetu mają być dozwolone. Reguły zapory nie wpływają na dostęp do samej witryny sieci Web Azure Portal.

Próby połączenia z Internetu i platformy Azure muszą być przekazywane przez zaporę przed uzyskaniem dostępu do bazy danych Azure Database for MySQL, jak pokazano na poniższym diagramie:

![Przykładowy przepływ działania zapory](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Łączenie się z Internetu
Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na serwerze Azure Database for MySQL.

Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, nawiązanie połączenia zostanie nadane.

Jeśli adres IP żądania wykracza poza zakresy określone w regułach zapory na poziomie bazy danych lub na poziomie serwera, żądanie połączenia kończy się niepowodzeniem.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Zalecane jest, aby znaleźć wychodzący adres IP dowolnej aplikacji lub usługi i jawnie zezwolić na dostęp do tych pojedynczych adresów IP lub zakresów. Na przykład można znaleźć wychodzący adres IP Azure App Service lub użyć publicznego adresu IP powiązanego z maszyną wirtualną lub innym zasobem (zobacz poniżej, aby uzyskać informacje na temat nawiązywania połączenia z prywatnym adresem IP maszyny wirtualnej za pośrednictwem punktów końcowych usługi). 

Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, możesz rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure. To ustawienie można włączyć z poziomu Azure Portal, ustawiając opcję **Zezwalaj na dostęp do usług platformy Azure** na **wartość z** okienka **zabezpieczenia połączenia** i naciskając polecenie **Zapisz**. W interfejsie wiersza polecenia platformy Azure ustawienie reguły zapory z adresem początkowym i końcowym równym 0.0.0.0 jest równoważne. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera Azure Database for MySQL.

> [!IMPORTANT]
> Opcja **Zezwalaj na dostęp do usług platformy Azure** umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

![Konfigurowanie zezwalania na dostęp do usług platformy Azure w portalu](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Łączenie z sieci wirtualnej
Aby bezpiecznie połączyć się z serwerem Azure Database for MySQL z sieci wirtualnej, należy rozważyć użycie [punktów końcowych usługi sieci wirtualnej](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Programowe zarządzanie regułami zapory
Oprócz Azure Portal reguły zapory można zarządzać programowo przy użyciu interfejsu wiersza polecenia platformy Azure. Zobacz też [Tworzenie reguł zapory Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Rozwiązywanie problemów z zaporą
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi Microsoft Azure Database for MySQL Server nie zachowuje się zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory serwera Azure Database for MySQL zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:** Jeśli logowanie nie ma uprawnień na serwerze Azure Database for MySQL lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for MySQL. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP:** Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for MySQL, a następnie Dodaj zakres adresów IP jako regułę zapory.

   * Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

* **Adres IP serwera wydaje się być publiczny:** Połączenia z serwerem Azure Database for MySQL są kierowane za pomocą publicznie dostępnej bramy platformy Azure. Rzeczywisty adres IP serwera jest jednak chroniony przez zaporę. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym architektury łączności](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)
- [Punkty końcowe usługi sieci wirtualnej w Azure Database for MySQL](./concepts-data-access-and-security-vnet.md)
