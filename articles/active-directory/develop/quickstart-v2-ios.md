---
title: Microsoft Identity platform iOS i macOS — Szybki Start | Azure
description: Dowiedz się, jak logować użytkowników i Microsoft Graph zapytań w aplikacji dla systemu iOS lub macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: c9eb8b3d909313470ee9febdc5b1c37eea834b08
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484047"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Szybki Start: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji dla systemu iOS lub macOS

Ten przewodnik Szybki Start zawiera przykładowy kod, który pokazuje, jak Natywna aplikacja dla systemu iOS lub macOS może używać platformy tożsamości firmy Microsoft do logowania się do osobistych, służbowych kont służbowych, uzyskiwania tokenu dostępu i wywoływania interfejsu API Microsoft Graph.

Ten przewodnik Szybki Start dotyczy zarówno aplikacji iOS, jak i macOS. Niektóre kroki są wymagane tylko w przypadku aplikacji dla systemu iOS. Te kroki wywołują, że są tylko dla systemu iOS.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * XCode 10 +
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opcja 1: Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację,
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://aka.ms/MobileAppReg) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji podczas logowania lub wyrażania zgody na aplikację.
>      - Pomiń inne konfiguracje na tej stronie.
>      - Wybierz pozycję `Register`.
> 1. W sekcji **Zarządzanie** wybierz pozycję `Authentication` > `Add Platform` > `iOS`.
>      - Wprowadź ***Identyfikator pakietu*** dla swojej aplikacji. Identyfikator pakietu jest tylko unikatowym ciągiem, który jednoznacznie identyfikuje aplikację, na przykład `com.<yourname>.identitysample.MSALMacOS`. Zanotuj wartość, której używasz.
>      - Należy pamiętać, że konfiguracja systemu iOS ma również zastosowanie do aplikacji macOS.
> 1. Wybierz pozycję `Configure` i Zapisz szczegóły ***konfiguracji MSAL*** w dalszej części tego przewodnika Szybki Start.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI przekierowania zgodny z brokerem uwierzytelniania.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-ios/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-sample-project"></a>Krok 2. Pobieranie przykładowego projektu

- [Pobierz przykład kodu dla systemu iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Pobierz przykładowy kod dla macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Krok 3. Instalacja zależności

W oknie terminalu przejdź do folderu z pobranym przykładem kodu i uruchom `pod install`, aby zainstalować najnowszą bibliotekę MSAL.

#### <a name="step-4-configure-your-project"></a>Krok 4. Konfigurowanie projektu

> [!div renderon="docs"]
> W przypadku wybrania opcji 1 powyżej można pominąć te kroki. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **plik viewcontroller. Swift** i Zastąp wiersz zaczynający się od "Let kClientID" następującym fragmentem kodu. Pamiętaj, aby zaktualizować wartość `kClientID` IDENTYFIKATORem klienta zapisanym podczas rejestrowania aplikacji w portalu wcześniej w przewodniku szybki start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Edytuj **plik viewcontroller. Swift** i Zastąp wiersz zaczynający się od "Let kAuthority" następującym fragmentem kodu:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Edytuj **plik viewcontroller. Swift** i Zastąp wiersz zaczynający się od "Let kGraphEndpoint" następującym fragmentem kodu:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Otwórz ustawienia projektu. W sekcji **tożsamość** wprowadź **Identyfikator pakietu** wprowadzony w portalu.
> 1. Tylko dla systemu iOS kliknij prawym przyciskiem myszy pozycję **info. plist** i wybierz polecenie **Otwórz jako** > **kod źródłowy**.
> 1. Tylko dla systemu iOS w węźle głównym DICT Zastąp `CFBundleURLSchemes` ***identyfikatorem pakietu*** wprowadzonym w portalu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Kompilacja & uruchomić aplikację.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **plik viewcontroller. Swift** i Zastąp wiersz zaczynający się od "Let kClientID" następującym fragmentem kodu. Pamiętaj, aby zaktualizować wartość `kClientID` przy użyciu clientID zapisanego podczas rejestrowania aplikacji w portalu wcześniej w tym przewodniku szybki start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Jeśli tworzysz aplikację dla [chmur narodowych usługi Azure AD](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints), Zastąp wiersz zaczynający się od "Let kGraphEndpoint" i "Let kAuthority" z prawidłowymi punktami końcowymi. W przypadku dostępu globalnego należy użyć wartości domyślnych:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Inne punkty końcowe są udokumentowane [tutaj](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Na przykład w celu uruchomienia przewodnika Szybki Start z usługą Azure AD (Niemcy) Użyj następujących sposobów:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Otwórz ustawienia projektu. W sekcji **tożsamość** wprowadź **Identyfikator pakietu** wprowadzony w portalu.
> 1. Tylko dla systemu iOS kliknij prawym przyciskiem myszy pozycję **info. plist** i wybierz polecenie **Otwórz jako** > **kod źródłowy**.
> 1. Tylko dla systemu iOS w węźle głównym DICT Zastąp `Enter_the_bundle_Id_Here` ***identyfikatorem pakietu*** użytym w portalu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Kompilacja & uruchomić aplikację. 

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się więcej na temat tego przewodnika Szybki start.

### <a name="get-msal"></a>Pobierz MSAL

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Możesz dodać bibliotekę MSAL do aplikacji w następujący sposób:

```
$ vi Podfile

```
Dodaj następujący element do tego plik podfile (z celem projektu):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Uruchom polecenie instalacji CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Zainicjuj MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```swift
import MSAL
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Gdzie: ||
> |---------|---------|
> | `clientId` | Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie *portal.azure.com* |
> | `authority` | Punkt końcowy platformy tożsamości firmy Microsoft. W większości przypadków będzie to adres *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Identyfikator URI przekierowania aplikacji. Można przekazać element "Nil", aby używał wartości domyślnej, lub niestandardowego identyfikatora URI przekierowania. |

### <a name="for-ios-only-additional-app-requirements"></a>Tylko dla systemu iOS, dodatkowe wymagania dotyczące aplikacji

Twoja aplikacja musi mieć również następujące elementy w `AppDelegate`. Dzięki temu MSAL SDK obsługuje odpowiedzi tokenów z aplikacji brokera uwierzytelniania podczas uwierzytelniania.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> W systemie iOS 13 +, jeśli przyjmie `UISceneDelegate` zamiast `UIApplicationDelegate`, zamiast tego należy umieścić ten kod w `scene:openURLContexts:` wywołaniu zwrotnym (zobacz [dokumentację firmy Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> W przypadku zapewnienia zgodności zarówno UISceneDelegate, jak i UIApplicationDelegate ze starszymi wersjami systemu iOS, wywołanie zwrotne MSAL musi zostać umieszczone w obu miejscach.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Na koniec aplikacja musi mieć wpis `LSApplicationQueriesSchemes` w ***info. plist*** obok `CFBundleURLTypes`. Przykład zawiera. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Użytkownicy logowania & tokeny żądania

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: uzyskiwanie tokenu interaktywnie

Niektóre sytuacje wymagają, aby użytkownicy mogli korzystać z platformy tożsamości firmy Microsoft. W takich przypadkach może być wymagane, aby użytkownik końcowy mógł wybrać swoje konto, wprowadzić swoje poświadczenia lub wyrazić zgodę na uprawnienia aplikacji. Na przykład: 

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik resetuje hasło, musi wprowadzić swoje poświadczenia 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane jest uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Uzyskaj token dostępu dyskretnie

Aplikacje nie powinny wymagać od użytkowników logowania się za każdym razem, gdy żądają tokenu. Jeśli użytkownik jest już zalogowany, ta metoda zezwala aplikacjom na żądanie tokenów dyskretnie. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |
> | `account` | Konto, dla którego jest żądany token. Ten przewodnik Szybki Start dotyczy aplikacji o pojedynczym koncie. Jeśli chcesz utworzyć aplikację obejmującą wiele kont, musisz zdefiniować logikę, aby zidentyfikować konto, które będzie używane dla żądań tokenów przy użyciu `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Następne kroki

Wypróbuj Samouczek systemu iOS, aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia aplikacji, w tym pełnym objaśnieniem tego samouczka Szybki Start.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się, jak utworzyć aplikację używaną w tym przewodniku Szybki Start

> [!div class="nextstepaction"]
> [Call Graph API iOS tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph dla systemu iOS)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
