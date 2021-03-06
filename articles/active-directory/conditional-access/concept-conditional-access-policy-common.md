---
title: Typowe zasady dostępu warunkowego — Azure Active Directory
description: Powszechnie używane zasady dostępu warunkowego dla organizacji
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfb5fb17abd5a433c177d3efc5a4f0a2cec3d4d7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186139"
---
# <a name="common-conditional-access-policies"></a>Typowe zasady dostępu warunkowego

[Wartości domyślne zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) są wspaniałe w przypadku niektórych organizacji, które wymagają większej elastyczności niż oferta. Na przykład wiele potrzeb umożliwia wykluczenie określonych kont, takich jak ich dostęp awaryjny lub konta administracyjne w ramach awarii z zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego. W przypadku tych organizacji typowe zasady, do których odwołuje się ten artykuł, mogą być używane.

![Zasady dostępu warunkowego w Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Więcej informacji o kontach dostępu awaryjnego i o tym, dlaczego są ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typowe zasady wdrożone przez organizacje

* [Wymagaj uwierzytelniania wieloskładnikowego dla administratorów](howto-conditional-access-policy-admin-mfa.md)\*
* [Wymagaj uwierzytelniania wieloskładnikowego w usłudze Azure management](howto-conditional-access-policy-azure-management.md)\*
* [Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](howto-conditional-access-policy-all-users-mfa.md)\*
* [Blokuj starsze uwierzytelnianie](howto-conditional-access-policy-block-legacy.md)\*
* [Dostęp warunkowy oparty na ryzyku (wymaga Azure AD — wersja Premium P2)](howto-conditional-access-policy-risk.md)
* [Wymagaj zaufanej lokalizacji rejestracji usługi MFA](howto-conditional-access-policy-registration.md)
* [Blokuj dostęp według lokalizacji](howto-conditional-access-policy-location.md)
* [Wymagaj zgodnego urządzenia](howto-conditional-access-policy-compliant-device.md)

\* te cztery zasady po skonfigurowaniu razem, będą mieć możliwość naśladowania funkcjonalności włączonej przez [Ustawienia domyślne zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="next-steps"></a>Następne kroki

- [Symuluj zachowanie podczas logowania za pomocą narzędzia What If dostępu warunkowego.](troubleshoot-conditional-access-what-if.md)
- [Użyj trybu tylko do raportowania dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)
