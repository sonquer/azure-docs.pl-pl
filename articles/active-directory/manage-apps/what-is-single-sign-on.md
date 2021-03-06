---
title: Logowanie jednokrotne do aplikacji — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wybrać metodę logowania jednokrotnego podczas konfigurowania aplikacji w Azure Active Directory (Azure AD). Użyj logowania jednokrotnego, aby użytkownicy nie musieli zapamiętywać haseł dla każdej aplikacji i uprościć administrowanie zarządzaniem kontami.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24a4209869d4c47f8ac73e250699ec55d006296
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786399"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Logowanie jednokrotne do aplikacji w Azure Active Directory

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w Azure Active Directory (Azure AD). W tym artykule opisano metody logowania jednokrotnego, które ułatwiają wybranie najbardziej odpowiedniej metody rejestracji jednokrotnej podczas konfigurowania aplikacji.

- **Logowanie jednokrotne**umożliwia użytkownikom logowanie jednokrotne przy użyciu jednego konta, aby uzyskać dostęp do urządzeń przyłączonych do domeny, zasobów firmy, aplikacji jako usługi (SaaS) i aplikacji sieci Web. Po zalogowaniu użytkownik może uruchamiać aplikacje z poziomu portalu pakietu Office 365 lub panelu dostępu webapps usługi Azure AD. Administratorzy mogą scentralizować zarządzanie kontami użytkowników i automatycznie dodawać lub usuwać dostęp użytkowników do aplikacji w oparciu o członkostwo w grupach.

- **Bez logowania jednokrotnego**użytkownicy muszą pamiętać hasła specyficzne dla aplikacji i zalogować się do każdej aplikacji. Pracownicy działu IT muszą tworzyć i aktualizować konta użytkowników dla każdej aplikacji, takiej jak Office 365, Box i Salesforce. Użytkownicy muszą pamiętać swoje hasła, a także poświęcać czas na zalogowanie się do każdej aplikacji.

## <a name="choosing-a-single-sign-on-method"></a>Wybieranie metody logowania jednokrotnego

Istnieje kilka sposobów konfigurowania aplikacji do logowania jednokrotnego. Wybór metody logowania jednokrotnego zależy od tego, w jaki sposób aplikacja jest skonfigurowana do uwierzytelniania.

- Aplikacje w chmurze mogą korzystać z metod logowania jednokrotnego OpenID Connect Connect, OAuth, SAML, opartych na hasłach, połączonych lub wyłączonych. 
- Aplikacje lokalne mogą korzystać z opartych na hasłach zintegrowanego uwierzytelniania systemu Windows, opartego na nagłówkach, połączonych lub wyłączonych metod logowania jednokrotnego. Opcje lokalne działają, gdy aplikacje są skonfigurowane pod kątem serwera proxy aplikacji.

Ten schemat blokowy pomaga określić, która metoda logowania jednokrotnego jest Najlepsza dla danej sytuacji.

![Schemat blokowy decyzji dla metody rejestracji jednokrotnej](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Poniższa tabela zawiera podsumowanie metod logowania jednokrotnego i linki do dalszych szczegółów.

| Metoda logowania jednokrotnego | Typy aplikacji | Kiedy stosować |
| :------ | :------- | :----- |
| [OpenID Connect Connect and OAuth](#openid-connect-and-oauth) | tylko w chmurze | Użyj OpenID Connect Connect i OAuth podczas tworzenia nowej aplikacji. Ten protokół upraszcza konfigurację aplikacji, ma łatwe w użyciu zestawy SDK i umożliwia aplikacji korzystanie z programu MS Graph.
| [SAML](#saml-sso) | Chmura i lokalna | Wybierz pozycję SAML wszędzie tam, gdzie to możliwe dla istniejących aplikacji, które nie używają OpenID Connect Connect lub OAuth. Protokół SAML działa w przypadku aplikacji, które uwierzytelniają się przy użyciu jednego z protokołów SAML.|
| [Oparte na hasłach](#password-based-sso) | Chmura i lokalna | Wybierz opcję hasła, gdy aplikacja jest uwierzytelniana przy użyciu nazwy użytkownika i hasła. Logowanie jednokrotne oparte na hasłach umożliwia bezpieczne przechowywanie i odtwarzanie haseł aplikacji przy użyciu rozszerzenia przeglądarki sieci Web lub aplikacji mobilnej. Ta metoda używa istniejącego procesu logowania dostarczonego przez aplikację, ale umożliwia administratorowi zarządzanie hasłami. |
| [Połączone](#linked-sign-on) | Chmura i lokalna | Wybierz pozycję dołączone logowanie, gdy aplikacja jest skonfigurowana pod kątem logowania jednokrotnego w innej usłudze dostawcy tożsamości. Ta opcja nie powoduje dodania logowania jednokrotnego do aplikacji. Jednak aplikacja może już mieć zaimplementowane Logowanie jednokrotne przy użyciu innej usługi, takiej jak Active Directory Federation Services.|
| [Disabled (Wyłączone)](#disabled-sso) | Chmura i lokalna | Wybierz pozycję wyłączone Logowanie jednokrotne, jeśli aplikacja nie jest gotowa do skonfigurowania do rejestracji jednokrotnej. Użytkownicy muszą wprowadzić swoją nazwę użytkownika i hasło za każdym razem, gdy uruchamiają tę aplikację.|
| [Zintegrowane uwierzytelnianie systemu Windows (IWA)](#integrated-windows-authentication-iwa-sso) | tylko lokalne | Wybierz pozycję IWA Logowanie jednokrotne dla aplikacji korzystających ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji obsługujących oświadczenia. W przypadku IWA łączniki serwera proxy aplikacji używają ograniczonego delegowania protokołu Kerberos (KCD) do uwierzytelniania użytkowników w aplikacji. |
| [Na podstawie nagłówka](#header-based-sso) | tylko lokalne | Użyj logowania jednokrotnego opartego na nagłówkach, gdy aplikacja używa nagłówków do uwierzytelniania. Logowanie jednokrotne oparte na nagłówkach wymaga PingAccess dla usługi Azure AD. Serwer proxy aplikacji używa usługi Azure AD do uwierzytelniania użytkownika, a następnie przekazuje ruch przez usługę łącznika.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect Connect and OAuth

Podczas opracowywania nowych aplikacji używaj nowoczesnych protokołów, takich jak OpenID Connect Connect i OAuth, aby osiągnąć najlepsze środowisko logowania jednokrotnego dla aplikacji na wielu platformach urządzeń. Uwierzytelnianie OAuth umożliwia użytkownikom lub administratorom [udzielenie zgody](configure-user-consent.md) na chronione zasoby, takie jak [Microsoft Graph](/graph/overview). Zapewniamy łatwy do przyjęcia [zestawy SDK](../develop/reference-v2-libraries.md) dla aplikacji, a ponadto aplikacja będzie gotowa do użycia [Microsoft Graph](/graph/overview).

Aby uzyskać więcej informacji, zobacz:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity platform Developer Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>Logowanie jednokrotne SAML

Za pomocą **logowania jednokrotnego SAML**usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje informacje logowania do aplikacji za pomocą protokołu połączenia. Korzystając z logowania jednokrotnego opartego na protokole SAML, można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML.

Wybierz Logowanie jednokrotne oparte na protokole SAML, gdy aplikacja je obsługuje.

Logowanie jednokrotne oparte na języku SAML jest obsługiwane w przypadku aplikacji korzystających z dowolnego z następujących protokołów:

- SAML 2.0
- WS-Federation

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na protokole SAML, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md). Ponadto wiele aplikacji oprogramowania jako usługi (SaaS) ma [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md) , który przeprowadzi Cię przez proces konfigurowania logowania jednokrotnego opartego na protokole SAML.

Aby skonfigurować aplikację dla protokołu WS-Federation, postępuj zgodnie z tymi samymi wskazówkami, aby skonfigurować aplikację dla logowania jednokrotnego opartego na protokole SAML, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md). W kroku, aby skonfigurować aplikację do korzystania z usługi Azure AD, musisz zastąpić adres URL logowania usługi Azure AD dla `https://login.microsoftonline.com/<tenant-ID>/wsfed`punktu końcowego usługi WS-Federation.

Aby skonfigurować aplikację lokalną na potrzeby logowania jednokrotnego opartego na protokole SAML, zobacz Rejestrowanie jednokrotne [SAML dla aplikacji lokalnych przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Aby uzyskać więcej informacji na temat protokołu SAML, zobacz Logowanie jednokrotne [protokołu SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Logowanie jednokrotne oparte na hasłach

Logowanie oparte na haśle polega na zalogowaniu się do aplikacji przy użyciu nazwy użytkownika i hasła podczas pierwszego uzyskiwania do nich dostępu. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji.

Logowanie jednokrotne oparte na hasłach korzysta z istniejącego procesu uwierzytelniania udostępnianego przez aplikację. Po włączeniu logowania jednokrotnego dla aplikacji usługa Azure AD gromadzi i bezpiecznie przechowuje nazwy użytkowników i hasła dla aplikacji. Poświadczenia użytkownika są przechowywane w stanie zaszyfrowanym w katalogu.

Wybierz Logowanie jednokrotne oparte na haśle, gdy:

- Aplikacja nie obsługuje protokołu SAML logowania jednokrotnego.
- Aplikacja uwierzytelnia się przy użyciu nazwy użytkownika i hasła zamiast tokenów dostępu i nagłówków.

Logowanie jednokrotne oparte na haśle jest obsługiwane w przypadku aplikacji opartych na chmurze, które mają stronę logowania opartą na języku HTML. Użytkownik może korzystać z dowolnej z następujących przeglądarek:

- Internet Explorer 11 w systemie Windows 7 lub nowszym
   > [!NOTE]
   > Program Internet Explorer ma ograniczoną obsługę i nie otrzymuje już nowych aktualizacji oprogramowania. Zalecana przeglądarka to Microsoft Edge.

- Microsoft Edge w systemie Windows 10 w wersji rocznicowej lub nowszej
- Microsoft Edge dla systemów iOS i Android
- Intune Managed Browser
- Program Chrome w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym
- Firefox 26,0 lub nowszy w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym

Aby skonfigurować aplikację w chmurze na potrzeby logowania jednokrotnego opartego na hasłach, zobacz [Konfigurowanie logowania](configure-password-single-sign-on-non-gallery-applications.md)jednokrotnego przy użyciu hasła.

Aby skonfigurować aplikację lokalną do logowania jednokrotnego za pomocą serwera proxy aplikacji, zobacz Przechowywanie [haseł w celu logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak uwierzytelnianie jest wykonywane na podstawie hasła Logowanie jednokrotne

W celu uwierzytelnienia użytkownika w aplikacji usługa Azure AD Pobiera poświadczenia użytkownika z katalogu i umieszcza je na stronie logowania aplikacji.  Usługa Azure AD bezpiecznie przekazuje poświadczenia użytkownika za pośrednictwem rozszerzenia przeglądarki sieci Web lub aplikacji mobilnej. Ten proces umożliwia administratorowi zarządzanie poświadczeniami użytkownika i nie wymaga zapamiętywania hasła przez użytkowników.

> [!IMPORTANT]
> Poświadczenia są ukrywane podczas procesu automatycznego logowania. Poświadczenia są jednak wykrywalne przy użyciu narzędzi do debugowania w sieci Web. Użytkownicy i Administratorzy muszą przestrzegać tych samych zasad zabezpieczeń, co w przypadku, gdy poświadczenia zostały wprowadzone bezpośrednio przez użytkownika.

### <a name="managing-credentials-for-password-based-sso"></a>Zarządzanie poświadczeniami logowania jednokrotnego opartego na hasłach

Hasła dla każdej aplikacji mogą być zarządzane przez administratora usługi Azure AD lub przez użytkowników.

Gdy administrator usługi Azure AD zarządza poświadczeniami:  

- Użytkownik nie musi resetować ani zapamiętać nazwy użytkownika i hasła. Użytkownik może uzyskać dostęp do aplikacji, klikając ją w panelu dostępu lub korzystając z podanego linku.
- Administrator może wykonywać zadania zarządzania dotyczące poświadczeń. Administrator może na przykład zaktualizować dostęp do aplikacji zgodnie z członkostwem w grupach użytkowników i stanem pracownika.
- Administrator może używać poświadczeń administracyjnych, aby zapewnić dostęp do aplikacji współużytkowanych przez wielu użytkowników. Na przykład administrator może zezwolić wszystkim, kto może uzyskać dostęp do aplikacji w celu uzyskania dostępu do aplikacji Media społecznościowe lub do udostępniania dokumentów.

Gdy użytkownik końcowy zarządza poświadczeniami:

- Użytkownicy mogą zarządzać swoimi hasłami, aktualizując lub usuwając je zgodnie z wymaganiami.
- Administratorzy nadal mogą ustawiać nowe poświadczenia dla aplikacji.

## <a name="linked-sign-on"></a>Logowanie połączone
Połączenie połączone umożliwia usłudze Azure AD udostępnianie logowania jednokrotnego do aplikacji, która jest już skonfigurowana do logowania jednokrotnego w innej usłudze. Połączona aplikacja może być wyświetlana użytkownikom końcowym w portalu pakietu Office 365 lub w portalu usługi Azure AD. Na przykład użytkownik może uruchomić aplikację, która została skonfigurowana do logowania jednokrotnego w Active Directory Federation Services 2,0 (AD FS) z poziomu portalu pakietu Office 365. Dodatkowe raporty są również dostępne dla połączonych aplikacji, które są uruchamiane z poziomu portalu pakietu Office 365 lub portalu aplikacji Azure AD. Aby skonfigurować aplikację do logowania połączonego, zobacz [Konfigurowanie połączenia połączonego](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Logowanie połączone do migracji aplikacji

Połączone logowanie może zapewnić spójny interfejs użytkownika podczas migrowania aplikacji w określonym czasie. Jeśli migrujesz aplikacje do Azure Active Directory, możesz użyć połączenia połączonego, aby szybko publikować linki do wszystkich aplikacji, które mają zostać zmigrowane.  Użytkownicy mogą znaleźć wszystkie linki w [portalu webapps](../user-help/active-directory-saas-access-panel-introduction.md) lub [uruchamiania aplikacji pakietu Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Użytkownicy nie będą wiedzieli, że uzyskują dostęp do połączonej aplikacji lub zmigrowanej aplikacji.  

Po uwierzytelnieniu użytkownika w połączonej aplikacji należy utworzyć rekord konta, aby użytkownik końcowy mógł uzyskać dostęp do logowania jednokrotnego. Inicjowanie obsługi tego rekordu konta może odbywać się automatycznie lub może być wykonywane ręcznie przez administratora.

## <a name="disabled-sso"></a>Wyłączone Logowanie jednokrotne

Tryb wyłączony oznacza, że logowanie jednokrotne nie jest używane dla aplikacji. Gdy Logowanie jednokrotne jest wyłączone, użytkownicy mogą wymagać dwukrotnego uwierzytelnienia. Najpierw użytkownicy uwierzytelniają się w usłudze Azure AD, a następnie logują się do aplikacji.

Użyj wyłączonego trybu logowania jednokrotnego:

- Jeśli nie masz gotowości do integracji tej aplikacji z logowaniem jednokrotnym w usłudze Azure AD, lub
- Jeśli testujesz inne aspekty aplikacji, lub
- Jako warstwa zabezpieczeń aplikacji lokalnej, która nie wymaga uwierzytelniania użytkowników. W przypadku wyłączenia użytkownik musi uwierzytelnić się.

## <a name="integrated-windows-authentication-iwa-sso"></a>Zintegrowane uwierzytelnianie systemu Windows (IWA) — Logowanie jednokrotne

[Serwer proxy aplikacji](application-proxy.md) umożliwia logowanie jednokrotne do aplikacji korzystających ze [zintegrowanego uwierzytelniania systemu Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)lub aplikacji obsługujących oświadczenia. Jeśli aplikacja używa IWA, serwer proxy aplikacji uwierzytelnia się w aplikacji przy użyciu ograniczonego delegowania protokołu Kerberos (KCD). W przypadku aplikacji obsługujących oświadczenia, która ufa Azure Active Directory, logowanie jednokrotne działa, ponieważ użytkownik został już uwierzytelniony za pomocą usługi Azure AD.

Wybierz pozycję zintegrowane uwierzytelnianie systemu Windows tryb logowania jednokrotnego, aby zapewnić Logowanie jednokrotne do aplikacji lokalnej, która jest uwierzytelniana za pomocą IWA.

Aby skonfigurować aplikację lokalną dla usługi IWA, zobacz [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak działa Logowanie jednokrotne za pomocą KCD
Ten diagram wyjaśnia przepływ, gdy użytkownik uzyskuje dostęp do aplikacji lokalnej korzystającej z IWA.

![Diagram przepływu uwierzytelniania Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Użytkownik wprowadza adres URL w celu uzyskania dostępu do lokalnej aplikacji za pomocą serwera proxy aplikacji.
1. Serwer proxy aplikacji przekierowuje żądanie do usług uwierzytelniania usługi Azure AD w celu wstępnego uwierzytelnienia. W tym momencie usługa Azure AD stosuje wszelkie odpowiednie zasady uwierzytelniania i autoryzacji, takie jak uwierzytelnianie wieloskładnikowe. Jeśli użytkownik jest zweryfikowany, usługa Azure AD tworzy token i wysyła go do użytkownika.
1. Użytkownik przekazuje token do serwera proxy aplikacji.
1. Serwer proxy aplikacji weryfikuje token i pobiera główną nazwę użytkownika (UPN) z tokenu. Następnie wysyła żądanie, nazwę UPN, a główna nazwa usługi (SPN) do łącznika za pośrednictwem podwójnego uwierzytelnionego bezpiecznego kanału.
1. Łącznik używa negocjacji ograniczonego delegowania protokołu Kerberos (KCD) z lokalną usługą AD, personifikując użytkownika w celu uzyskania tokenu Kerberos do aplikacji.
1. Active Directory wysyła token Kerberos dla aplikacji do łącznika.
1. Łącznik wysyła oryginalne żądanie do serwera aplikacji przy użyciu tokenu Kerberos otrzymanego z usługi AD.
1. Aplikacja wysyła odpowiedź do łącznika, który jest następnie zwracany do usługi serwera proxy aplikacji i na końcu do użytkownika.

## <a name="header-based-sso"></a>Logowanie jednokrotne oparte na nagłówkach

Logowanie jednokrotne oparte na nagłówkach działa dla aplikacji, które używają nagłówków HTTP do uwierzytelniania. Ta metoda logowania używa usługi uwierzytelniania innej firmy o nazwie PingAccess. Użytkownik musi tylko uwierzytelniać się w usłudze Azure AD.

Wybierz pozycję Logowanie jednokrotne oparte na nagłówkach, gdy serwer proxy aplikacji i PingAccess są skonfigurowane dla aplikacji.

Aby skonfigurować uwierzytelnianie oparte na nagłówkach, zobacz [uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Co to jest PingAccess dla usługi Azure AD?

Korzystając z PingAccess dla usługi Azure AD, użytkownicy mogą uzyskiwać dostęp do aplikacji, które używają nagłówków do uwierzytelniania i logowania jednokrotnego. Serwer proxy aplikacji traktuje te aplikacje, takie jak inne, za pomocą usługi Azure AD do uwierzytelniania dostępu, a następnie przekazywania ruchu przez usługę łącznika. Po wystąpieniu uwierzytelniania usługa PingAccess dokonuje translacji tokenu dostępu usługi Azure AD do formatu nagłówka, który jest wysyłany do aplikacji.

Użytkownicy nie będą zarejestrowani w przypadku logowania się do korzystania z aplikacji firmowych. Mogą oni nadal korzystać z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera proxy aplikacji kierują ruch zdalny do wszystkich aplikacji, a Równoważenie obciążenia będzie kontynuowane automatycznie.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak mogę uzyskać licencji na PingAccess?

Ponieważ ten scenariusz jest oferowany przez partnerstwo między usługą Azure AD i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje Azure AD — wersja Premium obejmują podstawową licencję PingAccess, która obejmuje maksymalnie 20 aplikacji. Jeśli musisz opublikować więcej niż 20 aplikacji opartych na nagłówkach, możesz uzyskać dodatkową licencję z PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Pokrewne artykuły:
* [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurowanie logowania jednokrotnego opartego na protokole SAML](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie logowania jednokrotnego opartego na hasłach](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurowanie logowania połączonego](configure-linked-sign-on.md)
* [Wprowadzenie do zarządzania dostępem do aplikacji](what-is-access-management.md)
* Link do pobierania: [plan wdrożenia z logowaniem](https://aka.ms/SSODeploymentPlan)jednokrotnym.
