---
title: Jak działa usługa Azure MFA — Azure Active Directory
description: Usługa Azure Multi-Factor Authentication pomaga w zabezpieczeniu dostępu do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484064"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: Azure Multi-Factor Authentication

Bezpieczeństwo dwuetapowej weryfikacji polega na podejściu warstwowym. Naruszenie wielu czynników uwierzytelniania stanowi znaczące wyzwanie dla osób atakujących. Nawet jeśli osoba atakująca zarządza, aby poznać hasło użytkownika, jest bezużyteczne bez również posiadania dodatkowej metody uwierzytelniania. Działa przez wymaganie co najmniej dwóch następujących metod uwierzytelniania:

* Coś, co wiesz (zazwyczaj hasło)
* Coś, co masz (zaufane urządzenie, które nie jest łatwo duplikowane, takie jak telefon)
* Coś, co masz (biometria)

<center>

![obrazu metod uwierzytelniania koncepcyjnego](./media/concept-mfa-howitworks/methods.png)</center>

Usługa Azure Multi-Factor Authentication (MFA) pomaga w zabezpieczeniu dostępu do danych i aplikacji przy jednoczesnym zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pośrednictwem różnych [metod uwierzytelniania](concept-authentication-methods.md). Użytkownicy mogą lub nie mogą zakwestionować usługi MFA w oparciu o decyzje konfiguracyjne wykonywane przez administratora.

## <a name="how-to-get-multi-factor-authentication"></a>Jak uzyskać Multi-Factor Authentication?

Multi-Factor Authentication jest częścią następujących ofert:

* **Azure Active Directory — wersja Premium** lub **Microsoft 365 Business** — w pełni funkcjonalne korzystanie z platformy Azure Multi-Factor Authentication przy użyciu zasad dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego.

* **Azure AD — wersja bezpłatna** lub autonomiczne licencje **pakietu Office 365** — Użyj [domyślnych ustawień zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) , aby wymagać uwierzytelniania wieloskładnikowego dla użytkowników i administratorów.

* **Azure Active Directory Administratorzy globalni** — podzbiór funkcji Multi-Factor Authentication platformy Azure jest dostępny jako środek do ochrony kont administratorów globalnych.

> [!NOTE]
> Nowi klienci nie mogą już kupować Multi-Factor Authentication platformy Azure jako autonomicznej oferty obowiązującej 1 września, 2018. Uwierzytelnianie wieloskładnikowe będzie nadal mieć dostępną funkcję w Azure AD — wersja Premium licencji.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników jest przyzwyczajonych do korzystania tylko z haseł do uwierzytelniania, ważne jest, aby Twoja organizacja komunikuje się ze wszystkimi użytkownikami dotyczącymi tego procesu. Świadomość może zmniejszyć prawdopodobieństwo, że użytkownicy wywołują pomoc techniczną w przypadku drobnych problemów związanych z usługą MFA. Istnieją jednak sytuacje, w których konieczne jest tymczasowe wyłączenie usługi MFA. Skorzystaj z poniższych wskazówek, aby zrozumieć, jak obsługiwać te scenariusze:

* Przeszkol personel pomocy technicznej, aby obsługiwał scenariusze, w których użytkownik nie może się zalogować, ponieważ nie ma dostępu do ich metod uwierzytelniania lub nie działa prawidłowo.
   * Korzystając z zasad dostępu warunkowego dla usługi Azure MFA, pracownicy pomocy technicznej mogą dodać użytkownika do grupy, która jest wykluczona z zasad wymagających uwierzytelniania wieloskładnikowego.
* Rozważ użycie dostępu warunkowego o nazwie Locations jako metody minimalizowania dwuetapowych wierszy weryfikacyjnych. Dzięki tej funkcji Administratorzy mogą ominąć weryfikację dwuetapową dla użytkowników, którzy logują się z bezpiecznej zaufanej lokalizacji sieciowej, takiej jak segment sieci używany na potrzeby dołączania do nowego użytkownika.
* Wdróż [Azure AD Identity Protection](../active-directory-identityprotection.md) i Wyzwól weryfikację dwuetapową opartą na wykryciu ryzyka.

## <a name="next-steps"></a>Następne kroki

- [Wdrożenie krok po kroku na platformie Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
