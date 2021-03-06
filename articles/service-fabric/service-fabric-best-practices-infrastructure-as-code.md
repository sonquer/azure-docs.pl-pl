---
title: Infrastruktura Service Fabric platformy Azure jako najlepsze rozwiązania dotyczące kodu
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania Service Fabric platformy Azure jako infrastruktury jako kodu.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551832"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kod

W scenariuszu produkcyjnym Utwórz klastry usługi Azure Service Fabric przy użyciu szablonów Menedżer zasobów. Szablony Menedżer zasobów zapewniają większą kontrolę nad właściwościami zasobów i zapewniają spójny model zasobów.

Przykładowe szablony Menedżer zasobów są dostępne dla systemów Windows i Linux w [przykładach platformy Azure w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony mogą służyć jako punkt początkowy dla szablonu klastra. Pobierz `azuredeploy.json` i `azuredeploy.parameters.json` i edytuj je, aby spełnić wymagania niestandardowe.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wdrożyć szablony `azuredeploy.json` i `azuredeploy.parameters.json` pobrane powyżej, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Tworzenie zasobu przy użyciu programu PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Zasoby usługi Azure Service Fabric

Możesz wdrażać aplikacje i usługi w klastrze usługi Service Fabric za pośrednictwem usługi Azure Resource Manager. Aby uzyskać szczegółowe informacje [, zobacz Zarządzanie aplikacjami i usługami jako zasoby Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) . Poniżej przedstawiono najlepsze rozwiązanie Service Fabric zasoby dotyczące aplikacji do uwzględnienia w Menedżer zasobów zasobów szablonów.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Aby wdrożyć aplikację przy użyciu Azure Resource Manager, musisz najpierw utworzyć pakiet aplikacji Service Fabric [sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) . Poniższy skrypt w języku Python stanowi przykład tworzenia sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguracja automatycznego uaktualniania systemu operacyjnego maszyny wirtualnej platformy Azure 
Uaktualnienie maszyn wirtualnych jest operacją inicjowaną przez użytkownika i zaleca się użycie [automatycznego uaktualnienia systemu operacyjnego zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) dla klastrów Service Fabric platformy Azure. Aplikacja aranżacji poprawek jest rozwiązaniem alternatywnym, które jest przeznaczone do przechowywania poza platformą Azure, chociaż można używać go na platformie Azure, z obciążeniem hostingu na platformie Azure. Poniżej przedstawiono właściwości szablonu skalowania maszyn wirtualnych obliczeniowych Menedżer zasobów, które umożliwiają automatyczne uaktualnianie systemu operacyjnego:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
W przypadku korzystania z automatycznych uaktualnień systemu operacyjnego z Service Fabric nowy obraz systemu operacyjnego jest przewinięcie jednej domeny aktualizacji jednocześnie w celu zapewnienia wysokiej dostępności usług uruchomionych w programie Service Fabric. Aby korzystać z automatycznych uaktualnień systemu operacyjnego w Service Fabric klaster musi być skonfigurowany do korzystania z warstwy trwałości Silver lub wyższej.

Upewnij się, że w następującym kluczu rejestru ustawiono wartość "false", aby zapobiec inicjowaniu nieskoordynowanych aktualizacji na maszynach hosta z systemem Windows: HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Poniżej przedstawiono właściwości szablonu skalowania maszyn wirtualnych Menedżer zasobów, aby ustawić wartość false dla klucza rejestru WindowsUpdate:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfiguracja uaktualniania klastra Service Fabric platformy Azure
Poniżej przedstawiono właściwość szablonu Menedżer zasobów klastra Service Fabric w celu włączenia automatycznego uaktualniania:
```json
"upgradeMode": "Automatic",
```
Aby ręcznie uaktualnić klaster, Pobierz dystrybucję pliku cab/deb na maszynę wirtualną klastra, a następnie Wywołaj następujące polecenie programu PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)
