---
title: Uwierzytelnianie usługi Azure AD & chmury narodowe | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o punktach końcowych rejestracji i uwierzytelniania aplikacji dla chmur krajowych.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698020"
---
# <a name="national-clouds"></a>Chmury narodowe

Chmury krajowe to fizyczne izolowane wystąpienia platformy Azure. Te regiony platformy Azure zaprojektowano tak, aby upewnić się, że wymagania dotyczące miejsca zamieszkania, suwerenności i zgodności są honorowane w granicach geograficznych.

W tym chmurę globalną usługa Azure Active Directory (Azure AD) jest wdrażana w następujących chmurach narodowych:  

- Platforma Azure dla instytucji rządowych
- Azure (Niemcy)
- Azure w Chinach — 21Vianet

Chmury narodowe są unikatowe i oddzielnym środowiskiem z globalnego systemu Azure. Ważne jest, aby pamiętać o najważniejszych różnicach podczas tworzenia aplikacji dla tych środowisk. Różnice obejmują rejestrowanie aplikacji, uzyskiwanie tokenów i Konfigurowanie punktów końcowych.

## <a name="app-registration-endpoints"></a>Punkty końcowe rejestracji aplikacji

Dla każdej z chmur krajowych istnieje osobna Azure Portal. Aby zintegrować aplikacje z platformą tożsamości firmy Microsoft w chmurze krajowej, należy zarejestrować aplikację osobno w każdym Azure Portal, który jest specyficzny dla środowiska.

W poniższej tabeli przedstawiono podstawowe adresy URL dla punktów końcowych usługi Azure AD używanych do rejestrowania aplikacji dla każdej chmury krajowej.

| Chmura krajowa | Punkt końcowy portalu usługi Azure AD |
|----------------|--------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://portal.azure.us` |
| Azure AD (Niemcy) | `https://portal.microsoftazure.de` |
| Usługa Azure AD Chiny obsługiwana przez firmę 21Vianet | `https://portal.azure.cn` |
| Azure AD (usługa globalna) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Punkty końcowe uwierzytelniania usługi Azure AD

Wszystkie chmury narodowe uwierzytelniają użytkowników oddzielnie w poszczególnych środowiskach i mają oddzielne punkty końcowe uwierzytelniania.

W poniższej tabeli przedstawiono podstawowe adresy URL dla punktów końcowych usługi Azure AD używanych do uzyskiwania tokenów dla każdej chmury krajowej.

| Chmura krajowa | Punkt końcowy uwierzytelniania usługi Azure AD |
|----------------|-------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://login.microsoftonline.us` |
| Azure AD (Niemcy)| `https://login.microsoftonline.de` |
| Usługa Azure AD Chiny obsługiwana przez firmę 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (usługa globalna)| `https://login.microsoftonline.com` |

Możesz tworzyć żądania do autoryzacji usługi Azure AD lub punktów końcowych tokenu przy użyciu odpowiedniego podstawowego adresu URL specyficznego dla regionu. Na przykład w przypadku platformy Azure (Niemcy):

  - Wspólny punkt końcowy autoryzacji jest `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Wspólny punkt końcowy tokenu jest `https://login.microsoftonline.de/common/oauth2/token`.

W przypadku aplikacji z jedną dzierżawą Zastąp wartość "Common" w poprzednich adresach URL IDENTYFIKATORem dzierżawy lub nazwą. Może to być na przykład `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Interfejs API Microsoft Graph

Aby dowiedzieć się, jak wywołać interfejsy API Microsoft Graph w środowisku chmury krajowej, przejdź do [Microsoft Graph w obszarze wdrożenia w chmurze krajowej](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Niektóre usługi i funkcje, które znajdują się w określonych regionach usługi globalnej, mogą nie być dostępne we wszystkich chmurach narodowych. Aby dowiedzieć się, jakie usługi są dostępne, przejdź do pozycji [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Aby dowiedzieć się, jak utworzyć aplikację za pomocą platformy tożsamości firmy Microsoft, postępuj zgodnie z [samouczkiem Microsoft Authentication Library (MSAL)](msal-national-cloud.md). W tej aplikacji zostanie zalogowany użytkownik i otrzymasz token dostępu w celu wywołania interfejsu API Microsoft Graph.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure — Chiny](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
- [Podstawowe informacje dotyczące uwierzytelniania usługi Azure AD](authentication-scenarios.md)
