---
title: Wprowadzenie do interfejsu API raportowania usługi Azure AD | Microsoft Docs
description: Jak rozpocząć pracę z interfejsem API raportowania Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: c841195cacd8c8d7698f69435572587bf04c3d67
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008355"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Wprowadzenie do interfejsu API raportowania Azure Active Directory

Azure Active Directory oferuje różne [raporty](overview-reports.md)zawierające przydatne informacje dotyczące aplikacji, takich jak systemy Siem, Inspekcja i narzędzia analizy biznesowej. 

Za pomocą interfejsu API Microsoft Graph dla raportów usługi Azure AD można uzyskać programistyczny dostęp do danych za pośrednictwem zestawu interfejsów API opartych na interfejsie REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera omówienie interfejsu API raportowania, w tym sposoby uzyskiwania do niego dostępu.

Jeśli występują problemy, zobacz [jak uzyskać pomoc techniczną dotyczącą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do interfejsu API raportowania, z interwencją użytkownika lub bez niego, należy wykonać następujące działania:

1. Przypisywanie ról (czytelnik zabezpieczeń, administrator zabezpieczeń, Administrator globalny)
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbierz ustawienia konfiguracji

Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API 

Punkt końcowy Microsoft Graph interfejsu API dla dzienników inspekcji jest `https://graph.microsoft.com/beta/auditLogs/directoryAudits`, a punkt końcowy interfejsu API Microsoft Graph dla logowań jest `https://graph.microsoft.com/beta/auditLogs/signIns`. Aby uzyskać więcej informacji, zobacz Dokumentacja [interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) i [Dokumentacja interfejsu API logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Ponadto można użyć [interfejsu API wykrywania ryzyka ochrony tożsamości](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) , aby uzyskać programowy dostęp do wykrywania zabezpieczeń przy użyciu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  **Https:\/\/graph.windows.net\/\<nazwa dzierżawcy\>\/raportów**\/punkt końcowy jest przestarzały. Użyj nowych punktów końcowych interfejsu API opisanych powyżej, aby programowo uzyskać dostęp do raportów o działaniach i zabezpieczeniach.
  
## <a name="apis-with-graph-explorer"></a>Interfejsy API z Eksploratorem grafów

Aby sprawdzić dane dotyczące logowania i inspekcji, można użyć [Eksploratora MSGraph](https://developer.microsoft.com/graph/graph-explorer) . Upewnij się, że logujesz się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika programu Graph Explorer, a następnie ustaw wartość **AuditLog. Read. All** i **Directory. Read. All** Permissions dla Twojej dzierżawy, jak pokazano.   

![Eksplorator programu Graph](./media/concept-reporting-api/graph-explorer.png)

![Modyfikuj interfejs użytkownika uprawnień](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Używanie certyfikatów do uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD 

Jeśli planujesz pobrać dane raportowania bez interwencji użytkownika, użyj interfejsu API raportowania usługi Azure AD z certyfikatami.

Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Następne kroki

 * [Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania](howto-configure-prerequisites-for-reporting-api.md) 
 * [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](tutorial-access-api-with-certificates.md)
 * [Rozwiązywanie problemów z błędami w interfejsie API raportowania usługi Azure AD](troubleshoot-graph-api.md)


