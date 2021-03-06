---
title: Tworzenie sieci wirtualnej — Szybki start — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu witryny Azure Portal. Sieć wirtualna umożliwia zasoby platformy Azure, takie jak maszyny wirtualne, bezpiecznie komunikują się ze sobą i z Internetem
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488471"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

Sieć wirtualna jest podstawowym blokiem konstrukcyjnym sieci prywatnej na platformie Azure. Pozwala ona na bezpieczne komunikowanie się ze sobą i z Internetem za pomocą zasobów platformy Azure, takich jak maszyny wirtualne. W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal. Następnie można wdrożyć dwie maszyny wirtualne w sieci wirtualnej, bezpiecznie komunikować się między nimi i łączyć się z maszynami wirtualnymi za pośrednictwem Internetu.


Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.

2. W portalu Azure Marketplace wybierz pozycję **sieć** > **Sieć wirtualna**.

3. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wpisz *myVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**.|
    | Podsieć — nazwa | Wprowadź nazwę *mojaPodsiecWirtualna*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |

4. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.

2. W portalu Azure Marketplace wybierz pozycję **Compute** > **Windows Server 2019 Datacenter**.

3. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji. |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź nazwę *myVm1*. |
    | Region | Wybierz pozycję **Wschodnie stany USA**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image (Obraz) | Pozostaw domyślne **centrum systemu Windows Server 2019**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |

4. Wybierz pozycję **Dalej: dyski**.

5. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

6. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **myVirtualNetwork**. |
    | Podsieć | Pozostaw wartość domyślną **myVirtualSubnet (10.1.0.0/24)** . |
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.

7. Wybierz pozycję **Dalej: Zarządzanie**.

8. W obszarze **Tworzenie maszyny wirtualnej — zarządzanie** dla opcji **Konto magazynu diagnostyki** wybierz ustawienie **Utwórz nowe**.

9. W obszarze **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź nazwę *myvmstorageaccount*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Rodzaj konta | Pozostaw wartość domyślną **Magazyn (ogólnego przeznaczenia, wersja 1)** . |
    | Wydajność | Pozostaw wartość domyślną **Standardowa**. |
    | Replikacja | Pozostaw wartość domyślną **Magazyn lokalnie nadmiarowy (LRS)** . |

10. Kliknij przycisk **OK**

11. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

12. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

1. Wykonaj kroki od 1 do 9 przedstawione powyżej.

    > [!NOTE]
    > W kroku 2 w polu **Nazwa maszyny wirtualnej** wprowadź *myVm2*.
    >
    > W kroku 7 w polu **Konto magazynu diagnostyki** wybierz pozycję **myvmstorageaccount**.

2. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przekierowanie do strony**Przeglądanie + tworzenie**, a platforma Azure zweryfikuje konfigurację.

3. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Po utworzeniu *myVm1*Połącz się z Internetem.

1. Na pasku wyszukiwania portalu wpisz *myVm1*.

2. Wybierz przycisk **Połącz**.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/quick-create-portal/connect-to-virtual-machine.png)

    Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

3. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

4. Otwórz pobrany plik *rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    2. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

5. Kliknij przycisk **OK**.

6. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

7. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. Na pulpicie zdalnym maszyny *myVm1* otwórz program PowerShell.

2. Wprowadź polecenie `ping myVm2`.

    Zostanie wyświetlony komunikat podobny do tego:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Polecenie `ping` kończy się niepowodzeniem, ponieważ polecenie `ping` używa protokołu ICMP. Domyślnie protokół ICMP jest blokowany przez zaporę Windows.

3. Aby umożliwić wykonanie polecenia ping na maszynie *myVm2* w celu komunikacji z maszyną *myVm1* w późniejszym kroku, wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    To polecenie zezwala na ruch przychodzący protokołu ICMP przez zaporę systemu Windows:

4. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm1*.

5. Ponownie wykonaj kroki opisane w sekcji [Nawiązywanie połączenia z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet), ale tym razem nawiąż połączenie z maszyną wirtualną *myVm2*.

6. W wierszu polecenia wprowadź polecenie `ping myvm1`.

    Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Otrzymujesz odpowiedzi z *myVm1*, ponieważ zezwolono na ICMP przez zaporę systemu Windows na maszynie wirtualnej *myVm1* w kroku 3.

7. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź w polu **wyszukiwania** w górnej części portalu **i wybierz pozycję** *moja zasobów z* wyników wyszukiwania.

2. Wybierz pozycję **Usuń grupę zasobów**.

3. W polu *WPISZ NAZWĘ GRUPY ZASOBÓW:* wprowadź nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpiecznie komunikuje się między tymi dwiema maszynami wirtualnymi. Aby dowiedzieć się więcej na temat ustawień sieci wirtualnych, zobacz [Manage a virtual network](manage-virtual-network.md) (Zarządzanie siecią wirtualną).

Domyślnie platforma Azure umożliwia nieograniczony dostęp do bezpiecznej komunikacji między maszynami wirtualnymi. Z drugiej strony zezwala tylko na połączenia przychodzące pulpitu zdalnego z maszynami wirtualnymi Windows z Internetu. Aby dowiedzieć się więcej na temat konfigurowania różnych typów komunikacji sieciowej między maszynami wirtualnymi, przejdź do samouczka [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).
