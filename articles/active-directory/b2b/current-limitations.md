---
title: Ograniczenia współpracy B2B — Azure Active Directory | Microsoft Docs
description: Bieżące ograniczenia dotyczące Azure Active Directory współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1717897261404a2ab8df723c280c9be6a2dacea4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77196137"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia dotyczące współpracy B2B usługi Azure AD
Azure Active Directory (Azure AD) Współpraca B2B jest obecnie objęta ograniczeniami opisanymi w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe podwójne uwierzytelnianie wieloskładnikowe
Usługa Azure AD B2B umożliwia Wymuszanie uwierzytelniania wieloskładnikowego w organizacji zasobów (w organizacji zapraszanej). Przyczyny tego podejścia są szczegółowo opisane w temacie [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). Jeśli partner ma już skonfigurowaną i wymuszać usługi uwierzytelniania wieloskładnikowego, może być konieczne przeprowadzenie uwierzytelniania jeden raz w swojej organizacji domowej, a następnie ponownie.

## <a name="instant-on"></a>Natychmiastowe logowanie
W przepływach współpracy B2B dodamy użytkowników do katalogu i aktualizujesz je dynamicznie podczas realizacji zaproszeń, przypisywania aplikacji i tak dalej. Aktualizacje i zapisy są zwykle wykonywane w jednym wystąpieniu katalogu i muszą być replikowane we wszystkich wystąpieniach. Replikacja została ukończona po zaktualizowaniu wszystkich wystąpień. Czasami gdy obiekt jest zapisywana lub aktualizowana w jednym wystąpieniu, a wywołanie do pobrania tego obiektu jest innym wystąpieniem, mogą wystąpić opóźnienia replikacji. Jeśli tak się stanie, Odśwież lub Ponów próbę, aby uzyskać pomoc. W przypadku pisania aplikacji przy użyciu naszego interfejsu API, a następnie ponawiając próbę, aby rozwiązać ten problem.

## <a name="azure-ad-directories"></a>Katalogi usługi Azure AD
Usługa Azure AD B2B podlega limitom katalogów usługi Azure AD. Aby uzyskać szczegółowe informacje o liczbie katalogów, które użytkownik może utworzyć, oraz o liczbie katalogów, do których może należeć użytkownik lub gość, zobacz [limity i ograniczenia usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Chmury narodowe
[Chmury krajowe](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) to fizyczne izolowane wystąpienia platformy Azure. Współpraca B2B nie jest obsługiwana w granicach chmury krajowej. Na przykład jeśli dzierżawa platformy Azure znajduje się w publicznej, globalnej chmurze, nie można zaprosić użytkownika, którego konto znajduje się w chmurze krajowej. Aby współpracować z użytkownikiem, poproś o podanie innego adresu e-mail lub Utwórz konto użytkownika w Twoim katalogu.

## <a name="azure-us-government-clouds"></a>Chmury dla instytucji rządowych USA platformy Azure
W chmurze platformy Azure dla instytucji rządowych USA współpraca B2B jest obecnie obsługiwana tylko między dzierżawcami, które są zarówno w chmurze platformy Azure USA, jak i obsługują współpracę B2B. Jeśli użytkownik zaprasza użytkownika w dzierżawie, który nie jest częścią chmury dla instytucji rządowych usługi Azure USA lub nie obsługuje jeszcze współpracy B2B, zaproszenie zakończy się niepowodzeniem lub użytkownik nie będzie mógł zrealizować zaproszenia. Aby uzyskać szczegółowe informacje o innych ograniczeniach, zobacz [Azure Active Directory — wersja Premium odmian P1 i P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Jak mogę sprawdzić, czy w mojej dzierżawie platformy Azure USA jest dostępna współpraca B2B?
Aby dowiedzieć się, czy dzierżawca w chmurze platformy Azure USA obsługuje współpracę B2B, wykonaj następujące czynności:

1. W przeglądarce przejdź do następującego adresu URL, zastępując nazwę dzierżawcy *&lt;dzierżawca&gt;* :

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Znajdź `"tenant_region_scope"` w odpowiedzi JSON:

   - Jeśli zostanie wyświetlona `"tenant_region_scope":"USGOV”`, B2B jest obsługiwany.
   - Jeśli `"tenant_region_scope":"USG"` pojawia się, B2B nie jest obsługiwana.
 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Delegowanie zaproszeń do współpracy B2B](delegate-invitations.md)

