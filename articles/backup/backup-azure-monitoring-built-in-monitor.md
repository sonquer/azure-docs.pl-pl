---
title: Monitorowanie Azure Backup chronionych obciążeń
description: W tym artykule omówiono możliwości monitorowania i powiadamiania dotyczące obciążeń Azure Backup przy użyciu Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172874"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorowanie obciążeń Azure Backup

Azure Backup oferuje wiele rozwiązań do tworzenia kopii zapasowych opartych na wymaganiach dotyczących kopii zapasowych i topologii infrastruktury (lokalnie i na platformie Azure). Każdy użytkownik lub administrator kopii zapasowej powinien zobaczyć, co dzieje się we wszystkich rozwiązaniach, i powinien zostać powiadomiony w ważnych scenariuszach. W tym artykule opisano możliwości monitorowania i powiadamiania udostępniane przez usługę Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Zadania tworzenia kopii zapasowej w magazynie Recovery Services

Azure Backup zapewnia wbudowane funkcje monitorowania i alertów dla obciążeń chronionych przez Azure Backup. W ustawieniach magazynu Recovery Services sekcja **monitorowanie** zawiera wbudowane zadania i alerty.

![Niezbudowane monitorowanie magazynu RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Zadania są generowane, gdy wykonywane są operacje, takie jak konfigurowanie kopii zapasowej, wykonywanie kopii zapasowej, przywracanie, Usuwanie kopii zapasowej itd.

Poniżej przedstawiono zadania z następujących rozwiązań Azure Backup:

- Kopia zapasowa maszyny wirtualnej platformy Azure
- Kopia zapasowa plików platformy Azure
- Kopia zapasowa platformy Azure, taka jak SQL
- Agent usługi Azure Backup (MAB)

NIE są wyświetlane zadania z programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (serwera usługi MAB).

> [!NOTE]
> Obciążenia platformy Azure, takie jak kopie zapasowe SQL na maszynach wirtualnych platformy Azure, mają ogromną liczbę zadań tworzenia kopii zapasowej. Na przykład kopie zapasowe dzienników można uruchamiać co 15 minut. W związku z tym w przypadku takich obciążeń bazy danych wyświetlane są tylko operacje wyzwalane przez użytkownika. Zaplanowane operacje tworzenia kopii zapasowej nie są wyświetlane.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alerty kopii zapasowych w magazynie Recovery Services

Alerty są szczególnie sytuacje, w których użytkownicy są powiadamiani, aby mogli podejmować odpowiednie działania. W sekcji **alerty kopii zapasowej** znajdują się alerty wygenerowane przez usługę Azure Backup. Te alerty są definiowane przez usługę i użytkownik nie może tworzyć alertów niestandardowych.

### <a name="alert-scenarios"></a>Scenariusze alertów

Poniższe scenariusze są definiowane przez usługę jako scenariusze z alertami.

- Niepowodzenia wykonywania kopii zapasowej/przywracania
- Wykonanie kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami dla agenta usługi Azure Backup (MAB)
- Zatrzymaj ochronę z zachowaniem Zachowaj dane/Zatrzymaj ochronę przy użyciu danych usuwania

### <a name="exceptions-when-an-alert-is-not-raised"></a>Wyjątki w przypadku niezgłoszenia alertu

Istnieje kilka wyjątków, które w przypadku niepowodzenia alertu nie są zgłaszane:

- Użytkownik jawnie anulował uruchomione zadanie
- Zadanie nie powiodło się, ponieważ trwa inne zadanie tworzenia kopii zapasowej (nic nie działa, ponieważ oczekujemy na zakończenie poprzedniego zadania)
- Zadanie tworzenia kopii zapasowej maszyny wirtualnej nie powiodło się, ponieważ kopia zapasowa maszyny wirtualnej platformy Azure już nie istnieje

Powyższe wyjątki zostały zaprojektowane z myślą o tym, że wynik tych operacji (przede wszystkim jest wyzwalany przez użytkownika) jest natychmiast wyświetlany na klientach Portal/PS/CLI. W związku z tym użytkownik jest natychmiast świadomy i nie potrzebuje powiadomienia.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alerty z następujących rozwiązań Azure Backup są wyświetlane w tym miejscu

- Kopie zapasowe maszyn wirtualnych platformy Azure
- Kopie zapasowe plików platformy Azure
- Kopie zapasowe obciążenia platformy Azure, takie jak SQL
- Agent usługi Azure Backup (MAB)

> [!NOTE]
> Alerty z programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (serwera usługi MAB) nie są wyświetlane w tym miejscu.

### <a name="alert-types"></a>Typy alertów

W oparciu o ważność alertu alerty można definiować w trzech typach:

- **Krytyczny**: w zasadzie wszystkie błędy tworzenia kopii zapasowej lub odzyskiwania (zaplanowane lub wywołane przez użytkownika) spowodują wygenerowanie alertu i będą wyświetlane jako alert krytyczny, a także operacje niszczące, takie jak usuwanie kopii zapasowej.
- **Ostrzeżenie**: Jeśli operacja tworzenia kopii zapasowej zakończy się pomyślnie, ale z kilkoma ostrzeżeniami, są one wyświetlane jako alerty ostrzegawcze.
- **Informacyjny**: w dniu dzisiejszym usługa Azure Backup nie generuje alertu informacyjnego.

## <a name="notification-for-backup-alerts"></a>Powiadomienia o alertach dotyczących kopii zapasowych

> [!NOTE]
> Konfigurację powiadomień można przeprowadzić tylko za pomocą witryny Azure Portal. Obsługa szablonu interfejsu Azure Resource Manager API (PS/CLI/REST) nie jest obsługiwana.

Po podniesieniu alertu użytkownicy będą powiadamiani. Azure Backup zapewnia wbudowany mechanizm powiadomień za pośrednictwem poczty e-mail. Jeden z nich może określać poszczególne adresy e-mail lub listy dystrybucyjne do powiadomienia o wygenerowaniu alertu. Możesz również wybrać, czy otrzymywać powiadomienia o każdym indywidualnym alercie, czy grupować je w co godzinę, a następnie otrzymywać powiadomienia.

![Powiadomienie e-mail dotyczące nieskompilowanego magazynu RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> Alerty dla kopii zapasowych SQL zostaną skonsolidowane i wiadomość e-mail będzie wysyłana tylko dla pierwszego wystąpienia. Jeśli jednak alert zostanie zdezaktywowany przez użytkownika, następne wystąpienie wyzwoli kolejną wiadomość e-mail.

Po skonfigurowaniu powiadomienia otrzymasz powitalną lub wprowadzającą wiadomość e-mail. Spowoduje to potwierdzenie, że Azure Backup mogą wysyłać wiadomości e-mail na te adresy, gdy zostanie zgłoszony alert.<br>

Jeśli częstotliwość została ustawiona na podsumowanie godzinowe, a alert został zgłoszony i rozwiązany w ciągu godziny, nie będzie częścią nadchodzącego podsumowania godzinowego.

> [!NOTE]
>
> - W przypadku wykonania operacji niszczącej, takiej jak **zatrzymanie ochrony z usuwaniem danych** , zostanie zgłoszony alert, a wiadomość e-mail zostanie wysłana do właścicieli subskrypcji, administratorów i współadministratorów, nawet jeśli nie skonfigurowano powiadomień dla magazynu usługi odzyskiwania.
> - Aby skonfigurować powiadomienie dla zadań zakończonych powodzeniem, użyj [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Dezaktywacja alertów

Aby dezaktywować/rozwiązywać aktywny alert, możesz kliknąć element listy odpowiadający Alertowi, który ma zostać zdezaktywowany. Spowoduje to otwarcie ekranu wyświetlającego szczegółowe informacje o alercie, z przyciskiem "Dezaktywuj" u góry. Kliknięcie tego przycisku spowoduje zmianę stanu alertu na nieaktywny. Możesz również dezaktywować alert, klikając prawym przyciskiem myszy element listy odpowiadający temu Alertowi i wybierając pozycję "Dezaktywuj".

![Dezaktywacja alertu dotyczącego magazynu RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Następne kroki

[Monitorowanie obciążeń usługi Azure Backup przy użyciu Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
