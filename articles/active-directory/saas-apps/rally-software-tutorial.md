---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Rally oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093177"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally

W tym samouczku dowiesz się, jak zintegrować Rally oprogramowania z usługi Azure Active Directory (Azure AD).
Integracja oprogramowania Rally z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania Rally.
* Użytkownikom można automatycznie zalogowany do Rally oprogramowania (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania Rally, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Rally oprogramowania pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rally oprogramowania obsługuje **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-rally-software-from-the-gallery"></a>Dodawanie Rally oprogramowania za pomocą galerii

Aby skonfigurować integrację Rally oprogramowania w usłudze Azure AD, należy dodać Rally oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Rally oprogramowania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Rally oprogramowania**, wybierz opcję **Rally oprogramowania** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Rally oprogramowania na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania Rally, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu Rally musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu Rally oprogramowania, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Rally oprogramowania logowania jednokrotnego](#configure-rally-software-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Rally oprogramowania](#create-rally-software-test-user)**  — aby odpowiednikiem Britta Simon Rally oprogramowania, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania Rally, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Rally oprogramowania** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Rally oprogramowania domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Rally oprogramowanie klienckie](https://help.rallydev.com/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie oprogramowania Rally** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-rally-software-single-sign-on"></a>Konfigurowanie wyścigów oprogramowania logowania jednokrotnego

1. Zaloguj się do Twojej **Rally oprogramowania** dzierżawy.

2. Na pasku narzędzi u góry kliknij **instalacji**, a następnie wybierz pozycję **subskrypcji**.
   
    ![Subskrypcja](./media/rally-software-tutorial/ic769531.png "subskrypcji")

3. Kliknij przycisk **akcji** przycisku. Wybierz **edytowania subskrypcji** w prawej górnej części paska narzędzi.

4. Na **subskrypcji** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **Zapisz i Zamknij**:
   
    ![Uwierzytelnianie](./media/rally-software-tutorial/ic769542.png "Uwierzytelnianie")
   
    a. Wybierz **wyścigów lub Usługa rejestracji Jednokrotnej uwierzytelnianie** z listy rozwijanej uwierzytelniania.

    b. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal. 

    c. W **SSO, wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania Rally za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Rally oprogramowania**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Rally oprogramowania**.

    ![Łącze Rally oprogramowania, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-rally-software-test-user"></a>Tworzenie użytkownika testowego Rally oprogramowania

Dla użytkowników usługi Azure AD można było się zalogować musi być obsługiwana na Rally aplikacji przy użyciu nazwy użytkowników usługi Azure Active Directory.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy Rally oprogramowania.

2. Przejdź do **instalacji \> użytkowników**, a następnie kliknij przycisk **+ Dodaj nowy**.
   
    ![Użytkownicy](./media/rally-software-tutorial/ic781039.png "Użytkownicy")

3. Wpisz nazwę w polu tekstowym nowego użytkownika, a następnie kliknij przycisk **Dodaj ze szczegółowymi informacjami**.

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:
   
    ![Utwórz użytkownika](./media/rally-software-tutorial/ic781040.png "Utwórz użytkownika")

    a. W **nazwa_użytkownika** polu tekstowym wpisz nazwę użytkownika, takie jak **Brittsimon**.
   
    b. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    c. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    d. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    e. Kliknij przycisk **Zapisz i Zamknij**.

   >[!NOTE]
   >Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika Rally oprogramowania lub interfejsów API dostarczonych przez oprogramowanie Rally.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rally oprogramowania w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Rally oprogramowania, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

