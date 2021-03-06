---
title: Nie można nawiązać połączenia z maszynami wirtualnymi platformy Azure, ponieważ port RDP nie jest włączony w sieciowej grupy zabezpieczeń | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem, w którym protokół RDP kończy się niepowodzeniem z powodu konfiguracji sieciowej grupy zabezpieczeń w Azure Portal | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: aedb9c8d178cb210efedf8ff4bcbbeca39f89e60
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981810"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nie można nawiązać zdalnego połączenia z maszyną wirtualną, ponieważ port RDP nie jest włączony w sieciowej grupy zabezpieczeń

W tym artykule wyjaśniono, jak rozwiązać problem polegający na tym, że nie można nawiązać połączenia z maszyną wirtualną platformy Azure z systemem Windows, ponieważ port Remote Desktop Protocol (RDP) nie jest włączony w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń).


> [!NOTE] 
> Platforma Azure ma dwa modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../azure-resource-manager/management/deployment-models.md). Zalecamy używanie Menedżer zasobów model wdrażania zamiast klasycznego modelu wdrażania dla nowych wdrożeń. 

## <a name="symptom"></a>Objaw

Nie można nawiązać połączenia RDP z maszyną wirtualną na platformie Azure, ponieważ port RDP nie jest otwarty w sieciowej grupie zabezpieczeń.

## <a name="solution"></a>Rozwiązanie 

Podczas tworzenia nowej maszyny wirtualnej cały ruch z Internetu jest blokowany domyślnie. 

Aby włączyć port RDP w sieciowej grupy zabezpieczeń, wykonaj następujące kroki:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W obszarze **Virtual Machines**wybierz maszynę wirtualną, na której występuje problem. 
3. W obszarze **Ustawienia**wybierz pozycję **Sieć**. 
4. W **regułach portów ruchu przychodzącego**Sprawdź, czy port RDP jest ustawiony prawidłowo. Poniżej przedstawiono przykładową konfigurację: 

    **Priorytet**: 300 </br>
    **Nazwa**: Port_3389 </br>
    **Port (miejsce docelowe)** : 3389 </br>
    **Protokół**: TCP </br>
    **Źródło**: dowolne </br>
    **Miejsca docelowe**: dowolne </br>
    **Akcja**: Zezwalaj </br>

Jeśli określisz źródłowy adres IP, to ustawienie zezwala na ruch tylko z określonego adresu IP lub zakresu adresów IP w celu nawiązania połączenia z maszyną wirtualną. Upewnij się, że komputer, którego używasz do uruchamiania sesji RDP, znajduje się w zakresie.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Group](../../virtual-network/security-overview.md).

> [!NOTE]
> Port RDP 3389 jest uwidoczniony w Internecie. W związku z tym zalecamy używanie tego portu tylko do celów testowych. W przypadku środowisk produkcyjnych zaleca się używanie sieci VPN lub połączenia prywatnego.

## <a name="next-steps"></a>Następne kroki

Jeśli port RDP został już włączony w sieciowej grupy zabezpieczeń, zobacz [Rozwiązywanie problemów z ogólnym błędem protokołu RDP na maszynie wirtualnej platformy Azure](./troubleshoot-rdp-general-error.md).



