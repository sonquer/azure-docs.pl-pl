---
title: Uzyskaj token do wywoływania internetowego interfejsu API (aplikacje jednostronicowe) — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (uzyskać token, aby wywołać interfejs API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: d5d48a2fc7aca184cf8b6e7761584a8800ca5151
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160070"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplikacja jednostronicowa: uzyskiwanie tokenu do wywoływania interfejsu API

Wzorcem uzyskiwania tokenów dla interfejsów API z MSAL. js jest najpierw próba żądania tokenu dyskretnego za pomocą metody `acquireTokenSilent`. Po wywołaniu tej metody Biblioteka najpierw sprawdza pamięć podręczną w magazynie przeglądarki, aby sprawdzić, czy istnieje prawidłowy token i zwraca go. Gdy w pamięci podręcznej nie ma prawidłowego tokenu, wysyła żądanie tokenu dyskretnego do Azure Active Directory (Azure AD) z ukrytego elementu iframe. Ta metoda umożliwia również bibliotece odnawianie tokenów. Aby uzyskać więcej informacji na temat sesji logowania jednokrotnego i okresu istnienia tokenu w usłudze Azure AD, zobacz [okresy istnienia tokenu](active-directory-configurable-token-lifetimes.md).

Żądania tokenu dyskretnego do usługi Azure AD mogą się nie powieść z przyczyn takich jak wygasła sesja usługi Azure AD lub zmiana hasła. W takim przypadku można wywołać jedną z metod interaktywnych (co spowoduje wyświetlenie monitu użytkownika) o uzyskanie tokenów:

* [Okno podręczne](#acquire-a-token-with-a-pop-up-window), przy użyciu `acquireTokenPopup`
* [Przekieruj](#acquire-a-token-with-a-redirect)przy użyciu `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Wybierz między podręcznym lub przekierowaniami

 W aplikacji nie można używać obu metod podręcznych i przekierowania. Wybór między podręcznym i przekierowaniami zależy od przepływu aplikacji:

* Jeśli nie chcesz, aby użytkownicy przechodzą poza główną stronę aplikacji podczas uwierzytelniania, zalecamy wyskakującą metodę. Ponieważ przekierowanie uwierzytelniania odbywa się w oknie podręcznym, stan głównej aplikacji jest zachowywany.

* Jeśli użytkownicy mają ograniczenia przeglądarki lub zasady, w których okna wyskakujące są wyłączone, można użyć metody redirect. Użyj metody redirect w przeglądarce Internet Explorer, ponieważ występują [znane problemy z wyskakującymi oknami w programie Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Można ustawić zakresy interfejsów API, które mają być uwzględniane przez token dostępu podczas kompilowania żądania tokenu dostępu. Należy zauważyć, że wszystkie żądane zakresy mogą nie zostać przyznane w tokenie dostępu. Jest to zależne od zgody użytkownika.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Uzyskiwanie tokenu za pomocą okna podręcznego

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy kod łączy opisany wcześniej wzorzec z metodami podręcznych czynności:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL zapewnia Interceptor HTTP, który automatycznie uzyskuje tokeny dostępu w trybie dyskretnym i dołącza je do żądań HTTP do interfejsów API.

W opcji konfiguracji `protectedResourceMap` można określić zakresy interfejsów API. `MsalInterceptor` będzie żądać tych zakresów podczas automatycznego uzyskiwania tokenów.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

W przypadku powodzenia i niepowodzenia pozyskiwania tokenów dyskretnych MSAL kątowy zapewnia wywołania zwrotne, które można subskrybować. Ważne jest również, aby pamiętać o anulowaniu subskrypcji.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternatywnie można jawnie uzyskać tokeny przy użyciu metod pozyskiwania tokenów, zgodnie z opisem w podstawowej bibliotece MSAL. js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Pozyskiwanie tokenu z przekierowaniem

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy wzorzec jest opisany wcześniej, ale przedstawia metodę Redirect, która umożliwia interaktywną pozyskiwanie tokenów. Należy zarejestrować wywołanie zwrotne przekierowania jak wspomniano wcześniej.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Żądaj opcjonalnych oświadczeń

Możesz użyć opcjonalnych oświadczeń do następujących celów:

- Uwzględnij dodatkowe oświadczenia w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu. 

Aby zażądać opcjonalnych oświadczeń w `IdToken`, można wysłać obiekt oświadczeń skonwertowanej do pola `claimsRequest` klasy `AuthenticationParameters.ts`.

```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Aby dowiedzieć się więcej, zobacz [opcjonalne oświadczenia](active-directory-optional-claims.md).

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Ten kod jest taki sam, jak opisano wcześniej.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-spa-call-api.md)
