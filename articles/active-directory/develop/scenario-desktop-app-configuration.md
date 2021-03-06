---
title: Konfigurowanie aplikacji klasycznych, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować kod aplikacji klasycznej, która wywołuje interfejsy API sieci Web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2a5a66f2801804b354dd8945ea7d8eb565e82cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702219"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web: Konfiguracja kodu

Teraz, po utworzeniu aplikacji, dowiesz się, jak skonfigurować kod przy użyciu współrzędnych aplikacji.

## <a name="microsoft-authentication-libraries"></a>Biblioteki uwierzytelniania firmy Microsoft

Następujące biblioteki uwierzytelniania firmy Microsoft (MSALs) obsługują aplikacje klasyczne.

  Biblioteka uwierzytelniania firmy Microsoft | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługuje tworzenie aplikacji klasycznych na wielu platformach, takich jak Linux, Windows i macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Obsługuje tworzenie aplikacji klasycznych na wielu platformach.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługuje tworzenie aplikacji klasycznych na wielu platformach.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Obsługuje aplikacje klasyczne, które działają tylko na macOS.

## <a name="public-client-application"></a>Publiczna aplikacja kliencka

Z punktu widzenia kodu aplikacje klasyczne są publicznymi aplikacjami klienckimi. Konfiguracja będzie się nieco różnić w zależności od tego, czy używasz uwierzytelniania interaktywnego, czy nie.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Należy skompilować i manipulować `IPublicClientApplication`MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Wyłącznie przez kod

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej i u użytkowników w Microsoft Azure chmurze publicznej z kontem służbowym lub konto Microsoft osobistym.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Jeśli zamierzasz korzystać z uwierzytelniania interakcyjnego lub przepływu kodu urządzenia, jak pokazano wcześniej, użyj modyfikatora `.WithRedirectUri`.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej z obiektu konfiguracji, który może być uzupełniony programowo lub odczytywany z pliku konfiguracyjnego.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Kompilowanie aplikacji można rozbudować przez dodanie szeregu modyfikatorów. Na przykład jeśli aplikacja ma być aplikacją wielodostępną w chmurze krajowej, na przykład w przypadku instytucji rządowych USA, można napisać:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET zawiera również modyfikator dla Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Na koniec Jeśli chcesz uzyskać tokeny dla dzierżawy usługi Azure Active Directory (Azure AD) B2C, określ swoją dzierżawę, jak pokazano w poniższym fragmencie kodu:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Dowiedz się więcej

Aby dowiedzieć się więcej o konfigurowaniu aplikacji klasycznej MSAL.NET:

- Aby uzyskać listę wszystkich modyfikatorów dostępnych na `PublicClientApplicationBuilder`, zobacz dokumentację referencyjną [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Aby uzyskać opis wszystkich opcji uwidocznionych w `PublicClientApplicationOptions`, zobacz [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) w dokumentacji referencyjnej.

### <a name="complete-example-with-configuration-options"></a>Ukończ przykład z opcjami konfiguracji

Załóżmy, że Aplikacja konsolowa platformy .NET Core ma następujący plik konfiguracji `appsettings.json`:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

W tym pliku znajduje się mały kod, który można odczytać za pomocą. Struktura konfiguracji podana w sieci:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Teraz, aby utworzyć aplikację, Napisz następujący kod:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Przed wywołaniem metody `.Build()` można zastąpić konfigurację z wywołaniami metod `.WithXXX`, tak jak wcześniej.

# <a name="javatabjava"></a>[Java](#tab/java)

Oto Klasa używana w przykładach programistycznych MSAL Java do konfigurowania przykładów: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej i u użytkowników w Microsoft Azure chmurze publicznej z kontem służbowym lub konto Microsoft osobistym.

### <a name="quick-configuration"></a>Szybka konfiguracja

Cel-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Adres
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Kompilowanie aplikacji można rozbudować przez dodanie szeregu modyfikatorów. Na przykład jeśli aplikacja ma być aplikacją wielodostępną w chmurze krajowej, na przykład w przypadku instytucji rządowych USA, można napisać:

Cel-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Adres

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji klasycznej](scenario-desktop-acquire-token.md)
