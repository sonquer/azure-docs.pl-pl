---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne — jak działa | Microsoft Docs'
description: W tym artykule opisano sposób działania funkcji logowania jednokrotnego w Azure Active Directory.
services: active-directory
keywords: Co to jest Azure AD Connect, zainstaluj Active Directory, wymagane składniki usługi Azure AD, logowania jednokrotnego, rejestracji jednokrotnej
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176676"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: Głębokie szczegółowee techniczne

Ten artykuł zawiera szczegółowe informacje techniczne na temat działania funkcji bezproblemowego logowania jednokrotnego (SSO) Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Jak bezproblemowo działa Logowanie jednokrotne?

Ta sekcja zawiera trzy części:

1. Konfiguracja funkcji bezproblemowego logowania jednokrotnego.
2. Jak transakcja logowania jednokrotnego użytkownika w przeglądarce sieci Web współpracuje z bezproblemowym logowaniem jednokrotnym.
3. Jak transakcja logowania jednokrotnego użytkownika na natywnym kliencie działa z bezproblemowym logowaniem jednokrotnym.

### <a name="how-does-set-up-work"></a>Jak działa Konfiguracja?

Bezproblemowe logowanie jednokrotne jest włączone przy użyciu Azure AD Connect, jak pokazano [poniżej](how-to-connect-sso-quick-start.md). Podczas włączania funkcji należy wykonać następujące czynności:

- Konto komputera (`AZUREADSSOACC`) jest tworzone w lokalnym Active Directory (AD) w każdym lesie usługi AD synchronizowanym z usługą Azure AD (przy użyciu Azure AD Connect).
- Ponadto wiele głównych nazw usług (SPN) protokołu Kerberos jest tworzonych do użycia podczas procesu logowania do usługi Azure AD.
- Klucz odszyfrowywania Kerberos konta komputera jest bezpiecznie współużytkowany z usługą Azure AD. Jeśli istnieje wiele lasów usługi AD, każde konto komputera będzie miało własny unikatowy klucz odszyfrowujący protokołu Kerberos.

>[!IMPORTANT]
> Konto `AZUREADSSOACC` komputera musi być silnie chronione ze względów bezpieczeństwa. Tylko Administratorzy domeny powinni mieć możliwość zarządzania kontem komputera. Upewnij się, że delegowanie Kerberos na koncie komputera jest wyłączone i że żadne inne konto w Active Directory nie ma uprawnień do `AZUREADSSOACC` delegowania na koncie komputera. Przechowywanie konta komputera w jednostce organizacyjnej (OU), w którym są bezpieczne przed przypadkowym usunięciem i gdzie tylko Administratorzy domeny mają dostęp. Klucz odszyfrowujący protokołu Kerberos na koncie komputera powinien również być traktowany jako poufne. Zdecydowanie zalecamy przeprowadzenie `AZUREADSSOACC` [klucza odszyfrowywania Kerberos](how-to-connect-sso-faq.md) konta komputera co najmniej co 30 dni.

Po zakończeniu konfiguracji bezproblemowe logowanie jednokrotne działa tak samo jak inne logowania korzystające ze zintegrowanego uwierzytelniania systemu Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak usługa logowania w przeglądarce sieci Web z bezproblemowym działaniem logowania jednokrotnego?

Przepływ logowania w przeglądarce sieci Web jest następujący:

1. Użytkownik próbuje uzyskać dostęp do aplikacji sieci Web (np. programu Outlook Web App — https://outlook.office365.com/owa/) z dołączonego do domeny urządzenia firmowego w sieci firmowej.
2. Jeśli użytkownik nie jest jeszcze zalogowany, użytkownik zostanie przekierowany do strony logowania usługi Azure AD.
3. Użytkownik wpisuje nazwę użytkownika na stronie logowania usługi Azure AD.

   >[!NOTE]
   >W przypadku [niektórych aplikacji](./how-to-connect-sso-faq.md), kroki 2 & 3 są pomijane.

4. Korzystając z języka JavaScript w tle, usługa Azure AD wzywa do przeglądarki za pośrednictwem 401 nieautoryzowanej odpowiedzi, aby zapewnić bilet protokołu Kerberos.
5. Przeglądarka z kolei żąda biletu od Active Directory dla `AZUREADSSOACC` konta komputera (co reprezentuje usługę Azure AD).
6. Active Directory lokalizuje konto komputera i zwraca bilet protokołu Kerberos do przeglądarki zaszyfrowanej przy użyciu klucza tajnego konta komputera.
7. Przeglądarka przekazuje bilet Kerberos uzyskany z Active Directory do usługi Azure AD.
8. Usługa Azure AD odszyfrowuje bilet protokołu Kerberos, który obejmuje tożsamość użytkownika zalogowanego na urządzeniu firmowym przy użyciu klucza współużytkowanego.
9. Po dokonaniu oceny usługa Azure AD zwraca token z powrotem do aplikacji lub prosi użytkownika o wykonanie dodatkowych prób, takich jak Multi-Factor Authentication.
10. Jeśli logowanie użytkownika zakończyło się pomyślnie, użytkownik będzie mógł uzyskać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki, które należy wykonać.

![Bezproblemowe logowanie jednokrotne — przepływ aplikacji sieci Web](./media/how-to-connect-sso-how-it-works/sso2.png)

Bezproblemowe logowanie jednokrotne jest wychodzące, co oznacza, że w przypadku niepowodzenia, środowisko logowania powróci do jego regularnego zachowania — to znaczy, że użytkownik musi wprowadzić hasło, aby się zalogować.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak logowanie się na natywnym kliencie z bezproblemowym działaniem logowania jednokrotnego?

Przepływ logowania na kliencie natywnym jest następujący:

1. Użytkownik próbuje uzyskać dostęp do natywnej aplikacji (na przykład klienta programu Outlook) z dołączonego do domeny urządzenia firmowego w sieci firmowej.
2. Jeśli użytkownik nie jest jeszcze zalogowany, aplikacja natywna Pobiera nazwę użytkownika z sesji systemu Windows na urządzeniu.
3. Aplikacja wysyła nazwę użytkownika do usługi Azure AD i pobiera punkt końcowy MEX usługi WS-Trust dla dzierżawy. Ten punkt końcowy protokołu WS-Trust jest używany wyłącznie przez funkcję bezproblemowego logowania jednokrotnego i nie jest ogólną implementacją protokołu WS-Trust w usłudze Azure AD.
4. Następnie aplikacja wysyła zapytanie do punktu końcowego MEX usługi WS-Trust, aby sprawdzić, czy jest dostępny punkt końcowy uwierzytelniania zintegrowanego. Punkt końcowy uwierzytelniania zintegrowanego jest używany wyłącznie przez funkcję bezproblemowego logowania jednokrotnego.
5. Jeśli krok 4 zakończy się pomyślnie, zostanie wystawione wyzwanie protokołu Kerberos.
6. Jeśli aplikacja jest w stanie pobrać bilet Kerberos, przekazuje go do punktu końcowego zintegrowanego uwierzytelniania usługi Azure AD.
7. Usługa Azure AD odszyfrowuje bilet protokołu Kerberos i weryfikuje go.
8. Usługa Azure AD podpisuje użytkownika w programie i wystawia token języka SAML dla aplikacji.
9. Następnie aplikacja przesyła token SAML do punktu końcowego tokenu OAuth2 usługi Azure AD.
10. Usługa Azure AD weryfikuje token SAML i wystąpiły problemy z tokenem dostępu aplikacji i tokenem odświeżania określonego zasobu oraz tokenem ID.
11. Użytkownik uzyskuje dostęp do zasobu aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki, które należy wykonać.

![Bezproblemowy przepływ aplikacji natywnych w trybie pojedynczego logowania](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Następne kroki

- [**Szybki Start**](how-to-connect-sso-quick-start.md) — Uzyskaj i korzystaj z bezproblemowego logowania jednokrotnego usługi Azure AD.
- [**Często zadawane pytania**](how-to-connect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.
