---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Igloo oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100580"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo

W tym samouczku dowiesz się, jak zintegrować Igloo oprogramowania z usługi Azure Active Directory (Azure AD).
Integracja oprogramowania Igloo z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania Igloo.
* Użytkownikom można automatycznie zalogowany do oprogramowania Igloo (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania Igloo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Igloo oprogramowania logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie igloo obsługuje **SP** jednokrotne logowanie inicjowane przez
* Oprogramowanie igloo obsługuje **Just In Time** aprowizacji użytkowników

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii

Aby skonfigurować integrację Igloo oprogramowania w usłudze Azure AD, należy dodać Igloo oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Igloo oprogramowania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Igloo oprogramowania**, wybierz opcję **Igloo oprogramowania** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Oprogramowanie igloo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu Igloo musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Igloo oprogramowania logowania jednokrotnego](#configure-igloo-software-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego oprogramowania Igloo](#create-igloo-software-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniu Igloo, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **oprogramowania Igloo** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Igloo oprogramowania domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Igloo oprogramowanie klienckie](https://www.igloosoftware.com/services/support) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie oprogramowania Igloo** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurowanie Igloo oprogramowania logowania jednokrotnego

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Igloo oprogramowania.

2. Przejdź do **Panel sterowania**.

     ![Panel sterowania](./media/igloo-software-tutorial/ic799949.png "Panel sterowania")

3. W **członkostwa** kliknij pozycję **logowania ustawień**.

    ![Ustawienia logowania](./media/igloo-software-tutorial/ic783968.png "ustawienia logowania")

4. W sekcji Konfiguracja SAML kliknij **skonfigurować uwierzytelnianie SAML**.

    ![Konfiguracja SAML](./media/igloo-software-tutorial/ic783969.png "Konfiguracja SAML")

5. W **Konfiguracja ogólna** sekcji, wykonaj następujące czynności:

    ![Konfiguracja ogólna](./media/igloo-software-tutorial/ic783970.png "Konfiguracja ogólna")

    a. W **nazwa połączenia** polu tekstowym wpisz nazwę niestandardową dla danej konfiguracji.

    b. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. Wybierz **odpowiedzi wylogowania i żądania HTTP typu** jako **WPIS**.

    e. Otwórz swoje **base-64** zakodowanego certyfikatu w programie Notatnik pobrane z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.

6. W **odpowiedzi i Konfiguracja uwierzytelniania**, wykonaj następujące czynności:

    ![Odpowiedź i konfiguracji uwierzytelniania](./media/igloo-software-tutorial/IC783971.png "odpowiedzi i konfiguracji uwierzytelniania")
  
    a. Jako **dostawcy tożsamości**, wybierz opcję **Microsoft ADFS**.

    b. Jako **typ identyfikatora**, wybierz opcję **adres E-mail**. 

    c. W **atrybut E-mail** polu tekstowym wpisz **emailaddress**.

    d. W **atrybut imię** polu tekstowym wpisz **givenname**.

    e. W **ostatniego atrybutu nazwy** polu tekstowym wpisz **nazwisko**.

7. Wykonaj poniższe kroki, aby ukończyć konfigurację:

    ![Tworzenie użytkownika na temat logowania się](./media/igloo-software-tutorial/IC783972.png "Tworzenie użytkownika na temat logowania się") 

    a. Jako **Tworzenie użytkownika na temat logowania się**, wybierz opcję **Utwórz nowego użytkownika w witrynie, gdy logują się oni**.

    b. Jako **ustawienia logowania**, wybierz opcję **SAML użyj przycisku na ekranie "Sign in"** .

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania Igloo za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **oprogramowania Igloo**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **oprogramowania Igloo**.

    ![Łącze oprogramowania Igloo na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-igloo-software-test-user"></a>Tworzenie użytkownika testowego Igloo oprogramowania

Brak elementu działania umożliwiające konfigurowanie aprowizacji użytkownika Igloo oprogramowania.  

Gdy przypisany użytkownik próbuje zalogować się do oprogramowania Igloo za pomocą panelu dostępu, oprogramowanie Igloo sprawdza, czy użytkownik istnieje.  Jeśli nie ma użytkownika konta dostępne jeszcze, są tworzone przez oprogramowanie Igloo.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Igloo oprogramowania w panelu dostępu, możesz powinny być automatycznie zalogowany do oprogramowania Igloo, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)