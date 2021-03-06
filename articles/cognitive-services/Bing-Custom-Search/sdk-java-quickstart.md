---
title: 'Szybki Start: wyszukiwanie niestandardowe Binga Biblioteka kliencka dla języka Java'
description: Rozpocznij pracę z biblioteką klienta wyszukiwanie niestandardowe Bing dla środowiska Java, żądając wyników wyszukiwania z wystąpienia wyszukiwanie niestandardowe Bing w tym przewodniku Szybki Start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1060cceb9241d9c1c490f7e7f12490c734b0a78d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384557"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Szybki Start: wyszukiwanie niestandardowe Binga Biblioteka kliencka dla języka Java

Wprowadzenie do biblioteki klienta wyszukiwanie niestandardowe Bing dla środowiska Java. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych, bezpłatnych funkcji wyszukiwania w usłudze AD dla interesujących Cię tematów.

Użyj biblioteki klienta wyszukiwanie niestandardowe Bing dla języka Java, aby:

* Znajdź wyniki wyszukiwania w sieci Web w wystąpieniu wyszukiwanie niestandardowe Bing. 

[Dokumentacja referencyjna](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [artefakt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [przykłady](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.
* Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](quick-start.md) .

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-bing-custom-search-azure-resource"></a>Tworzenie zasobu wyszukiwanie niestandardowe Bing platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla wyszukiwanie niestandardowe Bing przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

> [!TIP]
> Jeśli nie korzystasz z programu Gradle, możesz znaleźć szczegóły biblioteki klienta dla innych menedżerów zależności w [repozytorium centralnym Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie w katalogu roboczym. To polecenie tworzy podstawowe pliki kompilacji dla Gradle, w tym plik *Build. Gradle. KTS* , który jest używany w czasie wykonywania w celu skonfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

## <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w tej konfiguracji kompilacji. Upewnij się, że Biblioteka kliencka została uwzględniona w `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *BingCustomSearchSample. Java*. Otwórz go i Dodaj następujące instrukcje `import`:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Utwórz klasę o nazwie `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

W klasie Utwórz metodę `main` i zmienną dla klucza zasobu. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, Zamknij i Otwórz ponownie edytor, środowisko IDE lub powłokę, na której jest uruchomiona, aby uzyskać dostęp do zmiennej. Metody zostaną zdefiniowane później.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Model obiektów

Klient wyszukiwanie niestandardowe Bing jest obiektem [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) , który jest tworzony przy użyciu metody [Authenticate ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) obiektu [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) . Żądanie wyszukiwania można wysłać przy użyciu metody [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta.

Odpowiedź interfejsu API to obiekt [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) zawierający informacje o zapytaniu wyszukiwania i wynikach wyszukiwania.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta wyszukiwanie niestandardowe Bing dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Pobierz wyniki wyszukiwania z niestandardowego wystąpienia wyszukiwania](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Główna Metoda powinna zawierać obiekt [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) , który pobiera klucz i wywołuje jego `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Pobierz wyniki wyszukiwania z niestandardowego wystąpienia wyszukiwania

Użyj funkcji [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) klienta, aby wysłać zapytanie wyszukiwania do wystąpienia niestandardowego. Ustaw `withCustomConfig` na identyfikator konfiguracji niestandardowej lub wartość domyślną, aby `1`. Po otrzymaniu odpowiedzi z interfejsu API Sprawdź, czy nie znaleziono żadnych wyników wyszukiwania. Jeśli tak, Pobierz pierwszy wynik wyszukiwania, wywołując funkcję `webPages().value().get()` odpowiedzi i wypisujemy nazwę wyniku oraz adres URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Utwórz aplikację za pomocą następującego polecenia w katalogu głównym projektu:

```console
gradle build
```

Uruchom aplikację z celem `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
