---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Elium | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Elium

W tym samouczku dowiesz się, jak zintegrować usługę Elium z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Elium z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Elium.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Elium przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Elium.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Elium obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Aplikacja Elium obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-elium-from-the-gallery"></a>Dodawanie aplikacji Elium z galerii

Aby skonfigurować integrację aplikacji Elium z usługą Azure AD, musisz dodać aplikację Elium z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Elium** w polu wyszukiwania.
1. Wybierz pozycję **Elium** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Elium

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Elium przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Elium.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Elium, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-elium-sso)** w usłudze Elium, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Elium](#create-elium-test-user)** , aby dysponować odpowiednikiem B. Simon w Elium, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Elium** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Uzyskasz te wartości z **pliku metadanych dostawcy tożsamości**, dostępnego do pobrania na stronie `https://<platform-domain>.elium.com/login/saml2/metadata`, co objaśniono w dalszej części tego samouczka.

1. Aplikacja Elium oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Elium oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > To są oświadczenia domyślne. **Tylko oświadczenie e-mail jest wymagane**. W przypadku aprowizowania JIT również tylko oświadczenie e-mail jest obowiązkowe. Inne oświadczenia niestandardowe mogą się różnić w zależności od platformy klienta.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Elium** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Elium.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Elium**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-elium-sso"></a>Konfigurowanie logowania jednokrotnego Elium

1. Aby zautomatyzować konfigurację w programie Elium, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji Elium. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Elium. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować Elium, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Elium jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **User profile** (Profil użytkownika) w prawym górnym rogu, a następnie wybierz pozycję **Administration** (Administracja).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user1.png)

1. Wybierz kartę **Security** (Zabezpieczenia).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user2.png)

1. Przewiń w dół do sekcji **Single sign-on (SSO)** (Logowanie jednokrotne) i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user3.png)

    a. Skopiuj wartość pola **Verify that SAML2 authentication works for your account** (Sprawdź, czy uwierzytelnianie SAML2 działa dla Twojego konta) i wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Po skonfigurowaniu logowania jednokrotnego zawsze możesz uzyskać dostęp do domyślnej strony logowania zdalnego pod następującym adresem URL: `https://<platform_domain>/login/regular/login` 

    b. Zaznacz pole wyboru **Enable SAML2 federation** (Włącz federację SAML2).

    d. Zaznacz pole wyboru **JIT Provisioning** (Aprowizowanie JIT).

    d. Otwórz **metadane dostawcy usług**, klikając przycisk **Download** (Pobierz).

    e. Wyszukaj element **entityID** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **entityID**, a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. 

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user4.png)

    f. Wyszukaj element **AssertionConsumerService** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **Location**, a następnie wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user5.png)

    g. Otwórz w Notatniku plik metadanych pobrany z witryny Azure Portal, skopiuj jego zawartość i wklej ją w polu tekstowym **IdP Metadata** (Metadane dostawcy tożsamości).

    h. Kliknij przycisk **Save** (Zapisz).

### <a name="create-elium-test-user"></a>Tworzenie użytkownika testowego aplikacji Elium

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Elium. Aplikacja Elium obsługuje **aprowizowanie typu just-in-time**, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Elium, zostanie utworzony podczas próby uzyskania dostępu do aplikacji Elium.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej aplikacji Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Elium w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Elium, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Elium z usługą Azure AD](https://aad.portal.azure.com/)