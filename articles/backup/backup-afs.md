---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure w Azure Portal
description: Dowiedz się, jak używać Azure Portal do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938154"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services

W tym artykule wyjaśniono, jak używać Azure Portal do tworzenia kopii zapasowych [udziałów plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

W tym artykule dowiesz się, jak:

* Utwórz magazyn usługi Recovery Services.
* Odnajdź udziały plików i skonfiguruj kopie zapasowe.
* Uruchom zadanie tworzenia kopii zapasowej na żądanie, aby utworzyć punkt przywracania.

## <a name="prerequisites"></a>Wymagania wstępne

* Zidentyfikuj lub Utwórz [magazyn Recovery Services](#create-a-recovery-services-vault) w tym samym regionie, w którym znajduje się konto magazynu obsługujące udział plików.
* Upewnij się, że udział plików jest obecny w jednym z [obsługiwanych typów kont magazynu](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure w wersji zapoznawczej

Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Poniżej przedstawiono ograniczenia dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure:

* Obsługa kopii zapasowych udziałów plików platformy Azure w ramach kont magazynu z replikacją w ramach [magazynu Strefowo nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) jest obecnie ograniczona do [tych regionów](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup obecnie obsługuje Konfigurowanie zaplanowanych raz dziennie kopii zapasowych udziałów plików platformy Azure.
* Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
* Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
* Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu.
* Nie usuwaj migawek utworzonych przez Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania lub błędy przywracania.
* Nie usuwaj udziałów plików chronionych przez Azure Backup. Bieżące rozwiązanie usuwa wszystkie migawki wykonane przez Azure Backup po usunięciu udziału plików, dzięki czemu wszystkie punkty przywracania zostaną utracone.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.
* Możesz użyć [magazynu lokalnie nadmiarowego (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jako opcji niskiego kosztu.

Aby zmodyfikować typ replikacji magazynu:

1. W nowym magazynie wybierz pozycję **Właściwości** w sekcji **Ustawienia** .

1. Na stronie **Właściwości** w obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

    ![Aktualizacja konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.
>

## <a name="discover-file-shares-and-configure-backup"></a>Odnajdź udziały plików i skonfiguruj kopię zapasową

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn Recovery Services, którego chcesz użyć do utworzenia kopii zapasowej udziału plików.

1. Na pulpicie nawigacyjnym **magazynu Recovery Services** wybierz pozycję **+ kopia zapasowa**.

   ![Magazyn usługi Recovery Services](./media/backup-afs/recovery-services-vault.png)

    a. W obszarze **cel kopii zapasowej**ustaw, **gdzie działa Twoje obciążenie?** na **platformę Azure**.

    ![Wybierz udział plików platformy Azure jako cel kopii zapasowej](./media/backup-afs/backup-goal.png)

    b.  W **czym chcesz utworzyć kopię zapasową?** wybierz pozycję **udział plików platformy Azure** z listy rozwijanej.

    d.  Wybierz pozycję **kopia zapasowa** , aby zarejestrować rozszerzenie udziału plików platformy Azure w magazynie.

    ![Wybierz pozycję Kopia zapasowa, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-afs/register-extension.png)

1. Po wybraniu opcji **kopia zapasowa**zostanie otwarte okienko **kopia zapasowa** i zostanie wyświetlony komunikat z prośbą o wybranie konta magazynu z listy odnalezionych obsługiwanych kont magazynu. Są one skojarzone z tym magazynem lub znajdują się w tym samym regionie co magazyn, ale nie są jeszcze skojarzone z żadnym magazynem Recovery Services.

   ![Wybieranie konta magazynu](./media/backup-afs/select-storage-account.png)

1. Z listy odnalezionych kont magazynu wybierz konto, a następnie wybierz **przycisk OK**. Platforma Azure przeszukuje konto magazynu pod kątem udziałów plików, których kopię zapasową można utworzyć. Jeśli niedawno dodano udziały plików i nie są one widoczne na liście, poczekaj chwilę na wyświetlenie udziałów plików.

    ![Odnajdywanie udziałów plików](./media/backup-afs/discovering-file-shares.png)

1. Z listy **udziały plików** wybierz co najmniej jeden udział plików, dla którego chcesz utworzyć kopię zapasową. Kliknij przycisk **OK**.

1. Po wybraniu udziałów plików menu **kopia zapasowa** jest przełączane do **zasad tworzenia kopii zapasowych**. Z tego menu Wybierz istniejące zasady tworzenia kopii zapasowej lub Utwórz nowe. Następnie wybierz pozycję **Włącz kopię zapasową**.

    ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-afs/select-backup-policy.png)

Po ustawieniu zasad tworzenia kopii zapasowej migawka udziałów plików jest wykonywana w zaplanowanym czasie. Punkt odzyskiwania jest również zachowywany przez wybrany okres.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

Czasami może być konieczne wygenerowanie migawki kopii zapasowej lub punktu odzyskiwania poza godzinami zaplanowanymi w ramach zasad tworzenia kopii zapasowych. Typowym powodem generowania kopii zapasowej na żądanie jest prawidłowe po skonfigurowaniu zasad tworzenia kopii zapasowych. Na podstawie harmonogramu w ramach zasad tworzenia kopii zapasowych może to potrwać kilka godzin lub dni do momentu utworzenia migawki. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed wprowadzeniem planowanych zmian w udziałach plików.

### <a name="create-a-backup-job-on-demand"></a>Tworzenie zadania tworzenia kopii zapasowej na żądanie

1. Otwórz magazyn Recovery Services użyty do utworzenia kopii zapasowej udziału plików. W okienku **Przegląd** wybierz pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** .

   ![Wybierz elementy kopii zapasowej](./media/backup-afs/backup-items.png)

1. Po wybraniu **pozycji elementy kopii zapasowej**w okienku **Przegląd** pojawia się nowe okienko zawierające listę wszystkich **typów zarządzania kopiami zapasowymi** .

   ![Lista typów zarządzania kopiami zapasowymi](./media/backup-afs/backup-management-types.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową utworzono przy użyciu tego magazynu.

   ![Elementy kopii zapasowej usługi Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Pojawią się szczegóły **elementu kopia zapasowa** . W menu **element kopii zapasowej** wybierz pozycję **Utwórz kopię zapasową teraz**. Ponieważ to zadanie tworzenia kopii zapasowej jest na żądanie, nie ma żadnych zasad przechowywania skojarzonych z punktem odzyskiwania.

   ![Wybierz pozycję Utwórz kopię zapasową teraz](./media/backup-afs/backup-now.png)

1. Zostanie otwarte okienko **Tworzenie kopii zapasowej** . Określ ostatni dzień zachowywania punktu odzyskiwania. Możesz mieć maksymalnie 10 lat na tworzenie kopii zapasowej na żądanie.

   ![Wybierz datę przechowywania](./media/backup-afs/retention-date.png)

1. Wybierz **przycisk OK** , aby potwierdzić uruchomione zadanie tworzenia kopii zapasowej na żądanie.

1. Monitoruj powiadomienia portalu, aby śledzić Kończenie wykonywania zadania tworzenia kopii zapasowej. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu. Wybierz kolejno pozycje **zadania tworzenia kopii zapasowej** > **w toku**.

## <a name="next-steps"></a>Następne kroki

Omawiane kwestie:
* [Przywracanie udziałów plików platformy Azure](restore-afs.md)
* [Zarządzanie kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md)
