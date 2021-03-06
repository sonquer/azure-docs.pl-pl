---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do rozruchu sprężynowego przy użyciu konfiguracji aplikacji platformy Azure
description: Dodawanie flag funkcji do sprężynowych aplikacji rozruchowych i zarządzanie nimi przy użyciu konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/21/2020
ms.author: lcozzens
ms.openlocfilehash: 4438851ef7ea015060926075f46822de877b85b3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766435"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji z rozruchem wiosny

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji sieci Web ze sprężyną rozruchu, aby utworzyć kompleksową implementację zarządzania funkcjami. Za pomocą usługi konfiguracji aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany.

Biblioteki zarządzania funkcją rozruchu sprężynowego umożliwiają rozbudowanie platformy z kompleksową obsługą flag funkcji. Te biblioteki **nie** są zależne od żadnej biblioteki platformy Azure. Zapewniają one bezproblemowe integrację z konfiguracją aplikacji za pomocą dostawcy konfiguracji rozruchu sprężynowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Obsługiwany zestaw [SDK języka Java Development Kit](https://docs.microsoft.com/java/azure/jdk) z wersją 8.
- System [Apache Maven](https://maven.apache.org/download.cgi) w wersji 3,0 lub nowszej.

## <a name="create-an-app-configuration-instance"></a>Tworzenie wystąpienia konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Menedżer funkcji** >  **+ Dodaj** , aby dodać flagę funkcji o nazwie `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie beta](media/add-beta-feature-flag.png)

    Dla tej pory pozostaw `label` niezdefiniowane.

## <a name="create-a-spring-boot-app"></a>Tworzenie aplikacji Spring Boot

Użyj [sprężyny Initializr](https://start.spring.io/) , aby utworzyć nowy projekt z rozruchem sprężyny.

1. Przejdź do <https://start.spring.io/>.

2. Określ następujące opcje:

   - Wygeneruj projekt **Maven** z użyciem języka **Java**.
   - Określ wersję **rozruchu sprężynowego** , która jest równa lub większa niż 2,0.
   - Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.  W tym artykule są wykorzystywane `com.example` i `demo`.
   - Dodaj zależność **sieci Web sprężyny** .

3. Po określeniu poprzednich opcji wybierz pozycję **Generuj projekt**. Po wyświetleniu monitu Pobierz projekt na komputer lokalny.

## <a name="add-feature-management"></a>Dodawanie funkcji zarządzania funkcjami

1. Po wyodrębnieniu plików w systemie lokalnym aplikacja do rozruchowego sprężyny jest gotowa do edycji. Zlokalizuj *pliku pom. XML* w katalogu głównym aplikacji.

1. Otwórz plik *pliku pom. XML* w edytorze tekstów i Dodaj następujący tekst do listy `<dependencies>`.:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.2.1</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.1</version>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Istnieje Biblioteka zarządzania funkcją niezawierającą sieci Web, która nie jest zależna od sprężyny sieci Web. Różnice można znaleźć w [dokumentacji](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management) usługi GitHub.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Przejdź do katalogu `resources` aplikacji i Otwórz `bootstrap.properties`.  Jeśli plik nie istnieje, utwórz go. Dodaj następujący wiersz do pliku.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. W portalu konfiguracji aplikacji dla swojego magazynu konfiguracji wybierz `Access keys` z paska bocznego. Wybierz kartę klucze tylko do odczytu. Skopiuj wartość podstawowych parametrów połączenia.

1. Dodaj podstawowe parametry połączenia jako zmienną środowiskową przy użyciu nazwy zmiennej `APP_CONFIGURATION_CONNECTION_STRING`.

1. Otwórz główny plik Java aplikacji i dodaj element `@EnableConfigurationProperties`, aby włączyć tę funkcję.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```
1. Utwórz nowy plik Java o nazwie *MessageProperties.java* w katalogu pakietów swojej aplikacji.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Utwórz nowy plik Java o nazwie *HelloController.java* w katalogu pakietów swojej aplikacji. 

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")

    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("Beta"));
            return "welcome";
        }
    }
    ```

1. Utwórz nowy plik HTML o nazwie *Welcome. html* w katalogu templates aplikacji.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

6. Utwórz nowy folder o nazwie CSS w obszarze `static` i wewnątrz niego nowy plik CSS o nazwie *Main. css*.

    ```css
    html {
    position: relative;
    min-height: 100%;
    }
    body {
    margin-bottom: 60px;
    }
    .footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 60px;
    line-height: 60px;
    background-color: #f5f5f5;
    }

    body > .container {
    padding: 60px 15px 0;
    }

    .footer > .container {
    padding-right: 15px;
    padding-left: 15px;
    }

    code {
    font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację do rozruchu ze sprężyną przy użyciu Maven i uruchom ją.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Otwórz okno przeglądarki i przejdź do domyślnego adresu URL dla lokalnie hostowanej aplikacji sieci Web: `https://localhost:8080`.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. W portalu konfiguracji aplikacji wybierz pozycję **Menedżer funkcji**, a następnie Zmień stan klucza **beta** **na:**

    | Klucz | Stan |
    |---|---|
    | Beta | Włączone |

1. Odśwież stronę przeglądarki, aby zobaczyć nowe ustawienia konfiguracji.

    ![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji, który będzie używany do zarządzania funkcjami w aplikacji sieci Web ze sprężyną rozruchu za pośrednictwem [bibliotek zarządzania](https://go.microsoft.com/fwlink/?linkid=2074664)funkcjami.

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
- [Zarządzaj flagami funkcji](./manage-feature-flags.md).
- [Korzystanie z flag funkcji w aplikacji podstawowe rozruchowej](./use-feature-flags-spring-boot.md).
