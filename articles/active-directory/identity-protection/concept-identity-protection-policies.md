---
title: Zasady Azure AD Identity Protection
description: Identyfikowanie trzech zasad włączonych przy użyciu programu Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887418"
---
# <a name="identity-protection-policies"></a>Zasady ochrony tożsamości

Azure Active Directory Identity Protection obejmuje trzy domyślne zasady, które Administratorzy mogą włączyć. Te zasady obejmują ograniczone dostosowanie, ale mają zastosowanie w przypadku większości organizacji. Wszystkie zasady zezwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub konta administratora z przerwaniem](../users-groups-roles/directory-emergency-access.md).

![Zasady ochrony tożsamości](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Zasady rejestracji usługi Azure MFA

Ochrona tożsamości może pomóc organizacjom wdrożyć usługę Azure Multi-Factor Authentication (MFA) za pomocą zasad dostępu warunkowego, które wymagają rejestracji podczas logowania. Włączenie tych zasad jest świetnym sposobem zapewnienia, że nowi użytkownicy w organizacji zostali zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego w pierwszym dniu. Uwierzytelnianie wieloskładnikowe to jedna z metod samoobsługowego rozwiązywania problemów dotyczących zdarzeń związanych z ryzykiem w ramach usługi Identity Protection. Samokorygowanie umożliwia użytkownikom samodzielne podejmowanie działań w celu zmniejszenia liczby wywołań pomocy technicznej.

Więcej informacji o usłudze Azure Multi-Factor Authentication można znaleźć w artykule, [jak to działa: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka związanego z logowaniem

Program Identity Protection analizuje sygnały z każdego logowania, zarówno w czasie rzeczywistym, jak i offline, a następnie oblicza ocenę ryzyka na podstawie prawdopodobieństwa, że logowanie nie zostało wykonane przez użytkownika. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą wybrać blokowanie dostępu, zezwalanie na dostęp lub zezwalanie na dostęp, ale wymagają uwierzytelniania wieloskładnikowego.

W przypadku wykrycia ryzyka użytkownicy mogą wykonywać uwierzytelnianie wieloskładnikowe w celu samokorygowania i zamknąć ryzykowne zdarzenie logowania, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Przed wyzwoleniem zasad dotyczących ryzyka związanego z logowaniem użytkownicy muszą wcześniej zarejestrować się w usłudze Azure Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Niestandardowe zasady dostępu warunkowego

Administratorzy mogą również utworzyć niestandardowe zasady dostępu warunkowego, w tym ryzyko logowania jako warunek przypisania. Więcej informacji o dostępie warunkowym można znaleźć w artykule [co to jest dostęp warunkowy?](../conditional-access/overview.md)

![Zasady dotyczące ryzyka związanego z logowaniem niestandardowym dostępu warunkowego](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Zasady ryzyka dla użytkowników

Program Identity Protection może obliczyć, co jest bardzo zrozumiałe w przypadku zachowania użytkownika i którego należy użyć do podejmowania decyzji o podstawowym zagrożeniu. Ryzyko związane z użytkownikiem to obliczenie prawdopodobieństwa naruszenia zabezpieczeń tożsamości. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą wybrać blokowanie dostępu, zezwalanie na dostęp lub zezwalanie na dostęp, ale wymagać zmiany hasła przy użyciu funkcji samoobsługowego [resetowania hasła w usłudze Azure AD](../authentication/howto-sspr-deployment.md).

W przypadku wykrycia ryzyka użytkownicy mogą przeprowadzić Samoobsługowe resetowanie haseł w celu samoobsługowego rozwiązywania problemów i zamknąć zdarzenie związane z ryzykiem użytkownika, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Przed wyzwoleniem zasad ryzyka dla użytkowników należy wcześniej zarejestrować się do samoobsługowego resetowania hasła.

## <a name="next-steps"></a>Następne kroki

- [Włącz Samoobsługowe resetowanie haseł w usłudze Azure AD](../authentication/howto-sspr-deployment.md)

- [Włącz Multi-Factor Authentication platformy Azure](../authentication/howto-mfa-getstarted.md)

- [Włączanie zasad rejestracji w usłudze Azure Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)
