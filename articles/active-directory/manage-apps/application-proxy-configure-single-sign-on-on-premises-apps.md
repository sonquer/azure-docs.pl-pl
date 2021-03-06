---
title: Logowanie jednokrotne w usłudze SAML dla aplikacji lokalnych z serwerem proxy aplikacja usługi Azure AD
description: Dowiedz się, jak zapewnić Logowanie jednokrotne dla aplikacji lokalnych, które są zabezpieczone przy użyciu uwierzytelniania SAML. Zapewnianie dostępu zdalnego do aplikacji lokalnych przy użyciu serwera proxy aplikacji.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1046c11e064e69ed0ddb18c77bf5935ba60fb5aa
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461287"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Logowanie jednokrotne SAML dla aplikacji lokalnych przy użyciu serwera proxy aplikacji

Możesz udostępnić Logowanie jednokrotne do aplikacji lokalnych, które są zabezpieczone przy użyciu uwierzytelniania SAML i zapewniają dostęp zdalny do tych aplikacji za pomocą serwera proxy aplikacji. Za pomocą logowania jednokrotnego w protokole SAML usługa Azure Active Directory (Azure AD) uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje się informacji logowania jednokrotnego do aplikacji za pośrednictwem protokołu połączenia. Możesz również mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML. Po włączeniu serwera proxy aplikacji oprócz rejestracji jednokrotnej protokołu SAML użytkownicy będą mieli zewnętrzny dostęp do aplikacji i bezproblemowe środowisko logowania jednokrotnego.

Aplikacje muszą być w stanie korzystać z tokenów SAML wystawionych przez **Azure Active Directory**. Ta konfiguracja nie ma zastosowania do aplikacji korzystających z lokalnego dostawcy tożsamości. W tych scenariuszach zalecamy przeglądanie [zasobów na potrzeby migrowania aplikacji do usługi Azure AD](migration-resources.md).

Logowanie jednokrotne SAML z serwerem proxy aplikacji działa również z funkcją szyfrowania tokenu języka SAML. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](howto-saml-token-encryption.md).

Poniższe diagramy protokołów opisują sekwencję logowania jednokrotnego dla przepływu zainicjowanego przez dostawcę usług (zainicjowany z dodatkiem SP) i przepływem zainicjowanego przez dostawcę tożsamości (dostawcy tożsamości). Serwer proxy aplikacji współpracuje z logowaniem jednokrotnym SAML przez buforowanie żądania SAML i odpowiedzi do i z aplikacji lokalnej.

  ![Przepływ SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Przepływ SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Tworzenie aplikacji i Konfigurowanie protokołu SAML SSO

1. W Azure Portal wybierz pozycję **Azure Active Directory > aplikacje dla przedsiębiorstw** i wybierz pozycję **Nowa aplikacja**.

2. Wprowadź nazwę wyświetlaną nowej aplikacji, wybierz opcję **Zintegruj każdą inną aplikację, której nie ma w galerii**, a następnie wybierz pozycję **Utwórz**.

3. Na stronie **Przegląd** aplikacji wybierz pozycję **Logowanie jednokrotne**.

4. Wybierz pozycję **SAML** jako metodę logowania jednokrotnego.

5. Najpierw skonfiguruj Logowanie jednokrotne SAML do pracy w sieci firmowej. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** przejdź do nagłówka **Podstawowa konfiguracja SAML** i wybierz jego ikonę **edycji** (ołówek). Postępuj zgodnie z instrukcjami w [wprowadź podstawową konfigurację SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) , aby skonfigurować uwierzytelnianie oparte na protokole SAML dla aplikacji.

6. Dodaj co najmniej jednego użytkownika do aplikacji i upewnij się, że konto testowe ma dostęp do aplikacji. Po nawiązaniu połączenia z siecią firmową Użyj konta testowego, aby sprawdzić, czy użytkownik ma Logowanie jednokrotne do aplikacji. 

   > [!NOTE]
   > Po skonfigurowaniu serwera proxy aplikacji powrócisz i zaktualizujesz **adres URL odpowiedzi**SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikowanie aplikacji lokalnej przy użyciu serwera proxy aplikacji

Aby można było zapewnić Logowanie jednokrotne dla aplikacji lokalnych, należy włączyć serwer proxy aplikacji i zainstalować łącznik. Zapoznaj się z samouczkiem [Dodawanie lokalnej aplikacji do dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure AD](application-proxy-add-on-premises-application.md) , aby dowiedzieć się, jak przygotować środowisko lokalne, zainstalować i zarejestrować łącznik oraz przetestować łącznik. Następnie wykonaj następujące kroki, aby opublikować nową aplikację przy użyciu serwera proxy aplikacji. Inne ustawienia, które nie zostały wymienione poniżej, można znaleźć w sekcji [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) w samouczku.

1. Gdy aplikacja jest nadal otwarta w Azure Portal, wybierz pozycję **serwer proxy aplikacji**. Podaj **wewnętrzny adres URL** aplikacji. Jeśli używasz domeny niestandardowej, musisz również przekazać certyfikat SSL dla swojej aplikacji. 
   > [!NOTE]
   > Najlepszym rozwiązaniem jest użycie domen niestandardowych wszędzie tam, gdzie jest to możliwe dla zoptymalizowanego środowiska użytkownika. Dowiedz się więcej [na temat pracy z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

2. Wybierz **Azure Active Directory** jako metodę **wstępnego uwierzytelniania** dla aplikacji.

3. Skopiuj **zewnętrzny adres URL** aplikacji. Ten adres URL będzie potrzebny do ukończenia konfiguracji protokołu SAML.

4. Korzystając z konta testowego, spróbuj otworzyć aplikację z **zewnętrznym adresem URL** , aby sprawdzić, czy serwer proxy aplikacji został prawidłowo skonfigurowany. Jeśli występują problemy, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aktualizowanie konfiguracji protokołu SAML

1. Gdy aplikacja jest nadal otwarta w Azure Portal, wybierz pozycję **Logowanie jednokrotne**. 

2. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** przejdź do nagłówka **Podstawowa konfiguracja SAML** i wybierz jego ikonę **edycji** (ołówek). Upewnij się, że **zewnętrzny adres URL** skonfigurowany w serwerze proxy aplikacji jest wypełniony w polach **Identyfikator**, **adres URL odpowiedzi**i **wylogowywanie adresu URL** . Te adresy URL są wymagane do poprawnego działania serwera proxy aplikacji. 

3. Edytuj **adres URL odpowiedzi** skonfigurowany wcześniej, tak aby jego domena była osiągalna przez serwer proxy aplikacji. Na przykład jeśli **zewnętrzny adres URL** jest `https://contosotravel-f128.msappproxy.net` i oryginalny **adres url odpowiedzi** był `https://contosotravel.com/acs`, musisz zaktualizować oryginalny **adres URL odpowiedzi** , aby `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Wprowadź podstawowe dane konfiguracyjne SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Zaznacz pole wyboru obok zaktualizowanego **adresu URL odpowiedzi** , aby oznaczyć go jako domyślny.

   * Jeśli wymagany **adres URL odpowiedzi** jest już wymieniony, Oznacz ten **adres URL odpowiedzi** jako domyślny i Usuń wcześniej skonfigurowany **adres URL odpowiedzi**.

   * W przypadku przepływu zainicjowanego przez program SP upewnij się, że aplikacja zaplecza określa prawidłowy **adres URL odpowiedzi** lub adres URL usługi konsumenckej potwierdzenia do otrzymania tokenu uwierzytelniania.

    > [!NOTE]
    > Jeśli w aplikacji zaplecza **adres URL odpowiedzi** będzie wewnętrznym adresem URL, należy użyć [domen niestandardowych](application-proxy-configure-custom-domain.md) w celu uzyskania zgodności z wewnętrznymi i zewnętrznymi adresami URL lub zainstalować rozszerzenie moje aplikacje bezpieczne logowanie na urządzeniach użytkowników. To rozszerzenie zostanie automatycznie przekierowanie do odpowiedniej usługi serwera proxy aplikacji. Aby zainstalować rozszerzenie, zobacz [Moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testowanie aplikacji

Po zakończeniu wszystkie te kroki aplikacji powinna być uruchomiona. Aby przetestować aplikację:

1. Otwórz przeglądarkę i przejdź do **zewnętrznego adresu URL** , który został utworzony podczas publikowania aplikacji. 
1. Zaloguj się przy użyciu konta testowego, który jest przypisany do aplikacji. Powinno być możliwe załadowanie aplikacji i logowanie jednokrotne do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Jak usługa Azure serwer proxy aplikacji usługi Azure AD zapewnia Logowanie jednokrotne?](application-proxy-single-sign-on.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)
