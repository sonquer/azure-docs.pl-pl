---
title: Przygotuj platformę Azure do lokalnego odzyskiwania po awarii za pomocą Azure Site Recovery
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii maszyn lokalnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1ec668fac087773001ca401eefb5ca8bc10ea2b8
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620594"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Przygotowywanie platformy Azure do lokalnego odzyskiwania po awarii na platformie Azure

W tym artykule opisano sposób przygotowania zasobów i składników platformy Azure, dzięki czemu można skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V lub serwerów fizycznych systemu Windows/Linux na platformę Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

Ten artykuł jest pierwszym samouczkiem z serii pokazującej, jak skonfigurować odzyskiwanie po awarii dla lokalnych maszyn wirtualnych. 


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy konto platformy Azure ma uprawnienia replikacji.
> * Utwórz magazyn usługi Recovery Services. Magazyn przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć wirtualną platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przejściu w tryb failover, są one przyłączone do tej sieci.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli jest to możliwe, są używane opcje domyślne, i nie są przedstawione wszystkie ustawienia i ścieżki. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji jak to zrobić w spisie treści Site Recovery.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Zapoznaj się z architekturą dla programu [VMware](vmware-azure-architecture.md), [funkcji Hyper-V](hyper-v-azure-architecture.md)i odzyskiwania po awarii [serwera fizycznego](physical-azure-architecture.md) .
- Przeczytaj często zadawane pytania dotyczące oprogramowania [VMware](vmware-azure-common-questions.md) i [funkcji Hyper-V](hyper-v-azure-common-questions.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli masz już utworzone bezpłatne konto platformy Azure, jesteś administratorem subskrypcji i masz wymagane uprawnienia. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na koncie usługi Azure Storage.
- Zapisz na dysku zarządzanym platformy Azure.

Aby można było wykonać te zadania, do konta musi być przypisana wbudowana rola Współautor maszyny wirtualnej. Ponadto, aby zarządzać operacjami Site Recovery w magazynie, należy przypisać do konta rolę wbudowaną Site Recovery współautor.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu Usług odzyskiwania

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**, a następnie przeszukaj witrynę Marketplace pod kątem **odzyskiwania**.
2. Wybierz pozycję **kopia zapasowa i Site Recovery** z wyników wyszukiwania, a następnie na stronie kopia zapasowa i Site Recovery kliknij przycisk **Utwórz**. 
3. Na stronie **Tworzenie magazynu Recovery Services** wybierz **subskrypcję**. Korzystamy z **subskrypcji firmy Contoso**.
4. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka używamy grupy **contosoRG**.
5. W polu **Nazwa magazynu**wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tej serii samouczków należy użyć nazwy **ContosoVMVault**.
6. W **obszarze region**wybierz region, w którym powinien się znajdować magazyn. Użyj wartości **Europa Zachodnia**.
7. Wybierz pozycję **Przegląd + utwórz**.

   ![Tworzenie nowego magazynu](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nowy magazyn będzie teraz widoczny na **pulpicie nawigacyjnym** > **wszystkie zasoby**i na stronie **magazyny główne Recovery Services** .

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Maszyny lokalne są replikowane do usługi Azure Managed Disks. W przypadku przejścia w tryb failover maszyny wirtualne platformy Azure są tworzone na podstawie tych dysków zarządzanych i przyłączone do sieci platformy Azure określonej w tej procedurze.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. Zachowaj **Menedżer zasobów** wybrane jako model wdrażania.
3. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. W tym samouczku użyto nazwy **ContosoASRnet**.
4. W **obszarze przestrzeń adresowa**Wprowadź zakres adresów sieci wirtualnej w notacji CDR. Korzystamy z **10.1.0.0/24**.
5. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
6. Określ **grupę zasobów** , w której zostanie utworzona sieć. Należy użyć istniejącej grupy zasobów **contosoRG**.
7. W obszarze **Lokalizacja**wybierz ten sam region, w którym został utworzony magazyn Recovery Services. W naszym samouczku jest **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn.
8. W obszarze **zakres adresów**Wprowadź zakres sieci. Korzystamy z **10.1.0.0/24**, a nie do korzystania z podsieci.
9. Opuszczamy domyślne opcje podstawowej ochrony DDoS, bez punktu końcowego usługi ani zapory w sieci.
9. Wybierz pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po jego utworzeniu zobaczysz go na pulpicie nawigacyjnym Azure Portal.




## <a name="next-steps"></a>Następne kroki

- W przypadku odzyskiwania po awarii programu VMware [Przygotuj lokalną infrastrukturę VMware](tutorial-prepare-on-premises-vmware.md).
- W przypadku odzyskiwania po awarii funkcji Hyper-V [Przygotuj lokalne serwery funkcji Hyper-v](hyper-v-prepare-on-premises-tutorial.md).
- W przypadku odzyskiwania po awarii serwera fizycznego [Skonfiguruj serwer konfiguracji i środowisko źródłowe](physical-azure-disaster-recovery.md)
- [Dowiedz się więcej na temat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci platformy Azure.
- Informacje [o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) dyskach zarządzanych.
