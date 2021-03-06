---
title: Microsoft Identity platform Windows platformy UWP — Szybki Start | Azure
description: Dowiedz się, w jaki sposób aplikacja platforma uniwersalna systemu Windows (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 86401e0a827d1941b2d183d8c17371ba915c81ae
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063700"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Szybki start: wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji platformy UWP

Ten przewodnik Szybki Start zawiera przykładowy kod, który demonstruje, jak aplikacja platforma uniwersalna systemu Windows (platformy UWP) może logować użytkowników z kontami osobistymi lub kontami służbowymi, uzyskać token dostępu i wywoływać interfejs API Microsoft Graph.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> [!div renderon="docs" class="sxs-lookup"]
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i kliknij pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://aka.ms/MobileAppReg) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `UWP-App-calling-MsGraph`.
>      - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)** .
>      - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
> 1. W sekcji **identyfikatory URI przekierowania** | **sugerowane identyfikatory URI przekierowań dla klientów publicznych (Mobile, Desktop)** zaznacz pozycję **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Wybierz pozycję **Zapisz**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI przekierowania jako **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-uwp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2. Pobieranie projektu programu Visual Studio

 - [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3. Konfigurowanie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
1. Otwórz projekt w programie Visual Studio. Może zostać wyświetlony monit o zainstalowanie zestawu SDK platformy UWP. W takim przypadku Zaakceptuj.
1. Edytuj **MainPage.XAML.cs** i Zastąp wartości pola `ClientId`:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.    

> [!div renderon="docs"]
> Gdzie:
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
>
> > [!TIP]
> > Aby znaleźć wartość *identyfikatora aplikacji*, przejdź do sekcji **Przegląd** w portalu

#### <a name="step-4-run-your-application"></a>Krok 4. Uruchamianie aplikacji

Jeśli chcesz wypróbować ten przewodnik Szybki Start na komputerze z systemem Windows:

1. Na pasku narzędzi programu Visual Studio wybierz odpowiednią platformę (prawdopodobnie **x64** lub **x86**, a nie ARM).
   > Zwróć uwagę, że urządzenie docelowe zmienia się z *urządzenia* na *maszynę lokalną*
1. Wybierz Debuguj | **Uruchom bez debugowania**

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera więcej informacji na temat przewodnika Szybki start.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) to biblioteka służąca do logowania użytkowników i żądania tokenów zabezpieczających. Tokeny zabezpieczające są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft dla deweloperów. Bibliotekę MSAL można zainstalować, uruchamiając następujące polecenie w *Konsoli menedżera pakietów* programu Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```csharp
using Microsoft.Identity.Client;
```

Następnie MSAL jest inicjowany przy użyciu następującego kodu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

> |Gdzie: ||
> |---------|---------|
> | `ClientId` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

MSAL ma dwie metody uzyskiwania tokenów w aplikacji platformy UWP: `AcquireTokenInteractive` i `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Niektóre sytuacje wymagają wymuszenia, aby użytkownicy mogli korzystać z punktu końcowego platformy tożsamości firmy Microsoft za pomocą okna podręcznego, aby zweryfikować swoje poświadczenia lub wyrazić zgodę. Oto niektóre przykłady:

- Logowanie do aplikacji przez użytkowników po raz pierwszy
- Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Gdy aplikacja żąda dostępu do zasobu, użytkownik musi wyrazić zgodę na
- Gdy wymagane jest uwierzytelnianie dwuetapowe.

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API. |

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Użyj metody `AcquireTokenSilent`, aby uzyskać tokeny umożliwiające dostęp do chronionych zasobów po początkowej `AcquireTokenInteractive` metodzie. Nie chcesz wymagać od użytkownika weryfikowania poświadczeń za każdym razem, gdy potrzebują dostępu do zasobu. Większość czasu, w którym ma być uzyskiwany token i odnowienie bez żadnej interakcji z użytkownikiem

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy, takie jak `{ "user.read" }` dla programu Microsoft Graph lub `{ "api://<Application ID>/access_as_user" }` dla niestandardowych interfejsów internetowych API |
> | `firstAccount` | Określa pierwsze konto użytkownika w pamięci podręcznej (MSAL obsługuje wielu użytkowników w jednej aplikacji) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dotyczący aplikacji klasycznych systemu Windows, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym podręczniku Szybki start.

> [!div class="nextstepaction"]
> [UWP — Call Graph API tutorial (Samouczek dotyczący platformy UWP i wywoływania interfejsu API programu Graph)](tutorial-v2-windows-uwp.md)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
