---
title: Zarządzaj IoT Central z Azure PowerShell | Microsoft Docs
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central z Azure PowerShell i zarządzania nimi.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1598451ce184db5a25cac28870b70a446aef123c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198824"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Zarządzanie usługą IoT Central z programu Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) do zarządzania aplikacjami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz uruchamiać Azure PowerShell na komputerze lokalnym, zobacz Install the [Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). Po uruchomieniu Azure PowerShell lokalnie Użyj polecenia cmdlet **Connect-AzAccount** , aby zalogować się do platformy Azure przed podjęciem próby wykonania poleceń cmdlet w tym artykule.

> [!TIP]
> Jeśli musisz uruchomić polecenia programu PowerShell w innej subskrypcji platformy Azure, zobacz [Zmiana aktywnej subskrypcji](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instalowanie modułu IoT Central

Uruchom następujące polecenie, aby sprawdzić, czy [moduł IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) jest zainstalowany w środowisku programu PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Jeśli lista zainstalowanych modułów nie zawiera polecenia **AZ. IotCentral**, uruchom następujące polecenie:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Tworzenie aplikacji

Użyj polecenia cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) , aby utworzyć aplikację IoT Central w ramach subskrypcji platformy Azure. Na przykład:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skrypt najpierw tworzy grupę zasobów w regionie Wschodnie stany USA dla aplikacji. W poniższej tabeli opisano parametry używane z poleceniem **New-AzIotCentralApp** :

|Parametr         |Opis |
|------------------|------------|
|ResourceGroupName |Grupa zasobów zawierająca aplikację. Ta grupa zasobów musi już istnieć w Twojej subskrypcji. |
|Lokalizacja |Domyślnie to polecenie cmdlet używa lokalizacji z grupy zasobów. Obecnie można utworzyć aplikację IoT Central w **Australii**, **Azja i Pacyfik**, **Europie**lub **Stany Zjednoczone** lokalizacje geograficzne.  |
|Name (Nazwa)              |Nazwa aplikacji w Azure Portal. |
|Poddomena         |Poddomena w adresie URL aplikacji. W tym przykładzie adres URL aplikacji jest https://mysubdomain.azureiotcentral.com. |
|SKU               |Obecnie można użyć opcji **ST1** lub **ST2**. Zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Szablon          | Szablon aplikacji do użycia. Aby uzyskać więcej informacji, zobacz poniższą tabelę. |
|DisplayName       |Nazwa aplikacji wyświetlana w interfejsie użytkownika. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Wyświetlanie aplikacji IoT Central

Użyj polecenia cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) , aby wyświetlić listę aplikacji IoT Central i wyświetlić metadane.

## <a name="modify-an-application"></a>Modyfikowanie aplikacji

Użyj polecenia cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) , aby zaktualizować metadane aplikacji IoT Central. Na przykład, aby zmienić nazwę wyświetlaną aplikacji:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Użyj polecenia cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) , aby usunąć aplikację IoT Central. Na przykład:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu Azure PowerShell, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)
