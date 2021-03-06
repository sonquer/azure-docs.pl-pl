---
title: Tworzenie aplikacji demona, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9465b77d03fb3dbb668bd40bf2c7e6ded6891a05
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962065"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenariusz: aplikacja demona, która wywołuje interfejsy API sieci Web

Dowiedz się, co należy zrobić, aby utworzyć aplikację demona, która wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Przegląd

Aplikacja może uzyskać token wywołujący internetowy interfejs API w imieniu samego siebie (nie w imieniu użytkownika). Ten scenariusz jest przydatny w przypadku aplikacji demona. Używa on standardowego udzielenia [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0.

![Aplikacje demona](./media/scenario-daemon-app/daemon-app.svg)

Oto przykładowe przypadki użycia dla aplikacji demonów:

- Aplikacje sieci Web, które są używane do aprowizacji lub administrowania użytkownikami lub wykonywania procesów wsadowych w katalogu
- Aplikacje klasyczne (takie jak usługi systemu Windows w ramach procesów systemu Windows lub demonów w systemie Linux) wykonujące zadania wsadowe lub usługę systemu operacyjnego działającą w tle
- Interfejsy API sieci Web, które wymagają manipulowania katalogami, a nie określonymi użytkownikami

Istnieje inny typowy przypadek, w którym aplikacje niedemonowe korzystają z poświadczeń klienta: nawet jeśli działają w imieniu użytkowników, muszą uzyskać dostęp do internetowego interfejsu API lub zasobu w ramach swojej tożsamości ze względów technicznych. Przykładem jest dostęp do wpisów tajnych w Azure Key Vault lub bazy danych Azure SQL Database dla pamięci podręcznej.

Aplikacje, które uzyskują token dla własnych tożsamości:

- są poufnymi aplikacjami klienckimi. Te aplikacje, z uwzględnieniem, że uzyskują dostęp do zasobów niezależnie od użytkowników, muszą udowodnić swoją tożsamość. Są one również raczej poufnymi aplikacjami. Muszą zostać zatwierdzone przez administratorów dzierżawy usługi Azure Active Directory (Azure AD).
- Zarejestrowano wpis tajny (hasło aplikacji lub certyfikat) w usłudze Azure AD. Ten wpis tajny jest przesyłany podczas wywołania usługi Azure AD w celu uzyskania tokenu.

## <a name="specifics"></a>Szczegółowych informacji

> [!IMPORTANT]
>
> - Użytkownicy nie mogą korzystać z aplikacji demona. Aplikacja demona wymaga własnej tożsamości. Ten typ aplikacji żąda tokenu dostępu przy użyciu tożsamości aplikacji i prezentuje identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i identyfikator URI aplikacji do usługi Azure AD. Po pomyślnym uwierzytelnieniu demon otrzymuje token dostępu (i token odświeżania) z punktu końcowego platformy tożsamości firmy Microsoft. Token ten jest następnie używany do wywoływania internetowego interfejsu API (i jest odświeżany w razie potrzeby).
> - Ze względu na to, że użytkownicy nie mogą korzystać z aplikacji demona, nie jest możliwe przeprowadzenie zgody Przed zarejestrowaniem aplikacji należy skonfigurować wszystkie wymagane uprawnienia interfejsu API. Kod aplikacji po prostu żąda uprawnień zdefiniowanych statycznie. Oznacza to również, że aplikacje demona nie obsługują przyrostowej zgody.

Dla deweloperów kompleksowe środowisko dla tego scenariusza ma następujące aspekty:

- Aplikacje demonów mogą funkcjonować tylko w dzierżawach usługi Azure AD. Nie ma sensu Tworzenie aplikacji demona próbującej manipulować kontami osobistymi firmy Microsoft. Jeśli jesteś deweloperem aplikacji biznesowych (LOB), utworzysz aplikację demona w dzierżawie. Jeśli jesteś niezależnym dostawcą oprogramowania, możesz chcieć utworzyć aplikację demona wielodostępna. Każdy administrator dzierżawy będzie musiał wyrazić zgodę.
- Podczas [rejestracji aplikacji](./scenario-daemon-app-registration.md)identyfikator URI odpowiedzi nie jest wymagany. Musisz udostępnić wpisy tajne lub certyfikaty lub potwierdzenia z usługą Azure AD. Musisz również zażądać uprawnień aplikacji i udzielić zgody administratorowi na korzystanie z tych uprawnień aplikacji.
- [Konfiguracja aplikacji](./scenario-daemon-app-configuration.md) wymaga podania poświadczeń klienta jako współużytkowanych z usługą Azure AD podczas rejestracji aplikacji.
- [Zakres](scenario-daemon-acquire-token.md#scopes-to-request) używany do uzyskiwania tokenu z przepływem poświadczeń klienta musi być zakresem statycznym.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — Rejestracja aplikacji](./scenario-daemon-app-registration.md)
