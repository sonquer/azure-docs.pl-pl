---
title: Macierz obsługi dla agenta MARS
description: Ten artykuł zawiera podsumowanie Azure Backup pomocy technicznej podczas tworzenia kopii zapasowej maszyn, na których jest uruchomiony agent Microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 8f5ce33b5057b11caa33c0ae80cf72e1b13da5d0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425021"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowej za pomocą agenta Microsoft Azure Recovery Services (MARS)

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz tworzyć kopie zapasowe maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień i ograniczeń pomocy technicznej w przypadku używania agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych maszyn.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup używa agenta MARS do tworzenia kopii zapasowych danych z maszyn lokalnych i maszyn wirtualnych platformy Azure w magazynie kopii zapasowych Recovery Services na platformie Azure. Agent MARS może:

- Uruchom na lokalnych maszynach z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio do magazynu Recovery Services kopii zapasowych na platformie Azure.
- Uruchom na maszynach wirtualnych z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio w magazynie.
- Uruchom na serwerze Microsoft Azure Backup Server (serwera usługi MAB) lub w programie System Center Data Protection Manager (DPM). W tym scenariuszu maszyny i obciążenia wykonują kopie zapasowe w programie serwera usługi MAB lub na serwerze programu DPM. Następnie Agent MARS tworzy kopię zapasową tego serwera w magazynie na platformie Azure.

> [!NOTE]
>Azure Backup nie obsługuje automatycznego dostosowywania zegara dla czasu letniego (DST). Zmodyfikuj zasady, aby zapewnić, że zmiany czasu letniego są brane pod uwagę, aby zapobiec niezgodności między rzeczywistym czasem a zaplanowaną godziną tworzenia kopii zapasowej.

Opcje tworzenia kopii zapasowej zależą od tego, gdzie jest zainstalowany agent programu. Aby uzyskać więcej informacji, zobacz [Azure Backup architektury przy użyciu agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Aby uzyskać informacje na temat architektury kopii zapasowych serwera usługi MAB i programu DPM, zobacz [Tworzenie kopii zapasowej w programie DPM lub serwera usługi MAB](backup-architecture.md#architecture-back-up-to-dpmmabs). Zapoznaj się również z [wymaganiami](backup-support-matrix-mabs-dpm.md) dotyczącymi architektury tworzenia kopii zapasowych.

**Instalacja** | **Szczegóły**
--- | ---
Pobierz najnowszego agenta MARS | Możesz pobrać najnowszą wersję agenta z magazynu lub [pobrać ją bezpośrednio](https://aka.ms/azurebackup_agent).
Instalowanie bezpośrednio na komputerze | Agenta MARS można zainstalować bezpośrednio na lokalnym serwerze Windows lub na maszynie wirtualnej z systemem Windows, na której działa dowolny z [obsługiwanych systemów operacyjnych](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Zainstaluj na serwerze kopii zapasowej | Po skonfigurowaniu programu DPM lub serwera usługi MAB na potrzeby tworzenia kopii zapasowych na platformie Azure należy pobrać i zainstalować agenta MARS na serwerze. Agenta programu można zainstalować w [obsługiwanych systemach operacyjnych](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi serwera kopii zapasowych.

> [!NOTE]
> Domyślnie maszyny wirtualne platformy Azure, dla których włączono obsługę kopii zapasowej, mają instalację rozszerzenia Azure Backup. To rozszerzenie tworzy kopię zapasową całej maszyny wirtualnej. Możesz zainstalować i uruchomić agenta MARS na maszynie wirtualnej platformy Azure obok rozszerzenia, jeśli chcesz utworzyć kopię zapasową określonych folderów i plików, a nie na całej maszynie wirtualnej.
> Po uruchomieniu agenta MARS na maszynie wirtualnej platformy Azure program tworzy kopię zapasową plików lub folderów znajdujących się w magazynie tymczasowym na maszynie wirtualnej. Tworzenie kopii zapasowych kończy się niepowodzeniem, jeśli pliki lub foldery zostaną usunięte z magazynu tymczasowego lub jeśli magazyn tymczasowy zostanie usunięty.

## <a name="cache-folder-support"></a>Obsługa folderu pamięci podręcznej

W celu utworzenia kopii zapasowej danych przy użyciu agenta MARS Agent tworzy migawkę danych i zapisuje ją w folderze lokalnego pamięci podręcznej, zanim wyśle dane do platformy Azure. Folder pamięci podręcznej (Scratch) ma kilka wymagań:

**Chow** | **Szczegóły**
--- | ---
Rozmiar |  Ilość wolnego miejsca w folderze pamięci podręcznej powinna wynosić co najmniej 5 do 10 procent całkowitego rozmiaru danych kopii zapasowej.
Lokalizacja | Folder pamięci podręcznej musi być przechowywany lokalnie na komputerze, na którym jest wykonywana kopia zapasowa, i musi być w trybie online. Folder pamięci podręcznej nie powinien znajdować się w udziale sieciowym na nośniku wymiennym ani w woluminie w trybie offline.
Folder | Folder pamięci podręcznej nie powinien być szyfrowany na deduplikowanym woluminie lub w folderze skompresowanym, który jest rozrzedzony, lub ma punkt ponownej analizy.
Zmiany lokalizacji | Można zmienić lokalizację pamięci podręcznej przez zatrzymanie aparatu kopii zapasowej (`net stop bengine`) i skopiowanie folderu pamięci podręcznej na nowy dysk. (Upewnij się, że nowy dysk ma wystarczającą ilość miejsca). Następnie zaktualizuj dwa wpisy rejestru w obszarze **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** i **config/CloudBackupProvider/ScratchLocation**) do nowej lokalizacji i ponownie uruchom aparat.

## <a name="networking-and-access-support"></a>Obsługa sieci i dostępu

### <a name="url-and-ip-access"></a>Dostęp do adresów URL i adresów IP

Agent MARS musi mieć dostęp do tych adresów URL:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

I na te adresy IP:

- 20.190.128.0/18
- 40.126.0.0/18

Dostęp do wszystkich adresów URL i adresów IP wymienionych powyżej używa protokołu HTTPS na porcie 443.

### <a name="throttling-support"></a>Obsługa ograniczania przepustowości

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane. W agencie MARS Użyj **właściwości Zmień** , aby dostosować przepustowość.
Ograniczanie sieci | Niedostępne dla maszyn z kopią zapasową z systemem Windows Server 2008 R2, Windows Server 2008 z dodatkiem SP2 lub Windows 7.

## <a name="support-for-direct-backups"></a>Obsługa bezpośrednich kopii zapasowych

>[!NOTE]
> Agent MARS nie obsługuje podstawowych jednostek SKU systemu Windows Server.

Agenta MARS można użyć do utworzenia kopii zapasowej bezpośrednio na platformie Azure w systemach operacyjnych wymienionych poniżej, które są uruchamiane na:

1. Lokalne serwery z systemem Windows
2. Maszyny wirtualne platformy Azure z systemem Windows

Systemy operacyjne muszą być 64 bitowe i powinny mieć zainstalowane najnowsze pakiety i aktualizacje usług. W poniższej tabeli zestawiono te systemy operacyjne:

**System operacyjny** | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Tak | Nie |  Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8.1 (Enterprise, Pro)| Tak |Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8 (Enterprise, Pro) | Tak | Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Tak | Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows Server 2016 (wersje Standard, Datacenter, Essentials) | Tak | Tak | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (wersje Standard, Datacenter, Foundation, Essentials) | Tak | Tak | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2012 (wersje Standard, Datacenter, Foundation) | Tak | Tak |— .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0 <br> — Obsługa i zarządzanie obrazami wdrażania (DISM. exe)
Windows Storage Server 2016/2012 R2/2012 (standard, Grupa robocza) | Tak | Nie | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2019 (wersje Standard, Datacenter, Essentials) | Tak | Tak | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0

Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne serwera usługi MAB i DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limity kopii zapasowych

### <a name="size-limits"></a>Limity rozmiaru

Azure Backup ogranicza rozmiar źródła danych pliku lub folderu, dla którego można utworzyć kopię zapasową. Elementy, których kopię zapasową utworzono z pojedynczego woluminu, nie mogą przekroczyć rozmiarów podsumowanych w tej tabeli:

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 lub nowszy| 54 400 GB
Windows 7| 1 700 GB

### <a name="other-limitations"></a>Inne ograniczenia

- Usługa MARS nie obsługuje ochrony wielu maszyn o tej samej nazwie w pojedynczym magazynie.

## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików dla kopii zapasowej

**Typ** | **Pomoc techniczna**
--- | ---
Zaszyfrowane| Obsługiwane.
Skompresowane | Obsługiwane.
Rozrzedzone | Obsługiwane.
Skompresowane i rozrzedzone |Obsługiwane.
Twarde linki| Nieobsługiwane. Pominięto.
Punkt ponownej analizy| Nieobsługiwane. Pominięto.
Zaszyfrowane i rozrzedzone |Nieobsługiwane. Pominięto.
Skompresowany strumień| Nieobsługiwane. Pominięto.
Rozrzedzony strumień| Nieobsługiwane. Pominięto.
OneDrive (synchronizowane pliki to strumienie rozrzedzone)| Nieobsługiwane.
Foldery z włączonym Replikacja systemu plików DFS | Nieobsługiwane.

## <a name="supported-drives-or-volumes-for-backup"></a>Obsługiwane dyski lub woluminy na potrzeby tworzenia kopii zapasowych

**Dysk/wolumin** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu| Nieobsługiwane | Usługa kopiowania woluminów w tle (VSS) działa tylko wtedy, gdy wolumin jest zapisywalny.
Woluminy offline| Nieobsługiwane |Usługa VSS działa tylko wtedy, gdy wolumin jest w trybie online.
Udział sieciowy| Nieobsługiwane |Wolumin musi być lokalny na serwerze.
Woluminy zablokowane przez funkcję BitLocker| Nieobsługiwane |Wolumin musi zostać odblokowany przed rozpoczęciem tworzenia kopii zapasowej.
Identyfikacja systemu plików| Nieobsługiwane |Obsługiwany jest tylko system plików NTFS.
Nośnik wymienny| Nieobsługiwane |Wszystkie źródła elementów kopii zapasowej muszą mieć *ustalony* stan.
Deduplikowane dyski | Obsługiwane | Azure Backup konwertuje deduplikowane dane na normalne dane. Optymalizuje, szyfruje, przechowuje i wysyła dane do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej offline

Azure Backup obsługuje umieszczanie *w trybie offline* w celu transferu danych początkowej kopii zapasowej na platformę Azure przy użyciu dysków. Ta pomoc techniczna jest przydatna, jeśli początkowa kopia zapasowa prawdopodobnie będzie znajdować się w zakresie od terabajtów (TBs). Kopia zapasowa offline jest obsługiwana dla:

- Bezpośrednie tworzenie kopii zapasowych plików i folderów na maszynach lokalnych, na których jest uruchomiony agent MARS.
- Tworzenie kopii zapasowych obciążeń i plików z serwera DPM lub serwera usługi MAB.

Kopii zapasowej offline nie można używać dla plików stanu systemu.

## <a name="support-for-data-restoration"></a>Obsługa przywracania danych

Za pomocą funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md) Azure Backup można przywrócić dane przed ich skopiowaniem do magazynu. Maszyna, której kopia zapasowa jest wykonywana, musi działać .NET Framework 4.5.2 lub nowszy.

Kopie zapasowe nie mogą zostać przywrócone na komputerze docelowym, na którym działa Starsza wersja systemu operacyjnego. Na przykład kopie zapasowe wykonane z komputera z systemem Windows 7 można przywrócić w systemie Windows 8 lub nowszym. Ale nie można przywrócić kopii zapasowej wykonanej z komputera z systemem Windows 8 na komputerze z systemem Windows 7.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze tworzenia kopii zapasowych korzystającej z agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Dowiedz się, co jest obsługiwane podczas [uruchamiania agenta Mars na serwera usługi MAB lub serwerze DPM](backup-support-matrix-mabs-dpm.md).
