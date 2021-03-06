---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — Brama siatki sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń pobierania i usuwania zasobów bramy Service Fabric siatki.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906022"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Pobierz i Usuń zasoby bramy siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób bramy. |
| list | Wyświetla listę wszystkich zasobów bramy. |
| pokaż | Pobiera zasób bramy o podaną nazwę. |

## <a name="sfctl-mesh-gateway-delete"></a>Usuwanie bramy sieci sfctl
Usuwa zasób bramy.

Usuwa zasób bramy identyfikowany przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa zasobu bramy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-gateway-list"></a>Lista bram siatki sfctl
Wyświetla listę wszystkich zasobów bramy.

Pobiera informacje o wszystkich zasobach bramy w danej grupie zasobów. Informacje obejmują opis i inne właściwości bramy.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-gateway-show"></a>Pokaż bramę siatki sfctl
Pobiera zasób bramy o podaną nazwę.

Pobiera informacje o zasobie bramy o podanej nazwie. Informacje obejmują opis i inne właściwości bramy.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa zasobu bramy. |

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