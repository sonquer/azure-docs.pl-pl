---
title: Udostępnianie kont i poświadczeń — Azure Active Directory | Microsoft Docs
description: Opisuje, w jaki sposób Azure Active Directory umożliwia organizacjom bezpieczne udostępnianie kont dla aplikacji lokalnych i usług w chmurze konsumenta.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01ebde09517cbe7f093dd56a8a4116e76e64e38
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736768"
---
# <a name="sharing-accounts-with-azure-ad"></a>Udostępnianie kont za pomocą usługi Azure AD

## <a name="overview"></a>Omówienie

Czasami organizacje muszą używać pojedynczej nazwy użytkownika i hasła dla wielu osób, co zwykle odbywa się w dwóch przypadkach:

* Podczas uzyskiwania dostępu do aplikacji, które wymagają unikatowego logowania i hasła dla każdego użytkownika, zarówno lokalnych aplikacji, jak i usług w chmurze konsumentów (na przykład firmowych kont mediów społecznościowych).
* Podczas tworzenia środowisk wielu użytkowników. Może istnieć jedno konto lokalne, które ma podwyższone uprawnienia i służy do wykonywania podstawowych działań związanych z instalacją, administracją i odzyskiwaniem. Na przykład lokalne konto administratora globalnego dla pakietu Office 365 lub konta głównego w usłudze Salesforce.

Tradycyjnie te konta są udostępniane przez dystrybuowanie poświadczeń (username i Password) do odpowiednich osób lub przechowywanie ich w udostępnionej lokalizacji, w której wielu zaufanych agentów może uzyskiwać do nich dostęp.

Tradycyjny model udostępniania ma kilka wad:

* Umożliwienie dostępu do nowych aplikacji wymaga dystrybuowania poświadczeń do wszystkich osób, które wymagają dostępu.
* Każda aplikacja udostępniona może wymagać własnego unikatowego zestawu poświadczeń udostępnionych, co wymaga zapamiętania wielu zestawów poświadczeń przez użytkowników. Gdy użytkownicy muszą pamiętać wiele poświadczeń, ryzyko zwiększy konieczność stosowania ryzykownych praktyk. (na przykład zapisywanie haseł).
* Nie można określić, kto ma dostęp do aplikacji.
* Nie można określić, kto uzyskał *dostęp do* aplikacji.
* Jeśli chcesz usunąć dostęp do aplikacji, musisz zaktualizować poświadczenia i ponownie je rozesłać do wszystkich użytkowników, którzy potrzebują dostępu do tej aplikacji.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory udostępnianie konta

Usługa Azure AD udostępnia nowe podejście do korzystania z kont współużytkowanych, które eliminują te wady.

Administrator usługi Azure AD konfiguruje aplikacje, do których użytkownik może uzyskać dostęp za pomocą panelu dostępu i wybierając typ logowania jednokrotnego dla tej aplikacji. Jednym z tych typów jest *Logowanie*jednokrotne oparte na hasłach, dzięki czemu usługa Azure AD działa jako "Broker" podczas procesu logowania dla tej aplikacji.

Użytkownicy logują się jednokrotnie przy użyciu konta organizacyjnego. To konto jest tym samym, z którego regularnie korzysta do uzyskiwania dostępu do swojego pulpitu lub poczty e-mail. Mogą oni odnajdywać i uzyskiwać dostęp tylko do tych aplikacji, do których są przypisane. Dzięki udostępnionym kontom ta lista aplikacji może zawierać dowolną liczbę poświadczeń udostępnionych. Użytkownik końcowy nie musi pamiętać ani zapisywać różnych kont, które mogą być używane.

Współużytkowane konta nie tylko zwiększają nadzór i zwiększają użyteczność, a także zwiększają bezpieczeństwo. Użytkownicy z uprawnieniami do korzystania z poświadczeń nie widzą hasła udostępnionego, ale nie otrzymują uprawnień do używania hasła w ramach zorganizowanego przepływu uwierzytelniania. Ponadto niektóre aplikacje logowania jednokrotnego hasła umożliwiają korzystanie z usługi Azure AD do okresowego przerzucania haseł (aktualizacji). System używa dużych, złożonych haseł, które zwiększają bezpieczeństwo konta. Administrator może łatwo udzielić lub odwołać dostęp do aplikacji, wiedzący o tym, kto ma dostęp do konta i kto ma do niego dostęp w przeszłości.

Usługa Azure AD obsługuje konta współużytkowane w ramach dowolnego systemu lub planu licencjonowania Azure AD — wersja Premium dla wszystkich typów haseł. W galerii aplikacji można udostępniać konta dla dowolnych tysięcy wstępnie zintegrowanych aplikacji, a także dodawać własne aplikacje uwierzytelniające hasła przy użyciu [niestandardowych aplikacji SSO](../manage-apps/configure-single-sign-on-portal.md).

Funkcje usługi Azure AD, które umożliwiają udostępnianie kont, obejmują:

* [Logowanie jednokrotne przy użyciu hasła](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agent logowania jednokrotnego dla hasła
* [Przypisanie grupy](groups-self-service-management.md)
* Niestandardowe aplikacje hasła
* [Pulpit nawigacyjny/raporty użycia aplikacji](../active-directory-passwords-get-insights.md)
* Portale dostępu użytkowników końcowych
* [Serwer proxy aplikacji](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Udostępnianie konta

Aby używać usługi Azure AD do udostępniania konta, musisz:

* Dodawanie [galerii](https://azure.microsoft.com/marketplace/active-directory/) aplikacji aplikacji lub [aplikacji niestandardowej](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurowanie aplikacji do logowania jednokrotnego (SSO) hasła
* Użyj [przypisania opartego na grupach](groups-saasapps.md) i wybierz opcję, aby wprowadzić poświadczenia udostępnione

Możesz również zwiększyć bezpieczeństwo konta współużytkowanego przy użyciu uwierzytelniania wieloskładnikowego (MFA) (Dowiedz się więcej o [zabezpieczaniu aplikacji za pomocą usługi Azure AD](../authentication/concept-mfa-whichversion.md)) i można delegować możliwość zarządzania dostępem do aplikacji za pomocą samoobsługowej usługi [Azure AD. ](groups-self-service-management.md)zarządzanie grupami.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Samoobsługowe zarządzanie grupami/SSAA](groups-self-service-management.md)
