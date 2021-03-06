---
title: 'Samouczek: integracja Azure Active Directory z usługą Tableau online | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Tableau online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Tableau online

W tym samouczku dowiesz się, jak zintegrować usługę Tableau online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Tableau w trybie online z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Tableau online.
* Zezwól użytkownikom na automatyczne logowanie do Tableau online przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Tableau online.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Tableau online obsługuje logowanie jednokrotne w usłudze **SP**
* Po skonfigurowaniu usługi Tableau online można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau online z galerii

Aby skonfigurować integrację usługi Tableau w usłudze Azure AD, musisz dodać Tableau online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Tableau online** w polu wyszukiwania.
1. Wybierz pozycję **Tableau online** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze Tableau online na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Tableau online.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze Tableau online, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze Tableau online](#configure-tableau-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Tableau online](#create-tableau-online-test-user)** , aby uzyskać odpowiednik B. Simon w usłudze Tableau online, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD w usłudze Tableau online, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Tableau online** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Tableau domeny online i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Otrzymasz wartość `<entityid>` z sekcji **Konfiguracja Tableau online** w tym samouczku. Wartość identyfikatora jednostki będzie wartością **identyfikatora usługi Azure AD** w sekcji **Konfiguracja Tableau online** .

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi Tableau online** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład BrittaSimon\@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Tableau online.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Tableau online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Tableau online**.

    ![Link Tableau online na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-tableau-online-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Tableau online

1. W innym oknie przeglądarki Zaloguj się do aplikacji online Tableau. Przejdź do pozycji **Ustawienia** , a następnie pozycję **uwierzytelnianie**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Aby włączyć protokół SAML, w sekcji **typy uwierzytelniania** . Zaznacz **opcję Włącz dodatkową metodę uwierzytelniania** , a następnie zaznacz pole wyboru **SAML** .

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Przewiń w dół, aby **zaimportować plik metadanych do sekcji Tableau online** .  Kliknij przycisk Przeglądaj i zaimportuj plik metadanych, który został pobrany z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. W sekcji **potwierdzenia dopasowania** Wstaw odpowiednią nazwę potwierdzenia dostawcy tożsamości dla **adresu e-mail**, **imienia**i **nazwiska**. Aby uzyskać te informacje z usługi Azure AD: 
  
    a. W Azure Portal przejdź na stronę integracji aplikacji **online Tableau** .

    b. W sekcji **atrybuty użytkownika & oświadczenia** kliknij ikonę edycji.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection.png)

    d. Skopiuj wartość przestrzeni nazw dla tych atrybutów: imięname, email i nazwisko, wykonując następujące czynności:

   ![Logowanie jednokrotne w usłudze Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kliknij pozycję **użytkownik. dana** wartośćname

    e. Skopiuj wartość z pola tekstowego **przestrzeń nazw** .

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować wartości przestrzeni nazw dla wiadomości e-mail i nazwiska, powtórz powyższe kroki.

    g. Przejdź do aplikacji Tableau online, a następnie ustaw **& atrybuty użytkownika** w następujący sposób:

    * Wiadomość e-mail: **poczta** lub **userPrincipalName**

    * Imię: **podaną** nazwę

    * Nazwisko: **nazwisko**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Utwórz użytkownika testowego usługi Tableau online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Tableau online.

1. W witrynie **Tableau online**, kliknij pozycję **Ustawienia** , a następnie sekcję **uwierzytelnianie** . Przewiń w dół do sekcji **Zarządzanie użytkownikami** . Kliknij pozycję **Dodaj użytkowników** , a następnie kliknij pozycję **wprowadź adresy e-mail**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wybierz pozycję **Dodaj użytkowników dla (SAML) uwierzytelnianie**. W polu tekstowym **wprowadź adresy e-mail** Dodaj Britta. simon\@contoso.com
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kliknij pozycję **Dodaj użytkowników**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau online w panelu dostępu należy automatycznie zalogować się do Tableau online, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)