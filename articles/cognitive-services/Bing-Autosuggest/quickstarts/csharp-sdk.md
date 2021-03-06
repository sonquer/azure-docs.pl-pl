---
title: 'Szybki Start: automatyczne sugerowanie Binga Biblioteka kliencka dla platformy .NET | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta automatyczne sugerowanie Bing dla platformy .NET i uzyskaj sugestie dotyczące wyszukiwania na podstawie częściowych ciągów zapytań.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473927"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Szybki Start: automatyczne sugerowanie Binga Biblioteka kliencka dla platformy .NET

Wprowadzenie do biblioteki klienta automatyczne sugerowanie Bing dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj automatyczne sugerowanie Bing biblioteki klienta dla platformy .NET, aby uzyskać sugestie dotyczące wyszukiwania na podstawie częściowych ciągów zapytań.

[Dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [biblioteki kodu źródłowego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [pakietu (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [przykładowego kodu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-azure-resource"></a>Tworzenie zasobu platformy Azure

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

>[!NOTE]
> Punkty końcowe dla zasobów nieprzeznaczonych dla wersji próbnej utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Przy użyciu klucza i punktu końcowego z utworzonego zasobu Utwórz dwa zmienne środowiskowe do uwierzytelnienia:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` — klucz zasobu do uwierzytelniania żądań.
* `AUTOSUGGEST_ENDPOINT` — punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie wyglądać następująco: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Skorzystaj z instrukcji dotyczących systemu operacyjnego.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edytuj `.bash_profile`i Dodaj zmienną środowiskową:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia `dotnet new`, aby utworzyć nową aplikację konsolową o nazwie `bing-autosuggest-quickstart`. To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: *program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące dyrektywy `using`:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

W klasie `Program` Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

W metodzie `Main` aplikacji Dodaj następujące wywołania metody, które zostaną zdefiniowane później.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj automatyczne sugerowanie Bingą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta automatyczne sugerowanie Bing dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wyślij żądanie automatycznego sugerowania](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza automatyczne sugerowanie Bing o nazwie `AUTOSUGGEST_SUBSCRIPTION_KEY`i jeden dla punktu końcowego o nazwie `AUTOSUGGEST_ENDPOINT`.


W nowej metodzie asynchronicznej Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) .

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Wyślij żądanie automatycznego sugerowania

W tej samej metodzie Użyj metody [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta, aby wysłać zapytanie do usługi Bing. Następnie można wykonać iterację w odpowiedzi na [sugestie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) i wydrukować pierwszą sugestię.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą polecenia `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bing Autosuggest tutorial (Samouczek dotyczący automatycznego sugerowania Bing)](../tutorials/autosuggest.md)

## <a name="see-also"></a>Zobacz także

- [Czym jest funkcja automatycznego sugerowania Bing?](../get-suggested-search-terms.md)
- [Informacje o automatyczne sugerowanie Bing dotnet](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
