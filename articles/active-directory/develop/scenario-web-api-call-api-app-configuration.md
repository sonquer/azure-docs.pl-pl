---
title: Konfigurowanie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web (Konfiguracja kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834097"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Konfiguracja kodu

Po zarejestrowaniu internetowego interfejsu API można skonfigurować kod dla aplikacji.

Kod używany do konfigurowania internetowego interfejsu API, dzięki czemu wywołuje podrzędne interfejsy API sieci Web na podstawie kodu, który jest używany do ochrony internetowego interfejsu API. Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API: Konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod subskrybowany do OnTokenValidated

Na podstawie konfiguracji kodu dla wszelkich chronionych interfejsów API sieci Web należy zasubskrybować weryfikację tokenu okaziciela, który otrzymujesz po wywołaniu interfejsu API:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>W imieniu przepływu

Metoda AddAccountToCacheFromJwt () musi:

- Utworzenie wystąpienia biblioteki uwierzytelniania firmy Microsoft (MSAL) poufnej aplikacji klienckiej.
- Wywołaj `AcquireTokenOnBehalf` metody. To wywołanie wymienia token okaziciela, który został uzyskany przez klienta dla internetowego interfejsu API względem tokenu okaziciela dla tego samego użytkownika, ale ma interfejs API wywołujący podrzędny interfejs API.

### <a name="instantiate-a-confidential-client-application"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej

Ten przepływ jest dostępny tylko w poufnej przepływie klienta, dzięki czemu chroniony internetowy interfejs API udostępnia poświadczenia klienta (klucz tajny klienta lub certyfikat) do [klasy ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) za pomocą metody `WithClientSecret` lub `WithCertificate`.

![Lista metod IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Na koniec zamiast udowodnić swoją tożsamość za pośrednictwem klucza tajnego klienta lub certyfikatu, poufne aplikacje klienckie mogą udowodnić swoją tożsamość za pomocą potwierdzeń klientów.
Aby uzyskać więcej informacji na temat tego zaawansowanego scenariusza, zobacz [poufne potwierdzenia klientów](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Jak wywołać w imieniu

Należy wykonać wywołanie w imieniu (OBO), wywołując [metodę AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) w interfejsie `IConfidentialClientApplication`.

Klasa `UserAssertion` jest tworzona na podstawie tokenu nośnego, który jest odbierany przez internetowy interfejs API od własnych klientów. Istnieją [dwa konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Jeden, który pobiera token okaziciela sieci Web JSON (JWT)
* Jeden, który ma dowolny rodzaj potwierdzenia użytkownika, inny rodzaj tokenu zabezpieczającego, którego typ jest następnie określony w dodatkowym parametrze o nazwie `assertionType`

![UserAssertion — właściwości i metody](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

W rzeczywistości przepływ OBO jest często używany do uzyskiwania tokenu dla podrzędnego interfejsu API i przechowywania go w pamięci podręcznej tokenów użytkownika MSAL.NET. Można to zrobić, tak aby inne części interfejsu API sieci Web mogły później wywołać [zastąpień](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` w celu wywołania podrzędnych interfejsów API. To wywołanie ma wpływ na odświeżanie tokenów, w razie konieczności.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="javatabjava"></a>[Java](#tab/java)

Przepływ "w imieniu" (OBO) służy do uzyskania tokenu wywołującego podrzędny interfejs API sieci Web. W tym przepływie internetowy interfejs API odbiera token okaziciela z uprawnieniami delegowanymi przez użytkownika z aplikacji klienckiej, a następnie wymienia ten token dla innego tokenu dostępu, aby wywołać podrzędny interfejs API sieci Web.

Poniższy kod używa `SecurityContextHolder`u sprężynowej struktury zabezpieczeń w interfejsie API sieci Web w celu uzyskania zweryfikowanego tokenu okaziciela. Następnie używa biblioteki języka Java MSAL, aby uzyskać token dla interfejsu API podrzędnego przy użyciu wywołania `acquireToken` z `OnBehalfOfParameters`. MSAL buforuje token tak, aby kolejne wywołania interfejsu API mogły użyć `acquireTokenSilently`, aby uzyskać buforowany token.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Przepływ "w imieniu" (OBO) służy do uzyskania tokenu wywołującego podrzędny interfejs API sieci Web. W tym przepływie internetowy interfejs API odbiera token okaziciela z uprawnieniami delegowanymi przez użytkownika z aplikacji klienckiej, a następnie wymienia ten token dla innego tokenu dostępu, aby wywołać podrzędny interfejs API sieci Web.

Interfejs API sieci Web w języku Python będzie musiał użyć oprogramowania pośredniczącego w celu zweryfikowania tokenu okaziciela otrzymanego od klienta. Interfejs API sieci Web może następnie uzyskać token dostępu dla podrzędnego interfejsu API przy użyciu biblioteki języka Python MSAL, wywołując metodę [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Przykład pokazujący ten przepływ przy użyciu języka MSAL Python nie jest jeszcze dostępny.

---

Można także zobaczyć przykład implementacji przepływu OBO w [Node. js i Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokół)

Aby uzyskać więcej informacji na temat protokołu OBO, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji](scenario-web-api-call-api-acquire-token.md)
