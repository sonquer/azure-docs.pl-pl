---
title: Dostosuj przeglądarki & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować środowisko MSAL w przeglądarce dla systemu iOS/macOS w celu logowania użytkowników.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085903"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Instrukcje: Dostosowywanie przeglądarek i WebViews dla systemu iOS/macOS

Do uwierzytelniania interakcyjnego jest wymagana przeglądarka sieci Web. W systemie iOS Biblioteka Microsoft Authentication Library (MSAL) domyślnie używa domyślnej przeglądarki sieci Web (która może pojawić się na swojej aplikacji) w celu uwierzytelniania interakcyjnego w celu logowania użytkowników. Korzystanie z przeglądarki systemu umożliwia udostępnianie stanu logowania jednokrotnego (SSO) innym aplikacjom i aplikacjom sieci Web.

Możesz zmienić środowisko, dostosowując konfigurację do innych opcji wyświetlania zawartości sieci Web, takich jak:

Tylko dla systemu iOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Dla systemów iOS i macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL dla macOS obsługuje tylko `WKWebView`.

## <a name="system-browsers"></a>Przeglądarki systemu

W przypadku systemów iOS, `ASWebAuthenticationSession`, `SFAuthenticationSession`i `SFSafariViewController` są uważane za przeglądarki systemowe. Ogólnie rzecz biorąc, przeglądarki systemu udostępniają pliki cookie i inne dane witryny sieci Web za pomocą aplikacji Przeglądarka Safari.

Domyślnie program MSAL dynamicznie wykrywa wersję systemu iOS i wybiera zalecaną przeglądarkę systemową dostępną w tej wersji. W systemie iOS 12 zostanie `ASWebAuthenticationSession`. 

| Wersja | Przeglądarka sieci Web |
|:-------------:|:-------------:|
| System iOS 12 + | ASWebAuthenticationSession |
| System iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Deweloperzy mogą również wybrać inną przeglądarkę systemową dla aplikacji MSAL:

- `SFAuthenticationSession` jest wersją `ASWebAuthenticationSession`systemu iOS 11.
- `SFSafariViewController` jest bardziej ogólnego przeznaczenia i udostępnia interfejs do przeglądania sieci Web i może służyć również do celów logowania. W systemach iOS 9 i 10 pliki cookie i inne dane witryny sieci Web są udostępniane za pomocą przeglądarki Safari — ale nie w systemie iOS 11 i nowszych.

## <a name="in-app-browser"></a>Przeglądarka w aplikacji

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) to przeglądarka w aplikacji, która wyświetla zawartość sieci Web. Nie udostępnia ona plików cookie lub danych witryny sieci Web innym wystąpieniem **WKWebView** lub przeglądarce Safari. WKWebView to międzyplatformowa przeglądarka, która jest dostępna zarówno dla systemu iOS, jak i macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Udostępnianie plików cookie i zalogowanie jednokrotne (SSO)

Przeglądarka, z której korzystasz, ma wpływ na środowisko logowania jednokrotnego ze względu na sposób udostępniania plików cookie. W poniższej tabeli zestawiono obsługę logowania jednokrotnego dla każdej przeglądarki.

| Technologia    | Typ przeglądarki  | dostępność systemu iOS | dostępność macOS | Udostępnia pliki cookie i inne dane  | Dostępność MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 i w górę | macOS 10,15 i up | Yes | tylko system iOS | wystąpienia z/Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 i w górę | Nie dotyczy | Yes | tylko system iOS |  wystąpienia z/Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 i w górę | Nie dotyczy | Nie | tylko system iOS | Nie * *
| **SFSafariViewController** | System | iOS10 | Nie dotyczy | Yes | tylko system iOS |  wystąpienia z/Safari
| **WKWebView**  | W aplikacji | iOS8 i w górę | macOS 10,10 i up | Nie | iOS i macOS | Nie * *

\* * Aby logowanie jednokrotne działało, tokeny muszą być udostępniane między aplikacjami. Wymaga to pamięci podręcznej tokenu lub aplikacji brokera, takiej jak Microsoft Authenticator dla systemu iOS.

## <a name="change-the-default-browser-for-the-request"></a>Zmień domyślną przeglądarkę dla żądania

Możesz użyć przeglądarki w aplikacji lub konkretnej przeglądarki systemu w zależności od wymagań środowiska użytkownika, zmieniając następującą właściwość w `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Zmień na żądanie interaktywne

Każde żądanie można skonfigurować w celu przesłaniania domyślnej przeglądarki, zmieniając właściwość `MSALInteractiveTokenParameters.webviewParameters.webviewType` przed przekazaniem jej do interfejsu API `acquireTokenWithParameters:completionBlock:`.

Ponadto MSAL obsługuje przekazywanie w niestandardowym `WKWebView` przez ustawienie właściwości `MSALInteractiveTokenParameters.webviewParameters.customWebView`.

Na przykład:

Obiektowy C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Jeśli używasz niestandardowego widoku WebView, powiadomienia są używane do wskazywania stanu wyświetlanej zawartości sieci Web, na przykład:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opcje

Wszystkie typy przeglądarek sieci Web obsługiwane przez MSAL są zadeklarowane w [wyliczeniu MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)
