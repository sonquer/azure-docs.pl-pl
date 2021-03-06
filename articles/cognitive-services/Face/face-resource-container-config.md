---
title: Konfigurowanie kontenerów — Front
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera czołowego jest konfigurowane przy użyciu argumentów polecenia `docker run`. Wymagane i opcjonalne ustawienia.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 3c78c9eb85c3a8be236be5c3a24bd877db204b6c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167978"
---
# <a name="configure-face-docker-containers"></a>Konfigurowanie kontenerów platformy Docker

Środowisko uruchomieniowe kontenera **czołowego** jest konfigurowane przy użyciu argumentów polecenia `docker run`. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla ustawienia konfiguracji [`Billing`](#billing-configuration-setting) .

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** zarządzania zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _Cognitive Services_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie z etykietą `Endpoint`

Pamiętaj _, aby dodać kierowanie_ do punktu końcowego, jak pokazano w przykładzie. 

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Ustawienia konfiguracji CloudAI

Ustawienia konfiguracji w `CloudAI` sekcji Podaj opcji specyficznych dla kontenera unikatowe dla kontenera. Następujące ustawienia i obiekty, które są obsługiwane dla kontenera twarzy w `CloudAI` sekcji

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Obiekt | Scenariusz magazynowania używane przez kontener twarzy. Aby uzyskać więcej informacji na temat scenariuszy magazynowania i skojarzone ustawienia `Storage` obiektu, zobacz [scenariuszu miejsca do magazynowania](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Ustawienia scenariusza magazynu

Kontener twarzy przechowuje, obiektów blob, pamięci podręcznej, metadane i dane w kolejce, w zależności od tego, co jest magazynowana. Na przykład indeksy szkolenia i wyniki dla osób dużych grup są przechowywane jako dane obiektów blob. Kontener twarzy zawiera dwa scenariusze innego magazynu podczas interakcji z i przechowywania tych typów danych:

* Pamięć  
  Wszystkie cztery typy danych są przechowywane w pamięci. Nie są rozpowszechniane, nie są one trwałych. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera zostanie zniszczony.  
  Jest to scenariusz magazynowania domyślnego kontenera twarzy.
* Azure  
  Kontener twarzy korzysta z usługi Azure Storage i Azure Cosmos DB, aby rozdystrybuować te cztery rodzaje danych z magazynu trwałego. Dane obiektów blob i kolejek jest obsługiwane przez usługę Azure Storage. Metadane i dane pamięci podręcznej są obsługiwane przez Azure Cosmos DB. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera pozostaje przechowywanych w usłudze Azure Storage i Azure Cosmos DB.  
  Zasoby używane przez scenariusza magazynu platformy Azure mają następujące wymagania dodatkowe
  * Zasób usługi Azure Storage, należy użyć StorageV2 rodzaj konta
  * Zasób Azure Cosmos DB musi używać interfejsu API Azure Cosmos DB dla MongoDB

Scenariusze usługi storage i skojarzone ustawienia są zarządzane przez `Storage` obiekt w obszarze `CloudAI` sekcji konfiguracji. Następujące ustawienia konfiguracji są dostępne w `Storage` obiektu:

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `StorageScenario` | Ciąg | Scenariusz magazynowania obsługiwane przez kontener. Dostępne są następujące wartości<br/>`Memory` — Wartość domyślna. Kontener korzysta z magazynu nietrwałe, nierozpowszechniony i w pamięci, do użycia z jednym węzłem, tymczasowy. Jeśli kontener jest zatrzymana lub usunięta, jest niszczony magazynu dla tego kontenera.<br/>`Azure` -Container używa zasobów platformy Azure do przechowywania. Jeśli kontener jest zatrzymana lub usunięta, jest trwały magazyn dla tego kontenera.|
| `ConnectionStringOfAzureStorage` | Ciąg | Parametry połączenia dla zasobu usługi Azure Storage używany przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |
| `ConnectionStringOfCosmosMongo` | Ciąg | Parametry połączenia bazy danych MongoDB dla zasobu usługi Azure Cosmos DB używane przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |

Na przykład następujące polecenie Określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scenariusz magazynowania są obsługiwane niezależnie od instalacji i wyjściowych instaluje. Można określić kombinacji tych funkcji do jednego kontenera. Na przykład następujące polecenie definiuje instalowania powiązania platformy Docker, aby `D:\Output` folderu na komputerze hosta jako dane wyjściowe instalacji, następnie tworzy wystąpienie kontenera z twarzy obraz kontenera, zapisywanie plików dziennika w formacie JSON w celu instalacji danych wyjściowych. Polecenie również określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontenery ze stroną nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](face-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Opcjonalne| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery kroju nie używają tego.|
|Opcjonalne| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartością domyślną jest `/output`. Jest to Lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](face-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\`jako znaku kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność**argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego zasobu `Face` na stronie klucze `Face` platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie Przegląd `Face` platformy Azure.| Zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów usługi Azure `Cognitive Services`. 

## <a name="face-container-docker-examples"></a>Przykłady platformy Docker kontenera

Poniższe przykłady platformy Docker dotyczą kontenera rozpoznawania. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](face-how-to-install-containers.md)
