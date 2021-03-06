---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 4f861d5e7832512e2c2732f1ce5dbf6ac76b91b1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935816"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla ACI wartość musi być `ACI`. |
| `containerResourceRequirements` | Nie dotyczy | Kontener dla jednostek procesora i pamięci. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia. Wartości domyślne, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci Web. Domyślne, `0.5` |
| `location` | `location` | Region świadczenia usługi Azure, w którym ma zostać wdrożona ta usługa sieci Web. Jeśli nie zostanie określona, zostanie użyta Lokalizacja obszaru roboczego. Więcej szczegółów na temat dostępnych regionów można znaleźć tutaj: [regiony ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Określa, czy włączyć uwierzytelnianie dla tej usługi sieci Web. Wartość domyślna to false |
| `sslEnabled` | `ssl_enabled` | Określa, czy włączyć protokół SSL dla tej usługi sieci Web. Wartością domyślną jest false. |
| `appInsightsEnabled` | `enable_app_insights` | Określa, czy włączyć AppInsights dla tej usługi sieci Web. Wartość domyślna to false |
| `sslCertificate` | `ssl_cert_pem_file` | Plik certyfikatu wymagany w przypadku włączenia protokołu SSL |
| `sslKey` | `ssl_key_pem_file` | Plik klucza wymagany w przypadku włączenia protokołu SSL |
| `cname` | `ssl_cname` | Rekord CNAME dla jeśli jest włączony protokół SSL |
| `dnsNameLabel` | `dns_name_label` | Etykieta nazwy DNS dla punktu końcowego oceniania. Jeśli nie zostanie określona, zostanie wygenerowana unikatowa etykieta nazwy DNS dla punktu końcowego oceniania. |

Poniższy kod JSON jest przykładową konfiguracją wdrożenia do użycia z interfejsem wiersza polecenia:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```