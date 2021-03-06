---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl siatkę secretvalue
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do pobierania i usuwania Service Fabric siatki secretvalue.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905967"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwanego zasobu tajnego. |
| list | Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego. |
| pokaż | Wyświetla określoną wartość zasobu tajnego. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl siatki secretvalue usuwanie
Usuwa określoną wartość nazwanego zasobu tajnego.

Usuwa zasób wartości tajnej identyfikowany przez nazwę. Nazwa zasobu jest zazwyczaj wersją skojarzoną z tą wartością. Usuwanie zakończy się niepowodzeniem, jeśli określona wartość jest używana.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-list"></a>Lista secretvalue siatki sfctl
Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego.

Pobiera informacje o wszystkich zasobach wartości tajnych określonego zasobu tajnego. Informacje te obejmują nazwy zasobów wartości tajnych, ale nie wartości rzeczywiste.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl siatki secretvalue Pokaż
Wyświetla określoną wartość zasobu tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |
| --show-value | Pokaż rzeczywistą wartość wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).