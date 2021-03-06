---
title: Element interfejsu użytkownika listy rozwijanej
description: Opisuje element interfejsu użytkownika Microsoft. Common. DropDown dla Azure Portal. Użyj, aby wybrać dostępne opcje podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652387"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown — element interfejsu użytkownika

Kontrolka wyboru z listą rozwijaną.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"two"
```

## <a name="remarks"></a>Uwagi

- Etykieta dla `constraints.allowedValues` jest wyświetlanym tekstem elementu, a jego wartość jest wartością wyjściową elementu, gdy jest zaznaczone.
- Jeśli jest określony, wartość domyślna musi być etykietą obecną w `constraints.allowedValues`. Jeśli nie zostanie określony, zostanie wybrany pierwszy element w `constraints.allowedValues`. Wartość domyślna to **null**.
- `constraints.allowedValues` musi mieć co najmniej jeden element.
- Aby emulować wartość, która nie jest wymagana, Dodaj element z etykietą i wartością `""` (pusty ciąg) do `constraints.allowedValues`.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
