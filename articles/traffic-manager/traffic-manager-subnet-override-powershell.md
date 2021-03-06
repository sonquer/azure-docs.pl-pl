---
title: Przesłonięcie podsieci Traffic Manager platformy Azure przy użyciu Azure PowerShell | Microsoft Docs
description: Ten artykuł pomoże zrozumieć, jak Traffic Manager przesłonięcia podsieci służy do przesłonięcia metody routingu profilu Traffic Manager, aby skierować ruch do punktu końcowego na podstawie adresu IP użytkownika końcowego za pośrednictwem wstępnie zdefiniowanego zakresu adresów IP na potrzeby mapowania punktów końcowych przy użyciu platformy Azure Narzędzia.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938417"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Traffic Manager przesłonięcia podsieci przy użyciu programu Azure PowerShell

Przesłonięcie podsieci Traffic Manager umożliwia zmianę metody routingu profilu.  Dodanie przesłonięcia spowoduje przekierowanie ruchu na podstawie adresu IP użytkownika końcowego ze wstępnie zdefiniowanym zakresem adresów IP do mapowania punktu końcowego. 

## <a name="how-subnet-override-works"></a>Jak działa przesłonięcie podsieci

Gdy przesłonięcia podsieci są dodawane do profilu usługi Traffic Manager, Traffic Manager najpierw sprawdza, czy istnieje przesłonięcie podsieci dla adresu IP użytkownika końcowego. Jeśli zostanie znaleziony, zapytanie DNS użytkownika zostanie skierowane do odpowiadającego mu punktu końcowego.  Jeśli mapowanie nie zostanie znalezione, Traffic Manager powróci do oryginalnej metody routingu profilu. 

Zakresy adresów IP można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakresy adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z każdym punktem końcowym muszą być unikatowe dla tego punktu końcowego. Wszelkie nakładanie się zakresów adresów IP między różnymi punktami końcowymi spowoduje odrzucenie profilu przez Traffic Manager.

Istnieją dwa typy profilów routingu, które obsługują przesłonięcia podsieci:

* **Geograficzna** — Jeśli Traffic Manager odnajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, zostanie on rozesłany do punktu końcowego, niezależnie od kondycji punktu końcowego.
* **Wydajność** — Jeśli Traffic Manager odnajdzie przesłonięcie podsieci dla adresu IP zapytania DNS, tylko będzie kierować ruch do punktu końcowego, jeśli jest w dobrej kondycji.  Traffic Manager powróci do algorytmu heurystycznego routingu wydajności, jeśli punkt końcowy przesłonięcia podsieci nie jest w dobrej kondycji.

## <a name="create-a-traffic-manager-subnet-override"></a>Tworzenie przesłonięcia podsieci Traffic Manager

Aby utworzyć przesłonięcie podsieci Traffic Manager, można użyć Azure PowerShell, aby dodać podsieci dla zastąpienia do punktu końcowego Traffic Manager.

## <a name="azure-powershell"></a>Program Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera potrzebny jest moduł Azure PowerShell, 1.0.0 lub nowszy. Aby znaleźć zainstalowaną wersję, można uruchomić `Get-Module -ListAvailable Az`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić `Login-AzAccount`, aby zalogować się do platformy Azure.


1. **Pobierz punkt końcowy Traffic Manager:**

    Aby włączyć przesłonięcie podsieci, Pobierz punkt końcowy, do którego chcesz dodać przesłonięcie, i Zapisz go w zmiennej przy użyciu polecenia [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Zastąp nazwę, nazwa pliku i ResourceGroupName wartościami punktów końcowych, które są zmieniane.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Dodaj zakres adresów IP do punktu końcowego:**
    
    Aby dodać zakres adresów IP do punktu końcowego, użyj polecenie [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) , aby dodać zakres.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Po dodaniu zakresów Użyj polecenie [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) , aby zaktualizować punkt końcowy.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Usuwanie zakresu adresów IP można wykonać za pomocą polecenia [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Pobierz punkt końcowy Traffic Manager:**

    Aby włączyć przesłonięcie podsieci, Pobierz punkt końcowy, do którego chcesz dodać przesłonięcie, i Zapisz go w zmiennej przy użyciu polecenia [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Zastąp nazwę, nazwa pliku i ResourceGroupName wartościami punktów końcowych, które są zmieniane.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Usuń zakres adresów IP z punktu końcowego:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Po usunięciu zakresów Użyj polecenie [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) , aby zaktualizować punkt końcowy.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)Traffic Manager.

Informacje o [metodzie routingu ruchu podsieci](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
