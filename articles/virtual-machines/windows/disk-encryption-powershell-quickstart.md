---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows za pomocą Azure PowerShell
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Azure PowerShell utworzyć i zaszyfrować maszynę wirtualną z systemem Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246081"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu programu PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. W tym przewodniku szybki start pokazano, jak za pomocą modułu Azure PowerShell utworzyć maszynę wirtualną z systemem Windows, utworzyć Key Vault do przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną platformy Azure przy użyciu elementu [New-AzVM](/powershell/module/az.compute/new-azvm). Musisz podać poświadczenia w poleceniu cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Utwórz Key Vault skonfigurowany pod kątem kluczy szyfrowania

Klucz szyfrowania w usłudze Azure Disk Encryption jest przechowywany w Azure Key Vault. Utwórz Key Vault za pomocą elementu [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Aby włączyć Key Vault do przechowywania kluczy szyfrowania, użyj parametru-EnabledForDiskEncryption.

> [!Important]
> Każdy Key Vault musi mieć unikatową nazwę. Poniższy przykład tworzy Key Vault o nazwie *myKV*, ale należy nazwać coś innego.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj maszynę wirtualną za pomocą [opcji Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension wymaga pewnych wartości z obiektu Key Vault. Możesz uzyskać te wartości, przekazując unikatową nazwę magazynu kluczy do [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Po kilku minutach proces zwróci następujące elementy:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces szyfrowania można sprawdzić, uruchamiając [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Po włączeniu szyfrowania w zwróconych danych wyjściowych zostaną wyświetlone następujące elementy:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono maszynę wirtualną, która utworzyła Key Vault, w której włączono obsługę kluczy szyfrowania, oraz zaszyfrowaną MASZYNę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)