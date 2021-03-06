---
title: Rozszerzenie DSC platformy Azure dla systemu Linux
description: Instaluje pakiety OMI i DSC umożliwiające skonfigurowanie konfiguracji żądanego stanu przy użyciu maszyny wirtualnej z systemem Linux platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 102433b88ffb140cae46433be1c0edef90857e6d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969930"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozszerzenie DSC dla systemu Linux (Microsoft. OSTCExtensions. DSCForLinux)

Konfiguracja żądanego stanu (DSC) to platforma zarządzania, za pomocą której można zarządzać infrastrukturą IT i platformą programistyczną przy użyciu konfiguracji jako kodu.

> [!NOTE]
> Rozszerzenie DSC dla systemu Linux i [rozszerzenie maszyny wirtualnej Azure monitor dla systemu Linux](/azure/virtual-machines/extensions/oms-linux) obecnie stanowią konflikt i nie są obsługiwane w konfiguracji równoległej. Nie używaj obu rozwiązań jednocześnie na tej samej maszynie wirtualnej.

Rozszerzenie DSCForLinux jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta OMI i DSC na maszynach wirtualnych platformy Azure. Rozszerzenie DSC może również wykonywać następujące czynności:


- Zarejestruj maszynę wirtualną z systemem Linux na koncie Azure Automation, aby ściągnąć konfiguracje z usługi Azure Automation (Register ExtensionAction).
- Wypchnij konfiguracje MOF do maszyny wirtualnej z systemem Linux (wypychanie ExtensionAction).
- Zastosuj konfigurację meta MOF do maszyny wirtualnej z systemem Linux, aby skonfigurować serwer ściągania w celu ściągnięcia konfiguracji węzła (ściągania ExtensionAction).
- Instalowanie niestandardowych modułów DSC na maszynie wirtualnej z systemem Linux (Install ExtensionAction).
- Usuń niestandardowe moduły DSC z maszyny wirtualnej z systemem Linux (Usuń ExtensionAction).

 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie DSC Linux obsługuje wszystkie [dystrybucje systemu Linux zatwierdzone na platformie Azure](/azure/virtual-machines/linux/endorsed-distros) z wyjątkiem:

| Dystrybucja | Wersja |
|---|---|
| Debian | Wszystkie wersje |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie DSCForLinux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. Na przykład rozszerzenie Register wymaga łączności z usługą Automation. W przypadku innych akcji, takich jak ściąganie, instalacja wymaga łączności z usługą Azure Storage i GitHub. Zależy to od ustawień dostarczonych przez klienta.

## <a name="extension-schema"></a>Schemat rozszerzenia

### <a name="public-configuration"></a>Konfiguracja publiczna

Oto wszystkie obsługiwane parametry konfiguracji publicznej:

* `FileUri`: (opcjonalnie, String) identyfikator URI pliku MOF, meta File lub pliku ZIP zasobów niestandardowych.
* `ResourceName`: (opcjonalnie, String) nazwa niestandardowego modułu zasobów.
* `ExtensionAction`: (opcjonalnie, ciąg) określa, jakie jest rozszerzenie. Prawidłowe wartości to register, push, pull, install i Remove. Jeśli nie zostanie określony, jest domyślnie traktowany jako akcja push.
* `NodeConfigurationName`: (opcjonalnie, String) nazwa konfiguracji węzła do zastosowania.
* `RefreshFrequencyMins`: (opcjonalnie, int) określa, jak często (w minutach) próbuje uzyskać konfigurację z serwera ściągania. 
       Jeśli konfiguracja na serwerze ściągania różni się od bieżącej w węźle docelowym, zostanie ona skopiowana do oczekującego magazynu i zastosowana.
* `ConfigurationMode`: (opcjonalnie, ciąg) określa, jak Konfiguracja DSC powinna stosować konfigurację. Prawidłowe wartości to ApplyOnly, ApplyAndMonitor i ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcjonalnie, int) określa, jak często (w minutach) DSC zapewnia, że konfiguracja jest w żądanym stanie.

> [!NOTE]
> W przypadku używania wersji wcześniejszej niż 2,3, parametr mode jest taki sam jak ExtensionAction. Tryb wydaje się być przeciążonym terminem. Aby uniknąć nieporozumień, ExtensionAction jest używany w dalszej wersji 2,3. W celu zapewnienia zgodności z poprzednimi wersjami rozszerzenie obsługuje zarówno tryb, jak i ExtensionAction. 
>

### <a name="protected-configuration"></a>Konfiguracja chroniona

Poniżej przedstawiono wszystkie obsługiwane parametry konfiguracji chronionej:

* `StorageAccountName`: (opcjonalnie, String) nazwa konta magazynu, które zawiera plik
* `StorageAccountKey`: (opcjonalnie, ciąg) klucz konta magazynu, który zawiera plik
* `RegistrationUrl`: (opcjonalnie, String) adres URL konta Azure Automation
* `RegistrationKey`: (opcjonalny, String) klucz dostępu konta Azure Automation


## <a name="scenarios"></a>Scenariusze

### <a name="register-an-azure-automation-account"></a>Rejestrowanie konta Azure Automation
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Format programu PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Zastosuj plik konfiguracji MOF (na koncie usługi Azure Storage) do maszyny wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosuj plik konfiguracji MOF (w magazynie publicznym) do maszyny wirtualnej

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Zastosuj plik konfiguracji meta MOF (na koncie usługi Azure Storage) do maszyny wirtualnej

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Zastosuj plik konfiguracji meta MOF (w magazynie publicznym) do maszyny wirtualnej
Public. JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (pliku zip na koncie usługi Azure Storage) do maszyny wirtualnej
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Format programu PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalowanie niestandardowego modułu zasobów (pliku zip w magazynie publicznym) do maszyny wirtualnej
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Usuwanie niestandardowego modułu zasobów z maszyny wirtualnej
Public. JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Format programu PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem w przypadku wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu, na przykład dołączania do Azure Automation. 

Przykładowy szablon Menedżer zasobów to [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) i [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Aby uzyskać więcej informacji na temat szablonu Azure Resource Manager, zobacz [tworzenie Azure Resource Manager szablonów](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

### <a name="use-azure-cliazure-cli"></a>Korzystanie z [interfejsu wiersza polecenia platformy Azure] [Azure-CLI]
Przed wdrożeniem rozszerzenia DSCForLinux Skonfiguruj `public.json` i `protected.json` zgodnie z różnymi scenariuszami w sekcji 3.

#### <a name="classic"></a>Klasyczny
Klasyczny tryb wdrażania jest również nazywany trybem zarządzania usługami platformy Azure. Aby przełączyć się do niego, należy uruchomić:
```
$ azure config mode asm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Aby poznać najnowszą wersję rozszerzenia, uruchom polecenie:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Aby przełączyć się do trybu Azure Resource Manager, możesz uruchomić:
```
$ azure config mode arm
```

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> W trybie Azure Resource Manager `azure vm extension list` nie jest teraz dostępna.
>

### <a name="use-azure-powershellazure-powershell"></a>Użycie [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Klasyczny

Możesz zalogować się do konta platformy Azure w trybie zarządzania usługami platformy Azure, uruchamiając następujące operacje:

```powershell>
Add-AzureAccount
```

I Wdróż rozszerzenie DSCForLinux, uruchamiając:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Zmień zawartość $privateConfig i $publicConfig zgodnie z różnymi scenariuszami w poprzedniej sekcji.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Możesz zalogować się do konta platformy Azure w trybie Azure Resource Manager, uruchamiając następujące operacje:

```powershell>
Login-AzAccount
```

Aby dowiedzieć się więcej na temat używania Azure PowerShell z Azure Resource Manager, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Rozszerzenie DSCForLinux można wdrożyć, uruchamiając następujące narzędzia:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Zmień zawartość $privateConfig i $publicConfig zgodnie z różnymi scenariuszami w poprzedniej sekcji.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kod błędu: 51 reprezentuje nieobsługiwaną dystrybucję lub nieobsługiwaną akcję rozszerzenia.
W niektórych przypadkach nie można zainstalować programu OMI z systemem Linux w przypadku, gdy nowsza wersja OMI już istnieje na maszynie. [błąd odpowiedzi: (000003) nie jest dozwolone obniżanie poziomu]



### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](features-linux.md).
