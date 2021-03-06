---
title: Konfigurowanie kluczy zarządzanych przez klienta dla konta Azure Cosmos DB
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta dla konta Azure Cosmos DB przy użyciu Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 44bbd7eab80ecb1cbfef9738e42b4070dff31180
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506046"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Skonfiguruj klucze zarządzane przez klienta dla konta usługi Azure Cosmos za pomocą Azure Key Vault

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Dane przechowywane na koncie usługi Azure Cosmos są automatycznie i szyfrowane bezproblemowo. Azure Cosmos DB oferuje dwie opcje zarządzania kluczami używanymi do szyfrowania danych w spoczynku:

- **Klucze zarządzane przez usługę**: domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania danych na koncie usługi Azure Cosmos.

- **Klucze zarządzane przez klienta (CMK)** : Opcjonalnie możesz dodać drugą warstwę szyfrowania przy użyciu własnych kluczy.

Klucze zarządzane przez klienta muszą być przechowywane w [Azure Key Vault](../key-vault/key-vault-overview.md) i zapewniać klucz dla każdego konta usługi Azure Cosmos, które jest włączone przy użyciu kluczy zarządzanych przez klienta. Ten klucz służy do szyfrowania wszystkich danych przechowywanych na tym koncie.

> [!NOTE]
> Obecnie klucze zarządzane przez klienta są dostępne tylko dla nowych kont usługi Azure Cosmos. Należy je skonfigurować podczas tworzenia konta.

## <a id="register-resource-provider"></a>Zarejestruj dostawcę zasobów Azure Cosmos DB dla subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), przejdź do subskrypcji platformy Azure, a następnie wybierz pozycję **dostawcy zasobów** na karcie **Ustawienia** :

   ![Wpis "dostawcy zasobów" z menu po lewej stronie](./media/how-to-setup-cmk/portal-rp.png)

1. Wyszukaj dostawcę zasobów **Microsoft. DocumentDB** . Sprawdź, czy dostawca zasobów został już oznaczony jako zarejestrowany. W przeciwnym razie wybierz dostawcę zasobów i wybierz pozycję **zarejestruj**:

   ![Rejestrowanie dostawcy zasobów Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Skonfiguruj wystąpienie Azure Key Vault

Używanie kluczy zarządzanych przez klienta z Azure Cosmos DB wymaga ustawienia dwóch właściwości w wystąpieniu Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Te właściwości obejmują **usuwanie nietrwałe** i **nie Przeczyść**. Te właściwości nie są domyślnie włączone. Można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby dowiedzieć się, jak włączyć te właściwości na istniejącym wystąpieniu Azure Key Vault, zobacz sekcję "Włączanie usuwania nietrwałego" i "Włączanie ochrony przed czyszczeniem" w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia platformy Azure](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Dodawanie zasad dostępu do wystąpienia Azure Key Vault

1. W Azure Portal przejdź do wystąpienia Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Wybierz pozycję **zasady dostępu** w menu po lewej stronie:

   !["Zasady dostępu" z menu po lewej stronie](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Wybierz pozycję **+ Dodaj zasady dostępu**.

1. W menu rozwijanym **uprawnienia klucza** wybierz pozycję **Pobierz**, **Odpakuj klucz**i **Zawijaj uprawnienia klucza** :

   ![Wybieranie odpowiednich uprawnień](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. W obszarze **Wybierz podmiot zabezpieczeń**wybierz pozycję **nie wybrano**. Następnie wyszukaj **Azure Cosmos DB** podmiot zabezpieczeń i wybierz go (aby ułatwić znalezienie, możesz również wyszukać według identyfikatora podmiotu zabezpieczeń: `a232010e-820c-4083-83bb-3ace5fc29d0b` dla dowolnego regionu świadczenia usługi Azure, z wyjątkiem regionów Azure Government, w których identyfikator podmiotu zabezpieczeń jest `57506a73-e302-42a9-b869-6f12d9ec29e9`). Na koniec wybierz **pozycję Wybierz** u dołu. Jeśli podmiot zabezpieczeń **Azure Cosmos DB** nie znajduje się na liście, może być konieczne ponowne zarejestrowanie dostawcy zasobów **Microsoft. DocumentDB** zgodnie z opisem w sekcji [Rejestrowanie dostawcy zasobów](#register-resource-provider) w tym artykule.

   ![Wybierz Azure Cosmos DB podmiotu zabezpieczeń](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Wybierz pozycję **Dodaj** , aby dodać nowe zasady dostępu.

## <a name="generate-a-key-in-azure-key-vault"></a>Wygeneruj klucz w Azure Key Vault

1. W Azure Portal przejdź do wystąpienia Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Następnie wybierz pozycję **klucze** z menu po lewej stronie:

   ![Wpis "klucze" z menu po lewej stronie](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Wybierz pozycję **Generuj/Importuj**, podaj nazwę nowego klucza, a następnie wybierz rozmiar klucza RSA. Aby zapewnić najlepsze zabezpieczenia, zalecamy co najmniej 3072. Następnie wybierz pozycję **Utwórz**:

   ![Utwórz nowy klucz](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Po utworzeniu klucza wybierz nowo utworzony klucz, a następnie jego bieżącą wersję.

1. Skopiuj **Identyfikator klucza**klucza, z wyjątkiem części po ostatnim ukośniku:

   ![Kopiowanie identyfikatora klucza klucza](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Utwórz nowe konto usługi Azure Cosmos

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po utworzeniu nowego konta Azure Cosmos DB z Azure Portal wybierz opcję **klucz zarządzany przez klienta** w kroku **szyfrowanie** . W polu **Identyfikator URI klucza** wklej identyfikator URI/klucz Azure Key Vault klucza skopiowanego z poprzedniego kroku:

![Ustawianie parametrów CMK w Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Podczas tworzenia nowego konta Azure Cosmos DB przy użyciu programu PowerShell:

- Przekaż identyfikator URI klucza Azure Key Vault skopiowanego wcześniej we właściwości **keyVaultKeyUri** w **właściwościobject**.

- Użyj **2019-12-12** jako wersji interfejsu API.

> [!IMPORTANT]
> Należy ustawić parametr `Location` jawnie dla konta, aby można je było pomyślnie utworzyć przy użyciu kluczy zarządzanych przez klienta.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Podczas tworzenia nowego konta usługi Azure Cosmos za pomocą szablonu Azure Resource Manager:

- Przekaż identyfikator URI klucza Azure Key Vault, który został wcześniej skopiowany we właściwości **keyVaultKeyUri** w obiekcie **Properties** .

- Użyj **2019-12-12** jako wersji interfejsu API.

> [!IMPORTANT]
> Należy ustawić parametr `Location` jawnie dla konta, aby można je było pomyślnie utworzyć przy użyciu kluczy zarządzanych przez klienta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Wdróż szablon przy użyciu następującego skryptu programu PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Czy jest dostępna dodatkowa opłata za korzystanie z kluczy zarządzanych przez klienta?

Tak. Aby obsłużyć dodatkowe obciążenie obliczeniowe wymagane do zarządzania szyfrowaniem i odszyfrowywaniem danych przy użyciu kluczy zarządzanych przez klienta, wszystkie operacje wykonywane na koncie usługi Azure Cosmos zużywają 25% wzrostu w [jednostkach żądania](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jakie dane są szyfrowane za pomocą kluczy zarządzanych przez klienta?

Wszystkie dane przechowywane na koncie usługi Azure Cosmos są szyfrowane przy użyciu kluczy zarządzanych przez klienta, z wyjątkiem następujących metadanych:

- Nazwy [kont Azure Cosmos DB, baz danych i kontenerów](./account-overview.md#elements-in-an-azure-cosmos-account)

- Nazwy [procedur składowanych](./stored-procedures-triggers-udfs.md)

- Ścieżki właściwości zadeklarowane w [zasadach indeksowania](./index-policy.md)

- Wartości [kluczy partycji](./partitioning-overview.md) kontenera

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Czy dla istniejących kont usługi Azure Cosmos są obsługiwane klucze zarządzane przez klienta?

Ta funkcja jest obecnie dostępna tylko dla nowych kont.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Czy istnieje plan obsługi bardziej szczegółowego stopnia szczegółowości niż klucze poziomu konta?

Obecnie nie są uwzględniane klucze poziomu kontenera.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Jak klucze zarządzane przez klienta wpływają na kopię zapasową?

Azure Cosmos DB [regularnie i automatycznie tworzy kopie zapasowe](./online-backup-and-restore.md) danych przechowywanych na koncie. Ta operacja tworzy kopię zapasową zaszyfrowanych danych. Aby można było użyć przywróconej kopii zapasowej, wymagany jest klucz szyfrowania używany w momencie tworzenia kopii zapasowej. Oznacza to, że nie wprowadzono odwołania, a wersja klucza, która była używana w momencie tworzenia kopii zapasowej, nadal będzie włączona.

### <a name="how-do-i-revoke-an-encryption-key"></a>Jak mogę odwołać klucz szyfrowania?

Odwoływanie klucza jest wykonywane przez wyłączenie najnowszej wersji klucza:

![Wyłącz wersję klucza](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternatywnie, aby odwołać wszystkie klucze z wystąpienia Azure Key Vault, można usunąć zasady dostępu przyznane do Azure Cosmos DB podmiotu zabezpieczeń:

![Usuwanie zasad dostępu dla podmiotu zabezpieczeń Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?

Jedyną operacją możliwą do odwołania klucza szyfrowania jest usunięcie konta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat szyfrowania danych w Azure Cosmos DB](./database-encryption-at-rest.md).
- Zapoznaj się z omówieniem [bezpiecznego dostępu do danych w Cosmos DB](secure-access-to-data.md).
