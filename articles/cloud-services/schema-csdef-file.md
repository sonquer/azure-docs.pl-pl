---
title: Schemat definicji Cloud Services platformy Azure (plik. csdef) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: 11660fe4f76cad9577b66e72893670dd1b9b559d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449076"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat definicji Cloud Services platformy Azure (plik. csdef)
Plik definicji usługi definiuje model usługi dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji usługi. Wartości ustawień konfiguracji są ustawiane w pliku konfiguracji usługi zgodnie z opisem w [schemacie konfiguracji usługi w chmurze (klasycznej)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Domyślnie plik schematu konfiguracji Diagnostyka Azure jest instalowany w katalogu `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Zastąp `<version>` zainstalowaną wersją [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="basic-service-definition-schema"></a>Schemat definicji usługi podstawowej
Plik definicji usługi musi zawierać jeden `ServiceDefinition` elementu. Definicja usługi musi zawierać co najmniej jeden element roli (`WebRole` lub `WorkerRole`). Może zawierać do 25 ról zdefiniowanych w jednej definicji i można mieszać typy ról. Definicja usługi zawiera również opcjonalny `NetworkTrafficRules` element, który ogranicza, które role mogą komunikować się z określonymi wewnętrznymi punktami końcowymi. Definicja usługi zawiera również opcjonalny `LoadBalancerProbes` element, który zawiera określone przez klienta sondy kondycji punktów końcowych.

Podstawowy format pliku definicji usługi jest następujący:.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definicje schematu
W poniższych tematach opisano schemat:

- [LoadBalancerProbe — schemat](schema-csdef-loadbalancerprobe.md)
- [WebRole — schemat](schema-csdef-webrole.md)
- [WorkerRole — schemat](schema-csdef-workerrole.md)
- [NetworkTrafficRules — schemat](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a>Element ServiceDefinition
Element `ServiceDefinition` jest elementem najwyższego poziomu pliku definicji usługi.

W poniższej tabeli opisano atrybuty elementu `ServiceDefinition`.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagany. Nazwa usługi. Nazwa musi być unikatowa w ramach konta usługi.|
| topologyChangeDiscovery | Element opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast` — wysyła aktualizację tak szybko, jak to możliwe, do wszystkich wystąpień ról. W przypadku wybrania opcji, rola powinna być w stanie obsługiwać aktualizację topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk` — wysyła aktualizację do każdego wystąpienia roli w sposób sekwencyjny po pomyślnym zaakceptowaniu przez poprzednie wystąpienie aktualizacji.|
| schemaVersion           | Element opcjonalny. Określa wersję schematu definicji usługi. Wersja schematu umożliwia programowi Visual Studio wybranie poprawnych narzędzi zestawu SDK, które będą używane do sprawdzania poprawności schematu w przypadku, gdy więcej niż jedna wersja zestawu SDK jest zainstalowana obok siebie.|
| upgradeDomainCount      | Element opcjonalny. Określa liczbę domen uaktualnienia, dla których przydzielono role w tej usłudze. Wystąpienia roli są przypisywane do domeny uaktualnienia podczas wdrażania usługi. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie roli usługi w chmurze lub wdrożenia](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Zarządzanie dostępności maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [co to jest model usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia to 5.|
