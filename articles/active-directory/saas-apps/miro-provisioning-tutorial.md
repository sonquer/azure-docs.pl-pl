---
title: 'Samouczek: Konfigurowanie Miro dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Miro.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81eecaff-d888-472b-a1c2-0b7b0c9ccd8d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: b7e1907e3fa1eb9d775fb7662445b08d5671e0b6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063417"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Miro na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Miro i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Miro.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Miro](https://miro.com/pricing/)
* Konto użytkownika w Miro z uprawnieniami administratora.

## <a name="assigning-users-to-miro"></a>Przypisywanie użytkowników do Miro

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Miro. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Miro, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Miro

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Miro w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Miro należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-miro-for-provisioning"></a>Konfigurowanie Miro na potrzeby aprowizacji

1.  Aby pobrać wymagany **token tajny** Contact Miro support team w support@miro.com. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji Miro w Azure Portal.

## <a name="add-miro-from-the-gallery"></a>Dodaj Miro z galerii

Przed skonfigurowaniem usługi Miro do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Miro z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Miro z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Miro**, wybierz pozycję **Miro** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Miro na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Miro 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Miro na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Miro, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-tutorial)jednokrotne w Miro. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym Standard scim Miro, zapoznaj się z [tym](https://help.miro.com/hc/en-us/articles/360036777814)tematem.

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Miro w usłudze Azure AD

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Miro**.

    ![Link Miro na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://miro.com/api/v1/scim` w **adresie URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Miro. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Miro ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Save** (Zapisz).

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Miro**.

    ![Miro mapowania użytkowników](media/miro-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Miro w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Miro for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Miro atrybuty użytkownika](media/miro-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Miro**.

    ![Mapowania grup Miro](media/miro-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do Miro w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w Miro dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany. Usuń zaznaczenie opcji **Utwórz** i **Usuń** w obszarze **Akcje obiektu docelowego** , ponieważ Miro Standard scim API nie obsługuje tworzenia i usuwania grup.

    ![Atrybuty grupy Miro](media/miro-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla Miro, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Miro, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Miro.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Miro Standard scim Endpoint nie zezwala na operacje **tworzenia** i **usuwania** dla grup. Obsługuje tylko operację **aktualizacji** grupy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

