---
title: Uzyskaj pomoc dotyczącą portalu My Apps — Azure Active Directory | Microsoft Docs
description: Uzyskaj pomoc dotyczącą logowania do programu i wykonywania typowych zadań w portalu My Apps.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022345"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Rozwiązywanie problemów z portalem My Apps

Jeśli występują problemy z zalogowaniem się do lub przy użyciu portalu **Moje aplikacje** , wypróbuj te wskazówki dotyczące rozwiązywania problemów przed skontaktowaniem się z pomocą techniczną lub administratorem w celu uzyskania pomocy.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Mam problem z zainstalowaniem rozszerzenia moje aplikacje bezpieczne logowanie

Jeśli masz problemy z instalowaniem rozszerzenia moje aplikacje bezpieczne logowanie:

- Upewnij się, że używasz obsługiwanej przeglądarki, w tym:

    - **Microsoft Edge.** Działa w systemie Windows 10 w wersji rocznicowej lub nowszej.

    - **Google Chrome.** Uruchomiona w systemie Windows 7 lub nowszym oraz na Mac OS X lub nowszych.

    - **Mozilla Firefox 26,0 lub nowsza.** Uruchomiona w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym.

    - **Internet Explorer 11.** Uruchomiona w systemie Windows 7 lub nowszym (ograniczona obsługa).

- Upewnij się, że ustawienia rozszerzenia przeglądarki są włączone.

- Spróbuj ponownie uruchomić przeglądarkę i zalogować się do portalu **Moje aplikacje** .

- Spróbuj wyczyścić pliki cookie w przeglądarce, a następnie ponownie uruchom i zaloguj się do portalu **My Apps** .

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nie mogę zalogować się do portalu **My Apps**

Jeśli masz problemy z zalogowaniem się do portalu **Moje aplikacje** , możesz spróbować wykonać następujące czynności:

- Upewnij się, że używasz odpowiedniego adresu URL. Powinna być https://myapps.microsoft.com lub dostosowana Strona dla organizacji, np. https://myapps.microsoft.com/contoso.com.

- Upewnij się, że hasło jest poprawne i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła](active-directory-passwords-update-your-own-password.md)służbowego.

- Upewnij się, że informacje weryfikacyjne są aktualne i dokładne. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Multi-Factor Authentication dla mnie?](multi-factor-authentication-end-user.md) lub [Zmień metody i informacje zabezpieczające](security-info-add-update-methods-overview.md).

- Dodaj adres URL portalu **aplikacji** do **Właściwości internetowych > Zabezpieczenia > ustawienie zaufanych witryn** .

- Wyczyść pamięć podręczną przeglądarki, a następnie spróbuj ponownie się zalogować.

## <a name="my-password-isnt-working"></a>Moje hasło nie działa

Jeśli nie pamiętasz hasła, nigdy nie zostało ono odebrane z Twojej organizacji, są blokowane z Twojego konta lub chcesz zmienić hasło, zobacz [Pomoc, nie pamiętam hasła usługi Azure AD](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Chcę mieć możliwość zresetowania własnego hasła

Aby móc zresetować własne hasło, administrator musi najpierw włączyć funkcję dla swojej organizacji, a następnie zaktualizować i zweryfikować wymagane metody weryfikacji. Aby uzyskać więcej informacji o sposobach aktualizowania metod weryfikacji, zobacz [Rejestrowanie do samoobsługowego resetowania hasła](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Otrzymuję komunikat o odmowie dostępu podczas uruchamiania aplikacji

Jeśli otrzymujesz komunikat **odmowy dostępu** po uruchomieniu aplikacji z portalu **My App** , możesz spróbować wykonać następujące czynności:

- Upewnij się, że zainstalowano [rozszerzenie moje aplikacje bezpieczne logowanie](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) i że używasz [obsługiwanej przeglądarki](my-apps-portal-end-user-access.md#supported-browsers).

- Upewnij się, że używasz odpowiedniego adresu URL dla aplikacji oraz że adres URL znajduje się na liście **Właściwości internetowych > zabezpieczenia > listy zaufanych witryn** .

- Upewnij się, że hasło jest poprawne i nie wygasło. Aby uzyskać więcej informacji, zobacz [Resetowanie hasła](active-directory-passwords-update-your-own-password.md)służbowego.

- Upewnij się, że informacje weryfikacyjne są aktualne i dokładne. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Multi-Factor Authentication dla mnie?](multi-factor-authentication-end-user.md) lub [Zmień metody i informacje zabezpieczające](security-info-add-update-methods-overview.md).

- Wyczyść pamięć podręczną przeglądarki, a następnie spróbuj ponownie się zalogować.

Jeśli po wykonaniu tych czynności nadal nie możesz uzyskać dostępu do aplikacji, musisz skontaktować się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

Po zalogowaniu się do portalu **Moje aplikacje** możesz także zaktualizować informacje o profilu i koncie, informacje o grupie oraz informacje o przeglądaniu dostępu (Jeśli masz uprawnienia).

- [Dostęp do aplikacji i korzystanie z nich w portalu My Apps](my-apps-portal-end-user-access.md).

- [Zmień informacje o profilu](my-apps-portal-end-user-update-profile.md).

- [Wyświetlanie i aktualizowanie informacji dotyczących grup](my-apps-portal-end-user-groups.md).

- [Wykonaj własne przeglądy dostępu](my-apps-portal-end-user-access-reviews.md).
