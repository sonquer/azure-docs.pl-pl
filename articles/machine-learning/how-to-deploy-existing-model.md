---
title: Używanie i wdrażanie istniejących modeli
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać Azure Machine Learning z modelami, które zostały przeszkolone poza usługą. Można rejestrować modele utworzone poza Azure Machine Learning, a następnie wdrażać je jako usługę sieci Web lub moduł Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: ddd81c4788cae7c239678366305fe97c6c08ba99
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932214"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Użyj istniejącego modelu z Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak używać istniejącego modelu uczenia maszynowego z Azure Machine Learning.

Jeśli masz model uczenia maszynowego, który został przeszkolony poza Azure Machine Learning, możesz nadal używać usługi do wdrażania modelu jako usługi sieci Web lub urządzenia IoT Edge. 

> [!TIP]
> Ten artykuł zawiera podstawowe informacje dotyczące rejestrowania i wdrażania istniejącego modelu. Po wdrożeniu Azure Machine Learning zapewnia monitorowanie modelu. Umożliwia także przechowywanie danych wejściowych wysyłanych do wdrożenia, które mogą być używane do analizy dryfowania danych lub do uczenia nowych wersji modelu.
>
> Aby uzyskać więcej informacji na temat pojęć i terminów używanych w tym miejscu, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).
>
> Aby uzyskać ogólne informacje na temat procesu wdrażania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego](how-to-manage-workspace.md).

    > [!TIP]
    > W przykładach w języku Python w tym artykule założono, że zmienna `ws` jest ustawiona na Azure Machine Learning obszaru roboczego.
    >
    > Przykłady interfejsu wiersza polecenia używają symbolu zastępczego `myworkspace` i `myresourcegroup`. Zamień je na nazwę obszaru roboczego i grupę zasobów, która ją zawiera.

* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i [Machine Learning rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md).

* Model szkolony. Model musi być utrwalony w co najmniej jednym pliku w środowisku deweloperskim.

    > [!NOTE]
    > Aby zademonstrować rejestrowanie modelu przeszkolonego na Azure Machine Learning, przykładowe fragmenty kodu w tym artykule korzystają z modeli utworzonych przez projekt Paolo Ripamonti w serwisie Twitter tonacji Analysis: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Zarejestruj modele

Zarejestrowanie modelu umożliwia przechowywanie, wersję i śledzenie metadanych na temat modeli w obszarze roboczym. W poniższych przykładach języka Python i interfejsu wiersza polecenia katalog `models` zawiera pliki `model.h5`, `model.w2v`, `encoder.pkl`i `tokenizer.pkl`. Ten przykład przekazuje pliki znajdujące się w katalogu `models` jako nową rejestrację modelu o nazwie `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Aby uzyskać więcej informacji, zobacz Dokumentacja [model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Aby uzyskać więcej informacji, zobacz sekcję [AZ ml model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) Reference.


Aby uzyskać więcej informacji na temat rejestracji modelu ogólnie, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania definiuje środowisko używane do uruchamiania wdrożonego modelu. Konfiguracja wnioskowania odwołuje się do następujących jednostek, które są używane do uruchamiania modelu podczas jego wdrażania:

* Skrypt wejściowy. Ten plik (o nazwie `score.py`) ładuje model podczas uruchamiania wdrożonej usługi. Jest on również odpowiedzialny za otrzymywanie danych, przekazywanie go do modelu, a następnie zwracanie odpowiedzi.
* [Środowisko](how-to-use-environments.md)Azure Machine Learning. Środowisko definiuje zależności oprogramowania wymagające do uruchomienia modelu i skryptu wejścia.

Poniższy przykład pokazuje, jak utworzyć środowisko przy użyciu zestawu SDK, a następnie użyć go z konfiguracją wnioskowania:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Aby uzyskać więcej informacji zobacz następujące artykuły:

+ [Jak używać środowisk](how-to-use-environments.md).
+ Odwołanie [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .


Interfejs wiersza polecenia ładuje konfigurację wnioskowania z pliku YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

W interfejsie wiersza polecenia środowisko Conda jest zdefiniowane w pliku `myenv.yml`, do którego odwołuje się konfiguracja wnioskowania. Następujący YAML to zawartość tego pliku:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Skrypt wprowadzania

Skrypt wejścia ma tylko dwie wymagane funkcje, `init()` i `run(data)`. Te funkcje są używane do inicjowania usługi przy uruchamianiu i uruchamiania modelu przy użyciu danych żądania przekazaną przez klienta. Pozostała część skryptu obsługuje ładowanie i uruchamianie modeli.

> [!IMPORTANT]
> Brak ogólnego skryptu wejścia, który działa dla wszystkich modeli. Jest zawsze specyficzny dla modelu, który jest używany. Musi on zrozumieć sposób ładowania modelu, format danych, którego oczekuje model, oraz sposób oceny danych przy użyciu modelu.

Poniższy kod w języku Python jest przykładowym skryptem wprowadzania (`score.py`):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiowanie wdrożenia

Pakiet usługi [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) zawiera klasy używane do wdrożenia. Używana Klasa określa, gdzie model jest wdrażany. Na przykład, aby wdrożyć jako usługę sieci Web w usłudze Azure Kubernetes, użyj [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) , aby utworzyć konfigurację wdrożenia.

Poniższy kod w języku Python definiuje konfigurację wdrożenia dla lokalnego wdrożenia. Ta konfiguracja wdraża model jako usługę sieci Web na komputerze lokalnym.

> [!IMPORTANT]
> Wdrożenie lokalne wymaga działającej instalacji [platformy Docker](https://www.docker.com/) na komputerze lokalnym:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Aby uzyskać więcej informacji, zobacz Odwołanie [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) .

Interfejs wiersza polecenia ładuje konfigurację wdrożenia z pliku YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Wdrażanie w innym miejscu docelowym obliczeniowym, takim jak usługa Azure Kubernetes w chmurze platformy Azure, jest tak proste jak zmiana konfiguracji wdrożenia. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Poniższy przykład ładuje informacje z zarejestrowanego modelu o nazwie `sentiment`, a następnie wdraża je jako usługę o nazwie `sentiment`. Podczas wdrażania, konfiguracja i konfiguracja wdrożenia są używane do tworzenia i konfigurowania środowiska usługi:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Aby uzyskać więcej informacji, zobacz Dokumentacja [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) .

Aby wdrożyć model z interfejsu wiersza polecenia, użyj następującego polecenia. To polecenie wdraża wersję 1 zarejestrowanego modelu (`sentiment:1`) przy użyciu konfiguracji wnioskowania i wdrożenia przechowywanej w plikach `inferenceConfig.json` i `deploymentConfig.json`:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

Aby uzyskać więcej informacji na temat wdrażania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Żądanie — użycie odpowiedzi

Po wdrożeniu zostanie wyświetlony identyfikator URI oceniania. Ten identyfikator URI może być używany przez klientów do przesyłania żądań do usługi. Poniższy przykład to podstawowy klient języka Python, który przesyła dane do usługi i wyświetla odpowiedź:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji na temat korzystania ze wdrożonej usługi, zobacz [Tworzenie klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Następne kroki

* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Jak utworzyć klienta dla wdrożonego modelu](how-to-consume-web-service.md)
