---
title: Serializacja niestandardowa pamięci podręcznej tokenów (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zdeserializować pamięć podręczną tokenów dla MSAL dla języka Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704395"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializacja niestandardowego buforu tokenów w MSAL dla języka Python

W MSAL Python, pamięć podręczna tokenów w pamięci, która będzie trwała w czasie trwania sesji aplikacji, jest udostępniana domyślnie podczas tworzenia wystąpienia elementu [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serializacja pamięci podręcznej tokenów, tak aby różne sesje aplikacji mogły uzyskać do niej dostęp, nie jest ona dostępna. Dzieje się tak, ponieważ MSAL Python może być używany w typach aplikacji, które nie mają dostępu do systemu plików — takich jak aplikacje sieci Web. Aby mieć trwałą pamięć podręczną tokenów w aplikacji MSAL w języku Python, musisz zapewnić serializowaną niestandardową pamięć podręczną tokenów.

Strategie serializowania pamięci podręcznej tokenów różnią się w zależności od tego, czy piszesz publiczną aplikację kliencką (Desktop), czy poufną aplikację kliencką (aplikację sieci Web, internetowy interfejs API lub aplikację demona).

## <a name="token-cache-for-a-public-client-application"></a>Pamięć podręczna tokenów dla publicznej aplikacji klienckiej

Publiczne aplikacje klienckie działają na urządzeniu użytkownika i zarządzają tokenami dla pojedynczego użytkownika. W takim przypadku można serializować całą pamięć podręczną do pliku. Pamiętaj, aby zapewnić blokowanie plików, jeśli aplikacja lub inna aplikacja mogą uzyskać dostęp do pamięci podręcznej współbieżnie. Aby zapoznać się z prostym przykładem, jak serializować pamięć podręczną tokenów do pliku bez blokowania, zobacz przykład w dokumentacji dotyczącej klasy [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczna tokenów dla aplikacji sieci Web (poufne aplikacje klienckie)

W przypadku Web Apps lub interfejsów API sieci Web można użyć sesji lub pamięci podręcznej Redis lub bazy danych do przechowywania pamięci podręcznej tokenów. Powinna istnieć jedna pamięć podręczna tokenów dla każdego użytkownika (na konto), dlatego należy serializować pamięć podręczną tokenów na konto.

## <a name="next-steps"></a>Następne kroki

Zobacz [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) , aby zapoznać się z przykładem użycia pamięci podręcznej tokenów dla aplikacji sieci Web systemu Windows lub Linux lub interfejsu API sieci Web. Przykład dotyczy aplikacji sieci Web, która wywołuje interfejs API Microsoft Graph.
