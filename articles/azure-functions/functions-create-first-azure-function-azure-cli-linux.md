---
title: Tworzenie pierwszej funkcji w systemie Linux na platformie Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję hostowaną w systemie Linux na platformie Azure przy użyciu narzędzi wiersza polecenia, Azure Functions Core Tools i interfejsu CLI platformy Azure.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 972feedf880ed55210c8422094d5b26a85b31d5e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769408"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Szybki Start: Tworzenie pierwszej funkcji hostowanej w systemie Linux przy użyciu narzędzi wiersza polecenia

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.com/serverless) systemu Linux bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej. Hosting w systemie Linux wymaga [funkcji 2. x i nowszych](functions-versions.md). Funkcje bezserwerowe działają w [planie zużycia](functions-scale.md#consumption-plan).

W tym artykule Szybki start przedstawiono sposób użycia interfejsu wiersza polecenia platformy Azure w celu utworzenia pierwszej aplikacji funkcji działającej w systemie Linux. Kod funkcji jest tworzony lokalnie, a następnie wdrażany na platformie Azure za pomocą narzędzi [Azure Functions Core Tools](functions-run-local.md).

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux. W tym artykule opisano sposób tworzenia funkcji w języku JavaScript lub C#. Aby dowiedzieć się, jak tworzyć funkcje języka Python, zobacz [Tworzenie pierwszej funkcji języka Python za pomocą podstawowych narzędzi i interfejsu wiersza polecenia platformy Azure](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

+ Zainstaluj [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.6.666 lub nowszej.

+ Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

+ Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Tworzenie aplikacji funkcji systemu Linux na platformie Azure

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia bezserwerowe środowisko do wykonywania kodu funkcji. Umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów. Utwórz aplikację funkcji działającą w systemie Linux przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

W poniższym poleceniu zamiast symbolu zastępczego `<app_name>` użyj unikatowej nazwy aplikacji funkcji, a zamiast symbolu zastępczego `<storage_name>` użyj nazwy konta magazynu. `<app_name>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure. Należy również ustawić `<language>` środowisko uruchomieniowe dla aplikacji funkcji, z `dotnet` (C#), `node` (JavaScript/TypeScript) lub `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Po utworzeniu aplikacji funkcji zostanie wyświetlony następujący komunikat:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Teraz możesz opublikować swój projekt w nowej aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]