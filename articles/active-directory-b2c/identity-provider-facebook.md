---
title: Skonfiguruj konto i zaloguj się przy użyciu konta w usłudze Facebook
titleSuffix: Azure AD B2C
description: Zalogować się do klientów przy użyciu kont usługi Facebook w swoich aplikacjach za pomocą Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db6421167442e8aa3f0e7994dda35a8a5e475eb7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847578"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą konta w usłudze Facebook przy użyciu Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Tworzenie aplikacji w usłudze Facebook

Aby użyć konta w serwisie Facebook jako [dostawcy tożsamości](authorization-code-flow.md) w programie Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w swojej dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz zarejestrować się w [https://www.facebook.com/](https://www.facebook.com/).

1. Zaloguj się do usługi [Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta w serwisie Facebook.
1. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper w serwisie Facebook. W tym celu wybierz pozycję **Rozpocznij** w prawym górnym rogu strony, zaakceptuj zasady serwisu Facebook i wykonaj kroki rejestracji.
1. Wybierz pozycję **Moje aplikacje** , a następnie **Utwórz aplikację**.
1. Wprowadź **nazwę wyświetlaną** i prawidłowy **kontaktowy adres e-mail**.
1. Wybierz pozycję **Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebook i ukończenia kontroli zabezpieczeń w trybie online.
1. Wybierz pozycję **ustawienia** > **podstawowa**.
1. Wybierz **kategorię**, na przykład `Business and Pages`. Ta wartość jest wymagana przez serwis Facebook, ale nie jest używana do Azure AD B2C.
1. W dolnej części strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
1. W polu **adres URL witryny**wprowadź `https://your-tenant-name.b2clogin.com/` zastępowanie `your-tenant-name` nazwą dzierżawy. Wprowadź adres URL **zasad zachowania poufności informacji**, na przykład `http://www.contoso.com`. Adres URL zasad to strona, którą przechowujesz, aby zapewnić informacje o ochronie prywatności dla aplikacji.
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony skopiuj wartość **Identyfikator aplikacji**.
1. Wybierz pozycję **Pokaż** i skopiuj wartość **wpisu tajnego aplikacji**. Oba te elementy umożliwiają skonfigurowanie usługi Facebook jako dostawcy tożsamości w dzierżawie. **Wpis tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
1. Wybierz znak plus obok pozycji **produkty**, a następnie wybierz pozycję **Konfiguruj** w obszarze **Logowanie do serwisu Facebook**.
1. W obszarze **Logowanie do serwisu Facebook**wybierz pozycję **Ustawienia**.
1. W obszarze **prawidłowe identyfikatory URI przekierowań OAuth**wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Zastąp `your-tenant-name` nazwą dzierżawy. Wybierz pozycję **Zapisz zmiany** w dolnej części strony.
1. Aby udostępnić aplikację w serwisie Facebook Azure AD B2C, wybierz selektor stanu w prawym górnym rogu strony i **Włącz go,** aby udostępnić aplikację jako publiczną, a następnie wybierz pozycję **Przełącz tryb**.  W tym momencie stan powinien ulec zmianie z **opracowywania** na na **żywo**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta w serwisie Facebook jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
1. Wprowadź **nazwę**. Na przykład w *serwisie Facebook*.
1. W polu **Identyfikator klienta**wprowadź identyfikator aplikacji w usłudze Facebook, który został utworzony wcześniej.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny aplikacji.
1. Wybierz pozycję **Zapisz**.
