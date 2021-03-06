---
title: Struktura i składnia szablonu
description: Opisuje strukturę i właściwości szablonów Azure Resource Manager przy użyciu deklaracyjnej składni JSON.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 9cd602644ecf803e97254189cfc157d60713cc6c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209464"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Zrozumienie struktury i składni szablonów Azure Resource Manager

W tym artykule opisano strukturę szablonu Azure Resource Manager. Przedstawia różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach.

Ten artykuł jest przeznaczony dla użytkowników, którzy mają pewną wiedzę z szablonami Menedżer zasobów. Zawiera szczegółowe informacje na temat struktury szablonu. Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Format szablonu

W najprostszym strukturze szablon zawiera następujące elementy:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| $schema |Tak |Lokalizacja pliku schematu JSON opisującego wersję języka szablonu.<br><br> W przypadku wdrożeń grup zasobów Użyj: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>W przypadku wdrożeń subskrypcji Użyj: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Tak |Wersja szablonu (na przykład 1.0.0.0). Możesz podać dowolną wartość dla tego elementu. Użyj tej wartości, aby udokumentować znaczące zmiany w szablonie. W przypadku wdrażania zasobów przy użyciu szablonu Ta wartość może być używana do upewnienia się, że odpowiedni szablon jest używany. |
| apiProfile |Nie | Wersja interfejsu API, która służy jako kolekcja wersji interfejsu API dla typów zasobów. Użyj tej wartości, aby uniknąć konieczności określania wersji interfejsu API dla każdego zasobu w szablonie. W przypadku określenia wersji profilu interfejsu API i nieokreślenia wersji interfejsu API dla typu zasobu Menedżer zasobów używa wersji interfejsu API dla tego typu zasobu, który jest zdefiniowany w profilu.<br><br>Właściwość profil interfejsu API jest szczególnie przydatna w przypadku wdrażania szablonu w różnych środowiskach, takich jak Azure Stack i globalny platformę Azure. Użyj wersji profilu interfejsu API, aby upewnić się, że szablon automatycznie używa wersji, które są obsługiwane w obu środowiskach. Listę bieżących wersji profilu interfejsu API i wersje interfejsu API zasobów zdefiniowane w profilu znajdują się w temacie [profil interfejsu API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Aby uzyskać więcej informacji, zobacz [śledzenie wersji przy użyciu profilów interfejsu API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nie |Wartości, które są dostarczane, gdy wdrożenie jest wykonywane w celu dostosowania wdrożenia zasobów. |
| [modyfikacj](#variables) |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonów. |
| [obowiązki](#functions) |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w ramach szablonu. |
| [produkcyjnych](#resources) |Tak |Typy zasobów wdrożone lub zaktualizowane w grupie zasobów lub subskrypcji. |
| [wydajności](#outputs) |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element ma właściwości, które można ustawić. W tym artykule opisano bardziej szczegółowe sekcje szablonu.

## <a name="parameters"></a>Parametry

W sekcji Parametry szablonu należy określić, które wartości można wprowadzać podczas wdrażania zasobów. W szablonie jest ograniczona do 256 parametrów. Można zmniejszyć liczbę parametrów za pomocą obiektów, które zawierają wiele właściwości.

Dostępne właściwości dla parametru to:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| Nazwa parametru |Tak |Nazwa parametru. Musi być prawidłowym identyfikatorem języka JavaScript. |
| typ |Tak |Typ wartości parametru. Dozwolone typy i wartości to **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**i **Array**. Zobacz [typy danych](#data-types). |
| Właściwość defaultValue |Nie |Wartość domyślna parametru, jeśli nie podano wartości dla parametru. |
| allowedValues |Nie |Tablica dozwolonych wartości parametru, aby upewnić się, że podano odpowiednią wartość. |
| minValue |Nie |Minimalna wartość parametrów typu int, ta wartość jest dopuszczalna. |
| maxValue |Nie |Maksymalna wartość parametrów typu int, ta wartość jest dopuszczalna. |
| Element minLength |Nie |Minimalna długość dla parametrów typu String, Secure String i Array, ta wartość jest włącznie. |
| Element maxLength |Nie |Maksymalna długość parametrów ciągu, bezpiecznego ciągu i typu tablicy, ta wartość jest włącznie. |
| description |Nie |Opis parametru, który jest wyświetlany użytkownikom w portalu. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](#comments). |

Aby uzyskać przykłady użycia parametrów, zobacz [Parametry w szablonach Azure Resource Manager](template-parameters.md).

### <a name="data-types"></a>Typy danych

W przypadku liczb całkowitych przewidzianych jako parametry wbudowane zakres wartości może być ograniczony przez zestaw SDK lub narzędzie wiersza polecenia, które jest używane do wdrożenia. Na przykład w przypadku wdrażania szablonu przy użyciu programu PowerShell typy całkowite mogą przyjmować wartość od-2147483648 do 2147483647. Aby uniknąć tego ograniczenia, określ w [pliku parametrów](parameter-files.md)duże wartości całkowite. Typy zasobów stosują własne limity dla właściwości Integer.

Podczas określania wartości logicznych i liczb całkowitych w szablonie nie należy ująć wartości ze znakami cudzysłowu. Początkową i końcową wartość ciągu z podwójnymi cudzysłowami.

Obiekty zaczynają się od lewego nawiasu klamrowego i kończą z prawego nawiasu klamrowego. Tablice zaczynają się od lewego nawiasu i kończą się za pomocą prawego nawiasu.

Po wdrożeniu zasobów nie można odczytać zabezpieczonych ciągów i bezpiecznych obiektów.

Przykłady typów danych formatowania można znaleźć w temacie [formaty typów parametrów](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Zmienne

W sekcji zmienne można skonstruować wartości, które mogą być używane w całym szablonie. Nie musisz definiować zmiennych, ale często upraszczamy szablon przez zredukowanie złożonych wyrażeń.

W poniższym przykładzie przedstawiono dostępne opcje definiowania zmiennej:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Aby uzyskać informacje o używaniu `copy` do tworzenia kilku wartości dla zmiennej, zobacz [zmienna iteracja](copy-variables.md).

Aby zapoznać się z przykładami użycia zmiennych, zobacz [zmienne w szablonie Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Funkcje

W ramach szablonu można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Zwykle definiuje się skomplikowane wyrażenia, które nie powinny być powtarzane w całym szablonie. Funkcje zdefiniowane przez użytkownika można utworzyć na podstawie wyrażeń i [funkcji](template-functions.md) obsługiwanych w szablonach.

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Gdy używana jest [Funkcja Parameters](template-functions-deployment.md#parameters) w funkcji zdefiniowanej przez użytkownika, są ograniczone do parametrów tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może używać [funkcji Reference](template-functions-resource.md#reference).
* Parametry funkcji nie mogą mieć wartości domyślnych.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| przestrzeń nazw |Tak |Przestrzeń nazw dla funkcji niestandardowych. Użyj, aby uniknąć konfliktu nazw z funkcjami szablonu. |
| Nazwa funkcji |Tak |Nazwa funkcji niestandardowej. Podczas wywoływania funkcji Połącz nazwę funkcji z przestrzenią nazw. Na przykład aby wywołać funkcję o nazwie uniqueName w przestrzeni nazw contoso, użyj `"[contoso.uniqueName()]"`. |
| Nazwa parametru |Nie |Nazwa parametru, który ma być używany w funkcji niestandardowej. |
| wartość parametru-value |Nie |Typ wartości parametru. Dozwolone typy i wartości to **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**i **Array**. |
| Typ danych wyjściowych |Tak |Typ wartości danych wyjściowych. Wartości wyjściowe obsługują te same typy jak parametry wejściowe funkcji. |
| Wartość wyjściowa |Tak |Wyrażenie języka szablonu, które jest oceniane i zwracane przez funkcję. |

Przykłady korzystania z funkcji niestandardowych można znaleźć [w temacie Funkcje zdefiniowane przez użytkownika w szablonie Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Zasoby

W sekcji Resources (zasoby) zdefiniujesz wdrożone lub zaktualizowane zasoby.

Należy zdefiniować zasoby o następującej strukturze:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| condition | Nie | Wartość logiczna wskazująca, czy zasób zostanie zainicjowany podczas tego wdrożenia. Gdy `true`, zasób zostanie utworzony podczas wdrażania. Gdy `false`, zasób jest pomijany dla tego wdrożenia. Zobacz [warunek](conditional-resource-deployment.md). |
| typ |Tak |Typ zasobu. Ta wartość jest kombinacją przestrzeni nazw dostawcy zasobów i typu zasobu (np **. Microsoft. Storage/storageAccounts**). Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). W przypadku zasobu podrzędnego format typu zależy od tego, czy jest on zagnieżdżony w obrębie zasobu nadrzędnego, czy zdefiniowany poza zasobem nadrzędnym. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |
| apiVersion |Tak |Wersja interfejsu API REST do użycia podczas tworzenia zasobu. Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). |
| {1&gt;nazwa&lt;1} |Tak |Nazwa zasobu. Nazwa musi następować zgodnie z ograniczeniami składnika URI zdefiniowanymi w RFC3986. Usługi platformy Azure, które uwidaczniają nazwę zasobu podmiotom zewnętrznym, sprawdzają poprawność nazwy, aby upewnić się, że nie jest próbą sfałszowania innej tożsamości. W przypadku zasobu podrzędnego format nazwy zależy od tego, czy jest on zagnieżdżony w obrębie zasobu nadrzędnego, czy zdefiniowany poza zasobem nadrzędnym. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |
| komentarze |Nie |Twoje notatki umożliwiające dokumentowanie zasobów w szablonie. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](template-syntax.md#comments). |
| lokalizacja |Różnie |Obsługiwane lokalizacje geograficzne podanego zasobu. Można wybrać dowolną z dostępnych lokalizacji, ale zazwyczaj warto ją wybrać blisko użytkowników. Zwykle warto również umieścić zasoby, które współpracują ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale niektóre typy (takie jak przypisanie roli) nie wymagają lokalizacji. Zobacz [Ustawianie lokalizacji zasobu](resource-location.md). |
| dependsOn |Nie |Zasoby, które muszą zostać wdrożone przed wdrożeniem tego zasobu. Menedżer zasobów oblicza zależności między zasobami i wdraża je w odpowiedniej kolejności. Gdy zasoby nie są od siebie zależne, są wdrażane równolegle. Wartość może być rozdzielaną przecinkami listą nazw zasobów lub unikatowych identyfikatorów zasobów. Tylko zasoby, które są wdrożone w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie, muszą już istnieć. Należy unikać dodawania niepotrzebnych zależności, ponieważ mogą one spowalniać wdrożenie i tworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące ustawiania zależności, zobacz [Definiowanie zależności w szablonach Azure Resource Manager](define-resource-dependency.md). |
| tags |Nie |Tagi, które są skojarzone z zasobem. Zastosuj Tagi, aby logicznie organizować zasoby w ramach subskrypcji. |
| sku | Nie | Niektóre zasoby umożliwiają wartości, które definiują jednostkę SKU do wdrożenia. Na przykład można określić typ nadmiarowości dla konta magazynu. |
| Natur | Nie | Niektóre zasoby umożliwiają wartości, która definiuje typ wdrażanego zasobu. Na przykład można określić typ Cosmos DB, który ma zostać utworzony. |
| kopiowane |Nie |Jeśli potrzebujesz więcej niż jednego wystąpienia, liczba zasobów do utworzenia. Domyślny tryb jest równoległy. Określ tryb seryjny, gdy nie chcesz, aby wszystkie lub zasoby zostały wdrożone w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Tworzenie kilku wystąpień zasobów w Azure Resource Manager](copy-resources.md). |
| zamierza | Nie | Niektóre zasoby umożliwiają wartości definiujące plan do wdrożenia. Na przykład można określić obraz z witryny Marketplace dla maszyny wirtualnej. |
| właściwości |Nie |Ustawienia konfiguracji dotyczące zasobów. Wartości właściwości są takie same jak wartości podane w treści żądania dla operacji interfejsu API REST (Metoda PUT), aby utworzyć zasób. Możesz również określić tablicę kopiowania, aby utworzyć kilka wystąpień właściwości. Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). |
| zasoby |Nie |Zasoby podrzędne, które są zależne od definiowanego zasobu. Podaj tylko typy zasobów, które są dozwolone przez schemat zasobu nadrzędnego. Nie jest implikowana zależność od zasobu nadrzędnego. Należy jawnie zdefiniować tę zależność. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |

## <a name="outputs"></a>Wyjścia

W sekcji danych wyjściowych należy określić wartości, które są zwracane z wdrożenia. Zazwyczaj zwracane są wartości z zasobów, które zostały wdrożone.

Poniższy przykład pokazuje strukturę definicję danych wyjściowych:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| Nazwa wyjściowa |Tak |Nazwa wartości danych wyjściowych. Musi być prawidłowym identyfikatorem języka JavaScript. |
| condition |Nie | Wartość logiczna wskazująca, czy ta wartość wyjściowa jest zwracana. Gdy `true`, wartość jest uwzględniana w danych wyjściowych dla wdrożenia. Gdy `false`, wartość wyjściowa jest pomijana dla tego wdrożenia. Gdy nie zostanie określony, wartość domyślna to `true`. |
| typ |Tak |Typ wartości danych wyjściowych. Wartości wyjściowe obsługują te same typy jako parametrów wejściowych szablonu. W przypadku określenia elementu **SecureString** dla typu danych wyjściowych wartość nie jest wyświetlana w historii wdrożenia i nie można jej pobrać z innego szablonu. Aby użyć wartości klucza tajnego w więcej niż jednym szablonie, należy zapisać klucz tajny w Key Vault i odwołać się do wpisu tajnego w pliku parametrów. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](key-vault-parameter.md). |
| {1&gt;value&lt;1} |Tak |Wyrażenie języka szablonu, który jest obliczany i zwracany, jako wartość danych wyjściowych. |

Aby zapoznać się z przykładami sposobu korzystania z danych wyjściowych, zobacz dane [wyjściowe w szablonie Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Komentarze i metadane

Istnieje kilka opcji dodawania komentarzy i metadanych do szablonu.

### <a name="comments"></a>Komentarze

W przypadku komentarzy wbudowanych można użyć obu `//` lub `/* ... */`, ale ta składnia nie działa z wszystkimi narzędziami. Nie można użyć edytora szablonów portalu do pracy nad szablonami z wbudowanymi komentarzami. W przypadku dodania tego stylu komentarza upewnij się, że narzędzia, z których korzystasz, obsługują wbudowane Komentarze JSON.

> [!NOTE]
> Aby wdrożyć szablony z komentarzem przy użyciu interfejsu wiersza polecenia platformy Azure, należy użyć przełącznika `--handle-extended-json-format`.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

W Visual Studio Code [rozszerzenie narzędzi Azure Resource Manager](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) może automatycznie wykryć szablon Menedżer zasobów i odpowiednio zmienić tryb języka. Jeśli widzisz **szablon Azure Resource Manager** w prawym dolnym rogu vs Code, możesz użyć komentarzy wbudowanych. Komentarze w tekście nie są już oznaczone jako nieprawidłowe.

![Tryb szablonu Azure Resource Manager Visual Studio Code](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadane

Obiekt `metadata` można dodać niemal w dowolnym miejscu w szablonie. Menedżer zasobów ignoruje obiekt, ale Edytor JSON może ostrzec, że właściwość jest nieprawidłowa. W obiekcie Zdefiniuj potrzebne właściwości.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Dla **parametrów**dodaj obiekt `metadata` z właściwością `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Podczas wdrażania szablonu za pomocą portalu, tekst w opisie jest automatycznie używany jako Porada dla tego parametru.

![Pokaż poradę dotyczącą parametrów](./media/template-syntax/show-parameter-tip.png)

W przypadku **zasobów**dodaj element `comments` lub obiekt metadanych. W poniższym przykładzie pokazano zarówno element komentarzy, jak i obiekt metadanych.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

W **przypadku danych wyjściowych**Dodaj obiekt metadanych do wartości wyjściowej.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Nie można dodać obiektu metadanych do funkcji zdefiniowanych przez użytkownika.

## <a name="multi-line-strings"></a>Ciągi wielowierszowe

Możesz przerwać ciąg w wielu wierszach. Na przykład Właściwość Location i jeden z komentarzy w poniższym przykładzie JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Aby wdrożyć szablony z użyciem ciągów wielowierszowych przy użyciu interfejsu wiersza polecenia platformy Azure, należy użyć przełącznika `--handle-extended-json-format`.

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje o funkcjach, których można użyć w szablonie, zobacz [Azure Resource Manager Template Functions](template-functions.md).
* Aby połączyć kilka szablonów podczas wdrażania, zobacz [Używanie połączonych szablonów z Azure Resource Manager](linked-templates.md).
* Zalecenia dotyczące tworzenia szablonów można znaleźć w temacie [Azure Resource Manager Best Practices Template](template-best-practices.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia Menedżer zasobów szablonów, których można używać we wszystkich środowiskach platformy Azure i Azure Stack, zobacz [opracowywanie szablonów Azure Resource Manager dla spójności chmury](templates-cloud-consistency.md).
