---
title: Rozwiązywanie problemów z połączeniami — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak rozwiązywać problemy z połączeniem do Azure Database for PostgreSQL-jednego serwera.
keywords: Połączenie PostgreSQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c74b819893133116b8ac6905988f3fe11220ed95
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770071"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Rozwiązywanie problemów z połączeniem do Azure Database for PostgreSQL-pojedynczego serwera

Problemy z połączeniem mogą być spowodowane różnymi elementami, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje logowania
* Osiągnięto maksymalny limit dla niektórych zasobów Azure Database for PostgreSQL
* Problemy związane z infrastrukturą usługi
* Konserwacja w usłudze
* Alokacja obliczeniowa serwera jest zmieniana przez skalowanie liczby rdzeni wirtualnych lub przechodzenie do innej warstwy usług

Ogólnie rzecz biorąc problemy z połączeniem do Azure Database for PostgreSQL mogą być klasyfikowane w następujący sposób:

* Błędy przejściowe (krótko-lub sporadyczne)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie powtarzają się)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują, gdy konserwacja jest przeprowadzana, system napotyka błąd z sprzętem lub oprogramowaniem lub zmienia rdzeni wirtualnych lub warstwę usług serwera. Usługa Azure Database for PostgreSQL ma wbudowaną wysoką dostępność i została zaprojektowana z myślą o ograniczeniu tego rodzaju problemów automatycznie. Jednak aplikacja traci połączenie z serwerem przez krótki okres zwykle krótszy niż 60 sekund. Niektóre zdarzenia mogą sporadycznie podejmować środki zaradcze, na przykład wtedy, gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki rozwiązywania przejściowych problemów z łącznością

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://azure.microsoft.com/status) , aby uzyskać znane awarie, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje, które łączą się z usługą w chmurze, taką jak Azure Database for PostgreSQL, powinny oczekiwać błędów przejściowych i implementować logikę ponawiania w celu obsługi tych błędów zamiast obsłużyć te błędy jako błędy aplikacji dla użytkowników. Przegląd [obsługi błędów łączności przejściowej dla Azure Database for PostgreSQL](concepts-connectivity.md) w celu uzyskania najlepszych rozwiązań i projektowania wytycznych dotyczących obsługi błędów przejściowych.
3. Ponieważ serwer zbliża się do limitów zasobów, może wydawać się, że problem z łącznością jest przejściowy. Zobacz [ograniczenia w Azure Database for PostgreSQL](concepts-limits.md).
4. Jeśli problemy z łącznością będą kontynuowane lub jeśli czas, przez który aplikacja napotyka błąd, przekracza 60 sekund lub Jeśli zobaczysz wiele wystąpień błędu w danym dniu, Utwórz żądanie pomocy technicznej platformy Azure, wybierając pozycję **Uzyskaj pomoc** techniczną w witrynie [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z trwałymi błędami

Jeśli aplikacja trwale nie może nawiązać połączenia z Azure Database for PostgreSQL, zazwyczaj wskazuje problem z jedną z następujących czynności:

* Konfiguracja zapory serwera: Upewnij się, że Zapora serwera Azure Database for PostgreSQL jest skonfigurowana tak, aby zezwalać na połączenia z klienta, w tym serwery proxy i bramy.
* Konfiguracja zapory klienta: Zapora na kliencie musi zezwalać na połączenia z serwerem bazy danych. Adresy IP i porty serwera, które nie mogą być dozwolone, a także nazwy aplikacji, takie jak PostgreSQL w niektórych zaporach.
* Błąd użytkownika: być może masz błędne typy parametrów połączenia, takie jak nazwa serwera w parametrach połączenia lub brak sufiksu *\@ServerName* w nazwie użytkownika.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki rozwiązywania problemów z łącznością trwałą

1. Skonfiguruj [reguły zapory](howto-manage-firewall-using-portal.md) w taki sposób, aby zezwalały na adres IP klienta. Wyłącznie do celów testowania tymczasowego, należy skonfigurować regułę zapory przy użyciu adresu 0.0.0.0 jako początkowy adres IP i przy użyciu wartości 255.255.255.255 jako końcowego adresu IP. Spowoduje to otwarcie serwera do wszystkich adresów IP. Jeśli spowoduje to rozwiązanie problemu z łącznością, Usuń tę regułę i Utwórz regułę zapory dla odpowiednio ograniczonego adresu IP lub zakresu adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 5432 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia.
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym. Jeśli uważasz, że wystąpiła awaria regionalna, zobacz [Omówienie ciągłości działania w Azure Database for PostgreSQL](concepts-business-continuity.md) w celu wykonania czynności związanych z odzyskiwaniem do nowego regionu.

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów łączności przejściowej dla Azure Database for PostgreSQL](concepts-connectivity.md)
