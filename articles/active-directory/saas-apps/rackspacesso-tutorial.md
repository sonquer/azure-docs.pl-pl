---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Rackspace | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego Rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093195"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Rackspace

W tym samouczku dowiesz się, jak zintegrować Rackspace logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).
Integrowanie Rackspace logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Rackspace.
* Użytkownikom można automatycznie zalogowany do Rackspace SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Rackspace, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Usługa rejestracji Jednokrotnej Rackspace logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje logowanie Jednokrotne Rackspace **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-rackspace-sso-from-the-gallery"></a>Dodawanie rejestracji Jednokrotnej Rackspace z galerii

Aby skonfigurować integrację Rackspace logowania jednokrotnego w usłudze Azure AD, należy dodać Rackspace logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Rackspace logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Rackspace logowania jednokrotnego**, wybierz opcję **Rackspace logowania jednokrotnego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Usługa rejestracji Jednokrotnej Rackspace, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace w oparciu o nazwie użytkownika testowego **Britta Simon**.
Korzystając z logowania jednokrotnego przy użyciu Rackspace, użytkownicy Rackspace zostanie automatycznie utworzony podczas pierwszego logowania się do portalu Rackspace. 

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Rackspace logowania jednokrotnego logowania jednokrotnego](#configure-rackspace-sso-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Konfigurowanie atrybutów mapowania w Panelu sterowania Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)**  — Aby przypisać Rackspace role do użytkowników usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **logowania jednokrotnego Rackspace** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji przekazywania **plik metadanych usługodawcy** który można pobrać z [adresu URL](https://login.rackspace.com/federate/sp.xml) i wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](common/upload-metadata.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    ![image](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wymaganych adresów URL Uzyskaj automatycznie wypełniane automatycznie.

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://login.rackspace.com/federate/`

    ![Domena logowania jednokrotnego Rackspace i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)   

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

Ten plik zostanie przekazany do Rackspace, aby wypełnić wymagane ustawienia konfiguracji federacji tożsamości.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Rackspace logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **logowania jednokrotnego Rackspace** po stronie:

1. Zobacz dokumentację w [dostawca tożsamości do panelu sterowania](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Będzie ona przeprowadzą Cię przez kroki, aby:
    1. Tworzenie nowego dostawcy tożsamości
    1. Określ domenę poczty e-mail, który użytkownicy będą używać do identyfikowania Twojej firmy, podczas logowania.
    1. Przekaż **XML metadanych Federacji** pobrany wcześniej z Panelu sterowania platformy Azure.

Prawidłowo skonfiguruje podstawowych ustawień logowania jednokrotnego wymaganych dla platformy Azure i Rackspace do połączenia.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego Rackspace.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **logowania jednokrotnego Rackspace**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **logowania jednokrotnego Rackspace**.

    ![Link Rackspace logowania jednokrotnego na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Skonfiguruj mapowanie atrybutu w Panelu sterowania Rackspace

Używa Rackspace **zasad mapowania atrybut** przypisać Rackspace ról i grup do pojedynczego logowania jednokrotnego użytkowników. **Zasad mapowania atrybut** przekształca oświadczenia języka SAML programu Azure AD w polach konfiguracji użytkownika wymaga Rackspace. Więcej dokumentacji znajdują się w Rackspace [dokumentacji podstawy mapowanie atrybutu](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Niektóre kwestie:

* Jeśli chcesz przypisać różne poziomy dostępu Rackspace przy użyciu grup usługi Azure AD, musisz włączyć oświadczenia grupy na platformie Azure **logowania jednokrotnego Rackspace** ustawień logowania jednokrotnego. **Zasad mapowania atrybut** zostanie następnie użyte do dopasowania te grupy do żądanego Rackspace ról i grup:

    ![Grupy oświadczenia ustawienia](common/sso-groups-claim.png)

* Domyślnie usługa Azure AD wysyła grup UID programu Azure AD w oświadczenia języka SAML, a nazwa grupy. Jednak jeśli planowana jest synchronizacja usługi Active Directory lokalnych z usługą Azure AD, masz możliwość przesyłania rzeczywistej nazwy grup:

    ![Grupy oświadczenia Nazwa ustawienia](common/sso-groups-claims-names.png)

Poniższy przykład **zasad mapowania atrybut** pokazuje:
1. Ustawienie nazwy użytkownika Rackspace `user.name` oświadczenia języka SAML. Można użyć dowolnego oświadczeń, ale przeważnie, aby ustawić to do pola zawierające adres e-mail użytkownika.
1. Ustawianie ról Rackspace `admin` i `billing:admin` na koncie użytkownika, dopasowując grupy usługi Azure AD, nazwę grupy lub UID grupy. A *podstawienia* z `"{0}"` w `roles` pole jest używane i zostaną zastąpione przez wyniki `remote` reguły wyrażeń.
1. Za pomocą `"{D}"` *domyślne podstawienia* umożliwiające Rackspace pobrać dodatkowe pola SAML, wyszukując standardowych i znanych oświadczenia języka SAML w programie SAML exchange.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Upewnij się, że używasz edytora tekstu, który sprawdza poprawność składni YAML, edytując plik zasad.

Zobacz Rackspace [dokumentacji podstawy mapowanie atrybutu](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) więcej przykładów.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rackspace logowania jednokrotnego w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze logowania jednokrotnego Rackspace, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

Można również użyć **weryfikacji** znajdujący się w **logowania jednokrotnego Rackspace** pojedynczy ustawień logowania jednokrotnego:

   ![Przycisk Weryfikuj logowania jednokrotnego](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

