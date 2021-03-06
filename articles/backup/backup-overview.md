---
title: Co to jest Azure Backup?
description: Zawiera omówienie usługi Azure Backup i sposobu, w jaki przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294307"
---
# <a name="what-is-the-azure-backup-service"></a>Co to jest usługa Azure Backup?

Usługa Azure Backup udostępnia proste, bezpieczne i ekonomiczne rozwiązania do wykonywania kopii zapasowych danych i odzyskiwania ich z chmury platformy Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Dla jakich danych mogę tworzyć kopię zapasową?

- **Lokalne** — tworzenie kopii zapasowych plików, folderów i stanu systemu przy użyciu [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md). Lub Użyj agenta programu DPM lub Azure Backup Server (serwera usługi MAB) do ochrony lokalnych maszyn wirtualnych ([funkcji Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) i oprogramowania [VMware](backup-azure-backup-server-vmware.md)) i innych [obciążeń lokalnych](backup-mabs-protection-matrix.md) .
- **Maszyny wirtualne platformy Azure** - [tworzenia kopii zapasowych całych maszyn wirtualnych z systemem Windows/Linux](backup-azure-vms-introduction.md) (przy użyciu rozszerzeń kopii zapasowej) lub tworzenia kopii zapasowych plików, folderów i stanu systemu przy użyciu [agenta Mars](backup-azure-manage-mars.md).
- **Azure Files udziały** - [tworzenia kopii zapasowych udziałów plików platformy Azure na koncie magazynu](backup-afs.md)
- **SQL Server na maszynach wirtualnych platformy azure** -  [tworzenia kopii zapasowych SQL Server baz danych działających na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md)
- **SAP HANA baz danych w maszynach wirtualnych platformy azure** - [kopii zapasowych SAP HANA baz danych uruchomionych na maszynach wirtualnych](backup-azure-sap-hana-database.md)

![Omówienie usługi Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Dlaczego warto używać usługi Azure Backup?

Usługa Azure Backup oferuje następujące kluczowe korzyści:

- **Odciążanie lokalnej kopii zapasowej**: Azure Backup oferuje proste rozwiązanie do tworzenia kopii zapasowych zasobów lokalnych w chmurze. Uzyskaj krótkoterminowe i długoterminowe kopie zapasowe bez konieczności wdrażania złożonych lokalnych rozwiązań do wykonywania kopii zapasowych.
- **Tworzenie kopii zapasowych maszyn wirtualnych usługi Azure IaaS**: Azure Backup zapewnia niezależne i izolowane kopie zapasowe, aby zapewnić ochronę przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w magazynie Recovery Services przy użyciu wbudowanego zarządzania punktami odzyskiwania. Konfiguracja i skalowalność są proste, kopie zapasowe są optymalizowane i można je łatwo przywrócić w razie potrzeby.
- **Łatwe skalowanie** — usługa Azure Backup używa dostępnej mocy i nieograniczonej skali chmury Azure do zapewniania wysokiej dostępności, bez narzutu na konserwację lub monitorowanie.
- **Pobieranie nieograniczonego transferu danych**: Azure Backup nie ogranicza ilości przesyłanych danych przychodzących lub wychodzących lub opłaty za transferowane dane.
  - Dane wychodzące to dane transferowane z magazynu usługi Recovery Services podczas operacji przywracania.
  - W przypadku wykonywania początkowej kopii zapasowej w trybie offline za pomocą usługi Azure Import/Export w celu zaimportowania dużych ilości danych jest koszt związany z danymi przychodzącymi.  [Dowiedz się więcej](backup-azure-backup-import-export.md).
- **Zachowaj bezpieczeństwo danych**: Azure Backup udostępnia rozwiązania do zabezpieczania danych [podczas ich przesyłania](backup-azure-security-feature.md) [i przechowywania](backup-azure-security-feature-cloud.md).
- **Scentralizowane monitorowanie i zarządzanie**: Azure Backup zapewnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie Recovery Services. Te możliwości są dostępne bez dodatkowej infrastruktury zarządzania. Możesz również zwiększyć skalę monitorowania i raportowania przy [użyciu Azure monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Pobieranie kopii zapasowych spójnych**na poziomie aplikacji: kopia zapasowa spójna na poziomie aplikacji oznacza, że punkt odzyskiwania ma wszystkie dane wymagane do przywrócenia kopii zapasowej. Azure Backup zapewnia kopie zapasowe spójne z aplikacją, co zapewnia, że dodatkowe poprawki nie są wymagane do przywrócenia danych. Przywracanie danych spójnych na poziomie aplikacji skraca czas przywracania, co pozwala szybko powrócić do stanu roboczego.
- **Przechowuj krótkoterminowe i długoterminowe dane**: możesz używać [magazynów Recovery Services](backup-azure-recovery-services-vault-overview.md) do krótkoterminowego i długoterminowego przechowywania danych.
- **Automatyczne zarządzanie magazynem** — hybrydowe środowiska często wymagają heterogenicznego magazynu — znajdującego się w części lokalnie, a w części w chmurze. W przypadku Azure Backup nie ma kosztu korzystania z lokalnych urządzeń magazynujących. Usługa Azure Backup automatycznie przydziela pojemność i zarządza magazynem kopii zapasowych, przy czym użytkownik płaci tylko za faktyczne użycie. Płacisz tylko za zużyty magazyn. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup) o cenach.
- **Wiele opcji magazynowania** — usługa Azure Backup oferuje dwa typy replikacji, aby zapewnić wysoką dostępność magazynu/danych.
  - [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych istnieją w tym samym regionie. Magazyn LRS to ekonomiczna opcja ochrony danych przed awariami sprzętu lokalnego.
  - [Magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md) jest ustawieniem domyślnym i zalecaną opcją replikacji. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych). GRS koszty więcej niż LRS, ale GRS zapewnia wyższy poziom trwałości danych, nawet jeśli wystąpi awaria regionalna.

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj](backup-architecture.md) architekturę i składniki w różnych scenariuszach wykonywania kopii zapasowych.
- [Sprawdź](backup-support-matrix.md) wymagania dotyczące pomocy technicznej i ograniczenia dotyczące kopii zapasowych oraz [Utwórz kopię zapasową maszyny wirtualnej platformy Azure](backup-support-matrix-iaas.md).
