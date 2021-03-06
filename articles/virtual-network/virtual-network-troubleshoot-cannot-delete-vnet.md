---
title: Nie można usunąć sieci wirtualnej na platformie Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem polegający na tym, że nie można usunąć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056921"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Rozwiązywanie problemów z Nie można usunąć sieci wirtualnej na platformie Azure

Podczas próby usunięcia sieci wirtualnej w Microsoft Azure mogą pojawić się błędy. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy Brama sieci wirtualnej jest uruchomiona w sieci wirtualnej](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Sprawdź, czy Brama aplikacji jest uruchomiona w sieci wirtualnej](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Sprawdź, czy w sieci wirtualnej jest włączona usługa Azure Active Directory Domain](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Sprawdź, czy sieć wirtualna jest połączona z innym zasobem](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Sprawdź, czy maszyna wirtualna jest nadal uruchomiona w sieci wirtualnej](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Sprawdź, czy sieć wirtualna jest zablokowana](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy Brama sieci wirtualnej jest uruchomiona w sieci wirtualnej

Aby usunąć sieć wirtualną, należy najpierw usunąć bramę sieci wirtualnej.

W przypadku klasycznych sieci wirtualnych przejdź do strony **Przegląd** klasycznej sieci wirtualnej w Azure Portal. Jeśli Brama jest uruchomiona w sieci wirtualnej, w sekcji **połączenia sieci VPN** zostanie wyświetlony adres IP bramy. 

![Sprawdź, czy brama jest uruchomiona](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

W przypadku sieci wirtualnych przejdź do strony **Przegląd** sieci wirtualnej. Sprawdź **połączone urządzenia** dla bramy sieci wirtualnej.

![Sprawdź połączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Przed usunięciem bramy należy najpierw usunąć wszystkie obiekty **połączenia** w bramie. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy Brama aplikacji jest uruchomiona w sieci wirtualnej

Przejdź do strony **Przegląd** sieci wirtualnej. Sprawdź **podłączone urządzenia** dla bramy aplikacji.

![Sprawdź połączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Jeśli istnieje Brama aplikacji, należy ją usunąć przed usunięciem sieci wirtualnej.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Sprawdź, czy w sieci wirtualnej jest włączona usługa Azure Active Directory Domain

Jeśli usługa domena usługi Active Directory jest włączona i połączona z siecią wirtualną, nie można usunąć tej sieci wirtualnej. 

![Sprawdź połączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Aby wyłączyć usługę, zobacz temat [wyłączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Sprawdź, czy sieć wirtualna jest połączona z innym zasobem

Sprawdź, czy istnieją linki obwodów, połączenia i Komunikacja równorzędna sieci wirtualnych. Którekolwiek z nich może spowodować niepowodzenie usuwania sieci wirtualnej. 

Zalecana kolejność usuwania jest następująca:

1. Połączenia bramy
2. Bramy
3. Adresy IP
4. Wirtualne sieci równorzędne
5. App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Sprawdź, czy maszyna wirtualna jest nadal uruchomiona w sieci wirtualnej

Upewnij się, że żadna maszyna wirtualna nie znajduje się w sieci wirtualnej.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Sprawdź, czy sieć wirtualna jest zablokowana

Jeśli sieć wirtualna jest zablokowana w stanie migracji, nie można jej usunąć. Uruchom następujące polecenie, aby przerwać migrację, a następnie usuń sieć wirtualną.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Następne kroki

- [Azure Virtual Network](virtual-networks-overview.md)
- [Często zadawane pytania dotyczące sieci wirtualnych platformy Azure](virtual-networks-faq.md)
