---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Tworzenie maszyny wirtualnej na platformie Azure przy użyciu szablonów maszyn wirtualnych
description: Zawiera opis sposobu tworzenia maszyn wirtualnych na platformie Azure przy użyciu szablonów maszyny wirtualnej w infrastrukturze VMware dla chmury prywatnej automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 533aaab13f1b957e709f66b23b511fc199ee0285
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015205"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Tworzenie maszyny wirtualnej na platformie Azure przy użyciu szablonów maszyn wirtualnych w infrastrukturze VMware

Maszynę wirtualną można utworzyć w Azure Portal przy użyciu szablonów maszyn wirtualnych w infrastrukturze programu VMware, które zostały włączone przez administratora automatycznej synchronizacji dla Twojej subskrypcji.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-avs-virtual-machine"></a>Utwórz maszynę wirtualną o automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **Virtual Machines automatyczna synchronizacja**.

3. Kliknij pozycję **Dodaj**.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź podstawowe informacje kliknij przycisk **Dalej: rozmiar**.

    > [!NOTE]
    > Automatyczna synchronizacja maszyn wirtualnych na platformie Azure wymaga szablonu maszyny wirtualnej. Ten szablon maszyny wirtualnej powinien istnieć w chmurze prywatnej vCenter. Utwórz maszynę wirtualną w chmurze prywatnej z poziomu interfejsu użytkownika vCenter z żądanym systemem operacyjnym i konfiguracjami. Korzystając z instrukcji w obszarze [klonowanie maszyny wirtualnej do szablonu w VSphere klienta sieci Web](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), Utwórz szablon.

    ![Tworzenie maszyny wirtualnej do automatycznej synchronizacji — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | Subskrypcja | Subskrypcja platformy Azure skojarzona z chmurą prywatną.  |
    | Grupa zasobów | Grupa zasobów, do której zostanie przypisana maszyna wirtualna. Możesz wybrać istniejącą grupę lub utworzyć nową. |
    | Nazwa | Nazwa identyfikująca maszynę wirtualną.  |
    | Lokalizacja | Region świadczenia usługi Azure, w którym jest hostowana ta maszyna wirtualna.  |
    | Chmura prywatna | Automatyczna synchronizacja chmury prywatnej, w której chcesz utworzyć maszynę wirtualną. |
    | Pula zasobów | Mapowana Pula zasobów dla maszyny wirtualnej. Wybierz z dostępnych pul zasobów. |
    | Szablon vSphere | szablon vSphere dla maszyny wirtualnej.  |
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny wirtualnej (dla szablonów systemu Windows)|
    | Hasło <br>Potwierdź hasło | Hasło administratora maszyny wirtualnej (dla szablonów systemu Windows).  |

5. Wybierz liczbę rdzeni i pojemność pamięci dla maszyny wirtualnej, a następnie kliknij przycisk **Dalej: konfiguracje**. Zaznacz pole wyboru, jeśli chcesz uwidocznić pełną wirtualizację procesora CPU w systemie operacyjnym gościa, aby aplikacje wymagające wirtualizacji sprzętu mogły być uruchamiane na maszynach wirtualnych bez tłumaczenia binarnego lub właściwościami parawirtualizacji. Aby uzyskać więcej informacji, zapoznaj się z artykułem VMware [udostępnianie sprzętowej wirtualizacji](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)przez oprogramowanie VMware.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy sieciowe i dyski zgodnie z opisem w poniższych tabelach i kliknij kolejno pozycje **Przegląd + Utwórz**.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    W obszarze interfejsy sieciowe kliknij pozycję **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.

    | Kontrola | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę identyfikującą interfejs.  |
    | Network (Sieć) | Wybierz z listy skonfigurowanych rozproszonych grup portów w chmurze prywatnej vSphere.  |
    | Kartę | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł z bazy wiedzy VMware z [wybieraniem karty sieciowej dla maszyny wirtualnej](https://kb.vmware.com/s/article/1001805). |
    | Włącz przy rozruchu | Zdecyduj, czy włączyć sprzęt kart sieciowych podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **enable**. |

    W obszarze dyski kliknij pozycję **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę identyfikującą dysk.  |
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
    | Kontroler SCSI | Wybierz kontroler SCSI dla dysku.  |
    | Tryb | Określa, w jaki sposób dysk uczestniczy w migawce. Wybierz jedną z następujących opcji: <br> -Niezależne trwałe: wszystkie dane zapisywane na dysku są zapisywane trwale.<br> -Niezależne nietrwałe: zmiany zapisywane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej. Niezależny tryb nietrwały umożliwia zawsze ponowne uruchomienie maszyny wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz [dokumentację programu VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Po zakończeniu walidacji Sprawdź ustawienia i kliknij przycisk **Utwórz**. Aby wprowadzić zmiany, kliknij karty u góry lub kliknij pozycję.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — przegląd](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-avs-virtual-machines"></a>Wyświetlanie listy maszyn wirtualnych o automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **Virtual Machines automatyczna synchronizacja**.

3. Wybierz, na którym utworzono chmurę prywatną.

    ![Lista automatycznej synchronizacji Virtual Machines](media/list-cloudsimple-virtual-machines.png)

Lista maszyn wirtualnych o automatycznej synchronizacji obejmuje maszyny wirtualne utworzone na podstawie Azure Portal.  Maszyny wirtualne utworzone w chmurze prywatnej programu vCenter w mapowanej puli zasobów programu vCenter zostaną wyświetlone na liście.  
