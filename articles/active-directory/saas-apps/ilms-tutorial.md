---
title: 'Samouczek: Azure Active Directory integrację z usługą iLMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944017"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Samouczek: Integruj iLMS z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę iLMS z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi iLMS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do iLMS.
* Zezwól użytkownikom na automatyczne logowanie się do usługi iLMS przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej bezpłatnej wersji próbnej [](https://azure.microsoft.com/pricing/free-trial/).
* subskrypcja z włączonym logowaniem jednokrotnym (SSO) iLMS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. iLMS obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii

Aby skonfigurować integrację programu iLMS z usługą Azure AD, musisz dodać iLMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **iLMS** w polu wyszukiwania.
1. Wybierz pozycję **iLMS** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą iLMS przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w iLMS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą iLMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-ilms-sso)** jednokrotne w usłudze iLMS, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego iLMS](#create-ilms-test-user)** , aby uzyskać odpowiednik Britta Simon w iLMS, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ILMS** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu usługi **dostawcy tożsamości** , na stronie **Podstawowa konfiguracja protokołu SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wklej wartość **identyfikatora** skopiowaną z sekcji **dostawca usług** ustawień SAML w portalu administracyjnym iLMS.

    b. W polu tekstowym **adres URL odpowiedzi** wklej wartość **punktu końcowego (adres URL)** skopiowaną z sekcji **dostawca usług** ustawień protokołu SAML w portalu administratora iLMS, który ma następujący wzorzec`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **adres URL logowania** wklej wartość **punktu końcowego (adres URL)** skopiowaną z sekcji **dostawca usług** ustawień SAML w portalu administratora iLMS jako`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Aby włączyć Inicjowanie obsługi JIT, aplikacja iLMS oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    > [!NOTE]
    > Musisz włączyć opcję **Utwórz nierozpoznane konto użytkownika** w iLMS, aby zmapować te atrybuty. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) , aby uzyskać pomysł dotyczący konfiguracji atrybutów.

1. Oprócz powyższych, aplikacja iLMS oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | --------|------------- |
    | przegrod | user.department |
    | region | User. State |
    | Dział | user.jobtitle |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie iLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ilms-sso"></a>Konfigurowanie logowania jednokrotnego iLMS

1. W innym oknie przeglądarki sieci Web Zaloguj się do **portalu administratora iLMS** jako administrator.

2. Kliknij pozycję **Logowanie jednokrotne: SAML** na karcie **Ustawienia** , aby otworzyć ustawienia SAML, i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/1.png)

3. Rozwiń sekcję **dostawca usług** i skopiuj wartość **Identyfikator** i **punkt końcowy (adres URL)** .

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/2.png) 

4. W obszarze **dostawca tożsamości** kliknij pozycję **Importuj metadane**.

5. Wybierz plik **metadanych Federacji** pobrany z Azure Portal z sekcji **certyfikat podpisywania SAML** .

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Jeśli chcesz włączyć Inicjowanie obsługi JIT, aby utworzyć konta usługi iLMS dla użytkowników nierozpoznających, wykonaj następujące czynności:

    a. Zaznacz pole wyboru **Utwórz nierozpoznane konto użytkownika**.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapuj atrybuty w usłudze Azure AD o atrybuty w iLMS. W kolumnie atrybut Określ nazwę atrybutów lub wartość domyślną.

    c. Przejdź do karty **reguły biznesowe** i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/5.png)

    d. Zaznacz **opcję Utwórz nierozpoznane regiony,** działy i działy, aby utworzyć regiony, działy i działy, które jeszcze nie istnieją w czasie rejestracji jednokrotnej.

    e. Sprawdź **aktualizowanie profilu użytkownika podczas logowania** , aby określić, czy profil użytkownika jest aktualizowany przy użyciu każdego logowania jednokrotnego.

    f. Jeśli opcja **Aktualizuj puste wartości dla pól nieobowiązkowych w profilu użytkownika** jest zaznaczona, opcjonalne pola profilu, które są puste po zalogowaniu, również spowodują, że profil iLMS użytkownika będzie zawierał puste wartości dla tych pól.

    g. Zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem o błędzie** i wprowadź adres e-mail użytkownika, na którym chcesz otrzymać wiadomość e-mail z powiadomieniem o błędzie.

7. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi iLMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **iLMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ilms-test-user"></a>Utwórz użytkownika testowego iLMS

Aplikacja obsługuje funkcję udostępniania just in Time użytkownika i po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Po kliknięciu pola wyboru **Utwórz nierozpoznane konto użytkownika** podczas ustawienia konfiguracji SAML w portalu administracyjnym iLMS zostanie wykonane działanie JIT.

Jeśli musisz ręcznie utworzyć użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny iLMS jako administrator.

2. Kliknij pozycję **zarejestruj użytkownika** na karcie **Użytkownicy** , aby otworzyć stronę **Rejestrowanie użytkownika** .

   ![Dodawanie pracownika](./media/ilms-tutorial/3.png)

3. Na stronie **Rejestrowanie użytkownika** wykonaj następujące czynności.

    ![Dodawanie pracownika](./media/ilms-tutorial/create_testuser_add.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię, np. Britta.

    b. W polu tekstowym nazwisko wpisz nazwisko, takie jak Simon.

    c. W polu tekstowym **Identyfikator wiadomości e-mail** wpisz adres e-mail użytkownika, np BrittaSimon@contoso.com.

    d. Z listy rozwijanej **region** wybierz wartość dla regionu.

    e. Z listy rozwijanej **podział** wybierz wartość dla dzielenia.

    f. Z listy rozwijanej **dział** wybierz wartość dla działu.

    g. Kliknij polecenie **Zapisz**.

    > [!NOTE]
    > Możesz wysłać wiadomość e-mail z rejestracją do użytkownika, zaznaczając pole wyboru **Wyślij wiadomość e-mail** z rejestracją.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka iLMS w panelu dostępu należy automatycznie zalogować się do iLMS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
