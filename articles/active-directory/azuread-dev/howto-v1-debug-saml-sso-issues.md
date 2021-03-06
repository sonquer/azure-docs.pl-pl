---
title: Debugowanie logowania jednokrotnego opartego na protokole SAML Azure Active Directory | Microsoft Docs
description: Debuguj Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: ad948521a376dc8bdb3d868de643ce103f56735d
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164606"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debuguj Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Dowiedz się, jak znaleźć i naprawić problemy związane z [logowaniem](../manage-apps/what-is-single-sign-on.md) jednokrotnym dla aplikacji w usłudze Azure Active Directory (Azure AD) obsługującej [SAML (SAML) 2,0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zalecamy zainstalowanie [rozszerzenia moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). To rozszerzenie przeglądarki ułatwia zbieranie informacji o żądaniach SAML i odpowiedzi SAML, które są potrzebne do rozwiązywania problemów z logowaniem jednokrotnym. Jeśli nie można zainstalować rozszerzenia, w tym artykule opisano sposób rozwiązywania problemów z zainstalowanym rozszerzeniem i bez niego.

Aby pobrać i zainstalować rozszerzenie moje aplikacje bezpieczne logowanie, użyj jednego z poniższych linków.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testowanie logowania jednokrotnego opartego na protokole SAML

Aby przetestować Logowanie jednokrotne oparte na języku SAML między usługą Azure AD i aplikacją docelową:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny lub inny administrator, który ma uprawnienia do zarządzania aplikacjami.
1. W bloku po lewej stronie wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**. 
1. Z listy aplikacji dla przedsiębiorstw wybierz aplikację, dla której chcesz przetestować Logowanie jednokrotne, a następnie z opcji po lewej stronie wybierz opcję **Logowanie jednokrotne**.
1. Aby otworzyć środowisko testowania logowania jednokrotnego opartego na protokole SAML, przejdź do pozycji **Testuj Logowanie jednokrotne** (krok 5). Jeśli przycisk **test** jest wyszarzony, należy wypełnić i zapisać wymagane atrybuty najpierw w sekcji **Podstawowa konfiguracja SAML** .
1. W bloku **Testuj logowanie** jednokrotne Użyj poświadczeń firmowych, aby zalogować się do aplikacji docelowej. Możesz zalogować się jako bieżący użytkownik lub inny użytkownik. Jeśli zalogujesz się jako inny użytkownik, zostanie wyświetlony monit z prośbą o uwierzytelnienie.

    ![Zrzut ekranu przedstawiający stronę testowego logowania jednokrotnego SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Po pomyślnym zalogowaniu test zakończył się powodzeniem. W takim przypadku usługa Azure AD wystawiła token odpowiedzi SAML do aplikacji. Aplikacja użyła tokenu SAML w celu pomyślnego zalogowania się.

Jeśli wystąpi błąd na stronie logowania firmy lub stronie aplikacji, użyj jednej z następnych sekcji, aby rozwiązać ten problem.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Usuń błąd logowania na stronie logowania w firmie

Podczas próby zalogowania się na stronie logowania do firmy może zostać wyświetlony komunikat o błędzie podobny do poniższego.

![Przykład pokazujący błąd na stronie logowania w firmie](./media/howto-v1-debug-saml-sso-issues/error.png)

Aby debugować ten błąd, potrzebny jest komunikat o błędzie i żądanie SAML. Rozszerzenie moje aplikacje bezpieczne logowanie automatycznie zbiera te informacje i wyświetla wskazówki dotyczące rozwiązywania problemów w usłudze Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Aby rozwiązać błąd logowania z zainstalowanym rozszerzeniem moje aplikacje bezpieczne logowanie

1. Gdy wystąpi błąd, rozszerzenie przekierowuje z powrotem do bloku **rejestracji** jednokrotnej usługi Azure AD.
1. W bloku **Testuj logowanie** jednokrotne wybierz pozycję **Pobierz żądanie SAML**.
1. W zależności od błędu i wartości w żądaniu SAML powinny zostać wyświetlone wskazówki dotyczące rozwiązywania.
1. Zobaczysz przycisk **napraw** , aby automatycznie zaktualizować konfigurację w usłudze Azure AD w celu rozwiązania problemu. Jeśli ten przycisk nie jest wyświetlany, oznacza to, że problem z logowaniem nie jest spowodowany nieprawidłową konfiguracją w usłudze Azure AD.

Jeśli nie podano rozwiązania dla błędu logowania, sugerujemy użycie pola tekstowego opinii do poinformowania nas.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Aby rozwiązać ten problem bez instalowania rozszerzenia moje aplikacje bezpieczne logowanie

1. Skopiuj komunikat o błędzie w prawym dolnym rogu strony. Komunikat o błędzie zawiera:
    - Identyfikator korelacji i sygnatura czasowa. Te wartości są ważne podczas tworzenia przypadku pomocy technicznej w firmie Microsoft, ponieważ pomagają inżynierom identyfikować Twój problem i zapewnić dokładne rozwiązanie problemu.
    - Instrukcja identyfikująca główną przyczynę problemu.
1. Wróć do usługi Azure AD i Znajdź blok **Testuj logowanie** jednokrotne.
1. W polu tekstowym powyżej **Uzyskaj wskazówki dotyczące rozwiązywania problemów**wklej komunikat o błędzie.
1. Kliknij przycisk **Uzyskaj wskazówki dotyczące rozwiązywania** , aby wyświetlić kroki rozwiązywania problemu. Wskazówki mogą wymagać informacji z żądania SAML lub odpowiedzi SAML. Jeśli nie używasz rozszerzenia moje aplikacje bezpieczne logowanie, może być potrzebne narzędzie, takie jak [programu Fiddler](https://www.telerik.com/fiddler) , aby pobrać żądanie i odpowiedź SAML.
1. Upewnij się, że miejsce docelowe w żądaniu SAML odpowiada adresowi URL usługi logowania jednokrotnego protokołu SAML uzyskanej z usługi Azure AD.
1. Sprawdź, czy wystawca w żądaniu SAML ma ten sam identyfikator, który został skonfigurowany dla aplikacji w usłudze Azure AD. Usługa Azure AD używa wystawcy, aby znaleźć aplikację w katalogu.
1. Sprawdź, czy AssertionConsumerServiceURL jest miejscem, w którym aplikacja oczekuje na otrzymanie tokenu SAML z usługi Azure AD. Tę wartość można skonfigurować w usłudze Azure AD, ale nie jest to wymagane, jeśli jest ona częścią żądania języka SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Usuń błąd logowania na stronie aplikacji

Możesz zalogować się pomyślnie, a następnie na stronie aplikacji zostanie wyświetlony komunikat o błędzie. Dzieje się tak, gdy usługa Azure AD wystawił token do aplikacji, ale aplikacja nie akceptuje odpowiedzi.

Aby naprawić błąd, wykonaj następujące kroki:

1. Jeśli aplikacja znajduje się w galerii usługi Azure AD, sprawdź, czy zostały wykonane wszystkie czynności związane z integracją aplikacji z usługą Azure AD. Aby znaleźć instrukcje dotyczące integracji dla aplikacji, zobacz [listę samouczków integracji aplikacji SaaS](../saas-apps/tutorial-list.md).
1. Pobieranie odpowiedzi SAML.
    - Jeśli zainstalowano rozszerzenie moje aplikacje bezpieczne logowanie w bloku **Testuj logowanie** jednokrotne, kliknij pozycję **Pobierz odpowiedź SAML**.
    - Jeśli rozszerzenie nie jest zainstalowane, użyj narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) , aby pobrać odpowiedź SAML.
1. Zwróć uwagę na te elementy w tokenie odpowiedzi SAML:
   - Unikatowy identyfikator użytkownika NameID wartość i format
   - Oświadczenia wystawione w tokenie
   - Certyfikat używany do podpisywania tokenu.

     Aby uzyskać więcej informacji na temat odpowiedzi SAML, zobacz Logowanie jednokrotne [protokołu SAML](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Po przejrzeniu odpowiedzi SAML należy zapoznać się z tematem [błąd na stronie aplikacji po zalogowaniu się w](../manage-apps/application-sign-in-problem-application-error.md) celu uzyskania wskazówek dotyczących sposobu rozwiązania problemu. 
1. Jeśli nadal nie możesz zalogować się pomyślnie, możesz polecić dostawcę aplikacji, czego brakuje w odpowiedzi SAML.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy Logowanie jednokrotne działa w aplikacji, można [zautomatyzować Inicjowanie obsługi użytkowników i anulowanie aprowizacji do aplikacji SaaS](../manage-apps/user-provisioning.md) lub rozpocząć [pracę z dostępem warunkowym](../conditional-access/app-based-conditional-access.md).
