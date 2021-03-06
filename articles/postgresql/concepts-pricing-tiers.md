---
title: Warstwy cenowe — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano opcje obliczeń i magazynowania w Azure Database for PostgreSQL-pojedynczym serwerze.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: b5a2d8a812c5b3b0021ad4f74fc705049807cb1b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485067"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Warstwy cenowe w Azure Database for PostgreSQL — pojedynczy serwer

Serwer Azure Database for PostgreSQL można utworzyć w jednej z trzech różnych warstw cenowych: podstawowe, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości obliczeń w rdzeni wirtualnych, które mogą być inicjowane, pamięć na rdzeń wirtualny i technologia magazynowania służąca do przechowywania danych. Wszystkie zasoby są obsługiwane na poziomie serwera PostgreSQL. Serwer może mieć jedną lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeń | Gen 4, Gen 5 | Gen 4, Gen 5 | 5\. generacja |
| Rdzeni wirtualnych | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Pamięć na rdzeń wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 16 TB | od 5 GB do 16 TB |
| Okres przechowywania kopii zapasowej bazy danych | od 7 do 35 dni | od 7 do 35 dni | od 7 do 35 dni |

Aby wybrać warstwę cenową, należy użyć poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa | Obciążenia, które wymagają lekkich obliczeń i wydajności operacji we/wy. Przykłady obejmują serwery używane do programowania lub testowania oraz nierzadko używane aplikacje. |
| Ogólne zastosowanie | Większość obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy. Przykłady obejmują serwery do hostowania aplikacji internetowych i mobilnych oraz inne aplikacje dla przedsiębiorstw.|
| Pamięć | Obciążenia baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności. Przykładami mogą być serwery do przetwarzania danych w czasie rzeczywistym oraz aplikacji transakcyjnych lub analitycznych o wysokiej wydajności.|

Po utworzeniu serwera można zmienić liczbę rdzeni wirtualnych, generowanie sprzętu i warstwę cenową (oprócz i z Basic) w ciągu kilku sekund. Można także niezależnie dostosować ilość miejsca do magazynowania i okres przechowywania kopii zapasowych w górę lub w dół bez przestojów aplikacji. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Aby uzyskać więcej informacji, zobacz sekcję [skalowanie zasobów](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Generacja obliczeń i rdzeni wirtualnych

Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Chiny Wschodnie 1, Chiny Północne 1, US DoD (region środkowy) i US DoD (region wschodni) używają logicznych procesorów CPU, które są oparte na procesorach Intel E5-2673 v3 (Haswell) 2,4 GHz. Wszystkie pozostałe regiony wykorzystują jednostki logiczne procesorów generacji 5, które są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Magazyn

Zapewniana ilość miejsca w magazynie to pojemność magazynu dostępna dla serwera Azure Database for PostgreSQL. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera PostgreSQL. Całkowita ilość dostępnego miejsca w magazynie określa również wydajność we/wy dostępną dla serwera.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Magazyn podstawowy | Magazyn Ogólnego przeznaczenia | Magazyn Ogólnego przeznaczenia |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 16 TB | od 5 GB do 16 TB |
| Rozmiar przyrostu pamięci masowej | 1 GB | 1 GB | 1 GB |
| Liczba operacji we/wy na sekundę | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalna liczba operacji we/wy 20 000 | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalna liczba operacji we/wy 20 000 |

> [!NOTE]
> Magazyn o pojemności do 16TB i 20 000 operacji we/wy jest obsługiwany w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, zachodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Azja Południowo-Wschodnia Azja Wschodnia, Japonia Wschodnia, Korea środkowa , Korea Południowa, Australia Wschodnia, Australia Południowo-Wschodnia.
>
> Wszystkie inne regiony obsługują do 4 TB magazynu i 6000 operacji we/wy na sekundę.
>

Można dodać dodatkową pojemność magazynu podczas i po utworzeniu serwera oraz pozwolić systemowi na automatyczne zwiększanie ilości miejsca w oparciu o użycie magazynu w ramach obciążenia. 

>[!NOTE]
> Magazyn można skalować w górę, nie w dół.

Warstwa Podstawowa nie oferuje gwarancji operacji we/wy na sekundę. W warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci liczba operacji we/wy z zainicjowanym rozmiarem magazynu wynosi 3:1.

Możesz monitorować użycie we/wy w Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [Limit magazynu, procent magazynu, użycie magazynu i procent operacji we/wy](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Osiąganie limitu magazynu

Serwery z aprowizowanym magazynem o rozmiarze mniejszym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 512 MB lub 5% rozmiaru aprowizowanego magazynu. Serwery z aprowizowanym magazynem o rozmiarze większym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GB.

Jeśli na przykład Zainicjowano obsługę administracyjną 110 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 105 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli masz zainicjowany 5 GB miejsca w magazynie, serwer jest oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca osiągnie mniej niż 512 MB.

Gdy serwer jest ustawiony na tylko do odczytu, wszystkie istniejące sesje są rozłączone, a niezatwierdzone transakcje są wycofywane. Wszelkie kolejne operacje zapisu i zatwierdzenia transakcji kończą się niepowodzeniem. Wszystkie kolejne zapytania odczytu będą działały nieprzerwanie.  

Można zwiększyć ilość magazynu zainicjowanego na serwer lub rozpocząć nową sesję w trybie odczytu i zapisu oraz porzucić dane, aby odzyskiwać bezpłatny magazyn. Uruchomienie `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` ustawia tryb zapisu bieżącej sesji na odczyt. Aby uniknąć uszkodzenia danych, nie wykonuj żadnych operacji zapisu, gdy serwer nadal jest w stanie tylko do odczytu.

Zalecamy włączenie opcji autowzrostu magazynu lub skonfigurowanie alertu w celu powiadomienia użytkownika o tym, że magazyn serwera zbliża się do progu, aby można było uniknąć przekroczenia stanu tylko do odczytu. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [sposobu konfigurowania alertu](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Autouzupełnianie magazynu

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi wyjście z magazynu i staje się tylko do odczytu. Jeśli funkcja automatycznego zwiększania rozmiaru magazynu jest włączona, magazyn automatycznie rośnie bez wpływu na obciążenie. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie jest mniejsza niż 1 GB lub 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone powyżej.

Jeśli na przykład Zainicjowano obsługę administracyjną 1000 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 950 GB, rozmiar magazynu serwera zostanie zwiększony do 1050 GB. Alternatywnie, jeśli masz zainicjowany 10 GB miejsca w magazynie, rozmiar magazynu zostanie zwiększony do 15 GB, gdy jest mniej niż 1 GB miejsca w magazynie.

Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="backup"></a>Backup

Usługa automatycznie pobiera kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Możesz ustawić okres przechowywania na maksymalnie 35 dni. Przechowywanie można zmienić w dowolnym momencie w okresie istnienia serwera. Można wybrać między lokalnie nadmiarowe i geograficznie nadmiarowe kopie zapasowe. Dublowane nadmiarowe kopie zapasowe są również przechowywane w [obszarze geograficznym](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu obszaru, w którym został utworzony serwer. Ta nadmiarowość zapewnia poziom ochrony w przypadku awarii. Możliwe jest również przywrócenie serwera do dowolnego innego regionu świadczenia usługi Azure, w którym usługa jest dostępna w geograficznie nadmiarowych kopiach zapasowych. Po utworzeniu serwera nie jest możliwe przeprowadzenie zmiany między tymi dwiema opcjami magazynu kopii zapasowych.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rdzeni wirtualnych, generowanie sprzętu, warstwę cenową (poza i z warstwy Podstawowa), ilość miejsca do magazynowania oraz okres przechowywania kopii zapasowych. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Liczbę rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Rozmiar magazynu można zwiększyć tylko. Skalowanie zasobów można przeprowadzić za pomocą portalu lub interfejsu wiersza polecenia platformy Azure. Aby zapoznać się z przykładem skalowania przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [monitorowanie i skalowanie serwera Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure](scripts/sample-scale-server-up-or-down.md).

> [!NOTE] 
> Rozmiar magazynu można zwiększyć tylko. Po zwiększeniu nie można wrócić do mniejszego rozmiaru magazynu.

W przypadku zmiany liczby rdzeni wirtualnych, wygenerowania sprzętu lub warstwy cenowej kopia oryginalnego serwera zostanie utworzona przy użyciu nowej alokacji obliczeniowej. Gdy nowy serwer zostanie uruchomiony, połączenia zostaną przełączone na nowy serwer. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Długość tego okresu może być różna, lecz w większości przypadków jest to mniej niż minuta.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowych to prawdziwe operacje online. Brak przestoju i nie ma to znaczenia dla Twojej aplikacji. Jako że liczba operacji we/wy na sekundę jest skalowana wraz z rozmiarem magazynu, można zwiększyć liczbę operacji we/wy dla serwera, skalowanie w górę magazynu.

## <a name="pricing"></a>Cennik

Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/PostgreSQL/)usługi. Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) przedstawia miesięczny koszt na karcie **warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** i wybierz **Azure Database for PostgreSQL** , aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak utworzyć serwer PostgreSQL w portalu](tutorial-design-database-using-azure-portal.md).
- Dowiedz się więcej o [limitach usługi](concepts-limits.md). 
- Dowiedz się, jak [skalować w poziomie przy użyciu replik odczytu](howto-read-replicas-portal.md).
