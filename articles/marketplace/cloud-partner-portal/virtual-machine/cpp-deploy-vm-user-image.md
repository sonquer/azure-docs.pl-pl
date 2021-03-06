---
title: Wdrażanie maszyny wirtualnej platformy Azure na podstawie wirtualnego dysku twardego użytkownika | Portal Azure Marketplace
description: Wyjaśnia sposób wdrażania obrazu wirtualnego dysku twardego użytkownika w celu utworzenia wystąpienia maszyny wirtualnej platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 8421e9b7b7e2b7d13054e977da83be044b4e6af7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816643"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Wdrażanie maszyny wirtualnej platformy Azure na podstawie wirtualnego dysku twardego użytkownika

W tym artykule opisano sposób wdrażania uogólnionego obrazu wirtualnego dysku twardego w celu utworzenia nowego zasobu maszyny wirtualnej platformy Azure przy użyciu podanego szablonu Azure Resource Manager i skryptu Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Szablon wdrożenia wirtualnego dysku twardego

Skopiuj szablon Azure Resource Manager na potrzeby [wdrożenia wirtualnego dysku twardego](cpp-deploy-json-template.md) do pliku lokalnego o nazwie `VHDtoImage.json`.  Edytuj ten plik, aby podać wartości dla następujących parametrów. 

|  **Konstruktora**             |   **Opis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Istniejąca nazwa grupy zasobów platformy Azure.  Zwykle używaj tego samego RG skojarzonego z Twoim magazynem kluczy  |
| TemplateFile               | Pełna nazwa ścieżki do pliku `VHDtoImage.json`                                    |
| userStorageAccountName     | Nazwa konta magazynu                                                    |
| sNameForPublicIP           | Nazwa DNS publicznego adresu IP. Musi być małymi literami                                  |
| subscriptionId             | Identyfikator subskrypcji platformy Azure                                                  |
| Lokalizacja                   | Standardowa lokalizacja geograficzna platformy Azure grupy zasobów                       |
| vmName                     | Nazwa maszyny wirtualnej                                                    |
| vaultName                  | Nazwa magazynu kluczy                                                          |
| vaultResourceGroup         | Grupa zasobów magazynu kluczy
| certificateUrl             | Adres URL certyfikatu, w tym wersja przechowywana w magazynie kluczy, na przykład: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | Adres URL wirtualnego dysku twardego                                                   |
| vmSize                     | Rozmiar wystąpienia maszyny wirtualnej                                           |
| publicIPAddressName        | Nazwa publicznego adresu IP                                                  |
| virtualNetworkName         | Nazwa sieci wirtualnej                                                    |
| nicName                    | Nazwa karty sieciowej sieci wirtualnej                     |
| adminUserName              | Nazwa użytkownika konta administratora                                          |
| adminPassword              | Hasło administratora                                                          |
|  |  |


## <a name="powershell-script"></a>Skrypt programu PowerShell

Skopiuj i edytuj następujący skrypt, aby dostarczyć wartości dla zmiennych `$storageaccount` i `$vhdUrl`.  Wykonaj tę operację, aby utworzyć zasób maszyny wirtualnej platformy Azure na podstawie istniejącego uogólnionego wirtualnego dysku twardego.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu maszyny wirtualnej możesz przeprowadzić [certyfikowanie obrazu maszyny wirtualnej](./cpp-certify-vm.md).
