---
title: Funkcje szablonu — logiczne
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager, aby określić wartości logiczne.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: aef520a26124a85f414c4f4aa1a3e307d383c29b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207217"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funkcje logiczne dla szablonów Azure Resource Manager

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [lub](#and)
* [logiczna](#bool)
* [przypadku](#if)
* [niemożliwe](#not)
* [lub](#or)

## <a name="and"></a>i

`and(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametrów mają wartość true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy wartość jest równa true. |
| arg2 |Yes |wartość logiczna |Druga wartość do sprawdzenia, czy ma wartość true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty do sprawdzenia, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wszystkie wartości mają wartość true; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>logiczna

`bool(arg1)`

Konwertuje parametr na wartość logiczną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |ciąg lub int |Wartość do przekonwertowania na wartość logiczną. |

### <a name="return-value"></a>Wartość zwracana
Wartość logiczna przekonwertowanej wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) pokazuje, jak używać bool z ciągiem lub liczbą całkowitą.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Zwraca wartość na podstawie tego, czy warunek ma wartość true lub false.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| condition |Yes |wartość logiczna |Wartość, aby sprawdzić, czy jest to wartość true lub false. |
| trueValue |Yes | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość true. |
| falseValue |Yes | ciąg, int, Object lub Array |Wartość, która ma zostać zwrócona, gdy warunek ma wartość false. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr ma **wartość true**; w przeciwnym razie zwraca trzeci parametr.

### <a name="remarks"></a>Uwagi

Gdy warunek ma **wartość true**, Szacowana jest tylko wartość true. Gdy warunek ma **wartość false**, Szacowana jest tylko wartość false. Za pomocą funkcji **if** można uwzględnić wyrażenia, które są warunkowo prawidłowe. Na przykład można odwołać się do zasobu, który istnieje w ramach jednego warunku, ale nie pod drugim warunkiem. Przykład warunkowego szacowania wyrażeń przedstawiono w poniższej sekcji.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) pokazuje, jak używać funkcji `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| yesOutput | Ciąg | tak |
| NoOutput | Ciąg | nie |
| objectOutput | Obiekt | {"test": "wartość1"} |

Poniższy [przykładowy szablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) pokazuje, jak używać tej funkcji z wyrażeniami, które są warunkowo prawidłowe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>niemożliwe

`not(arg1)`

Konwertuje wartość logiczną na wartość odwrotną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Wartość do przekonwertowania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli parametr ma **wartość false**. Zwraca **wartość false** , jeśli parametr ma **wartość true**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa **nie** z [równą](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

Dane wyjściowe z poprzedniego przykładu to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>lub

`or(arg1, arg2, ...)`

Sprawdza, czy parametr ma wartość true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy wartość jest równa true. |
| arg2 |Yes |wartość logiczna |Druga wartość do sprawdzenia, czy ma wartość true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty do sprawdzenia, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli dowolna wartość jest równa true; w przeciwnym razie **false**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [tworzenie Azure Resource Manager szablonów](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z Azure Resource Manager](linked-templates.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

