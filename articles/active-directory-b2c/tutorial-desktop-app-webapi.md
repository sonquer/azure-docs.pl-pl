---
title: 'Samouczek: udzielanie dostępu do internetowego interfejsu API platformy Node. js z aplikacji klasycznej'
description: Samouczek dotyczący sposobu używania Active Directory B2C do ochrony interfejsu API sieci Web Node. js i wywoływania go z aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a72c45f80d03cda7b176c421cc5498dd16c242ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850041"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywołać interfejs API sieci Web w środowisku Node. js chronionych przez program Azure Active Directory B2C (Azure AD B2C) z aplikacji klasycznej Windows Presentation Foundation (WPF), a także chronić ją przez Azure AD B2C.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i wymagania wstępne w [samouczku: uwierzytelnianie użytkowników w natywnym kliencie klasycznym](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze **zakresy** dla zakresu `demo.read`, który ma być używany w późniejszym kroku podczas konfigurowania aplikacji klasycznej. Pełna wartość zakresu jest podobna do `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z natywnej aplikacji klienckiej, należy przyznać zarejestrowanej natywnej aplikacji klienckiej uprawnienia do internetowego interfejsu API, który został zarejestrowany w Azure AD B2C.

W samouczku wymagań wstępnych zarejestrowano natywną aplikację kliencką o nazwie *nativeapp1*. Poniższe kroki umożliwiają skonfigurowanie natywnej rejestracji aplikacji przy użyciu zakresów interfejsu API, które zostały uwidocznione dla *webapi1* w poprzedniej sekcji. Dzięki temu aplikacja klasyczna może uzyskać token dostępu z Azure AD B2C, z którego korzysta interfejs API sieci Web w celu sprawdzenia i zapewnienia dostępu do zakresu zasobów. W dalszej części tego samouczka konfigurujesz i uruchamiasz zarówno przykład kodu aplikacji klasycznych, jak i interfejsu API sieci Web.

#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *nativeapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Kliknij przycisk **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz natywną aplikację kliencką, która powinna mieć dostęp do interfejsu API. Na przykład *nativeapp1*.
1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** .
1. Wybierz interfejs API, do którego ma zostać udzielony dostęp do natywnej aplikacji klienckiej. Na przykład *webapi1*.
1. W obszarze **uprawnienie**rozwiń pozycję **Demonstracja**, a następnie wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)** .
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów. Propagowanie uprawnień może potrwać kilka minut.

* * *

Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z klasycznej aplikacji WPF. Aplikacja klasyczna uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-samples"></a>Skonfiguruj przykłady

Teraz, gdy internetowy interfejs API jest zarejestrowany i masz dostępne zakresy i uprawnienia, skonfigurujesz przykłady aplikacji klasycznych i interfejsów API sieci Web do korzystania z dzierżawy Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aktualizowanie aplikacji klasycznej

W ramach wymagań wstępnych dotyczących tego artykułu zmodyfikowano [aplikację klasyczną WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) , aby umożliwić logowanie się przy użyciu przepływu użytkowników w dzierżawie Azure AD B2C. W tej sekcji zostanie zaktualizowana ta sama aplikacja, aby odwołać się do internetowego interfejsu API, który został zarejestrowany wcześniej, *webapi1*.

1. Otwórz rozwiązanie **Active-Directory-B2C-WPF** (`active-directory-b2c-wpf.sln`) w programie Visual Studio.
1. W projekcie **Active-Directory-B2C-WPF** otwórz plik *App.XAML.cs* i Znajdź następujące definicje zmiennych.
    1. Zastąp wartość zmiennej `ApiScopes` wartością zanotowaną wcześniej podczas definiowania **demonstracji. zakres odczytu** .
    1. Zastąp wartość zmiennej `ApiEndpoint` **identyfikatorem URI przekierowania** , który został zarejestrowany wcześniej podczas rejestrowania internetowego interfejsu API (na przykład *webapi1*) w dzierżawie.

    Oto przykład:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Pobierz i zaktualizuj przykład interfejsu API środowiska Node. js

Następnie Pobierz przykład kodu internetowego interfejsu API środowiska Node. js z usługi GitHub i skonfiguruj go tak, aby korzystał z internetowego interfejsu API, który został zarejestrowany w dzierżawie Azure AD B2C.

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Przykładowy internetowy interfejs API platformy Node.js korzysta z biblioteki Passport.js, aby umożliwić usłudze Azure AD B2C ochronę wywołań do interfejsu API.

1. Otwórz plik `index.js`.
1. Zaktualizuj te definicje zmiennych przy użyciu następujących wartości. Zmień `<web-API-application-ID>` na **Identyfikator aplikacji (klienta)** internetowego interfejsu API, który został zarejestrowany wcześniej (*webapi1*). Zmień `<your-b2c-tenant>` na nazwę dzierżawy Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Ponieważ interfejs API jest uruchamiany lokalnie, zaktualizuj ścieżkę w marszrucie dla metody GET do `/` zamiast lokalizacji `/hello`aplikacji demonstracyjnej:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Uruchamianie przykładów

### <a name="run-the-nodejs-web-api"></a>Uruchamianie interfejsu API sieci Web środowiska Node. js

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Uruchom następujące polecenia:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Uruchamianie aplikacji klasycznej

1. Otwórz rozwiązanie **active-directory-b2c-wpf** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby uruchomić aplikację klasyczną.
3. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](tutorial-desktop-app.md).
4. Wybierz przycisk **wywoływania interfejsu API** .

Aplikacja klasyczna wysyła żądanie do uruchomionego lokalnie interfejsu API sieci Web i po sprawdzeniu prawidłowego tokenu dostępu wyświetla nazwę wyświetlaną zalogowanego użytkownika.

![Wyświetlana nazwa wyświetlana w górnym okienku aplikacji klasycznej WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Aplikacja klasyczna chroniona przez Azure AD B2C wywołuje lokalnie uruchomiony interfejs API sieci Web, który jest również chroniony przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawców tożsamości do aplikacji w Azure Active Directory B2C](tutorial-add-identity-providers.md)
