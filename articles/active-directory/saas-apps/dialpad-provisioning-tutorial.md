---
title: 'Samouczek: Konfigurowanie Dialpad dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Dialpad.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058370"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Dialpad na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Dialpad i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Dialpad.

> [!NOTE]
>  Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Dialpad](https://www.dialpad.com/pricing/).
* Konto użytkownika w Dialpad z uprawnieniami administratora.

## <a name="assign-users-to-dialpad"></a>Przypisywanie użytkowników do Dialpad
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Dialpad. Po ustaleniu tych użytkowników i/lub grup można przypisywać do Dialpad, postępując zgodnie z poniższymi instrukcjami:
 
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Dialpad

 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Dialpad w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Dialpad należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="setup-dialpad-for-provisioning"></a>Konfiguracja Dialpad na potrzeby aprowizacji

Przed skonfigurowaniem usługi Dialpad do automatycznego aprowizacji użytkowników w usłudze Azure AD należy pobrać pewne informacje o aprowizacji z Dialpad.

1. Zaloguj się do [konsoli administracyjnej Dialpad](https://dialpadbeta.com/login) i wybierz pozycję **Ustawienia administratora**. Upewnij się, że na liście rozwijanej wybrano **moją firmę** . Przejdź do **uwierzytelniania > klucze interfejsu API**.

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Wygeneruj nowy klucz, klikając pozycję **Dodaj klucz** i konfigurując właściwości tokenu tajnego.

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kliknij przycisk **kliknij, aby pokazać wartość** dla ostatnio utworzonego klucza interfejsu API i skopiuj pokazaną wartość. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji Dialpad w Azure Portal. 

    ![Utwórz token Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dodaj Dialpad z galerii

Aby skonfigurować Dialpad do automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz dodać Dialpad z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Dialpad z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Dialpad**, a następnie wybierz pozycję **Dialpad** w panelu wyniki.
    ![Dialpad na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżnionego poniżej w oddzielnej przeglądarce. 

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/dialpad05.png)

6. W prawym górnym rogu wybierz pozycję **Zaloguj się > Użyj Dialpad online**.

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Ponieważ Dialpad jest aplikacją OpenIDConnect, wybierz logowanie do Dialpad przy użyciu konta służbowego firmy Microsoft.

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta Dialpad.

    ![Dialpad Dodaj Standard scim](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Dialpad

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Dialpad na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Dialpad w usłudze Azure AD:

1. Zaloguj się do [Azure portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Dialpad**.

    ![Link Dialpad na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://dialpad.com/scim` w **adresie URL dzierżawy**. Wprowadź wartość, która została pobrana i zapisana wcześniej z Dialpad w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Dialpad. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Dialpad ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Save** (Zapisz).

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Dialpad**.

    ![Dialpad mapowania użytkowników](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Dialpad w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Dialpad for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Dialpad atrybuty użytkownika](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Dialpad, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Dialpad, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie Dialpad.

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Ograniczenia łącznika
* Dialpad nie obsługuje już dzisiaj nazw grup. Oznacza to, że wszelkie zmiany wartości **DisplayName** grupy w usłudze Azure AD nie zostaną zaktualizowane i odzwierciedlone w Dialpad.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
