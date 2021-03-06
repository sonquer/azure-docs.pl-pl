---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją dmarcian | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823696"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Samouczek: Integruj dmarcian z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę dmarcian z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi dmarcian z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do dmarcian.
* Zezwól użytkownikom na automatyczne logowanie się do usługi dmarcian przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączonym logowaniem jednokrotnym (SSO) dmarcian.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* dmarcian obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-dmarcian-from-the-gallery"></a>Dodawanie aplikacji dmarcian z galerii

Aby skonfigurować integrację aplikacji dmarcian z usługą Azure AD, musisz dodać aplikację dmarcian z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dmarcian** w polu wyszukiwania.
1. Wybierz pozycję **dmarcian** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą dmarcian przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w dmarcian.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą dmarcian, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-dmarcian-sso)** jednokrotne w usłudze dmarcian, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego dmarcian](#create-dmarcian-test-user)** , aby dysponować odpowiednikiem B. Simon w dmarcian, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Dmarcian** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizujesz te wartości, używając rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, co zostało opisane w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurowanie logowania jednokrotnego dmarcian

1. Aby zautomatyzować konfigurację w programie dmarcian, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup dmarcian** , aby skierować do aplikacji dmarcian. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi dmarcian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować dmarcian, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy dmarcian jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Profile** (Profil) w prawym górnym rogu, a następnie przejdź do obszaru **Preferences** (Preferencje).

    ![Preferencje](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Przewiń w dół i kliknij sekcję **Single Sign-On** (Logowanie jednokrotne), a następnie kliknij przycisk **Configure** (Konfiguruj).

    ![Logowanie jednokrotne](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na stronie **SAML Single Sign-On** (Logowanie jednokrotne języka SAML) ustaw pozycję **Status** (Stan) na **Enabled** (Włączono) i wykonaj następujące kroki:

    ![Uwierzytelnianie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **adres URL usługi Assertion Consumer Service** dla wystąpienia i wkleić go w polu tekstowym **Adres URL odpowiedzi** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **identyfikator jednostki** dla wystąpienia i wkleić go w polu tekstowym **Identyfikator** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Identity Provider Metadata** (Metadane dostawcy tożsamości) wklej **adres URL metadanych federacyjnych aplikacji** skopiowany z witryny Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Attribute Statements** (Instrukcje atrybutów) wklej adres URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * W sekcji **Set up Login URL** (Konfigurowanie adresu URL logowania) skopiuj **adres URL logowania** dla wystąpienia i wklej go w polu tekstowym **Adres URL logowania** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

        > [!Note]
        > Możesz zmodyfikować **adres URL logowania** zgodnie z zasadami Twojej organizacji.

    * Kliknij polecenie **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi dmarcian.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **dmarcian**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-dmarcian-test-user"></a>Tworzenie użytkownika testowego aplikacji dmarcian

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji dmarcian, należy ich aprowizować w tej aplikacji. W aplikacji dmarcian aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do aplikacji dmarcian jako administrator zabezpieczeń.

2. Kliknij pozycję **Profile** (Profil) w górnym prawym rogu i przejdź do obszaru **Manage Users** (Zarządzanie użytkownikami).

    ![Użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Po prawej stronie sekcji **SSO Users** (Użytkownicy logowania jednokrotnego) kliknij pozycję **Add New User** (Dodaj nowego użytkownika).

    ![Dodawanie użytkownika](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. W wyskakującym okienku **Add New User** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![Nowy użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. W polu tekstowym **adres e-mail nowego użytkownika** wprowadź adres e-mail użytkownika, np **.\@brittasimon contoso.com**.

    b. Jeśli chcesz nadać użytkownikowi uprawnienia administratora, zaznacz pole **Make User an Admin** (Oznacz użytkownika jako administratora).

    c. Kliknij przycisk **Dodaj użytkownika**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka dmarcian w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji dmarcian, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

