---
title: Konfigurowanie Azure Key Vault zapór i sieci wirtualnych — Azure Key Vault
description: Instrukcje krok po kroku dotyczące konfigurowania zapór Key Vault i sieci wirtualnych
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 870064406c86e9cef6a45dfbe47c61cdaa9ecab0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426313"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór Azure Key Vault i sieci wirtualnych

Ten artykuł zawiera instrukcje krok po kroku dotyczące konfigurowania zapór Azure Key Vault i sieci wirtualnych w celu ograniczenia dostępu do magazynu kluczy. [Punkty końcowe usługi sieci wirtualnej dla Key Vault](key-vault-overview-vnet-service-endpoints.md) umożliwiają ograniczenie dostępu do określonej sieci wirtualnej i zestawu adresów IPv4 (protokołu internetowego w wersji 4).

> [!IMPORTANT]
> Po zastosowaniu reguł zapory użytkownicy mogą wykonywać Key Vault operacje [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu Azure Portal:

1. Przejdź do magazynu kluczy, który chcesz zabezpieczyć.
2. Wybierz pozycję **Sieć**, a następnie wybierz kartę **zapory i sieci wirtualne** .
3. W obszarze **Zezwalaj na dostęp z**, wybierz opcję **wybrane sieci**.
4. Aby dodać istniejące sieci wirtualne do zapór i reguł sieci wirtualnej, wybierz pozycję **+ Dodaj istniejące sieci wirtualne**.
5. W nowym bloku, który zostanie otwarty, wybierz subskrypcję, sieci wirtualne i podsieci, dla których chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli wybrane sieci wirtualne i podsieci nie mają włączonych punktów końcowych usługi, potwierdź, że chcesz włączyć punkty końcowe usługi, a następnie wybierz pozycję **Włącz**. Wprowadzenie zmian może potrwać do 15 minut.
6. W obszarze **sieci IP**Dodaj zakresy adresów IPv4, wpisując zakresy adresów IPv4 w [notacji CIDR (bezklasowe Routing między domenami)](https://tools.ietf.org/html/rfc4632) lub pojedynczych adresów IP.
7. Wybierz pozycję **Zapisz**.

Możesz również dodać nowe sieci wirtualne i podsieci, a następnie włączyć punkty końcowe usługi dla nowo utworzonych sieci wirtualnych i podsieci, wybierając pozycję **+ Dodaj nową sieć wirtualną**. Następnie postępuj zgodnie z monitami.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure 

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i [Zaloguj się](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Wyświetl listę dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Dodaj zakres adresów IP, z którego ma być dozwolony ruch.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla wszystkich zaufanych usług, ustaw `bypass` na `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Włącz reguły sieci, ustawiając domyślną akcję na `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu programu PowerShell:

1. Zainstaluj najnowszą [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)i [Zaloguj się](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Wyświetl listę dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Dodaj zakres adresów IP, z którego ma być dozwolony ruch.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla wszystkich zaufanych usług, ustaw `bypass` na `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Włącz reguły sieci, ustawiając domyślną akcję na `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Dokumentacja

* Polecenie interfejsu wiersza polecenia platformy Azure: [AZ datamagazyn Network-Rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Polecenia cmdlet Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Następne kroki

* [Punkty końcowe usługi sieci wirtualnej dla Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)
