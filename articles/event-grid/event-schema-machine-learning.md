---
title: Schemat zdarzenia Machine Learning Azure Event Grid
description: Opisuje właściwości, które są dostępne dla zdarzeń Obszar roboczy usługi Machine Learning z Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132877"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Schemat zdarzeń Azure Event Grid dla Azure Machine Learning

Ten artykuł zawiera właściwości i schemat zdarzeń obszaru roboczego usługi Machine Learning. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

Aby zapoznać się z listą przykładowych skryptów i samouczków, zobacz [Źródło zdarzeń platformy Azure](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Azure Machine Learning emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Uruchamiany, gdy nowy model lub wersja modelu zostały pomyślnie zarejestrowane. |
| Microsoft. MachineLearningServices. ModelDeployed | Uruchamiany, gdy modele zostały pomyślnie wdrożone w punkcie końcowym. |
| Microsoft. MachineLearningServices. RunCompleted | Uruchamiany, gdy przebieg został pomyślnie ukończony. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Uruchamiany, gdy monitor dryfowania zestawu danych wykryje dryf. |

## <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.

Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Zdarzenie Microsoft. MachineLearningServices. ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Zdarzenie Microsoft. MachineLearningServices. ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Zdarzenie Microsoft. MachineLearningServices. RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Zdarzenie Microsoft. MachineLearningServices. DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Event Grid udostępnia tę wartość. |
| subject | ciąg | Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzenia magazynu obiektów BLOB. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Event Grid definiuje schemat właściwości najwyższego poziomu. Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości dla każdego typu zdarzenia:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ModelName | ciąg | Nazwa zarejestrowanego modelu. |
| ModelVersion | int | Wersja zarejestrowanego modelu. |
| ModelTags | obiekt | Tagi modelu, który został zarejestrowany. |
| ModelProperties | obiekt | Właściwości modelu, który został zarejestrowany. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ServiceName | ciąg | Nazwa wdrożonej usługi. |
| Servicecomputetype | ciąg | Typ obliczeń (np. ACI, AKS) wdrożonej usługi. |
  | ModelIds | ciąg | Rozdzielana przecinkami lista identyfikatorów modeli. Identyfikatory modeli wdrożonych w usłudze. |
| Tagi | obiekt | Tagi wdrożonej usługi. |
| Właściwości serviceproperties | obiekt | Właściwości wdrożonej usługi. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ExperimentId | ciąg | Identyfikator eksperymentu, do którego należy uruchomienie. |
| Eksperymentname | ciąg | Nazwa eksperymentu, do którego należy uruchomienie. |
| RunId | ciąg | Identyfikator przebiegu, który został ukończony. |
| RunType | ciąg | Typ uruchomienia ukończonego przebiegu. |
| RunTags | obiekt | Tagi ukończonego przebiegu. |
| RunProperties | obiekt | Właściwości ukończonego uruchomienia. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| DataDriftId | ciąg | Identyfikator monitora dryfowania danych, który wyzwolił zdarzenie. |
| Datadryfname | ciąg | Nazwa monitora dryfowania danych, który wyzwolił zdarzenie. |
| RunId | ciąg | Identyfikator uruchomienia, który wykrył dryf danych. |
| BaseDatasetId | ciąg | Identyfikator podstawowego zestawu danych używanego do wykrywania dryfu. |
| TargetDatasetId | ciąg | Identyfikator docelowego zestawu danych używanego do wykrywania dryfu. |
| DriftCoefficient | double | Wynik współczynnika, który wyzwolił zdarzenie. |
| StartTime | datetime | Godzina rozpoczęcia serii czasu docelowej zestawu danych, która spowodowała wykrywanie dryfu.  |
| EndTime | datetime | Godzina zakończenia serii czasu docelowej zestawu danych, która spowodowała wykrywanie dryfu. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md)
* Aby zapoznać się z wprowadzeniem do korzystania z Azure Event Grid z Azure Machine Learning, zobacz temat [Korzystanie z zdarzeń Azure Machine Learning](/azure/machine-learning/service/concept-event-grid-integration)
* Przykład korzystania z Azure Event Grid z Azure Machine Learning można znaleźć w temacie [Tworzenie przepływów pracy uczenia maszynowego](/azure/machine-learning/service/how-to-use-event-grid) na podstawie zdarzeń
