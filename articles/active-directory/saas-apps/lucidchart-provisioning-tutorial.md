---
title: 'Samouczek: Inicjowanie obsługi użytkowników dla LucidChart — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze LucidChart.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057332"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie LucidChart na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w LucidChart i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do LucidChart. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa LucidChart z [planem Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) lub lepsza
* Konto użytkownika w LucidChart z uprawnieniami administratora

## <a name="assigning-users-to-lucidchart"></a>Przypisywanie użytkowników do LucidChart

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji LucidChart. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji LucidChart, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Ważne wskazówki dotyczące przypisywania użytkowników do LucidChart

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do LucidChart w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do LucidChart należy wybrać rolę **użytkownika** lub inną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. **Domyślna rola dostępu** nie działa w przypadku inicjowania obsługi administracyjnej, a użytkownicy są pomijani.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurowanie aprowizacji użytkowników do LucidChart

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika LucidChart, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze LucidChart na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla LucidChart, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika w usłudze Azure AD LucidChart

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > Wszystkie aplikacje** .

2. Jeśli już skonfigurowano LucidChart do logowania jednokrotnego, Wyszukaj wystąpienie elementu LucidChart przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Lucidchart** w galerii aplikacji. Wybierz pozycję LucidChart z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

3. Wybierz wystąpienie elementu LucidChart, a następnie wybierz kartę **Inicjowanie obsługi** .

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Inicjowanie obsługi LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. W sekcji **poświadczenia administratora** wprowadź **klucz tajny** wygenerowany przez konto Lucidchart (możesz znaleźć token w ramach swojego konta: **Team** > **App Integration** > **Standard scim**).

    ![Inicjowanie obsługi LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Lucidchart. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi LucidChart ma uprawnienia administratora, a następnie spróbuj ponownie wykonać krok 5.

7. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach dotyczących aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru "Wyślij powiadomienie e-mail, gdy wystąpi błąd".

8. Kliknij przycisk **Save** (Zapisz).

9. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do Lucidchart**.

10. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Lucidchart. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Lucidchart for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługę Azure AD Provisioning dla usługi LucidChart, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

12. Kliknij przycisk **Save** (Zapisz).

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do LucidChart w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
