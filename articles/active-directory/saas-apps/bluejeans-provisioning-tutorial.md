---
title: 'Samouczek: Konfigurowanie BlueJeans dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059035"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BlueJeans na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w BlueJeans i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do BlueJeans.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawa BlueJeans z planem [My Company](https://www.BlueJeans.com/pricing) lub lepsza
* Konto użytkownika w BlueJeans z uprawnieniami administratora

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API BlueJeans](https://BlueJeans.github.io/developer), który jest dostępny dla zespołów BlueJeans w planie standardowym lub lepszym.

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie rozwiązania BlueJeans z galerii

Przed skonfigurowaniem usługi BlueJeans do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać BlueJeans z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **BlueJeans**, wybierz pozycję **BlueJeans** w panelu wyników, a następnie wybierz przycisk **Dodaj** , aby dodać aplikację.

    ![Rozwiązanie BlueJeans na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Przypisywanie użytkowników do BlueJeans

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do BlueJeans. Po ustaleniu tych użytkowników i/lub grup można przypisywać do BlueJeans, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Ważne wskazówki dotyczące przypisywania użytkowników do BlueJeans

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do BlueJeans w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do BlueJeans należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurowanie automatycznej aprowizacji użytkowników do BlueJeans

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie BlueJeans na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla BlueJeans, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](bluejeans-tutorial.md)jednokrotne w BlueJeans. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla BlueJeans w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **BlueJeans**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BlueJeans**.

    ![Link rozwiązania BlueJeans na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. W sekcji **poświadczenia administratora** wprowadź **nazwę użytkownika admin**i **hasło administratora** konta BlueJeans. Przykłady tych wartości:

   * W polu **Nazwa użytkownika administratora** Wypełnij pole Nazwa użytkownika konta administratora w dzierżawie BlueJeans. Przykład: admin@contoso.com.

   * W polu **hasło administratora** Wypełnij hasło odpowiednie do nazwy użytkownika administratora.

6. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą BlueJeans. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi BlueJeans ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kliknij przycisk **Save** (Zapisz).

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do BlueJeans**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do BlueJeans w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie BlueJeans for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę Azure AD Provisioning dla BlueJeans, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić BlueJeans, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie BlueJeans.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* BlueJeans nie zezwala na nazwy użytkowników, których długość przekracza 30 znaków.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
