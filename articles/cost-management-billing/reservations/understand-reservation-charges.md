---
title: Informacje o rabatach na rezerwacje dla baz danych Azure SQL Database | Microsoft Docs
description: Dowiedz się, w jaki sposób rabat na rezerwacje jest stosowany do działających baz danych Azure SQL Database.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f0b093950b7f8fbbfbb0ca8ceb48cb0aa6341e10
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200286"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>W jaki sposób rabat na rezerwacje jest stosowany do baz danych Azure SQL Database

Po zakupie pojemności zarezerwowanej usługi Azure SQL Database rabat na rezerwację jest automatycznie stosowany do baz danych SQL Database pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje koszty zasobów obliczeniowych bazy danych SQL Database. Opłata jest naliczana za oprogramowanie, magazyn i sieć według normalnych stawek. Koszty licencjonowania baz danych SQL Database można pokryć w ramach [Korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Aby uzyskać informacje dotyczące wystąpień zarezerwowanych maszyny wirtualnej, zobacz [Objaśnienie rabatu na wystąpienia zarezerwowane maszyny wirtualnej platformy Azure](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="discount-applied-to-sql-databases"></a>Rabat dotyczący baz danych SQL Database

 Rabat na pojemność zarezerwowaną usługi SQL Database jest stosowany względem działających baz danych SQL Database z rozliczeniem godzinowym. Kupowana rezerwacja jest dopasowywana do użycia zasobów obliczeniowych emitowanych przez działające bazy danych SQL Database. W przypadku baz danych SQL Database, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych baz danych SQL Database pasujących do atrybutów rezerwacji. Rabat może być stosowany względem baz danych SQL Database, które działają równolegle. Jeśli nie masz baz danych SQL Database, które działają przez pełną godzinę i pasują do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu na rezerwację w tej godzinie.

W poniższych przykładach pokazano, w jaki sposób rabat na pojemność zarezerwowaną usługi SQL Database jest stosowany w zależności od liczby zakupionych rdzeni oraz czasu ich działania.

- Scenariusz 1. Zakupiono pojemność zarezerwowaną usługi SQL Database dla 8-rdzeniowej bazy danych SQL Database. Uruchomiono 16-rdzeniową bazę danych SQL Database, która pasuje do pozostałych atrybutów rezerwacji. Naliczono opłatę według stawki płatności zgodnie z rzeczywistym użyciem dla 8 rdzeni użycia zasobów obliczeniowych usługi SQL Database. Zastosowano rabat na rezerwację na jedną godzinę dla 8 rdzeni użycia zasobów obliczeniowych usługi SQL Database.

Na potrzeby pozostałych przykładów przyjęto założenie, że zakupiona pojemność zarezerwowana usługi SQL Database jest przeznaczona dla 16-rdzeniowej bazy danych SQL Database, a pozostałe atrybuty rezerwacji pasują do działających baz danych SQL Database.

- Scenariusz 2. Uruchomiono dwie 8-rdzeniowe bazy danych SQL Database na jedną godzinę. Rabat na rezerwację 16 rdzeni jest stosowany do użycia zasobów obliczeniowych dla obu 8-rdzeniowych baz danych SQL Database.
- Scenariusz 3. Uruchomiono jedną 16-rdzeniową bazę danych SQL Database od godziny 13:00 do 13:30. Uruchomiono kolejną 16-rdzeniową bazę danych SQL Database od godziny 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.
- Scenariusz 4. Uruchomiono jedną 16-rdzeniową bazę danych SQL Database od godziny 13:00 do 13:45. Uruchomiono kolejną 16-rdzeniową bazę danych SQL Database od godziny 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu baz danych jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie zasobów obliczeniowych przez resztę czasu jest stosowany rabat na rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)
