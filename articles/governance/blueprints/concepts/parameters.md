---
title: Używanie parametrów do tworzenia planów dynamicznych
description: Poznaj parametry statyczne i dynamiczne oraz sposób ich używania do tworzenia bezpiecznych i dynamicznych planów.
ms.date: 03/12/2019
ms.topic: conceptual
ms.openlocfilehash: 68987b3e0f418721986003dc796f00ac1dd6dda1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644974"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Tworzenie dynamicznych planów za poorednictwem parametrów

W pełni zdefiniowany plan z różnymi artefaktami (takimi jak grupy zasobów, szablony Menedżer zasobów, zasady lub przypisania ról) umożliwia szybkie tworzenie i spójne Tworzenie obiektów na platformie Azure. Aby umożliwić elastyczne korzystanie z tych wzorców i kontenerów projektu wielokrotnego użytku, plany platformy Azure obsługują parametry. Parametr tworzy elastyczność podczas definiowania i przypisywania, aby zmienić właściwości artefaktów wdrożonych przez plan.

Prostym przykładem jest artefakt grupy zasobów. Po utworzeniu grupy zasobów muszą być podane dwie wymagane wartości: Nazwa i lokalizacja. W przypadku dodawania grupy zasobów do planu, jeśli parametry nie istnieją, należy zdefiniować tę nazwę i lokalizację dla każdego użycia planu. To powtórzenie spowodowałoby każde użycie planu do tworzenia artefaktów w tej samej grupie zasobów. Zasoby znajdujące się w tej grupie zasobów byłyby zduplikowane i powodują konflikt.

> [!NOTE]
> Nie jest to problem z dwoma różnymi planami, aby uwzględnić grupę zasobów o tej samej nazwie.
> Jeśli grupa zasobów uwzględniona w planie już istnieje, plan nadal tworzy powiązane artefakty w tej grupie zasobów. Może to spowodować konflikt, ponieważ dwa zasoby o tej samej nazwie i typie zasobu nie mogą istnieć w ramach subskrypcji.

Rozwiązaniem tego problemu są parametry. Plany umożliwiają definiowanie wartości dla każdej właściwości artefaktu podczas przypisywania do subskrypcji. Parametr umożliwia ponowne użycie planu, który tworzy grupę zasobów i inne zasoby w ramach jednej subskrypcji bez konfliktu.

## <a name="blueprint-parameters"></a>Parametry strategii

Za pomocą interfejsu API REST, parametry można tworzyć na podstawie samego planu. Parametry te różnią się od parametrów dla każdego z obsługiwanych artefaktów. Gdy parametr jest tworzony w planie, może być używany przez artefakty w tym planie. Przykładem może być prefiks nazwy grupy zasobów. Artefakt może użyć parametru strategii, aby utworzyć "głównie dynamiczny" parametr. Jako że parametr można także zdefiniować podczas przypisywania, ten wzorzec umożliwia spójność, która może być zgodna z regułami nazewnictwa. Aby uzyskać instrukcje, zobacz [Ustawianie parametrów statycznych — parametr poziomu](#blueprint-level-parameter)planu.

### <a name="using-securestring-and-secureobject-parameters"></a>Używanie parametrów secureString i secureobject

Ponieważ _artefakt_ szablonu Menedżer zasobów obsługuje parametry typu **secureString** i **secureobject** , plany platformy Azure wymagają, aby każdy z nich był połączony z Azure Key Vault.
Ta miara zabezpieczeń zapobiega niebezpiecznemu wykorzystaniu wpisów tajnych wraz z planem i zachęcaniu do pracy z bezpiecznymi wzorcami. Plany platformy Azure wspierają ten środek zabezpieczeń, co umożliwia wykrywanie dołączania dowolnego bezpiecznego parametru w _artefaktie_szablonu Menedżer zasobów. Podczas przypisywania zostanie wyświetlony komunikat o następujących właściwościach Key Vault na wykryty bezpieczny parametr:

- Identyfikator zasobu Key Vault
- Key Vault nazwa klucza tajnego
- Key Vault wersja klucza tajnego

Jeśli przypisanie planu używa **tożsamości zarządzanej przypisanej do systemu**, przywoływane Key Vault _muszą_ istnieć w tej samej subskrypcji, do której jest przypisana definicja planu.

Jeśli przypisanie planu używa **tożsamości zarządzanej przypisanej przez użytkownika**, przywoływane Key Vault _mogą_ istnieć w scentralizowanej subskrypcji. Tożsamość zarządzana musi mieć przyznane odpowiednie prawa na Key Vault przed przypisaniem planu.

> [!IMPORTANT]
> W obu przypadkach Key Vault musi mieć **włączony dostęp do Azure Resource Manager dla wdrożenia szablonu** skonfigurowanego na stronie **zasady dostępu** . Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [Key Vault-Enable Template Deployment](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [Key Vault Omówienie](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Typy parametrów

### <a name="static-parameters"></a>Parametry statyczne

Wartość parametru zdefiniowana w definicji strategii jest nazywana **parametrem statycznym**, ponieważ każde użycie strategii spowoduje wdrożenie artefaktu przy użyciu tej wartości statycznej. W przykładzie grupy zasobów, gdy nie ma sensu nazwy grupy zasobów, może to mieć sens dla lokalizacji. Następnie każde przypisanie planu utworzy grupę zasobów, niezależnie od jej wywołania podczas przypisywania w tej samej lokalizacji. Ta elastyczność pozwala na selektywne Określanie, co jest wymagane, a co można zmienić podczas przypisywania.

#### <a name="setting-static-parameters-in-the-portal"></a>Ustawianie parametrów statycznych w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij istniejący plan, a następnie kliknij pozycję **Edytuj plan** lub kliknij pozycję **+ Utwórz plan** i Wypełnij informacje na karcie **podstawy** .

1. Kliknij przycisk **Dalej: artefakty** lub kliknij kartę **artefakty** .

1. Artefakty dodawane do planu, który ma opcje parametrów, są wyświetlane **parametry X z Y wypełnione** w kolumnie **Parameters** . Kliknij wiersz artefaktu, aby edytować parametry artefaktu.

   ![Parametry strategii w definicji planu](../media/parameters/parameter-column.png)

1. Na stronie **Edytowanie artefaktu** są wyświetlane opcje wartości odpowiednie dla artefaktu klikniętego w polu. Każdy parametr artefaktu ma tytuł, pole wartości i pole wyboru. Ustaw pole tak, aby nie było sprawdzane jako **parametr statyczny**. W poniższym przykładzie tylko _Lokalizacja_ jest **parametrem statycznym** , ponieważ nie jest zaznaczone i sprawdzana jest _Nazwa grupy zasobów_ .

   ![Zaplanuj parametry statyczne w artefaktie planu](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Ustawianie parametrów statycznych z interfejsu API REST

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

##### <a name="blueprint-level-parameter"></a>Parametr poziomu planu

Podczas tworzenia strategii za pomocą interfejsu API REST można utworzyć [Parametry](#blueprint-parameters)strategii. W tym celu należy użyć następującego identyfikatora URI interfejsu API REST i formatu treści:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Po utworzeniu parametru poziomu planu można go używać na artefaktach dodanych do tego planu.
Poniższy przykład interfejsu API REST tworzy artefakt przypisania roli w planie i używa parametru poziomu planu.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

W tym przykładzie właściwość **principalIds** używa parametru poziomu strategii **właściciele** przy użyciu wartości `[parameters('owners')]`. Ustawienie parametru na artefaktie przy użyciu parametru poziomu planu jest nadal przykładem **parametru statycznego**. Nie można ustawić parametru poziomu planu podczas przypisywania planu i będzie on taki sam jak wartość w każdym przypisaniu.

##### <a name="artifact-level-parameter"></a>Parametr poziomu artefaktu

Tworzenie **parametrów statycznych** w artefaktie jest podobne, ale przyjmuje wartość prostą zamiast używać funkcji `parameters()`. Poniższy przykład tworzy dwa parametry statyczne, **TagName** i **tagValue**. Wartość na każdej z nich jest podawana bezpośrednio i nie używa wywołania funkcji.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parametry dynamiczne

Przeciwieństwem parametru statycznego jest **parametr dynamiczny**. Ten parametr nie jest zdefiniowany w planie, ale zamiast tego jest definiowany podczas każdego przydziału planu. W przykładzie grupy zasobów użycie **parametru dynamicznego** ma sens dla nazwy grupy zasobów. Zapewnia inną nazwę dla każdego przydziału planu. Aby zapoznać się z listą funkcji planu, zobacz Dokumentacja [funkcji](../reference/blueprint-functions.md) strategii.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ustawianie parametrów dynamicznych w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij prawym przyciskiem myszy strategię, którą chcesz przypisać. Wybierz pozycję **Przypisz plan** lub kliknij plan, który chcesz przypisać, a następnie kliknij przycisk **Przypisz plan** .

1. Na stronie **Przypisz plan** Znajdź sekcję **Parametry artefaktu** . Każdy artefakt z co najmniej jednym **parametrem dynamicznym** wyświetla artefakt i opcje konfiguracji. Podaj wymagane wartości parametrów przed przypisaniem planu. W poniższym przykładzie _Nazwa_ jest **parametrem dynamicznym** , który musi być zdefiniowany, aby można było wykonać przypisanie do planu.

   ![Parametr dynamiczny strategii podczas przypisywania strategii](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ustawianie parametrów dynamicznych z interfejsu API REST

Ustawienie **parametrów dynamicznych** podczas przypisywania jest wykonywane przez bezpośrednie wprowadzenie wartości. Zamiast używać funkcji, takiej jak [Parametry ()](../reference/blueprint-functions.md#parameters), podana wartość jest odpowiednim ciągiem. Artefakty dla grupy zasobów są definiowane przy użyciu właściwości "Nazwa szablonu", **Nazwa**i **Lokalizacja** . Wszystkie inne parametry dla dołączonego artefaktu są zdefiniowane w **parametrach** z **\<nazw\>** i pary kluczy **wartości** . Jeśli plan jest skonfigurowany dla parametru dynamicznego, który nie jest dostarczany podczas przypisywania, przypisanie zakończy się niepowodzeniem.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Następne kroki

- Zobacz listę [funkcji](../reference/blueprint-functions.md)strategii.
- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).