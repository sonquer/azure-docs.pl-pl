---
title: Skonfiguruj odzyskiwanie po awarii fizycznych serwerów lokalnych przy użyciu Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych serwerów z systemami Windows i Linux, korzystając z usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980312"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych serwerów fizycznych

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku przedstawiono sposób konfigurowania odzyskiwania po awarii lokalnych fizycznych serwerów z systemami Windows i Linux na platformie Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj platformę Azure i lokalne wymagania wstępne
> * Tworzenie magazynu usługi Recovery Services dla usługi Site Recovery 
> * Skonfiguruj źródłowe i docelowe środowiska replikacji
> * Tworzenie zasad replikacji
> * Włącz replikację dla serwera

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Upewnij się, że rozumiesz [architekturę i składniki](physical-azure-architecture.md) w tym scenariuszu.
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że serwery, które chcesz replikować, są zgodne z [wymaganiami maszyny wirtualnej platformy Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Przygotuj platformę Azure. Potrzebna jest subskrypcja platformy Azure, Sieć wirtualna platformy Azure i konto magazynu.
- Przygotuj konto do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

Przed rozpoczęciem należy zauważyć, że:

- Po przejściu do trybu failover na platformie Azure serwery fizyczne nie mogą zostać przywrócone do lokalnych maszyn fizycznych. Można wrócić po awarii do maszyn wirtualnych VMware. 
- Ten samouczek konfiguruje odzyskiwanie awaryjne serwera fizycznego na platformie Azure przy użyciu najprostszych ustawień. Jeśli chcesz dowiedzieć się więcej o innych opcjach, zapoznaj się z naszymi przewodnikami:
    - Skonfiguruj [Źródło replikacji](physical-azure-set-up-source.md), w tym Site Recovery serwer konfiguracji.
    - Konfigurowanie [miejsca docelowego replikacji](physical-azure-set-up-target.md).
    - Konfigurowanie [zasad replikacji](vmware-azure-set-up-replication.md) i [włączanie replikacji](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Skonfiguruj konto platformy Azure

Pobierz [konto platformy Microsoft Azure](https://azure.microsoft.com/).

- Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowiedz się więcej o [Site Recovery cenach](site-recovery-faq.md#pricing)i uzyskaj [szczegółowe informacje o cenach](https://azure.microsoft.com/pricing/details/site-recovery/).
- Dowiedz się, które [regiony są obsługiwane](https://azure.microsoft.com/pricing/details/site-recovery/) przez Site Recovery.

### <a name="verify-azure-account-permissions"></a>Weryfikowanie uprawnień konta platformy Azure

Upewnij się, że Twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformę Azure.

- Przejrzyj [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) wymagane do replikowania maszyn na platformę Azure.
- Weryfikowanie i modyfikowanie uprawnień [dostępu opartych na rolach](../role-based-access-control/role-assignments-portal.md) . 



### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Skonfiguruj [Sieć platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci, gdy są tworzone po przejściu w tryb failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn Recovery Services


## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Skonfiguruj [konto usługi Azure Storage](../storage/common/storage-account-create.md).

- Site Recovery replikuje maszyny lokalne do usługi Azure Storage. Maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu do trybu failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Na każdym serwerze, który ma zostać zreplikowany, musi być zainstalowana usługa mobilności. Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla serwera. Aby automatycznie zainstalować program, należy przygotować konto, które będzie używane przez Site Recovery do uzyskiwania dostępu do serwera.

- Możesz użyć domeny lub konta lokalnego
- W przypadku maszyn wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym. W tym celu w rejestrze w obszarze **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**Dodaj wpis DWORD **LocalAccountTokenFilterPolicy**o wartości 1.
- Aby dodać wpis rejestru w celu wyłączenia ustawienia z interfejsu wiersza polecenia, wpisz: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku systemu Linux konto powinno być kontem głównym na źródłowym serwerze z systemem Linux.


## <a name="create-a-vault"></a>Tworzenie magazynu

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz elementy, które mają być replikowane, i Replikuj je do.

1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasobów kliknij pozycję **Site Recovery** > **Przygotowanie infrastruktury** > **Cel ochrony**.
3. W obszarze **cel ochrony**wybierz pozycję **Azure** > **nie zwirtualizowany/inny**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Skonfiguruj serwer konfiguracji, zarejestruj go w magazynie i odnajdź maszyny wirtualne.

1. Kliknij **Site Recovery** > **przygotuj infrastrukturę** > **Źródło**.
2. Jeśli nie masz serwera konfiguracji, kliknij przycisk **+ serwer konfiguracji**.
3. W obszarze **Dodawanie serwera**Sprawdź, czy **serwer konfiguracji** jest wyświetlany w polu **Typ serwera**.
4. Pobierz plik instalacyjny programu Site Recovery Unified Setup.
5. Pobierz klucz rejestracji magazynu. Jest on potrzebny po uruchomieniu ujednoliconej konfiguracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Konfiguracja źródła](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Rejestrowanie serwera konfiguracji w magazynie

Wykonaj następujące czynności przed rozpoczęciem: 

#### <a name="verify-time-accuracy"></a>Sprawdź dokładność czasu
Na komputerze serwera konfiguracji upewnij się, że zegar systemowy jest zsynchronizowany z [serwerem czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Powinien być zgodny. Jeśli jest to 15 minut przed lub za, instalacja może zakończyć się niepowodzeniem.

#### <a name="verify-connectivity"></a>Sprawdź łączność
Upewnij się, że komputer może uzyskać dostęp do tych adresów URL w oparciu o środowisko: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Reguły zapory oparte na adresach IP powinny zezwalać na komunikację ze wszystkimi adresami URL platformy Azure wymienionymi powyżej za pośrednictwem portu HTTPS (443). Aby uprościć i ograniczyć zakres adresów IP, zalecane jest filtrowanie adresów URL.

- **Komercyjne adresy** IP — Zezwalaj na [przedziały adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz port HTTPS (443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji, aby obsługiwać adresy URL usługi AAD, kopii zapasowej, replikacji i magazynu.  
- **Adresy IP instytucji rządowych** — zezwalaj na [Azure Government zakresy adresów URL centrum](https://www.microsoft.com/en-us/download/details.aspx?id=57063)danych oraz port HTTPS (443) dla wszystkich regionów USGov (Wirginia, Texas, Arizona i Iowa) do obsługi usługi AAD, kopii zapasowych, replikacji i adresów URL magazynu.  

#### <a name="run-setup"></a>Uruchamianie instalatora
Uruchom ujednoliconą konfigurację jako administrator lokalny, aby zainstalować serwer konfiguracji. Serwer przetwarzania oraz główny serwer docelowy są również instalowane domyślnie na serwerze konfiguracji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po zakończeniu rejestracji serwer konfiguracji zostanie wyświetlony na stronie **ustawienia** > **serwery** w magazynie.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Cel](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij pozycję **Infrastruktura usługi Site Recovery** > **Zasady replikacji** >  **+Zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa, jak często są tworzone punkty odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W obszarze **częstotliwość tworzenia migawek**na poziomie aplikacji Określ, jak często (w minutach) będą tworzone punkty odzyskiwania zawierające migawki spójne z aplikacjami. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji są typu **"przedstawiciel"** , zostanie utworzona zasada powrotu po awarii nr **przedstawiciel-zasady-powrót** po awarii. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdego serwera.

- Site Recovery zainstaluje usługę mobilności po włączeniu replikacji.
- Po włączeniu replikacji dla serwera może upłynąć 15 minut lub dłużej, aby zmiany zaczęły obowiązywać i pojawiają się w portalu.

1. Kliknij kolejno pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny**wybierz pozycję **maszyny fizyczne**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W obszarze **cel**wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne platformy Azure po przejściu do trybu failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure (wersja klasyczna lub zarządzanie zasobami).
6. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn. 
9. W obszarze **maszyny fizyczne**i kliknij pozycję **+ maszyna fizyczna**. Określ nazwę i adres IP. Wybierz system operacyjny maszyny, którą chcesz replikować. Odnajdywanie i wykrycie serwerów może potrwać kilka minut. 
10. W obszarze **Właściwości** > **Konfigurowanie właściwości** wybierz konto, które będzie używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi Mobility na maszynie.
11. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


Aby monitorować dodawane serwery, można sprawdzić czas ostatniego wykrycia dla nich na **serwerach konfiguracji** > **ostatnim kontakcie na stronie**. Aby dodać maszyny bez oczekiwania na zaplanowaną godzinę odnajdywania, wyróżnij serwer konfiguracji (nie klikaj go), a następnie kliknij przycisk **Odśwież**.

## <a name="next-steps"></a>Następne kroki

[Uruchom przechodzenie do szczegółów odzyskiwania po awarii](tutorial-dr-drill-azure.md).
