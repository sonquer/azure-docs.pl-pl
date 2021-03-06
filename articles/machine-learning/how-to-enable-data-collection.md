---
title: Zbieranie danych z modeli produkcyjnych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zbierać dane modelu danych wejściowych Azure Machine Learning w usłudze Azure Blob Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771397"
---
# <a name="collect-data-for-models-in-production"></a>Zbieranie danych dla modeli w środowisku produkcyjnym

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Zestaw SDK monitorowania Azure Machine Learning zostanie wkrótce wycofany. Zestaw SDK jest nadal odpowiedni dla deweloperów, którzy obecnie używają zestawu SDK do monitorowania dryfowania danych w modelach. Jednak w przypadku nowych klientów zalecamy korzystanie z uproszczonego [monitorowania danych z Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

W tym artykule pokazano, jak zbierać dane modelu wejściowego z Azure Machine Learning. Przedstawiono w nim również sposób wdrażania danych wejściowych w klastrze usługi Azure Kubernetes Service (AKS) i przechowywania danych wyjściowych w usłudze Azure Blob Storage.

Po włączeniu kolekcjonowania zbierane dane ułatwiają:

* [Monitoruj dryfy danych](how-to-monitor-data-drift.md) w miarę wprowadzania danych produkcyjnych do modelu.

* Podejmowanie lepszych decyzji o tym, kiedy należy ponownie przeprowadzić uczenie lub zoptymalizować model.

* Przeszkol model z zebranymi danymi.

## <a name="what-is-collected-and-where-it-goes"></a>Co to jest zbierane i gdzie się odbywa

Mogą być zbierane następujące dane:

* Modeluj dane wejściowe z usług sieci Web wdrożonych w klastrze AKS. Audio, obrazy i wideo głosowe *nie* są zbierane.
  
* Model prognozy, przy użyciu danych wejściowych w środowisku produkcyjnym.

>[!NOTE]
> Preagregacja i obliczenia przedniego na tych danych nie są obecnie częścią usługi kolekcji.

Dane wyjściowe są zapisywane w magazynie obiektów BLOB. Ponieważ dane są dodawane do magazynu obiektów blob, można wybrać ulubione narzędzie w celu uruchomienia analizy.

Ścieżka do danych wyjściowych w obiekcie blob poniżej następującej składni:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> W wersjach zestawu Azure Machine Learning SDK dla języka Python starszej niż wersja 0.1.0 A16 argument `designation` ma nazwę `identifier`. Jeśli kod został opracowany przy użyciu wcześniejszej wersji, należy odpowiednio go zaktualizować.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).

- Musi być zainstalowany obszar roboczy uczenia AzureMachine, katalog lokalny zawierający Twoje skrypty oraz zestaw Azure Machine Learning SDK dla języka Python. Aby dowiedzieć się, jak je zainstalować, zobacz [jak skonfigurować środowisko programistyczne](how-to-configure-environment.md).

- Potrzebny jest szkolony model uczenia maszynowego do wdrożenia w usłudze AKS. Jeśli nie masz modelu, zapoznaj się z samouczkiem dotyczącym [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md) .

- Potrzebny jest klaster AKS. Aby uzyskać informacje na temat sposobu tworzenia i wdrażania ich w programie, zobacz artykuł [jak wdrożyć i gdzie](how-to-deploy-and-where.md).

- [Skonfiguruj środowisko](how-to-configure-environment.md) i zainstaluj [zestaw SDK monitorowania Azure Machine Learning](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Włączanie zbierania danych

Zbieranie danych można włączyć niezależnie od modelu wdrażanego za pomocą Azure Machine Learning lub innych narzędzi.

Aby włączyć zbieranie danych, należy:

1. Otwórz plik oceniania.

1. Dodaj [następujący kod](https://aka.ms/aml-monitoring-sdk) w górnej części pliku:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Zadeklaruj swoje zmienne kolekcji danych w Twojej `init` funkcji:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Identyfikator korelacji* jest opcjonalnym parametrem. Nie musisz używać go, jeśli model nie jest wymagany. Użycie *korelacji* ułatwia łatwiejsze mapowanie przy użyciu innych danych, takich jak *LoanNumber* lub *CustomerID*.
    
    Parametr *identyfikatora* jest później używany do kompilowania struktury folderów w obiekcie blob. Można jej użyć do rozróżniania danych pierwotnych od przetworzonych danych.

1. Dodaj następujące wiersze kodu w celu `run(input_df)` funkcji:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Podczas wdrażania usługi w AKS *nie* jest automatycznie ustawiana **wartość true** dla zbierania danych. Zaktualizuj plik konfiguracji, tak jak w poniższym przykładzie:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Możesz również włączyć Application Insights do monitorowania usług, zmieniając tę konfigurację:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Aby utworzyć nowy obraz i wdrożyć model uczenia maszynowego, zobacz [jak wdrożyć i gdzie](how-to-deploy-and-where.md).

Jeśli masz już usługę z zależnościami zainstalowanymi w pliku środowiska i plikiem oceniania, Włącz zbieranie danych, wykonując następujące czynności:

1. Przejdź do [Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz pozycję **wdrożenia** > **wybierz pozycję Usługa** > **Edytuj**.

   ![Edytowanie usługi](././media/how-to-enable-data-collection/EditService.PNG)

1. W obszarze **Ustawienia zaawansowane**wybierz pozycję **Włącz zbieranie danych modelu**.

    [![wybierz Zbieranie danych](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Możesz również wybrać opcję **Włącz diagnostykę AppInsights** , aby śledzić kondycję usługi.

1. Wybierz pozycję **Aktualizuj** , aby zastosować zmiany.

## <a name="disable-data-collection"></a>Wyłączanie zbierania danych

Zbieranie danych można zatrzymać w dowolnym momencie. Aby wyłączyć zbieranie danych, użyj kodu Python lub Azure Machine Learning.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opcja 1 — wyłączanie zbierania danych w Azure Machine Learning

1. Zaloguj się do [Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz pozycję **wdrożenia** > **wybierz pozycję Usługa** > **Edytuj**.

   [![wybierz opcję Edytuj](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. W obszarze **Ustawienia zaawansowane**wyczyść pole wyboru **Włącz zbieranie danych modelu**.

    [![wyczyścić pole wyboru zbierania danych](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Wybierz **aktualizacji** do zastosowania zmiany.

Możesz również uzyskać dostęp do tych ustawień w obszarze roboczym w [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opcja 2 — Wyłączanie zbierania danych przy użyciu języka Python

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Weryfikowanie i analizowanie danych

Możesz wybrać narzędzie preferencji do analizowania danych zbieranych w magazynie obiektów BLOB.

### <a name="quickly-access-your-blob-data"></a>Szybkie uzyskiwanie dostępu do danych obiektów BLOB

1. Zaloguj się do [Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz pozycję **Magazyn**.

    [![wybrać opcję magazynu](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Postępuj zgodnie ze ścieżką do danych wyjściowych obiektu BLOB przy użyciu następującej składni:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analizowanie danych modelu za pomocą Power BI

1. Pobierz i Otwórz [Power BI Desktop](https://www.powerbi.com).

1. Wybierz pozycję **Pobierz dane** i wybierz pozycję [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [Konfiguracja Power BI obiektu BLOB ![](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Dodaj nazwę konta magazynu, a następnie wprowadź klucz magazynu. Te informacje można znaleźć, wybierając pozycję **ustawienia** > **klucze dostępu** w obiekcie blob.

1. Wybierz kontener **dane modelu** i wybierz pozycję **Edytuj**.

    [![nawigatora Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. W edytorze zapytań kliknij kolumnę **Nazwa** i Dodaj konto magazynu.

1. Wprowadź ścieżkę modelu do filtru. Jeśli chcesz wyszukać tylko pliki z określonego roku lub miesiąca, po prostu rozwiń ścieżkę filtru. Aby na przykład wyszukać dane z marca, Użyj tej ścieżki filtru:

   /modeldata/\<subskrypcji >/\<ResourceGroupName >/\<WorkspaceName >/\<WebServiceName >/\<ModelName >/\<modelversion >/\<oznaczenie >/\<rok >/3

1. Filtrowanie danych, które są istotne dla użytkownika na podstawie wartości **nazw** . Jeśli przechowujesz prognozy i dane wejściowe, musisz utworzyć zapytanie dla każdej z nich.

1. Wybierz podwójną strzałkę w dół obok nagłówka kolumny **zawartości** , aby połączyć pliki.

    [![Power BI zawartość](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kliknij przycisk **OK**. Ładowania danych.

    [![Power BI łączenie plików](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Wybierz pozycję **Zamknij i Zastosuj**.

1. Jeśli dodano dane wejściowe i przewidywania, tabele są automatycznie uporządkowane według wartości **IdentyfikatorŻądania** .

1. Rozpocznij tworzenie niestandardowych raportów na podstawie danych modelu.

### <a name="analyze-model-data-using-azure-databricks"></a>Analizowanie danych modelu za pomocą Azure Databricks

1. Utwórz [obszar roboczy Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Przejdź do obszaru roboczego usługi Databricks.

1. W obszarze roboczym datakostki wybierz pozycję **Przekaż dane**.

    [![wybranie opcji przekazywania danych](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Wybierz pozycję **Utwórz nową tabelę** i wybierz **inne źródła danych** > **Azure Blob Storage** > **Utwórz tabelę w notesie**.

    [Tworzenie tabeli ![ych datakostek](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Zaktualizuj lokalizację danych. Oto przykład:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [Konfiguracja ![datakosteks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Postępuj zgodnie z instrukcjami szablonu, aby wyświetlić i przeanalizować dane.
