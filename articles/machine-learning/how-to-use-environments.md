---
title: Utwórz środowiska ML wielokrotnego użytku
titleSuffix: Azure Machine Learning
description: Twórz środowiska i zarządzaj nimi, aby uzyskać model szkoleń i wdrożeń. Zarządzaj pakietami języka Python i innymi ustawieniami środowiska.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 9d6c823b5dc85676b15188c8f1783e50e5ed441b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717816"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Ponowne używanie środowisk do szkolenia i wdrażania przy użyciu Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak tworzyć [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning i zarządzać nimi. Używaj środowisk do śledzenia i odtwarzania zależności oprogramowania projektów podczas ich rozwoju.

Zarządzanie zależnościami oprogramowania to typowe zadanie dla deweloperów. Chcesz upewnić się, że kompilacje są odtwarzalne bez rozbudowanej ręcznej konfiguracji oprogramowania. Azure Machine Learning `Environment` klasy kont dla lokalnych rozwiązań programistycznych, takich jak PIP i Conda, a także udostępnia rozwiązanie do programowania w chmurze lokalnej i rozproszonej.

W przykładach w tym artykule przedstawiono sposób wykonywania tych operacji:

* Utwórz środowisko i określ zależności pakietów.
* Pobierz i zaktualizuj środowiska.
* Użyj środowiska do uczenia się.
* Użyj środowiska do wdrożenia usługi sieci Web.

Aby uzyskać ogólne omówienie sposobu działania środowisk w Azure Machine Learning, zobacz [co to są środowiska ml?](concept-environments.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Tworzenie środowiska

W poniższych sekcjach opisano różne sposoby tworzenia środowiska dla eksperymentów.

### <a name="use-a-curated-environment"></a>Korzystanie z nadzorowanego środowiska

Możesz wybrać jedno z nadzorowanych środowisk, które mają być uruchamiane: 

* Środowisko _Azure-minimalne_ zawiera minimalny zestaw pakietów umożliwiających śledzenie uruchamiania i przekazywanie zasobów. Można go użyć jako punktu wyjścia dla własnego środowiska.

* Środowisko uczenia maszynowego zawiera wspólne pakiety _do_ nauki o danych. Pakiety te obejmują Scikit-uczenie, Pandas, matplotlib i większy zestaw pakietów Azure SDK.

Środowiska nadzorowane są obsługiwane przez buforowane obrazy platformy Docker. Ta kopia zapasowa zmniejsza koszt przygotowania do uruchomienia.

Użyj metody `Environment.get`, aby wybrać jedno z nadzorowanych środowisk:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Można wyświetlić listę środowisk nadzorowanych i ich pakietów przy użyciu następującego kodu:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Nie uruchamiaj nazwy środowiska z prefiksem platformy _Azure_ . Ten prefiks jest zarezerwowany dla środowisk nadzorowanych.

### <a name="instantiate-an-environment-object"></a>Tworzenie wystąpienia obiektu środowiska

Aby ręcznie utworzyć środowisko, zaimportuj klasę `Environment` z zestawu SDK. Następnie użyj poniższego kodu, aby utworzyć wystąpienie obiektu środowiska.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Użyj plików specyfikacji Conda i PIP

Możesz również utworzyć środowisko na podstawie specyfikacji Conda lub pliku wymagań PIP. Użyj metody [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) lub [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) . W argumencie metody Podaj nazwę środowiska i ścieżkę do pliku, który chcesz.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Korzystanie z istniejących środowisk Conda

Jeśli masz istniejące środowisko Conda na komputerze lokalnym, możesz użyć usługi do utworzenia obiektu środowiska. Korzystając z tej strategii, można ponownie użyć lokalnego środowiska interaktywnego na zdalnych uruchomieniach.

Poniższy kod tworzy obiekt środowiska z istniejącego środowiska Conda `mycondaenv`. Używa metody [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Twórz środowiska automatycznie

Automatyczne tworzenie środowiska przez przesłanie przebiegu szkoleniowego. Prześlij przebieg przy użyciu metody `submit()`. Podczas przesyłania przebiegu szkoleniowego Kompilowanie nowego środowiska może potrwać kilka minut. Czas trwania kompilacji zależy od rozmiaru wymaganych zależności. 

Jeśli nie określisz środowiska w konfiguracji uruchomieniowej przed przesłaniem przebiegu, zostanie utworzone środowisko domyślne.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Podobnie, jeśli używasz obiektu [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) do szkolenia, możesz bezpośrednio przesłać wystąpienie szacowania jako uruchomienie bez określania środowiska. Obiekt `Estimator` już hermetyzuje środowisko i element docelowy obliczeń.

## <a name="add-packages-to-an-environment"></a>Dodawanie pakietów do środowiska

Dodaj pakiety do środowiska przy użyciu plików Conda, PIP lub Private koła. Określ każdą zależność pakietu przy użyciu klasy [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) . Dodaj go do `PythonSection`środowiska.

### <a name="conda-and-pip-packages"></a>Pakiety Conda i PIP

Jeśli pakiet jest dostępny w repozytorium pakietu Conda, zalecamy użycie instalacji programu Conda zamiast instalacji PIP. Pakiety Conda są zwykle dołączone do wstępnie skompilowanych plików binarnych, które zwiększają niezawodność instalacji.

Poniższy przykład dodaje do środowiska. Dodaje on wersję 0.21.3 `scikit-learn`. Dodaje również pakiet `pillow` `myenv`. W przykładzie zastosowano odpowiednio metodę [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) i metodę [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) .

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Pliki kół prywatnych

Możesz użyć prywatnych plików kółka PIP, najpierw przekazując je do magazynu obszaru roboczego. Są one przekazywane przy użyciu statycznej metody [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) . Następnie należy przechwytywać adres URL magazynu i przekazać adres URL do metody `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Zarządzaj środowiskami

Zarządzaj środowiskami, aby można było je aktualizować, śledzić i ponownie używać w celach obliczeniowych i z innymi użytkownikami obszaru roboczego.

### <a name="register-environments"></a>Zarejestruj środowiska

Środowisko jest automatycznie rejestrowane w obszarze roboczym podczas przesyłania uruchomienia lub wdrożenia usługi sieci Web. Możesz również ręcznie zarejestrować środowisko przy użyciu metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Ta operacja powoduje, że środowisko jest śledzone i zgodne z wersją w chmurze. Jednostka może być współdzielona między użytkownikami obszaru roboczego.

Poniższy kod rejestruje środowisko `myenv` w obszarze roboczym `ws`.

```python
myenv.register(workspace=ws)
```

W przypadku korzystania ze środowiska po raz pierwszy w szkole lub wdrożeniu zostanie ono zarejestrowane w obszarze roboczym. Następnie zostanie skompilowany i wdrożony w miejscu docelowym obliczeń. Usługa buforuje środowiska. W przypadku korzystania z pamięci podręcznej trwa znacznie mniej czasu niż w przypadku użycia nowej usługi lub aktualizacji, która została zaktualizowana.

### <a name="get-existing-environments"></a>Pobierz istniejące środowiska

Klasa `Environment` oferuje metody umożliwiające pobieranie istniejących środowisk w obszarze roboczym. Możesz pobrać środowiska według nazwy, listy lub określonego przebiegu szkoleniowego. Te informacje ułatwiają rozwiązywanie problemów, inspekcję i odtwarzalność.

#### <a name="view-a-list-of-environments"></a>Wyświetlanie listy środowisk

Wyświetl środowiska w obszarze roboczym, korzystając z klasy [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) . Następnie wybierz środowisko do ponownego użycia.

#### <a name="get-an-environment-by-name"></a>Pobieranie środowiska według nazwy

Możesz również uzyskać określone środowisko według nazwy i wersji. Poniższy kod używa metody [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) do pobierania `1` wersji środowiska `myenv` w obszarze roboczym `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Uczenie środowiska określonego dla uruchamiania

Aby uzyskać środowisko, które zostało użyte do określonego uruchomienia po zakończeniu szkolenia, użyj metody [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) w klasie `Run`.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizowanie istniejącego środowiska

Załóżmy, że zmienisz istniejące środowisko, na przykład dodając pakiet języka Python. Nowa wersja środowiska jest następnie tworzona podczas przesyłania przebiegu, wdrażania modelu lub ręcznego rejestrowania środowiska. Wersja pozwala przeglądać zmiany środowiska w miarę upływu czasu.

Aby zaktualizować wersję pakietu języka Python w istniejącym środowisku, określ numer wersji tego pakietu. Jeśli nie używasz dokładnego numeru wersji, Azure Machine Learning będzie ponownie używać istniejącego środowiska z oryginalnymi wersjami pakietu.

### <a name="debug-the-image-build"></a>Debuguj kompilację obrazu

W poniższym przykładzie zastosowano metodę [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) , aby ręcznie utworzyć środowisko jako obraz platformy Docker. Monitoruje dzienniki wyjściowe z kompilacji obrazu przy użyciu [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Utworzony obraz zostanie wyświetlony w wystąpieniu Azure Container Registry obszaru roboczego. Te informacje ułatwiają debugowanie.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Włącz platformę Docker

 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) klasy Azure Machine Learning `Environment` umożliwia precyzyjne dostosowywanie i kontrolowanie systemu operacyjnego gościa, w którym jest uruchamiane szkolenie.

Podczas `enable` platformy Docker usługa kompiluje obraz platformy Docker. Tworzy również środowisko języka Python, które używa Twoich specyfikacji w tym kontenerze platformy Docker. Ta funkcja zapewnia dodatkową izolację i odtwarzalność dla przebiegów szkoleniowych.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Nowo utworzony obraz platformy Docker jest domyślnie wyświetlany w rejestrze kontenerów skojarzonym z obszarem roboczym.  Nazwa repozytorium ma postać *Azure/azureml_\<uuid\>* . Część unikatowego identyfikatora (*UUID*) nazwy odpowiada skrótowi obliczonemu na podstawie konfiguracji środowiska. Ta zgodność umożliwia usłudze określenie, czy obraz danego środowiska już istnieje do ponownego użycia.

Ponadto usługa automatycznie używa jednego z [obrazów podstawowych](https://github.com/Azure/AzureML-Containers)opartych na Ubuntu Linux. Instaluje określone pakiety języka Python. Obraz podstawowy ma wersje procesora CPU i procesora GPU. Azure Machine Learning automatycznie wykrywa, która wersja ma być używana.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> Jeśli określisz `environment.python.user_managed_dependencies=False` podczas korzystania z niestandardowego obrazu platformy Docker, usługa utworzy środowisko Conda w ramach obrazu. Wykonanie przebiegu w tym środowisku nie będzie możliwe przy użyciu żadnych bibliotek języka Python zainstalowanych na obrazie podstawowym. Ustaw parametr na `True`, aby użyć własnych zainstalowanych pakietów.

## <a name="use-environments-for-training"></a>Używanie środowisk do szkolenia

Aby przesłać przebieg szkolenia, należy połączyć środowisko, [obiekt docelowy obliczeń](concept-compute-target.md)i Twój szkoleniowy skrypt w języku Python do konfiguracji uruchomieniowej. Ta konfiguracja jest obiektem otoki używanym do przesyłania przebiegów.

Podczas przesyłania przebiegu szkoleniowego Kompilowanie nowego środowiska może potrwać kilka minut. Czas trwania zależy od rozmiaru wymaganych zależności. Środowiska są buforowane przez usługę. Tak długo, jak definicja środowiska pozostaje niezmieniona, ponosisz pełny czas instalacji tylko raz.

Poniższy przykład lokalnego uruchomienia skryptu pokazuje, gdzie używać [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) jako obiektu otoki.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Aby wyłączyć historię uruchamiania lub uruchomić migawki, użyj ustawienia w obszarze `ScriptRunConfig.run_config.history`.

Jeśli nie określisz środowiska w konfiguracji uruchomieniowej, usługa utworzy środowisko domyślne podczas przesyłania przebiegu.

### <a name="use-an-estimator-for-training"></a>Użyj szacowania do szkolenia

Jeśli używasz [szacowania](how-to-train-ml-models.md) do szkolenia, możesz przesłać wystąpienie usługi szacowania bezpośrednio. Jest już hermetyzowane środowisko i element docelowy obliczeń.

Poniższy kod używa szacowania do szkolenia jednego węzła. Jest ona uruchamiana na zdalnym obliczaniu dla modelu `scikit-learn`. Przyjęto założenie, że wcześniej utworzono obiekt docelowy obliczeń, `compute_target`i obiekt magazynu danych `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Używanie środowisk do wdrażania usług sieci Web

Środowiska można używać podczas wdrażania modelu jako usługi sieci Web. Ta funkcja umożliwia przeodtwarzalny, połączony przepływ pracy. W tym przepływie pracy można nauczyć, testować i wdrażać model przy użyciu tych samych bibliotek w ramach obliczeń szkoleniowych i obliczeń wnioskowania.

Aby wdrożyć usługę sieci Web, Połącz środowisko, obliczenia wnioskowania, skrypt oceniania i zarejestrowany model w obiekcie wdrożenia, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

W tym przykładzie przyjęto założenie, że zostało wykonane uruchomienie szkoleniowe. Teraz chcesz wdrożyć ten model do Azure Container Instances. Podczas kompilowania usługi sieci Web pliki modelu i oceniania są instalowane na obrazie, a do obrazu zostanie dodany stos wnioskowania Azure Machine Learning.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Przykład notesów

Ten [przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) rozwija się wraz z pojęciami i metodami pokazanymi w tym artykule.

[Wdrożenie modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker](how-to-deploy-custom-docker-image.md) demonstruje sposób wdrażania modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker.

W tym [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) pokazano, jak wdrożyć model Spark jako usługę sieci Web.

## <a name="create-and-manage-environments-with-the-cli"></a>Tworzenie środowisk i zarządzanie nimi za pomocą interfejsu wiersza polecenia

[Interfejs wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md) stanowi odbicie większości funkcji zestawu SDK języka Python. Służy do tworzenia środowisk i zarządzania nimi. Polecenia omówione w tej sekcji przedstawiają podstawowe funkcje.

Następujące polecenie tworzy szkielet plików dla domyślnej definicji środowiska w określonym katalogu. Te pliki są plikami JSON. Działają one podobnie do odpowiedniej klasy w zestawie SDK. Za pomocą tych plików można tworzyć nowe środowiska z ustawieniami niestandardowymi. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Uruchom następujące polecenie, aby zarejestrować środowisko z określonego katalogu.

```azurecli-interactive
az ml environment register -d myenvdir
```

Uruchom następujące polecenie, aby wyświetlić listę wszystkich zarejestrowanych środowisk.

```azurecli-interactive
az ml environment list
```

Pobierz zarejestrowane środowisko przy użyciu następującego polecenia.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Następne kroki

* Aby użyć zarządzanego obiektu docelowego obliczeń do uczenia modelu, zobacz [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md).
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl [Informacje o zestawie SDK klas`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Więcej informacji o pojęciach i metodach opisanych w tym artykule można znaleźć w [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
