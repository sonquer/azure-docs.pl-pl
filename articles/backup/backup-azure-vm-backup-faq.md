---
title: Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705415"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi [Azure Backup](backup-introduction-to-azure-backup.md) .

## <a name="backup"></a>Tworzenie kopii zapasowych

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Które obrazy maszyn wirtualnych można włączyć do tworzenia kopii zapasowych podczas ich tworzenia?

Podczas tworzenia maszyny wirtualnej można włączyć tworzenie kopii zapasowych dla maszyn wirtualnych z [obsługiwanymi systemami operacyjnymi](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Czy koszt kopii zapasowej obejmuje koszt maszyny wirtualnej?

Nie. Koszty kopii zapasowej są oddzielone od kosztów maszyny wirtualnej. Dowiedz się więcej o [cenach Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Które uprawnienia są wymagane, aby włączyć tworzenie kopii zapasowej maszyny wirtualnej?

Jeśli jesteś współautorem maszyny wirtualnej, możesz włączyć tworzenie kopii zapasowej na maszynie wirtualnej. Jeśli używasz roli niestandardowej, musisz mieć następujące uprawnienia, aby włączyć tworzenie kopii zapasowej na maszynie wirtualnej:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Jeśli magazyn Recovery Services i maszyna wirtualna mają różne grupy zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów dla magazynu Recovery Services.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Czy zadanie tworzenia kopii zapasowej na żądanie używa tego samego harmonogramu przechowywania co zaplanowane kopie zapasowe?

Nie. Określ zakres przechowywania dla zadania tworzenia kopii zapasowej na żądanie. Domyślnie jest ono przechowywane przez 30 dni, gdy jest wyzwalane z poziomu portalu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ostatnio na niektórych maszynach wirtualnych została włączona usługa Azure Disk Encryption. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Podaj uprawnienia do Azure Backup dostępu Key Vault. Określ uprawnienia w programie PowerShell zgodnie z opisem w sekcji **Włączanie kopii zapasowej** w dokumentacji [programu Azure Backup PowerShell](backup-azure-vms-automation.md) .

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Dyski maszyny wirtualnej zostały zmigrowane do dysków zarządzanych. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Tak, kopie zapasowe działają bezproblemowo. Nie trzeba ponownie konfigurować żadnych elementów.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Dlaczego nie widzę mojej maszyny wirtualnej w kreatorze konfigurowania kopii zapasowych?

W kreatorze są wyświetlane tylko maszyny wirtualne znajdujące się w tym samym regionie co magazyn, które nie są już tworzone w ramach kopii zapasowej.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Moja maszyna wirtualna jest wyłączona. Czy zostanie zaplanowana kopia zapasowa na żądanie, czy też

Tak. Kopie zapasowe są uruchamiane, gdy maszyna jest wyłączona. Punkt odzyskiwania jest oznaczony jako spójny pod kątem awarii.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Czy mogę anulować zadanie tworzenia kopii zapasowej w toku?

Tak. Zadanie tworzenia kopii zapasowej można anulować w stanie trwającej **migawki** . Nie można anulować zadania, jeśli transfer danych z migawki jest w toku.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Włączono blokadę dla grupy zasobów utworzonej przez usługę Azure Backup (tj. `AzureBackupRG_<geo>_<number>`), czy moje kopie zapasowe będą nadal działały?

Jeśli zablokujesz grupę zasobów utworzoną przez usługę Azure Backup, kopie zapasowe będą się kończyć niepowodzeniem, ponieważ obowiązuje limit 18 punktów przywracania.

Użytkownik musi usunąć blokadę i wyczyścić kolekcję punktów przywracania z tej grupy zasobów, aby umożliwić pomyślne tworzenie kopii zapasowych w przyszłości, [wykonaj następujące kroki](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) , aby usunąć kolekcję punktów przywracania.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Czy usługa Azure Backup obsługuje standardowe dyski zarządzane przez dysk SSD?

Tak, Azure Backup obsługuje [dyski zarządzane w warstwie Standardowa SSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Czy można utworzyć kopię zapasową maszyny wirtualnej z dyskiem z obsługą akcelerator zapisu (WA)?

Nie można wykonać migawek na dysku z obsługą WA. Jednak usługa Azure Backup może wykluczyć dysk z obsługą WA z kopii zapasowej.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mam maszynę wirtualną z dyskami akcelerator zapisu (WA) i zainstalowaną SAP HANA. Jak mogę utworzyć kopię zapasową?

Azure Backup nie może utworzyć kopii zapasowej dysku z obsługą WA, ale może go wykluczyć z kopii zapasowej. Jednak kopia zapasowa nie zapewni spójności bazy danych, ponieważ nie jest wykonywana kopia zapasowa informacji na dysku z obsługą WA. Można utworzyć kopię zapasową dysków przy użyciu tej konfiguracji, jeśli chcesz utworzyć kopię zapasową dysku systemu operacyjnego i utworzyć kopię zapasową dysków, które nie są włączone.

Korzystamy z prywatnej wersji zapoznawczej SAP HANA kopii zapasowej z celem punktu odzyskiwania wynoszącym 15 minut. Jest on zbudowany w podobny sposób do kopii zapasowej bazy danych SQL i używa interfejsu backInt dla rozwiązań innych firm certyfikowanych przez SAP HANA. Jeśli jesteś zainteresowanym, Wyślij wiadomość e-mail na adres `AskAzureBackupTeam@microsoft.com` z podmiotem **rejestracja w celu utworzenia prywatnej wersji zapoznawczej SAP HANA na maszynach wirtualnych platformy Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Co to jest maksymalne opóźnienie, które może oczekiwać w czasie rozpoczęcia wykonywania kopii zapasowej z zaplanowanego czasu wykonywania kopii zapasowej ustawionej w ramach zasad kopii zapasowych maszyny wirtualnej?

Zaplanowana kopia zapasowa zostanie wyzwolona w ciągu 2 godzin od zaplanowanego czasu tworzenia kopii zapasowej. Jeśli na przykład maszyny wirtualne 100 mają czas rozpoczęcia wykonywania kopii zapasowej zaplanowane o godzinie 2:00, a następnie przez maksymalnie 4:00, wszystkie maszyny wirtualne 100 z maszyną wirtualną będą miały uruchomione zadanie tworzenia kopii zapasowej. Jeśli zaplanowane kopie zapasowe zostały wstrzymane ze względu na awarię i wznowienie/ponowną próbę, można rozpocząć tworzenie kopii zapasowej poza zaplanowanym dwugodzinnym oknem.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Jaki jest minimalny dozwolony zakres przechowywania dla codziennego punktu kopii zapasowej?

Zasady tworzenia kopii zapasowych maszyn wirtualnych platformy Azure obsługują minimalny zakres przechowywania wynoszący siedem dni do 9999 dni. Wszelkie modyfikacje istniejących zasad tworzenia kopii zapasowych maszyn wirtualnych przy użyciu mniej niż siedmiu dni będą wymagały aktualizacji w celu spełnienia minimalnego zakresu przechowywania wynoszącego siedem dni.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Czy można tworzyć kopie zapasowe lub przywracać dyski selektywne dołączone do maszyny wirtualnej?

Azure Backup teraz obsługuje selektywne tworzenie kopii zapasowych i przywracanie dysków przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.

Obecnie Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) w maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowej maszyny wirtualnej. Funkcja wykluczania dysku umożliwia utworzenie kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dotyczące dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzestawu dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i magazynu.

Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas na AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Przywracanie

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak mogę zdecydować, czy przywracać tylko dyski, czy pełną maszynę wirtualną?

Należy wziąć pod uwagę przywracanie maszyny wirtualnej jako opcję szybkiego tworzenia dla maszyny wirtualnej platformy Azure. Ta opcja umożliwia zmianę nazw dysków, kontenerów używanych przez dyski, publiczne adresy IP i nazwy interfejsów sieciowych. Zmiana zachowuje unikatowe zasoby podczas tworzenia maszyny wirtualnej. Maszyna wirtualna nie została dodana do zestawu dostępności.

Jeśli chcesz, możesz użyć opcji Przywróć dysk:

- Dostosuj utworzoną maszynę wirtualną. Na przykład zmień rozmiar.
- Dodaj ustawienia konfiguracji, które nie były w trakcie wykonywania kopii zapasowej.
- Kontrolowanie konwencji nazewnictwa dla utworzonych zasobów.
- Dodaj maszynę wirtualną do zestawu dostępności.
- Dodaj inne ustawienia, które należy skonfigurować przy użyciu programu PowerShell lub szablonu.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Czy można przywrócić kopie zapasowe niezarządzanych dysków maszyny wirtualnej po uaktualnieniu do dysków zarządzanych?

Tak, można użyć kopii zapasowych wykonanych przed migracją dysków z niezarządzanych do zarządzanych.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak mogę przywrócić maszynę wirtualną do punktu przywracania, zanim maszyna wirtualna została zmigrowana do dysków zarządzanych?

Proces przywracania pozostaje taki sam. Jeśli punkt odzyskiwania znajduje się w danym punkcie w czasie, gdy maszyna wirtualna ma dyski niezarządzane, można [przywrócić dyski jako niezarządzane](tutorial-restore-disk.md#unmanaged-disks-restore). Jeśli maszyna wirtualna ma dyski zarządzane, możesz [przywrócić dyski jako dyski zarządzane](tutorial-restore-disk.md#managed-disk-restore). Następnie można [utworzyć maszynę wirtualną na podstawie tych dysków](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Dowiedz się więcej](backup-azure-vms-automation.md#restore-an-azure-vm) o tym, jak to zrobić w programie PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Czy można przywrócić maszynę wirtualną, która została usunięta?

Tak. Nawet jeśli usuniesz maszynę wirtualną, możesz przejść do odpowiedniego elementu kopii zapasowej w magazynie i przywrócić go z punktu odzyskiwania.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Jak przywrócić maszynę wirtualną do tych samych zestawów dostępności?

W przypadku dysku zarządzanego maszyny wirtualnej platformy Azure przywracanie do zestawów dostępności jest włączone przez udostępnienie opcji w szablonie podczas przywracania jako dyski zarządzane. Ten szablon zawiera parametr wejściowy o nazwie **zestawy dostępności**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak szybsze przywracanie wydajności?

Funkcja [natychmiastowego przywracania](backup-instant-restore-capability.md) pomaga w szybszym wykonywaniu kopii zapasowych i natychmiast przywracaniu z migawek.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co się stanie w przypadku zmiany ustawień magazynu kluczy dla zaszyfrowanej maszyny wirtualnej?

Po zmianie ustawień magazynu kluczy dla zaszyfrowanej maszyny wirtualnej kopie zapasowe będą nadal współpracują z nowym zestawem szczegółów. Jednak po przywróceniu z punktu odzyskiwania przed zmianą należy przywrócić klucze tajne w magazynie kluczy, aby można było utworzyć maszynę wirtualną. Aby uzyskać więcej informacji, zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Operacje, takie jak przechodzenie do trybu tajnego/klucza, nie wymagają tego kroku, a ten sam magazyn kluczy może być używany po przywróceniu.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Czy mogę uzyskać dostęp do maszyny wirtualnej po jej przywróceniu, ponieważ maszyna wirtualna ma przerwane relacje z kontrolerem domeny?

Tak, można uzyskać dostęp do maszyny wirtualnej po jej przywróceniu, ponieważ maszyna wirtualna ma przerwane relacje z kontrolerem domeny. Aby uzyskać więcej informacji, zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co się stanie w przypadku zmodyfikowania zasad tworzenia kopii zapasowych?

Utworzono kopię zapasową maszyny wirtualnej przy użyciu ustawień harmonogram i przechowywanie w zmodyfikowanych lub nowych zasadach.

- Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania są oznaczane i przechowywane zgodnie z nowymi zasadami.
- W przypadku skrócenia okresu przechowywania punkty odzyskiwania są oznaczane do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usuwane.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak mogę przenieść maszyny wirtualnej, której kopia zapasowa została utworzona przez Azure Backup do innej grupy zasobów?

1. Tymczasowe zatrzymywanie tworzenia kopii zapasowej i zachowywanie danych kopii zapasowej.
2. Przenieś maszynę wirtualną do docelowej grupy zasobów.
3. Ponowna obsługa kopii zapasowych w tym samym lub nowym magazynie.

Można przywrócić maszynę wirtualną z dostępnych punktów przywracania utworzonych przed operacją przenoszenia.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Czy istnieje ograniczenie liczby maszyn wirtualnych, które można skojarzyć z tymi samymi zasadami tworzenia kopii zapasowych?

Tak, istnieje limit 100 maszyn wirtualnych, które mogą być skojarzone z tymi samymi zasadami tworzenia kopii zapasowych z poziomu portalu. Zalecamy, aby dla ponad 100 maszyn wirtualnych utworzyć wiele zasad tworzenia kopii zapasowych z tym samym harmonogramem lub innym harmonogramem.
