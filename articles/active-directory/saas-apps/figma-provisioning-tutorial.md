---
title: 'Samouczek: Konfigurowanie Figma automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057961"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Figma na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Figma i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Figma.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Figma](https://www.figma.com/pricing/).
* Konto użytkownika w Figma z uprawnieniami administratora.

## <a name="assign-users-to-figma"></a>Przypisz użytkowników do Figma.
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Figma. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Figma, postępując zgodnie z poniższymi instrukcjami:
 
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Figma

 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Figma w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Figma należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-figma-for-provisioning"></a>Konfigurowanie Figma na potrzeby aprowizacji

Przed skonfigurowaniem usługi Figma do automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z Figma.

1. Zaloguj się do [konsoli administracyjnej Figma](https://www.Figma.com/). Kliknij ikonę koła zębatego obok dzierżawy.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Przejdź do **ogólne > Aktualizuj logowanie w ustawieniach**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Skopiuj **Identyfikator dzierżawy**. Ta wartość zostanie użyta do skonstruowania adresu URL punktu końcowego Standard scim, który zostanie wprowadzony do pola **adresu URL dzierżawy** na karcie aprowizacji aplikacji Figma w Azure Portal.

    ![Utwórz token Figma](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Przewiń w dół i kliknij pozycję **Generuj token interfejsu API**.

    ![Utwórz token Figma](media/Figma-provisioning-tutorial/token.png)

5. Skopiuj wartość **tokenu interfejsu API** . Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji Figma w Azure Portal. 

    ![Utwórz token Figma](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Dodaj Figma z galerii

Aby skonfigurować Figma automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać Figma z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Figma**, wybierz pozycję **Figma** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Figma na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Figma 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Figma na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Figma, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](figma-tutorial.md)jednokrotne w Figma. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Figma w usłudze Azure AD:

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Figma**.

    ![Link Figma na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://www.figma.com/scim/v2/<TenantID>` w **adresie URL dzierżawy** , gdzie **TenantID** jest wartością pobieraną wcześniej z Figma. Wprowadź wartość **tokenu interfejsu API** w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Figma. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Figma ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Save** (Zapisz).

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Figma**.

    ![Figma mapowania użytkowników](media/Figma-provisioning-tutorial/figma05.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Figma w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Figma for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Figma atrybuty użytkownika](media/Figma-provisioning-tutorial/figma06.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Figma, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Figma, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Figma.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)