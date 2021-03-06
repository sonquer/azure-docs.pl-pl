---
title: Używanie Azure Key Vault w szablonach
description: Dowiedz się, jak używać usługi Azure Key Vault do bezpiecznego przekazywania wartości parametrów podczas wdrażania szablonu usługi Resource Manager
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 527002bee1bec4097cf62155efebae06880bad97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472394"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Samouczek: Integrowanie Azure Key Vault w rozmieszczeniu szablonu Menedżer zasobów

Dowiedz się, jak pobrać wpisy tajne z magazynu kluczy platformy Azure i przekazać klucze tajne jako parametry podczas wdrażania Azure Resource Manager. Wartość parametru nigdy nie jest ujawniana, ponieważ odwołuje się tylko do identyfikatora magazynu kluczy. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](./key-vault-parameter.md).

W samouczku [Ustawianie kolejności wdrażania zasobów](./template-tutorial-create-templates-with-dependent-resources.md) utworzysz maszynę wirtualną. Musisz podać nazwę użytkownika i hasło administratora maszyny wirtualnej. Zamiast podać hasło, możesz wstępnie zapisać hasło w magazynie kluczy platformy Azure, a następnie dostosować szablon w celu pobrania hasła z magazynu kluczy podczas wdrażania.

![Diagram przedstawiający integrację szablonu Menedżer zasobów z magazynem kluczy](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie magazynu kluczy
> * Otwieranie szablonu szybkiego startu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Visual Studio Code z rozszerzeniem Menedżer zasobów Tools. [Aby utworzyć szablony Azure Resource Manager, zobacz temat używanie Visual Studio Code](use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Oto przykład dla generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Sprawdź, czy wygenerowane hasło spełnia wymagania dotyczące hasła maszyny wirtualnej. Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Wymagania dotyczące hasła maszyny wirtualnej można znaleźć w temacie [jakie są wymagania dotyczące haseł podczas tworzenia maszyny wirtualnej?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Przygotowanie magazynu kluczy

W tej sekcji utworzysz Magazyn kluczy i dodasz do niego klucz tajny, aby można było pobrać klucz tajny podczas wdrażania szablonu. Istnieje wiele sposobów tworzenia magazynu kluczy. W tym samouczku użyjesz Azure PowerShell do wdrożenia [szablonu Menedżer zasobów](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ten szablon wykonuje następujące czynności:

* Tworzy magazyn kluczy z włączoną właściwością `enabledForTemplateDeployment`. Ta właściwość musi mieć *wartość true* , aby proces wdrażania szablonu mógł uzyskać dostęp do wpisów tajnych, które są zdefiniowane w magazynie kluczy.
* Dodaje wpis tajny do magazynu kluczy. Wpis tajny przechowuje hasło administratora maszyny wirtualnej.

> [!NOTE]
> Jako użytkownik, który wdraża szablon maszyny wirtualnej, jeśli nie jesteś właścicielem lub współautorem magazynu kluczy, właściciel lub współautor musi udzielić dostępu do konta *Microsoft./magazynów/magazynu/wdrożenia/akcji* dla magazynu kluczy. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania bezpiecznej wartości parametru podczas wdrażania](./key-vault-parameter.md).

Aby uruchomić Poniższy skrypt Azure PowerShell, wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy okienko powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * Nazwa grupy zasobów jest nazwą projektu, ale z dołączoną do niej **RG** . Aby ułatwić [czyszczenie zasobów utworzonych w tym samouczku](#clean-up-resources), Użyj tej samej nazwy projektu i nazwy grupy zasobów podczas [wdrażania kolejnego szablonu](#deploy-the-template).
> * Domyślną nazwą wpisu tajnego jest **vmAdminPassword**. Jest on stałe w szablonie.
> * Aby włączyć szablon do pobrania klucza tajnego, należy włączyć zasady dostępu o nazwie "Włącz dostęp do Azure Resource Manager dla wdrożenia szablonu" dla magazynu kluczy. Te zasady są włączone w szablonie. Aby uzyskać więcej informacji na temat zasad dostępu, zobacz [wdrażanie magazynów kluczy i wpisów tajnych](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Szablon ma jedną wartość wyjściową o nazwie *keyVaultId*. Zanotuj wartość identyfikatora do późniejszego użycia podczas wdrażania maszyny wirtualnej. Format identyfikatora zasobu:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Po skopiowaniu i wklejeniu identyfikatora można go podzielić na wiele wierszy. Scal wiersze i Przytnij dodatkowe spacje.

Aby sprawdzić poprawność wdrożenia, uruchom następujące polecenie programu PowerShell w tym samym okienku powłoki, aby pobrać klucz tajny w postaci zwykłego tekstu. Polecenie działa tylko w tej samej sesji powłoki, ponieważ używa zmiennej *$keyVaultName*, która jest zdefiniowana w poprzednim skrypcie programu PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teraz przygotowano Magazyn kluczy i wpis tajny. W poniższych sekcjach pokazano, jak dostosować istniejący szablon w celu pobrania klucza tajnego podczas wdrażania.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku jest nazywany [wdrożeniem prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **Otwórz plik**.

1. W polu **Nazwa pliku** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik. Scenariusz jest taki sam jak ten, który jest używany w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./template-tutorial-create-templates-with-dependent-resources.md).
   Szablon definiuje pięć zasobów:

   * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Warto mieć podstawowe informacje na temat szablonu przed jego dostosowaniem.

1. Wybierz pozycję **plik** > **Zapisz jako**, a następnie Zapisz kopię pliku na komputerze lokalnym przy użyciu nazwy *azuredeploy. JSON*.

1. Powtórz kroki 1-3, aby otworzyć następujący adres URL, a następnie Zapisz plik jako *azuredeploy. Parameters. JSON*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edytowanie pliku parametrów

Nie musisz wprowadzać żadnych zmian w pliku szablonu.

1. W Visual Studio Code Otwórz plik *azuredeploy. Parameters. JSON* , jeśli nie jest jeszcze otwarty.
1. Zaktualizuj parametr `adminPassword` do:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Zastąp wartość **identyfikatora** identyfikatorem zasobu magazynu kluczy utworzonego w poprzedniej procedurze.

    ![Integruj Magazyn kluczy i szablon Menedżer zasobów pliku parametrów wdrożenia maszyny wirtualnej](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Zaktualizuj następujące wartości:

    * **adminUsername**: nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: Nazwij wartość dnsLabelPrefix.

    Przykłady nazw znajdują się w powyższym obrazie.

1. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami podanymi w temacie [Wdrażanie szablonu](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Przekaż zarówno *azuredeploy. JSON* , jak i *azuredeploy. Parameters. JSON* do Cloud Shell, a następnie użyj następującego skryptu programu PowerShell, aby wdrożyć szablon:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Podczas wdrażania szablonu należy użyć tej samej grupy zasobów, która została użyta w magazynie kluczy. Takie podejście ułatwia czyszczenie zasobów, ponieważ należy usunąć tylko jedną grupę zasobów zamiast dwóch.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej Przetestuj poświadczenia logowania przy użyciu hasła przechowywanego w magazynie kluczy.

1. Otwórz [Portalu Azure](https://portal.azure.com).

1. Wybierz pozycję **grupy zasobów** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. Wybierz pozycję **Połącz** w górnej części.
1. Wybierz pozycję **Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do maszyny wirtualnej przy użyciu hasła przechowywanego w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zasoby platformy Azure nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku pobrano wpis tajny z magazynu kluczy platformy Azure. Następnie klucz tajny jest używany we wdrożeniu szablonu. Aby dowiedzieć się, jak tworzyć połączone szablony, zobacz:

> [!div class="nextstepaction"]
> [Create linked templates (Tworzenie połączonych szablonów)](./template-tutorial-create-linked-templates.md)
