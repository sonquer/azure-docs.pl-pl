---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Netskope konsola administratora | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i konsola administratora Netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dec8f8065114b89dfa7bcaceee3f26855953dde2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z usługą Netskope konsola administratora

W tym samouczku dowiesz się, jak zintegrować konsola administratora Netskope z usługą Azure Active Directory (Azure AD). W przypadku integrowania konsola administratora Netskope z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Netskope konsola administratora.
* Zezwól użytkownikom na automatyczne logowanie do Netskope konsola administratora z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Netskope konsola administratora subskrypcję z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Netskope konsola administratora obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Dodawanie konsola administratora Netskope z galerii

Aby skonfigurować integrację Netskope konsola administratora z usługą Azure AD, musisz dodać Netskope konsola administratora z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netskope Konsola administratora** w polu wyszukiwania.
1. Wybierz pozycję **Netskope Konsola administratora** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Netskope konsola administratora

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Netskope konsola administratora przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Netskope konsola administratora.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Netskope konsola administratora, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Netskope Konsola administratora Logowanie jednokrotne](#configure-netskope-administrator-console-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz Netskope Konsola administratora użytkownika testowego](#create-netskope-administrator-console-test-user)** , aby uzyskać odpowiednik B. Simon w Netskope Konsola administratora połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Netskope Konsola administratora** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `Netskope_<OrgKey>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Te wartości zostaną omówione w dalszej części tego samouczka.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej Netskope Konsola administratora klienta](mailto:support@netskope.com) , aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Netskope konsola administratora oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Netskope konsola administratora oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------| --------- |
    | rola administratora | user.assignedroles |

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) , aby dowiedzieć się, jak utworzyć role w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Konsola administratora Netskope** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Netskope konsola administratora.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Netskope Konsola administratora**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurowanie Netskope konsola administratora Logowanie jednokrotne

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny Netskope konsola administratora firmy jako administrator.

1. Kliknij kartę **Ustawienia** w okienku nawigacji po lewej stronie.

    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij pozycję Karta **Administracja** .

    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Kliknij kartę **Logowanie jednokrotne** .

    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/config-sso.png)

1. W sekcji **Ustawienia sieciowe** wykonaj następujące czynności:
    
    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Skopiuj wartość **adresu URL usługi konsumenckej potwierdzenia** i wklej ją do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Skopiuj wartość **Identyfikator jednostki dostawcy usługi** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

1. Kliknij pozycję **Edytuj ustawienia** w sekcji **Ustawienia rejestracji jednokrotnej/SLO** .

    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. W oknie podręcznym **Ustawienia** wykonaj następujące kroki:

    ![Konfiguracja konsola administratora Netskope](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Wybierz pozycję **Włącz logowanie jednokrotne**.

    b. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany certyfikat zakodowany w formacie base64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu dostawcy tożsamości** .

    e. Wybierz pozycję **Włącz logowanie jednokrotne**.

    f. W polu tekstowym **adres URL dostawcy tożsamości SLO** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    g. Kliknij przycisk **Prześlij**.

### <a name="create-netskope-administrator-console-test-user"></a>Utwórz użytkownika testowego konsola administratora Netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny Netskope konsola administratora firmy jako administrator.

1. Kliknij kartę **Ustawienia** w okienku nawigacji po lewej stronie.

    ![Tworzenie Netskope konsola administratora użytkownika](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij pozycję **aktywna karta platformy** .

    ![Tworzenie Netskope konsola administratora użytkownika](./media/netskope-cloud-security-tutorial/user1.png)

1. Kliknij kartę **Użytkownicy** .

    ![Tworzenie Netskope konsola administratora użytkownika](./media/netskope-cloud-security-tutorial/add-user.png)

1. Kliknij pozycję **Dodaj użytkowników**.

    ![Tworzenie Netskope konsola administratora użytkownika](./media/netskope-cloud-security-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, który chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Tworzenie Netskope konsola administratora użytkownika](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka konsola administratora Netskope w panelu dostępu należy automatycznie zalogować się do konsola administratora Netskope, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Netskope konsola administratora z usługą Azure AD](https://aad.portal.azure.com/)