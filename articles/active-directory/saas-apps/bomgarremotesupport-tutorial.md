---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z obsługą zdalną BeyondTrust | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zdalną pomocą techniczną BeyondTrust.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą BeyondTrust Remote support

W tym samouczku dowiesz się, jak zintegrować zdalną obsługę BeyondTrust z usługą Azure Active Directory (Azure AD). W przypadku integrowania zdalnej obsługi BeyondTrust z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zdalnej pomocy technicznej BeyondTrust.
* Zezwól użytkownikom na automatyczne logowanie do BeyondTrust zdalnej pomocy technicznej przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* BeyondTrust zdalna obsługa logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zdalna pomoc techniczna w BeyondTrust obsługuje logowanie jednokrotne w usłudze **SP**
* Obsługa zdalna BeyondTrust obsługuje funkcję aprowizacji użytkowników **just in Time**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Dodawanie zdalnej obsługi BeyondTrust z galerii

Aby skonfigurować integrację zdalnej obsługi BeyondTrust z usługą Azure AD, musisz dodać zdalną obsługę BeyondTrust z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BeyondTrust Remote support** w polu wyszukiwania.
1. Wybierz pozycję **BeyondTrust Remote support** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby zdalnej obsługi BeyondTrust

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą zdalnej obsługi BeyondTrust przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze BeyondTrust Remote support.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zdalnej obsługi BeyondTrust, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj obsługę zdalną logowania JEDNOkrotnego](#configure-beyondtrust-remote-support-sso)** w usłudze BeyondTrust, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego obsługi zdalnej BeyondTrust](#create-beyondtrust-remote-support-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w usłudze BeyondTrust Remote support, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **BeyondTrust Remote support** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<HOSTNAME>.bomgar.com/saml`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<HOSTNAME>.bomgar.com`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Te wartości zostaną omówione w dalszej części tego samouczka.

1. Aplikacja do obsługi zdalnej BeyondTrust oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych BeyondTrust aplikacja do obsługi zdalnej oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------| ----------|
    | GivenName | user.givenname |
    | EmailAddress | user.mail |
    | Nazwa | user.userprincipalname |
    | Nazwa użytkownika | user.userprincipalname |
    | Grupy | User. Groups |
    | Unikatowy identyfikator użytkownika | user.userprincipalname |

    > [!NOTE]
    > W przypadku przypisywania grup usługi Azure AD dla aplikacji BeyondTrust Remote support należy zmodyfikować opcję "grupy zwrócone w" z brakiem na grupę zabezpieczeń. Grupy zostaną zaimportowane do aplikacji jako ich identyfikatory obiektów. Identyfikator obiektu grupy usługi Azure AD można znaleźć, sprawdzając właściwości w interfejsie Azure Active Directory. Będzie to wymagane do odwoływania się do grup usługi Azure AD i przypisywania ich do odpowiednich zasad grupy.

1. Podczas ustawiania unikatowego identyfikatora użytkownika ta wartość musi być ustawiona na NameID-format: **persistent**. Musimy to być trwały identyfikator, aby prawidłowo identyfikować i kojarzyć użytkownika z poprawnymi zasadami grupy w celu uzyskania uprawnień. Kliknij ikonę Edytuj, aby otworzyć okno dialogowe **atrybuty użytkownika & oświadczenia** , aby edytować unikatową wartość identyfikatora użytkownika.

1. W sekcji **Zarządzanie** zgłoszeniem kliknij **Format identyfikatora nazwy** i ustaw wartość na **trwały** , a następnie kliknij przycisk **Zapisz**.

    ![Atrybuty użytkownika i oświadczenia](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie zdalnej obsługi BeyondTrust** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do zdalnej pomocy technicznej usługi BeyondTrust.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BeyondTrust Remote Support (obsługa zdalna**).
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurowanie zdalnej obsługi logowania jednokrotnego BeyondTrust

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby uzyskać pomoc zdalną BeyondTrust jako administrator.

1. Kliknij menu **stan** i skopiuj **Identyfikator**, **adres URL odpowiedzi** i **adres URL logowania** , a następnie użyj tych wartości w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    ![Konfigurowanie zdalnej obsługi BeyondTrust](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Przejdź do interfejsu BeyondTrust zdalnej pomocy technicznej w `https://support.example.com/login` gdzie **support.example.com** jest podstawową nazwą hosta urządzenia i Uwierzytelnij się przy użyciu poświadczeń administracyjnych.

1. Przejdź do obszarze **użytkownicy & zabezpieczenia** > **dostawcy zabezpieczeń**.

1. Z menu rozwijanego wybierz pozycję **SAML** i kliknij przycisk **Utwórz dostawcę** .

1. W sekcji Ustawienia dostawcy tożsamości istnieje możliwość przekazania metadanych dostawcy tożsamości. Znajdź plik XML metadanych pobrany z Azure Portal i kliknij przycisk **Przekaż** . **Identyfikator jednostki**, **adres URL usługi logowania** jednokrotnego i certyfikat zostaną automatycznie przekazane, a **powiązanie protokołu** należy zmienić na **post protokołu HTTP**. Zobacz zrzut ekranu poniżej:

    ![Konfigurowanie zdalnej obsługi BeyondTrust](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Utwórz użytkownika testowego wsparcia zdalnego BeyondTrust

Skonfigurujemy tutaj ustawienia udostępniania użytkownika. Wartości użytych w tej sekcji są przywoływane w sekcji **atrybuty użytkownika & oświadczenia** w Azure Portal. Ta wartość została skonfigurowana tak, aby była wartością domyślną, która została już zaimportowana w momencie tworzenia, ale w razie potrzeby można dostosować ją.

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Te grupy i atrybuty poczty e-mail nie są niezbędne dla tej implementacji. W przypadku używania grup usługi Azure AD i przypisywania ich do zasad grupy obsługi zdalnej BeyondTrust dla uprawnień, identyfikator obiektu grupy musi być przywoływany za pośrednictwem jego właściwości w Azure Portal i umieszczony w sekcji "dostępne grupy". Po zakończeniu tej operacji identyfikator obiektu/Grupa usługi AD będzie teraz dostępna do przypisywania do zasad grupy w celu uzyskania uprawnień.

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user2.png)

![Tworzenie użytkownika](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Alternatywnie można ustawić domyślne zasady grupy dla dostawcy zabezpieczeń SAML2. Zdefiniowanie tej opcji spowoduje przypisanie wszystkich użytkowników, którzy uwierzytelniają się za pomocą protokołu SAML w ramach zasad grupy. Ogólne zasady składowe są zawarte w BeyondTrust zdalnej pomocy technicznej/uprzywilejowanego dostępu zdalnego z ograniczonymi uprawnieniami, które mogą służyć do testowania uwierzytelniania i przypisywania użytkowników do odpowiednich zasad. Użytkownicy nie będą zapełniać listy użytkowników SAML2 za pomocą parametru/login > użytkowników & zabezpieczenia do momentu pierwszego pomyślnego uwierzytelnienia. Dodatkowe informacje na temat zasad grupy można znaleźć w następującym łączu: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka obsługa zdalna BeyondTrust w panelu dostępu należy automatycznie zalogować się do zdalnej pomocy technicznej usługi BeyondTrust, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zdalną obsługę usługi BeyondTrust w usłudze Azure AD](https://aad.portal.azure.com/)
