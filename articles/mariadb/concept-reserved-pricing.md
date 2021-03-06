---
title: Przedpłata za obliczenia z zarezerwowaną pojemnością — Azure Database for MariaDB
description: Przedpłata za Azure Database for MariaDB zasoby obliczeniowe z zarezerwowaną pojemnością
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 5821fb03dcf4331cbab5744f056521468a56d5d2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773111"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Przedpłata za Azure Database for MariaDB zasoby obliczeniowe z zarezerwowaną pojemnością

Azure Database for MariaDB teraz pomaga zaoszczędzić pieniądze dzięki wypłaceniu zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki Azure Database for MariaDB zarezerwowanej pojemności nastąpi zobowiązanie z góry na serwery MariaDB przez okres jednego roku, aby uzyskać znaczący rabat w kosztach obliczeniowych. Aby kupić Azure Database for MariaDB zarezerwowaną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin. </br>

Nie trzeba przypisywać rezerwacji do określonych serwerów Azure Database for MariaDB. Już uruchomione Azure Database for MariaDB lub te, które są nowo wdrożone, będą automatycznie korzystały z zarezerwowanych cen. Zakup rezerwacji polega na przedpłaceniu za koszt obliczeń przez okres jednego roku. Gdy tylko kupisz rezerwację, opłaty za usługę Azure Database for MariaDB, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci lub magazyn związany z serwerem bazy danych MariaDB. Na koniec okresu rezerwacji Pomoc dotycząca rozliczeń wygaśnie, a Azure Database for MariaDB są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [Azure Database for MariaDB zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/mariadb/). </br>

W [Azure Portal](https://portal.azure.com/)można kupić Azure Database for MariaDB zarezerwowaną pojemność. Aby kupić zarezerwowaną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić Azure Database for MariaDB zarezerwowaną pojemność. </br>

Szczegółowe informacje na temat tego, w jaki sposób Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym [użyciem są](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) obciążani opłatami za zakup rezerwacji [.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>Określ właściwy rozmiar serwera przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone bazy danych w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.</br>

Załóżmy na przykład, że korzystasz z jednego ogólnego celu, 5 rdzeń – 32 rdzeń wirtualny MariaDB Database i dwóch zoptymalizowanych pod kątem pamięci, 5 rdzeń – 16 rdzeń wirtualny MariaDB baz danych. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu, dodatkowym celu ogólnego przeznaczenia, 5 rdzeń – 32 rdzeń wirtualny bazy danych i jednej pamięci zoptymalizowanej jako serwer bazy danych 5 rdzeń-16 rdzeń wirtualny. Załóżmy, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku należy zakupić 64 (2x32) rdzeni wirtualnych, 1-letnią rezerwację dla jednej bazy danych ogólnego przeznaczenia-5 rdzeń i 48 (2x16 + 16) rdzeń wirtualny 1 roku dla jednolitej pamięci bazy danych zoptymalizowanej-5 rdzeń


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Kupowanie Azure Database for MariaDB zarezerwowanej pojemności

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3.  Wybierz pozycję **Dodaj** , a następnie w okienku rezerwacje zakupu wybierz pozycję **Azure Database for MariaDB** , aby zakupić nową rezerwację dla baz danych MariaDB.
4.  Wypełnij pola wymagane. Istniejące lub nowe bazy danych zgodne z wybranymi atrybutami kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba serwerów Azure Database for MariaDB, które pobierają rabat, zależą od wybranego zakresu i ilości.


![Przegląd cen zarezerwowanych](media/concepts-reserved-pricing/mariadb-reserved-price.png)


W poniższej tabeli opisano wymagane pola.

| Pole | Opis |
| :------------ | :------- |
| Subskrypcja   | Subskrypcja używana do płacenia za Azure Database for MariaDB zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry Azure Database for MariaDB zastrzeżonej rezerwacji. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.
| Zakres | Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: </br></br> Rdzeń wirtualny rabat rezerwacji jest stosowany do serwerów Azure Database for MariaDB uruchomionych w ramach dowolnych subskrypcji w kontekście rozliczeń. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.</br></br> **Pojedyncza subskrypcja**— rabat rezerwacji rdzeń wirtualny jest stosowany do serwerów Azure Database for MariaDB w tej subskrypcji. </br></br> **Pojedynczej grupy zasobów**, Rabat rezerwacji jest stosowany do Azure Database for MariaDB serwerów w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.
| Region | Region świadczenia usługi Azure objęty Azure Database for MariaDB rezerwacji zarezerwowanej pojemności.
| Typ wdrożenia | Typ zasobu Azure Database for MariaDB, dla którego chcesz kupić rezerwację.
| Warstwa wydajności | Warstwa usług dla serwerów Azure Database for MariaDB.
| Okres obowiązywania Umowy | Jeden rok
| Ilość | Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji Azure Database for MariaDB zarezerwowanej. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania programu lub planowania uruchamiania serwerów Azure Database for MariaDB z łączną pojemnością obliczeniową 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich serwerów.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. 

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby serwerów Azure Database for MariaDB, które pasują do Azure Database for MariaDB zakres rezerwacji zarezerwowanej pojemności i atrybuty. Możesz zaktualizować zakres rezerwacji zarezerwowanej pojemności usługi Azure Database for MariaDB za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API. </br></br>
Aby dowiedzieć się, jak zarządzać zarezerwowaną Azure Database for MariaDB pojemności, zobacz Zarządzanie Azure Database for MariaDB zarezerwowaną pojemnością.

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to są Azure Reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Zarządzanie usługą Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Informacje na temat rabatu na rezerwacje platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
