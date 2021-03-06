---
title: Ciągłe wdrażanie dla usługi Azure Functions
description: Użyj funkcji ciągłego wdrażania Azure App Service, aby opublikować swoje funkcje.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230903"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions

Możesz użyć Azure Functions do ciągłego wdrażania kodu przy użyciu [integracji kontroli źródła](functions-deployment-technologies.md#source-control). Integracja kontroli źródła umożliwia przepływ pracy, w którym aktualizacja kodu wyzwala wdrożenie na platformie Azure. Jeśli dopiero zaczynasz Azure Functions, Rozpocznij od przejrzenia [Azure Functions przegląd](functions-overview.md).

Ciągłe wdrażanie jest dobrym rozwiązaniem dla projektów, w których integrujesz wiele i częste udziały. W przypadku korzystania z ciągłego wdrażania należy zachować pojedyncze Źródło prawdy dla kodu, dzięki czemu zespoły mogą łatwo współpracować. Wdrożenie ciągłe można skonfigurować w Azure Functions z następujących lokalizacji kodu źródłowego:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednostką wdrożenia funkcji na platformie Azure jest aplikacja funkcji. Wszystkie funkcje w aplikacji funkcji są wdrażane w tym samym czasie. Po włączeniu ciągłego wdrażania dostęp do kodu funkcji w Azure Portal jest skonfigurowany jako *tylko do odczytu* , ponieważ źródło prawdy jest ustawione na wartość w innym miejscu.

## <a name="requirements-for-continuous-deployment"></a>Wymagania dotyczące ciągłego wdrażania

Aby ciągłe wdrożenie powiodło się, struktura katalogów musi być zgodna z podstawową strukturą folderów, która Azure Functions oczekiwać.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Ciągłe wdrażanie nie jest jeszcze obsługiwane w przypadku aplikacji systemu Linux działających w ramach planu zużycia. 

## <a name="credentials"></a>Konfigurowanie ciągłego wdrażania

Aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji, wykonaj te kroki. Kroki pokazują integrację z repozytorium GitHub, ale podobne kroki dotyczą Azure Repos lub innych repozytoriów kodu źródłowego.

1. W aplikacji funkcji w [Azure Portal](https://portal.azure.com)wybierz pozycję **funkcje platformy** > **centrum wdrażania**.

    ![Otwórz centrum wdrażania](./media/functions-continuous-deployment/platform-features.png)

2. W **centrum wdrażania**wybierz pozycję **GitHub**, a następnie wybierz pozycję **Autoryzuj**. Jeśli masz już autoryzowany serwis GitHub, wybierz pozycję **Kontynuuj**. 

    ![Azure App Service centrum wdrażania](./media/functions-continuous-deployment/github.png)

3. W witrynie GitHub wybierz przycisk **Autoryzuj AzureAppService** . 

    ![Autoryzuj Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    W obszarze **centrum wdrażania** w Azure Portal wybierz pozycję **Kontynuuj**.

4. Wybierz jednego z następujących dostawców kompilacji:

    * **App Service Build Service**: najlepiej, gdy nie potrzebujesz kompilacji lub jeśli potrzebujesz kompilacji ogólnej.
    * **Azure Pipelines (wersja zapoznawcza)** : Najlepsza, gdy potrzebna jest większa kontrola nad kompilacją. Ten dostawca jest obecnie w wersji zapoznawczej.

    ![Wybierz dostawcę kompilacji](./media/functions-continuous-deployment/build.png)

5. Skonfiguruj informacje specyficzne dla określonej opcji kontroli źródła. W przypadku usługi GitHub należy wprowadzić lub wybrać wartości dla **organizacji**, **repozytorium**i **gałęzi**. Wartości są zależne od lokalizacji kodu. Następnie wybierz pozycję **Kontynuuj**.

    ![Konfigurowanie usługi GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Przejrzyj wszystkie szczegóły, a następnie wybierz pozycję **Zakończ** , aby ukończyć konfigurację wdrożenia.

    ![Podsumowanie](./media/functions-continuous-deployment/summary.png)

Po zakończeniu procesu cały kod z określonego źródła zostanie wdrożony w aplikacji. W tym momencie zmiany w źródle wdrożenia wyzwalają wdrożenie tych zmian w aplikacji funkcji na platformie Azure.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Przenieś istniejące funkcje do ciągłego wdrażania

Jeśli masz już zapisaną funkcję w [Azure Portal](https://portal.azure.com) i chcesz pobrać zawartość aplikacji przed przełączeniem do ciągłego wdrażania, przejdź na kartę **Przegląd** aplikacji funkcji. Wybierz przycisk **Pobierz zawartość aplikacji** .

![Pobierz zawartość aplikacji](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji nie można już edytować plików źródłowych w portalu funkcji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
