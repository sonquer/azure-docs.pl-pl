---
title: Żądaj oświadczeń niestandardowych (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak żądać oświadczeń niestandardowych.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085609"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Instrukcje: żądania oświadczeń niestandardowych przy użyciu MSAL dla systemów iOS i macOS

Program OpenID Connect Connect umożliwia opcjonalne żądanie powrotu pojedynczych oświadczeń z punktu końcowego UserInfo i/lub identyfikatora. Żądanie oświadczeń jest reprezentowane jako obiekt JSON, który zawiera listę żądanych oświadczeń. Aby uzyskać więcej informacji, zobacz [OpenID Connect Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) .

Biblioteka Microsoft Authentication Library (MSAL) dla systemów iOS i macOS umożliwia żądanie określonych oświadczeń w scenariuszach pozyskiwania tokenów interaktywnych i dyskretnych. Robi to za pomocą `claimsRequest` parametru.

Istnieje wiele scenariuszy, w których jest to wymaganie. Na przykład:

- Żądanie oświadczeń poza standardowym zestawem dla aplikacji.
- Żądanie określonych kombinacji standardowych oświadczeń, których nie można określić za pomocą zakresów dla aplikacji. Na przykład, jeśli token dostępu zostanie odrzucony z powodu braku oświadczeń, aplikacja może zażądać brakujących oświadczeń przy użyciu MSAL.

> [!NOTE]
> MSAL pomija pamięć podręczną tokenów dostępu za każdym razem, gdy żądanie oświadczeń jest określone. Należy podać parametr `claimsRequest` tylko wtedy, gdy potrzebne są dodatkowe oświadczenia (w przeciwieństwie do tego samego parametru `claimsRequest` w każdym wywołaniu interfejsu API MSAL).

`claimsRequest` można określić w `MSALSilentTokenParameters` i `MSALInteractiveTokenParameters`:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` można utworzyć na podstawie reprezentacji NSString żądania oświadczeń JSON. 

Cel-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Adres

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Można ją także zmodyfikować, żądając dodatkowych określonych oświadczeń:

Cel-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Adres

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` powinna być następnie ustawiona w parametrach tokenów i udostępniana jednemu z interfejsów API pozyskiwania tokenów MSAL:

Cel-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Adres

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)
