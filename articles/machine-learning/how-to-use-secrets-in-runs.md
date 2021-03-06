---
title: Korzystanie z wpisów tajnych w przebiegach szkoleniowych
titleSuffix: Azure Machine Learning
description: Przekaż wpisy tajne do przebiegów szkoleniowych w sposób bezpieczny przy użyciu Key Vault obszaru roboczego
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 722e329b1a2d069b614a301a5eb2f8642b697295
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538711"
---
# <a name="use-secrets-in-training-runs"></a>Korzystanie z wpisów tajnych w przebiegach szkoleniowych
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak korzystać z wpisów tajnych w programie szkoleniowym w sposób bezpieczny. Na przykład aby połączyć się z zewnętrzną bazą danych w celu wykonywania zapytań dotyczących danych szkoleniowych, należy przekazać nazwę użytkownika i hasło do zdalnego kontekstu uruchomienia. Kodowanie takich wartości do skryptów szkoleniowych w postaci zwykłego tekstu jest niebezpieczne, ponieważ uwidacznia wpis tajny. 

Zamiast tego Obszar roboczy usługi Azure Machine Learning ma [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) jako skojarzony zasób. Ten Key Vault może służyć do bezpiecznego przekazywania kluczy tajnych do zdalnego uruchamiania za pomocą zestawu interfejsów API w Azure Machine Learning SDK języka Python.

Podstawowym przepływem korzystania z kluczy tajnych jest:
 1. Na komputerze lokalnym Zaloguj się do platformy Azure i Połącz się z obszarem roboczym.
 2. Na komputerze lokalnym Ustaw klucz tajny w obszarze roboczym Key Vault.
 3. Prześlij zdalne uruchomienie.
 4. W ramach przebiegu zdalnego Pobierz klucz tajny z wartości klucza i użyj go.

## <a name="set-secrets"></a>Ustaw wpisy tajne

W Azure Machine Learning Python SDK Klasa [magazynu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) kluczy zawiera metody ustawiania wpisów tajnych. W lokalnej sesji języka Python najpierw Uzyskaj odwołanie do Key Vault obszaru roboczego, a następnie użyj metody [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) , aby ustawić wpis tajny według nazwy i wartości.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Nie należy umieszczać wartości tajnych w kodzie języka Python, ponieważ jest ona niezabezpieczona, aby można było ją zapisać w pliku jako zwykły tekst. Zamiast tego należy uzyskać wartość klucza tajnego ze zmiennej środowiskowej, na przykład Azure DevOps Build Secret lub z interakcyjnych danych wejściowych użytkownika.

Nazwy wpisów tajnych można wyświetlić za pomocą metody [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) . Metoda __set_secret__ aktualizuje wartość klucza tajnego, jeśli nazwa już istnieje.

## <a name="get-secrets"></a>Pobierz wpisy tajne

W kodzie lokalnym można użyć metody[magazynu kluczy. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) , aby uzyskać wartość klucza tajnego według nazwy.

W obszarze uruchomienia przesłane przy użyciu [eksperymentu. Prześlij](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)Użyj metody [Run. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) . Ze względu na to, że przesłany przebieg ma świadomość swojego obszaru roboczego, ta metoda skrótów tworzy Tworzenie wystąpienia obszaru roboczego i zwraca wartość tajną bezpośrednio.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Należy zachować ostrożność, aby nie ujawniać wartości klucza tajnego przez zapisanie lub wydrukowanie.

Metody Set i Get mają również wersje usługi Batch [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) i [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) do uzyskiwania dostępu do wielu wpisów tajnych jednocześnie.

## <a name="next-steps"></a>Następne kroki

 * [Wyświetl przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Dowiedz się więcej o zabezpieczeniach przedsiębiorstwa z Azure Machine Learning](concept-enterprise-security.md)
