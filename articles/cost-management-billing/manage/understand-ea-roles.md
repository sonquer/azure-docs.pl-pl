---
title: Omówienie ról administracyjnych dla przedsiębiorstwa na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat ról administratorów przedsiębiorstwa na platformie Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9fab4d8ba0cf2e6f684a1b9de177084f8ce31604
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462194"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure

Aby ułatwić zarządzanie użyciem i wydatkami w organizacji, klienci platformy Azure z umową Enterprise Agreement (EA) mogą przypisywać pięć odrębnych ról administracyjnych:

- Administratora przedsiębiorstwa
- Administrator przedsiębiorstwa (tylko odczyt)<sup>1</sup>
- Administrator działu
- Administrator działu (tylko odczyt)
- Właściciel konta

<sup>1</sup> Kontakt dla rozliczeń dotyczących umowy EA będzie objęty tą rolą.

Te role są specyficzne dla procesu zarządzania umowami Enterprise Agreement platformy Azure. Są one dodatkiem do wbudowanych ról platformy Azure, które umożliwiają kontrolowanie dostępu do zasobów. Aby uzyskać więcej informacji, zobacz temat [Wbudowane role dla zasobów platformy Azure](../../role-based-access-control/built-in-roles.md).

W poniższych sekcjach opisano ograniczenia i możliwości poszczególnych ról.

## <a name="user-limit-for-admin-roles"></a>Limit liczby użytkowników dla ról administratorów

|Rola| Limit użytkowników|
|---|---|
|Administratora przedsiębiorstwa|Nieograniczona liczba|
|Administrator przedsiębiorstwa (tylko odczyt)|Nieograniczona liczba|
|Administrator działu|Nieograniczona liczba|
|Administrator działu (tylko odczyt)|Nieograniczona liczba|
|Właściciel konta|1 na konto<sup>2</sup>|

<sup>2</sup> Każde konto wymaga unikatowego konta Microsoft lub konta służbowego.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura i uprawnienia organizacji według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt)|Właściciel konta|
|---|---|---|---|---|---|
|Wyświetlanie administratorów przedsiębiorstwa|✔|✔|✘|✘|✘|
|Dodawanie lub usuwanie administratorów przedsiębiorstwa|✔|✘|✘|✘|✘|
|Wyświetlanie kontaktów dla powiadomień<sup>3</sup> |✔|✔|✘|✘|✘|
|Dodawanie lub usuwanie kontaktów dla powiadomień<sup>3</sup> |✔|✘|✘|✘|✘|
|Tworzenie działów i zarządzanie nimi |✔|✘|✘|✘|✘|
|Wyświetlanie administratorów działu|✔|✔|✔|✔|✘|
|Dodawanie lub usuwanie administratorów działu|✔|✘|✔|✘|✘|
|Wyświetlanie kont w rejestracji |✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✘|
|Dodawanie kont do rejestracji i zmienianie właściciela konta|✔|✘|✔<sup>4</sup>|✘|✘|
|Tworzenie subskrypcji i uprawnień subskrypcji oraz zarządzanie nimi|✘|✘|✘|✘|✔|

- <sup>3</sup> Kontakty dla powiadomień otrzymują wiadomości e-mail dotyczące umowy Enterprise Agreement platformy Azure.
- <sup>4</sup> Zadanie jest ograniczone do kont w Twoim dziale.


## <a name="usage-and-costs-access-by-role"></a>Dostęp do danych użycia i kosztów według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt) |Właściciel konta|
|---|---|---|---|---|---|
|Wyświetlanie salda środków, w tym zobowiązania pieniężnego|✔|✔|✘|✘|✘|
|Wyświetlanie limitów przydziału wydatków dla działu|✔|✔|✘|✘|✘|
|Ustawianie limitów przydziału wydatków dla działu|✔|✘|✘|✘|✘|
|Wyświetlanie arkusza cen umów EA organizacji|✔|✔|✘|✘|✘|
|Wyświetlanie szczegółów użycia i kosztów|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✔<sup>6</sup>|
|Zarządzanie zasobami w witrynie Azure Portal|✘|✘|✘|✘|✔|

- <sup>5</sup> Wymaga, aby administrator przedsiębiorstwa włączył zasady **Administrator przedsiębiorstwa — wyświetlanie opłat** w witrynie Enterprise Portal. Administrator działu będzie następnie widzieć szczegóły kosztów dla działu.
- <sup>6</sup> Wymaga, aby administrator przedsiębiorstwa włączył zasady **Właściciel konta — wyświetlanie opłat** w witrynie Enterprise Portal. Właściciel konta będzie następnie widzieć szczegóły kosztów dla konta.


## <a name="pricing-in-azure-portal"></a>Ceny w witrynie Azure Portal

W zależności od roli administracyjnej i sposobu ustawienia zasad wyświetlania opłat przez administratora przedsiębiorstwa w witrynie Azure Portal mogą być widoczne różne ceny. Dwie zasady w witrynie Enterprise Portal, które mają wpływ na ceny widoczne w witrynie Azure Portal, to:

- Administrator działu — wyświetlanie opłat
- Właściciel konta — wyświetlanie opłat

Aby dowiedzieć się, jak ustawić te zasady, zobacz temat [Manage access to billing information for Azure](manage-billing-access.md) (Zarządzanie dostępem do informacji rozliczeniowych dotyczących platformy Azure).

W poniższej tabeli przedstawiono relację między rolami administracyjnymi umów Enterprise Agreement, zasadami wyświetlania opłat, rolą kontroli dostępu na podstawie ról (RBAC) w witrynie Azure Portal oraz cenami widocznymi w witrynie Azure Portal. Administrator przedsiębiorstwa zawsze widzi szczegóły użycia w oparciu o ceny umowy EA organizacji. Natomiast administrator działu i właściciel konta zobaczą różne widoki cen w zależności od zasad wyświetlania opłat i odpowiedniej roli RBAC. Rola Administrator działu wymieniona w poniższej tabeli odnosi się do ról Administrator działu i Administrator działu (tylko odczyt).

|Rola administratora umowy Enterprise Agreement|Zasady wyświetlania opłat dla roli|Rola RBAC|Widok cen|
|---|---|---|---|
|Właściciel konta LUB administrator działu|✔ Włączone|Właściciel|Ceny umowy EA organizacji|
|Właściciel konta LUB administrator działu|✘ Wyłączone|Właściciel|Ceny detaliczne|
|Właściciel konta LUB administrator działu|✔ Włączone |brak|Brak cen|
|Właściciel konta LUB administrator działu|✘ Wyłączone |brak|Brak cen|
|Brak|Nie dotyczy |Właściciel|Ceny detaliczne|

Rola administratora przedsiębiorstwa i zasady wyświetlania opłat są ustawiane w witrynie Enterprise Portal. Rolę RBAC można zaktualizować w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- [Manage access to billing information for Azure (Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure)](manage-billing-access.md)
- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](../../role-based-access-control/role-assignments-portal.md)
- [Wbudowane role dla zasobów platformy Azure](../../role-based-access-control/built-in-roles.md)
