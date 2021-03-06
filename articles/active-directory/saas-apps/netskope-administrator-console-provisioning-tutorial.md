---
title: 'Samouczek: Konfigurowanie konsola administratora Netskope dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Netskope konsola administratora.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061293"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konsola administratora Netskope na potrzeby automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Netskope konsola administratora i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Netskope konsola administratora.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Netskope konsola administratora dzierżawy](https://www.netskope.com/)
* Konto użytkownika w Netskope konsola administratora z uprawnieniami administratora.

## <a name="assigning-users-to-netskope-administrator-console"></a>Przypisywanie użytkowników do Netskope konsola administratora

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Netskope konsola administratora. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Netskope konsola administratora, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Netskope konsola administratora

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Netskope konsola administratora do testowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Netskope konsola administratora należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Konfigurowanie konsola administratora Netskope na potrzeby aprowizacji

1. Zaloguj się do [konsoli administracyjnej Konsola administratora Netskope](https://netskope.goskope.com/). Przejdź do **strony głównej > ustawienia**.

    ![Konsola administracyjna konsola administratora Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Przejdź do **menu Narzędzia**. W menu **Narzędzia** przejdź do pozycji **Narzędzia katalogów > Integracja Standard scim**.

    ![Narzędzia konsola administratora Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope konsola administratora Dodaj Standard scim](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Przewiń w dół i kliknij przycisk **Dodaj token** . W oknie dialogowym **Dodawanie nazwy klienta OAuth** Podaj **nazwę klienta** i kliknij przycisk **Zapisz** .

    ![Netskope Dodawanie tokenu konsola administratora](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope konsola administratora nazwę klienta](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Skopiuj **adres URL serwera Standard scim** i **token**. Te wartości zostaną wprowadzone w polach adres URL dzierżawy i klucz tajny tokenu odpowiednio na karcie aprowizacji aplikacji Netskope konsola administratora w Azure Portal.

    ![Netskope konsola administratora Utwórz token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Dodawanie konsola administratora Netskope z galerii

Przed skonfigurowaniem konsola administratora Netskope na potrzeby automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Netskope konsola administratora z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Netskope konsola administratora z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Netskope Konsola administratora**, wybierz pozycję **Netskope Konsola administratora** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Netskope konsola administratora na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Netskope konsola administratora 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Netskope konsola administratora na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla Netskope konsola administratora, postępując zgodnie z instrukcjami podanymi w temacie [Netskope Konsola administratora logowania](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)jednokrotnego. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym Standard scim Netskope konsola administratora, zapoznaj się z [tym](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)tematem.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla Netskope konsola administratora w usłudze Azure AD:

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Netskope Konsola administratora**.

    ![Link konsola administratora Netskope na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość **adresu URL serwera Standard scim** , która została pobrana wcześniej w **adresie URL dzierżawy**. Wprowadź wartość **tokenu** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z Netskope Konsola administratora. Jeśli połączenie nie powiedzie się, upewnij się, że konto konsola administratora Netskope ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Save** (Zapisz).

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Netskope Konsola administratora**.

    ![Netskope konsola administratora mapowania użytkowników](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Netskope konsola administratora w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Netskope Konsola administratora dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Netskope konsola administratora atrybuty użytkownika](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Konsola administratora Netskope**.

    ![Netskope mapowania grup konsola administratora](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Netskope konsola administratora w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Netskope Konsola administratora dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy konsola administratora Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Netskope konsola administratora, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Netskope konsola administratora, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w Netskope Konsola administratora.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

