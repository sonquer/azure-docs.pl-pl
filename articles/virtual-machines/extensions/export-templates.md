---
title: Eksportowanie grup zasobów platformy Azure zawierających rozszerzenia maszyn wirtualnych
description: Eksportowanie szablonów Menedżer zasobów zawierających rozszerzenia maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073123"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych

Grupy zasobów platformy Azure można eksportować do nowego szablonu Menedżer zasobów, który można następnie ponownie wdrożyć. Proces eksportowania interpretuje istniejące zasoby i tworzy szablon Menedżer zasobów, który po wdrożeniu powoduje powstanie podobnej grupy zasobów. W przypadku korzystania z opcji eksportowania grupy zasobów dla grupy zasobów zawierającej rozszerzenia maszyn wirtualnych należy rozważyć kilka elementów, takich jak zgodność rozszerzenia i ustawienia chronione.

Ten dokument zawiera szczegółowe informacje o tym, jak działa proces eksportowania grupy zasobów w odniesieniu do rozszerzeń maszyn wirtualnych, w tym listy obsługiwanych rozszerzeń i szczegółowych informacji na temat obsługi zabezpieczonych danych.

## <a name="supported-virtual-machine-extensions"></a>Obsługiwane rozszerzenia maszyny wirtualnej

Dostępnych jest wiele rozszerzeń maszyn wirtualnych. Nie wszystkie rozszerzenia można eksportować do szablonu Menedżer zasobów przy użyciu funkcji "skrypt automatyzacji". Jeśli rozszerzenie maszyny wirtualnej nie jest obsługiwane, należy je ręcznie umieścić w wyeksportowanym szablonie.

Następujące rozszerzenia można eksportować za pomocą funkcji skryptu automatyzacji.

| Wewnętrzny ||||
|---|---|---|---|
| Kopia zapasowa Acronis | Usługi Datadog Agent systemu Windows | Stosowanie poprawek systemu operacyjnego w systemie Linux | Migawka maszyny wirtualnej Linux
| Acronis kopia zapasowa systemu Linux | Rozszerzenie platformy Docker | Agent Puppet |
| Bg Info | Rozszerzenie DSC | Site 24x7 APM Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Serwer 24x7 z systemem Linux |
| CTM agenta BMC systemu Windows | DynaTrace systemu Windows | Lokacja 24x7 systemu Windows Server |
| Klient Chef | Usługa Defender Security Application HPE | Trend Micro DSA |
| Niestandardowy skrypt | IaaS chroniące przed złośliwym kodem | Trend Micro DSA Linux |
| Rozszerzenie niestandardowego skryptu | Diagnostyka IaaS | Dostęp do maszyny wirtualnej dla systemu Linux |
| Skrypt niestandardowy dla systemu Linux | Klient Chef systemu Linux | Dostęp do maszyny wirtualnej dla systemu Linux |
| Agent usługi Datadog systemu Linux | Diagnostyka systemu Linux | Migawka maszyny wirtualnej |

## <a name="export-the-resource-group"></a>Eksportowanie grupy zasobów

Aby wyeksportować grupę zasobów do szablonu wielokrotnego użytku, wykonaj następujące czynności:

1. Logowanie się do witryny Azure Portal
2. W menu centrum kliknij pozycję grupy zasobów.
3. Wybierz docelową grupę zasobów z listy
4. W bloku grupy zasobów kliknij pozycję skrypt automatyzacji.

![Eksportowanie szablonu](./media/export-templates/template-export.png)

Skrypt Azure Resource Manager automations generuje szablon Menedżer zasobów, plik parametrów i kilka przykładowych skryptów wdrażania, takich jak PowerShell i interfejs wiersza polecenia platformy Azure. Na tym etapie wyeksportowany szablon można pobrać przy użyciu przycisku Pobierz, który został dodany jako nowy szablon do biblioteki szablonów lub ponownie wdrożony przy użyciu przycisku Wdróż.

## <a name="configure-protected-settings"></a>Konfigurowanie ustawień chronionych

Wiele rozszerzeń maszyn wirtualnych platformy Azure zawiera konfigurację ustawień chronionych, która szyfruje dane poufne, takie jak poświadczenia i ciągi konfiguracyjne. Ustawienia chronione nie są eksportowane ze skryptem automatyzacji. W razie potrzeby chronione ustawienia należy ponownie wstawić do wyeksportowanego szablonu.

### <a name="step-1---remove-template-parameter"></a>Krok 1. Usuwanie parametru szablonu

Po wyeksportowaniu grupy zasobów tworzony jest jeden parametr szablonu, aby podać wartość eksportowanych chronionych ustawień. Ten parametr można usunąć. Aby usunąć parametr, zapoznaj się z listą parametrów i Usuń parametr, który wygląda podobnie do tego przykładu JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2. Uzyskiwanie właściwości ustawień chronionych

Ponieważ każde chronione ustawienie ma zestaw wymaganych właściwości, należy zebrać listę tych właściwości. Każdy parametr konfiguracji ustawień chronionych można znaleźć w [schemacie Azure Resource Manager w witrynie GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ten schemat zawiera tylko zestawy parametrów dla rozszerzeń wymienionych w sekcji przegląd tego dokumentu. 

W repozytorium schematów Wyszukaj żądane rozszerzenie, w tym przykładzie `IaaSDiagnostics`. Po zlokalizowaniu `protectedSettings` obiektu, zanotuj każdy z nich. W przykładzie rozszerzenia `IaasDiagnostic` wymagane parametry są `storageAccountName`, `storageAccountKey`i `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3. ponowne tworzenie chronionej konfiguracji

W wyeksportowanym szablonie Wyszukaj `protectedSettings` i Zastąp wyeksportowany obiekt chronionego ustawienia nowym, który zawiera wymagane parametry rozszerzenia i wartość dla każdej z nich.

W przykładzie rozszerzenia `IaasDiagnostic` Nowa konfiguracja chronionego ustawienia będzie wyglądać następująco:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Końcowy zasób rozszerzenia wygląda podobnie do następującego przykładu JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

W przypadku używania parametrów szablonu do podania wartości właściwości należy je utworzyć. Podczas tworzenia parametrów szablonu dla wartości ustawień chronionych upewnij się, że używasz `SecureString` typu parametru, aby zabezpieczyć wartości poufne. Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Tworzenie szablonów Azure Resource Manager](../../resource-group-authoring-templates.md).

W przykładzie rozszerzenia `IaasDiagnostic` w sekcji Parametry szablonu Menedżer zasobów zostaną utworzone następujące parametry.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

W tym momencie szablon można wdrożyć przy użyciu dowolnej metody wdrażania szablonu.
