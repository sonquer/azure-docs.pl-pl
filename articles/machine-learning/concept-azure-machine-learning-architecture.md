---
title: Architektura & kluczowe pojęcia
titleSuffix: Azure Machine Learning
description: Poznaj architekturę, terminy, koncepcje i przepływy pracy, które składają się Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 70d843f5773f66e6e17c40d0441553e3cb096c64
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462160"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak działa Azure Machine Learning: architektura i koncepcje

Poznaj architekturę, koncepcje i przepływ pracy dla Azure Machine Learning. Główne składniki usługi i ogólny przepływ pracy związane z korzystaniem z usługi są pokazane na poniższym diagramie:

![Azure Machine Learning architektura i przepływ pracy](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy modelu uczenia maszynowego jest zwykle następujący:

1. **Trasy**
    + Opracowywanie skryptów szkoleniowych dotyczących uczenia maszynowego w języku **Python** lub przy użyciu projektanta wizualnego.
    + Utwórz i skonfiguruj **obiekt docelowy obliczeń**.
    + **Prześlij skrypty** do skonfigurowanego obiektu docelowego obliczeń do uruchomienia w tym środowisku. Podczas szkoleń skrypty mogą odczytywać dane z **magazynu**danych lub zapisywać do niego. Rekordy wykonywania są zapisywane jako **uruchomienia** w **obszarze roboczym** i pogrupowane w obszarze **eksperymenty**.

1. **Pakiet** — po znalezieniu zadowalającego przebiegu Zarejestruj trwały model w **rejestrze modelu**.

1. **Sprawdź Poprawność** - **Zbadaj eksperyment** rejestrowanych metryk z bieżącego i przeszłego uruchomienia. Jeśli metryki nie wskazują żądanego wyniku, pętla Wróć do kroku 1 i wykonuje iterację w skryptach.

1. **Wdróż** — opracowuje skrypt oceniania, który używa modelu i **wdraża model** jako **usługę sieci Web** na platformie Azure lub na **IoT Edge urządzenie**.

1. **Monitor** -monitoruje **dryf danych** między zestawem danych szkoleń i danymi wnioskowania wdrożonego modelu. W razie potrzeby Wróć do kroku 1, aby ponownie przeprowadzić uczenie modelu przy użyciu nowych danych szkoleniowych.

## <a name="tools-for-azure-machine-learning"></a>Narzędzia dla Azure Machine Learning

Użyj tych narzędzi dla Azure Machine Learning:

+  Korzystanie z usługi w dowolnym środowisku języka Python z [zestawem SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Korzystanie z usługi w dowolnym środowisku R z [zestawem SDK Azure Machine Learning dla języka r](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatyzuj działania uczenia maszynowego za pomocą [interfejsu wiersza polecenia Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Użyj programu [Azure Machine Learning Designer (wersja zapoznawcza)](concept-designer.md) , aby wykonać etapy przepływu pracy bez pisania kodu.


> [!NOTE]
> Chociaż w tym artykule opisano warunki i pojęcia używane przez Azure Machine Learning, nie zdefiniowano warunków i koncepcji dla platformy Azure. Aby uzyskać więcej informacji na temat terminologii platformy Azure, zobacz [słownik Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Słownik

* [Działanie](#activities)
* [Obszary](#workspaces)
    * [Eksperymenty](#experiments)
        * [Run](#runs) 
            * [Uruchom konfigurację](#run-configurations)
            * [Zdjęcie](#snapshots)
            * [Śledzenie git](#github-tracking-and-integration)
            * [Rejestrować](#logging)
    * [Potoki ML](#ml-pipelines)
    * [Przykładów](#models)
        * [Wiejski](#environments)
        * [Skrypt szkoleniowy](#training-scripts)
        * [Szacowania](#estimators)
    * [Punktów końcowych](#endpoints)
        * [Usługa sieci Web](#web-service-endpoint)
        * [Moduły IoT](#iot-module-endpoints)
    * [& Magazynów danych](#datasets-and-datastores)
    * [Cele obliczeń](#compute-targets)

### <a name="activities"></a>Działania

Działanie reprezentuje długotrwałej operacji. Następujące operacje są przykłady działań:

* Tworzenie lub usuwanie obliczeniowego elementu docelowego
* Uruchomienie skryptu na obliczeniowego elementu docelowego

Działania mogą udostępniać powiadomienia za pomocą zestawu SDK lub internetowego interfejsu użytkownika, dzięki czemu można łatwo monitorować postęp tych operacji.

### <a name="workspaces"></a>Obszary robocze

[Obszar roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla Azure Machine Learning. Zapewnia centralne miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z Azure Machine Learning. Obszar roboczy można udostępniać innym osobom. Aby uzyskać szczegółowy opis obszarów roboczych, zobacz [co to jest obszar roboczy Azure Machine Learning?](concept-workspace.md)

### <a name="experiments"></a>Eksperymenty

Eksperyment to grupa wielu przebiegów z określonego skryptu. Zawsze należy do obszaru roboczego. Po przesłaniu przebiegu można podać nazwę eksperymentu. Informacje dotyczące uruchomienia są przechowywane w ramach tego eksperymentu. Jeśli przesyłasz przebieg i określisz nieistniejącą nazwę eksperymentu, zostanie automatycznie utworzony nowy eksperyment z nowo określoną nazwą.

Przykład korzystania z eksperymentu można znaleźć w [samouczku: uczenie pierwszego modelu](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Wyczerpani

Uruchomienie to pojedyncze wykonanie skryptu szkoleniowego. Eksperyment zwykle zawiera wiele uruchomień.

Azure Machine Learning rejestruje wszystkie uruchomienia i zapisuje następujące informacje w eksperymentie:

* Metadane dotyczące przebiegu (Sygnatura czasowa, czas trwania itd.)
* Metryki rejestrowane przez skrypt
* Pliki wyjściowe, które są zbierane przez eksperyment lub jawnie przekazane przez użytkownika
* Migawka katalogu, który zawiera skrypty, przed przebiegu

Podczas przesyłania skryptu w celu uczenia modelu należy utworzyć przebieg. Uruchom może mieć zero lub więcej uruchomień podrzędnych. Na przykład uruchomienie najwyższego poziomu może mieć dwa uruchomienia podrzędne, z których każdy może mieć własne uruchomienie podrzędne.

### <a name="run-configurations"></a>Uruchom konfiguracje

Konfiguracja przebiegu to zestaw instrukcji, które definiują sposób uruchamiania skryptu w określonym elemencie docelowym obliczeń. Konfiguracja obejmuje szeroki zestaw definicji zachowań, takich jak użycie istniejącego środowiska Python lub użycie środowiska Conda, które jest kompilowane ze specyfikacji.

Konfigurację przebiegu można utrwalić do pliku znajdującego się w katalogu, który zawiera skrypt szkoleniowy lub który może być skonstruowany jako obiekt w pamięci i użyty do przesłania uruchomienia.

Na przykład Uruchom konfiguracje, zobacz [Wybieranie i używanie elementu docelowego obliczeń do uczenia modelu](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Migawki

W przypadku przesyłania przebiegu Azure Machine Learning kompresuje katalog zawierający skrypt jako plik zip i wysyła go do obiektu docelowego obliczeń. Następnie plik zip zostanie wyodrębniony, a skrypt zostanie uruchomiony w tym miejscu. Usługa Azure Machine Learning są także przechowywane w pliku zip jako migawka jako część rekordu uruchomienia. Każda osoba mająca dostęp do obszaru roboczego można przeglądać rekordu uruchomienia i pobrać migawki.

> [!NOTE]
> Aby zapobiec dołączeniu niepotrzebnych plików do migawki, należy wykonać ignorowanie pliku (. gitignore lub. amlignore). Umieść ten plik w katalogu migawek i Dodaj do niego nazwy plików. Plik. amlignore używa tej samej [składni i wzorców co plik. gitignore](https://git-scm.com/docs/gitignore). Jeśli istnieją oba pliki, plik. amlignore ma pierwszeństwo.

### <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Działa to z przebiegami przesłanymi przy użyciu potoku szacowania, ML lub uruchamiania skryptu. Działa również w przypadku przebiegów przesłanych z zestawu SDK lub interfejsu wiersza polecenia Machine Learning.

Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Rejestrowanie

Podczas opracowywania rozwiązania Użyj zestawu SDK języka Python Azure Machine Learning w skrypcie języka Python, aby rejestrować arbitralne metryki. Po uruchomieniu Zbadaj metryki, aby określić, czy przebieg został utworzony przez model, który chcesz wdrożyć.

### <a name="ml-pipelines"></a>Potokach uczenia Maszynowego

Potoki uczenia maszynowego umożliwiają tworzenie przepływów pracy, które łączą etapy uczenia maszynowego, oraz zarządzanie nimi. Na przykład potok może obejmować przygotowanie danych, szkolenie modeli, wdrożenie modelu i etapy wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może uruchamiane w różnych celów obliczeń. 

Kroki potoku są wielokrotnego użytku i można je uruchomić bez ponownego uruchomienia kolejnych kroków, jeśli dane wyjściowe tego kroku nie uległy zmianie. Na przykład możesz ponownie przeprowadzić uczenie modelu bez ponownego uruchamiania kosztownych kroków przygotowywania danych, jeśli dane nie uległy zmianie. Potoki umożliwiają również analitykom danych współpracę podczas pracy nad osobnymi obszarami przepływu pracy uczenia maszynowego.

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego za pomocą tej usługi, zobacz [potoki i Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modele

W najprostszym modelem jest fragmentem kodu, który przyjmuje dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje algorytmu, wybierając dostarczanie danych i dostosowywanie hiperparametrów. Szkolenie jest procesem iteracyjnym, tworzącego trenowanego modelu, który hermetyzuje, jakie zakorzenione podczas procesu uczenia modelu.

Model jest generowany przez uruchomienie w usłudze Azure Machine Learning. Można również użyć modelu, który jest przeszkolony poza Azure Machine Learning. Model można zarejestrować w obszarze roboczym Azure Machine Learning.

Azure Machine Learning to struktura niezależny od. Podczas tworzenia modelu można użyć dowolnej popularnej platformy uczenia maszynowego, takiej jak Scikit-Learning, XGBoost, PyTorch, TensorFlow i łańcucha.

Aby zapoznać się z przykładem szkoleń dotyczących modelu przy użyciu Scikit — uczenie i szacowania, zobacz [Samouczek: uczenie modelu klasyfikacji obrazów za pomocą Azure Machine Learning](tutorial-train-models-with-aml.md).

**Rejestr modelu** śledzi wszystkie modele w obszarze roboczym Azure Machine Learning.

Modele są identyfikowane przez nazwę i wersję. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie jak istniejący, rejestr założono, że jest to nowa wersja. Wersja jest zwiększana, a nowy model jest zarejestrowany pod tą samą nazwą.

Po zarejestrowaniu modelu można podać dodatkowe Tagi metadanych, a następnie użyć tagów podczas wyszukiwania modeli.

> [!TIP]
> Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym Azure Machine Learning. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

Nie można usunąć zarejestrowanego modelu, który jest używany przez aktywne wdrożenie.

Aby zapoznać się z przykładem rejestrowania modelu, zobacz [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Środowiska

Środowiska usługi Azure ML służą do określania konfiguracji (Docker/Python/Spark/itp.) służącej do tworzenia odtwarzalnego środowiska na potrzeby przygotowywania danych, szkolenia modeli i modelowania. Są one zarządzane i obsługiwane przez program w obszarze roboczym Azure Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego między różnymi obiektami docelowymi obliczeniowymi.

Możesz użyć obiektu środowiska na lokalnym obliczeniu do opracowania skryptu szkoleniowego i ponownie użyć tego samego środowiska na Azure Machine Learning obliczeń dla szkolenia modeli w dużej skali, a nawet wdrożyć model przy użyciu tego samego środowiska. 

Dowiedz się [, jak utworzyć i zarządzać środowiskiem ml wielokrotnego użytku](how-to-use-environments.md) na potrzeby szkolenia i wnioskowania.

### <a name="training-scripts"></a>Skryptów szkolenia

Do nauczenia modelu, można określić katalog, który zawiera skrypt szkolenia i skojarzone pliki. Należy również określić nazwę eksperymentu, która jest używana do przechowywania informacji zebranych podczas szkolenia. Podczas szkolenia cały katalog jest kopiowany do środowiska szkoleniowego (elementu docelowego obliczeń), a skrypt określony przez konfigurację uruchamiania jest uruchamiany. Migawka katalogu znajduje się również w obszarze eksperymentu, w obszarze roboczym.

Aby zapoznać się z przykładem, zobacz [Samouczek: uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Szacowania

Aby ułatwić uczenie modeli przy użyciu popularnych struktur, Klasa szacowania umożliwia łatwe konstruowanie konfiguracji uruchomieniowych. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które korzystają z wybranej platformy szkoleniowej (na przykład scikit-Learning).

W przypadku zadań PyTorch, TensorFlow i łańcucha, Azure Machine Learning również udostępnia odpowiednie [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) szacowania, aby uprościć korzystanie z tych platform.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Uczenie modeli ml z szacowania](how-to-train-ml-models.md).
* [Pytorch modele uczenia głębokiego na dużą skalę w Azure Machine Learning](how-to-train-pytorch.md).
* [Uczenie i rejestrowanie modeli TensorFlow na dużą skalę przy użyciu Azure Machine Learning](how-to-train-tensorflow.md).
* [Uczenie i rejestrowanie modeli łańcucha na dużą skalę przy użyciu Azure Machine Learning](how-to-train-chainer.md).

### <a name="endpoints"></a>Punkty końcowe

Punkt końcowy to tworzenie wystąpienia modelu w usłudze sieci Web, która może być hostowana w chmurze lub module IoT na potrzeby wdrożeń zintegrowanych urządzeń.

#### <a name="web-service-endpoint"></a>Punkt końcowy usługi sieci Web

Podczas wdrażania modelu jako usługi sieci Web punkt końcowy można wdrożyć na Azure Container Instances, usłudze Azure Kubernetes lub FPGA. Usługę można utworzyć z modelu, skryptu i skojarzonych plików. Są one umieszczane w podstawowym obrazie kontenera, który zawiera środowisko wykonawcze dla modelu. Obraz zawiera punkt końcowy HTTP o zrównoważonym obciążeniu, który odbiera żądania oceniania wysyłane do usługi sieci Web.

Platforma Azure pomaga monitorować usługę sieci Web, zbierając Application Insights dane telemetryczne lub model telemetrii, jeśli wybrano opcję włączenia tej funkcji. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniach Application Insights i konta magazynu.

Jeśli włączono automatyczne skalowanie, platforma Azure automatycznie skaluje wdrożenie.

Aby zapoznać się z przykładem wdrażania modelu jako usługi sieci Web, zobacz [Wdrażanie modelu klasyfikacji obrazów w Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Punkty końcowe modułu IoT

Wdrożony punkt końcowy modułu IoT to kontener platformy Docker, który obejmuje model i skojarzony skrypt lub aplikację oraz wszelkie dodatkowe zależności. Te moduły są wdrażane przy użyciu Azure IoT Edge na urządzeniach brzegowych.

Jeśli włączono monitorowanie, platforma Azure zbiera dane telemetryczne z modelu w module Azure IoT Edge. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Azure IoT Edge zapewnia, że moduł jest uruchomiony, i monitoruje urządzenie, które je obsługuje.


### <a name="compute-instance"></a>Wystąpienie obliczeniowe (wersja zapoznawcza)

**Wystąpienie obliczeniowe Azure Machine Learning** (dawniej jest maszyną wirtualną) to w pełni zarządzana stacja robocza oparta na chmurze, która obejmuje wiele narzędzi i środowisk zainstalowanych na potrzeby uczenia maszynowego. Wystąpienia obliczeniowe mogą służyć jako element docelowy obliczeń dla zadań szkoleniowych i inferencing. W przypadku dużych zadań [Azure Machine Learning klastrów obliczeniowych](how-to-set-up-training-targets.md#amlcompute) z możliwościami skalowania wielu węzłów jest lepszym rozwiązaniem docelowym obliczeń.

Dowiedz się więcej o [wystąpieniach obliczeniowych](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Zestawy danych i magazyny datastores

**Azure Machine Learning zestawy** danych (wersja zapoznawcza) ułatwiają uzyskiwanie dostępu i pracę z danymi. Zestawy danych zarządzają danymi w różnych scenariuszach, takich jak szkolenia modeli i tworzenie potoku. Za pomocą zestawu SDK Azure Machine Learning można uzyskać dostęp do magazynu bazowego, eksplorować dane i zarządzać cyklem życia różnych definicji zestawu danych.

Zestawy danych udostępniają metody pracy z danymi w popularnych formatach, takich jak używanie `from_delimited_files()` lub `to_pandas_dataframe()`.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Rejestrowanie zestawów danych Azure Machine Learning](how-to-create-register-datasets.md).  Aby uzyskać więcej przykładów użycia zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Magazyn** danych to Abstrakcja magazynu przez konto usługi Azure Storage. Magazyn danych może używać kontenera obiektów blob platformy Azure lub udziału plików platformy Azure jako magazynu zaplecza. Każdy obszar roboczy ma domyślny magazyn danych i można zarejestrować dodatkowe magazyny danych. Użyj interfejsu API zestawu SDK języka Python lub wiersza polecenia Azure Machine Learning, aby przechowywać i pobierać pliki z magazynu danych.

### <a name="compute-targets"></a>Docelowe obiekty obliczeniowe

[Obiekt docelowy obliczeń](concept-compute-target.md) pozwala określić zasób obliczeniowy służący do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. Ta lokalizacja może być maszyną lokalną lub zasobem obliczeniowym opartym na chmurze.

Dowiedz się więcej o [dostępnych celach obliczeniowych na potrzeby szkolenia i wdrażania](concept-compute-target.md).

### <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

* [Co to jest Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md)
* [Samouczek (część 1): uczenie modelu](tutorial-train-models-with-aml.md)
