---
title: Wdrażanie modeli ml do aplikacji Azure Functions (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model w aplikacji Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 29c91cf14413a11804de82eeaf08d628b125d76a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471945"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Wdróż model uczenia maszynowego w Azure Functions (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model na podstawie Azure Machine Learning jako aplikacji funkcji w programie Azure Functions.

> [!IMPORTANT]
> Chociaż obie Azure Machine Learning i Azure Functions są ogólnie dostępne, możliwość spakowania modelu z usługi Machine Learning Service for Functions jest dostępna w wersji zapoznawczej.

Za pomocą Azure Machine Learning można tworzyć obrazy platformy Docker z przeszkolonych modeli uczenia maszynowego. Azure Machine Learning teraz zawiera funkcję wersji zapoznawczej umożliwiającą tworzenie tych modeli uczenia maszynowego w aplikacjach funkcji, które można [wdrożyć w Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

    > [!IMPORTANT]
    > W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
    >
    > * `ws` — Azure Machine Learning obszaru roboczego.
    > * `model` — zarejestrowany model, który zostanie wdrożony.
    > * `inference_config` — konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Deploying Models with Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano podstawowe elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.
    >
    > Domyślnie podczas tworzenia pakietów dla funkcji dane wejściowe są traktowane jako tekst. Jeśli interesuje się zużywanie nieprzetworzonych bajtów danych wejściowych (na przykład dla wyzwalaczy obiektów BLOB), należy użyć [AMLRequest do akceptowania danych pierwotnych](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania odwołuje się do skryptu wejścia i innych zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure Functions należy użyć obiektu [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Należy pamiętać, że w przypadku definiowania środowiska niestandardowego należy dodać polecenie "Azure-default" z wersją > = 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web. Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania w usłudze Functions nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Zainstaluj pakiet SDK wersji zapoznawczej dla obsługi funkcji

Aby skompilować pakiety dla Azure Functions, należy zainstalować pakiet SDK w wersji zapoznawczej.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure Functions, Użyj usługi [Azure. contrib. Functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) lub określonej funkcji pakietu dla wyzwalacza, którego chcesz używać. Poniższy fragment kodu pokazuje, jak utworzyć nowy pakiet z wyzwalaczem obiektu BLOB z konfiguracji modelu i wnioskowania:

> [!NOTE]
> W fragmencie kodu założono, że `model` zawiera zarejestrowany model i że `inference_config` zawiera konfigurację środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Gdy `show_output=True`, zostanie wyświetlony wynik procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego. Po skompilowaniu obrazu zostanie wyświetlona lokalizacja w Azure Container Registry. Zwrócona Lokalizacja ma format `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Pakowanie dla funkcji obecnie obsługuje wyzwalacze HTTP, wyzwalacze obiektów blob i wyzwalacze usługi Service Bus. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [Azure Functions powiązania](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ są używane podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj poniższego polecenia, aby uzyskać poświadczenia logowania dla Azure Container Registry zawierającej obraz. Zastąp `<myacr>` wartością zwróconą wcześniej z `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Dane wyjściowe tego polecenia są podobne do następującego dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Zapisz wartość dla __nazwy użytkownika__ i jednego z __haseł__.

1. Jeśli nie masz jeszcze grupy zasobów lub planu usługi App Service do wdrożenia usługi, następujące polecenia pokazują, jak utworzyć obie:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    W tym przykładzie jest używana warstwa cenowa systemu _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > Obrazy utworzone przez Azure Machine Learning używają systemu Linux, dlatego należy użyć parametru `--is-linux`.

1. Utwórz konto magazynu, które ma być używane na potrzeby magazynu zadań sieci Web, i Pobierz jego parametry połączenia. Zastąp `<webjobStorage>` nazwą, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Aby utworzyć aplikację funkcji, użyj następującego polecenia. Zastąp `<app-name>` nazwą, której chcesz użyć. Zastąp `<acrinstance>` i `<imagename>` wartościami zwracanymi `package.location` wcześniej. Zastąp wartość Zamień `<webjobStorage>` nazwą konta magazynu z poprzedniego kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > W tym momencie aplikacja funkcji została utworzona. Jednak ponieważ nie podano parametrów połączenia dla wyzwalacza obiektu BLOB lub poświadczeń do Azure Container Registry zawierającego obraz, aplikacja funkcji jest nieaktywna. W następnych krokach podano parametry połączenia oraz informacje o uwierzytelnianiu dla rejestru kontenerów. 

1. Utwórz konto magazynu, które ma być używane na potrzeby magazynu wyzwalacza obiektów blob, i Pobierz jego parametry połączenia. Zastąp `<triggerStorage>` nazwą, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Zarejestruj te parametry połączenia, aby udostępnić aplikację funkcji. Będziemy używać go później, gdy podasz pytania o `<triggerConnectionString>`

1. Utwórz kontenery dla danych wejściowych i wyjściowych na koncie magazynu. Zastąp `<triggerConnectionString>` ciągiem połączenia zwróconym wcześniej:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Aby skojarzyć parametry połączenia wyzwalacza z aplikacją funkcji, użyj następującego polecenia. Zastąp `<app-name>` nazwą aplikacji funkcji. Zastąp `<triggerConnectionString>` ciągiem połączenia zwróconym wcześniej:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Musisz pobrać tag skojarzony z utworzonym kontenerem przy użyciu poniższego polecenia. Zastąp `<username>` nazwą użytkownika zwróconą wcześniej z rejestru kontenerów:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Zapisz zwracaną wartość, która zostanie użyta jako `imagetag` w następnym kroku.

1. Aby zapewnić aplikacji funkcji z poświadczeniami, które są potrzebne do uzyskania dostępu do rejestru kontenerów, użyj następującego polecenia. Zastąp `<app-name>` nazwą, której chcesz użyć. Zastąp `<acrinstance>` i `<imagetag>` wartościami z polecenia AZ CLI Call w poprzednim kroku. Zamień `<username>` i `<password>` na pobrane wcześniej informacje logowania ACR:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

W tym momencie aplikacja funkcji rozpocznie ładowanie obrazu.

> [!IMPORTANT]
> Załadowanie obrazu może potrwać kilka minut. Postęp można monitorować przy użyciu witryny Azure Portal.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować aplikację funkcji w dokumentacji [funkcji](/azure/azure-functions/functions-create-function-linux-custom-image) .
* Dowiedz się więcej o usłudze BLOB Storage wyzwala [powiązania usługi Azure Blob Storage](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Wdróż model do Azure App Service](how-to-deploy-app-service.md).
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
