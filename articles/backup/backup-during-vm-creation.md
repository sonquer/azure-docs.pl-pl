---
title: Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure
description: Opisuje sposób włączania tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej platformy Azure przy użyciu Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449913"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure

Użyj usługi Azure Backup, aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure. Kopie zapasowe maszyn wirtualnych są wykonywane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych, a punkty odzyskiwania są tworzone na podstawie kopii zapasowych. Punkty odzyskiwania są przechowywane w magazynach Recovery Services.

W tym artykule opisano sposób włączania tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej w Azure Portal.  

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Sprawdź](backup-support-matrix-iaas.md#supported-backup-actions) , które systemy operacyjne są obsługiwane w przypadku włączenia tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Jeśli jeszcze nie zalogowano się na koncie, zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Tworzenie maszyny wirtualnej z skonfigurowaną kopią zapasową

1. W Azure Portal kliknij pozycję **Utwórz zasób**.

2. W portalu Azure Marketplace kliknij pozycję **obliczenia**, a następnie wybierz obraz maszyny wirtualnej.

3. Skonfiguruj maszynę wirtualną zgodnie z instrukcjami dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) .

4. Na karcie **Zarządzanie** w obszarze **Włączanie kopii zapasowej**kliknij **pozycję włączone.**
5. Azure Backup kopie zapasowe w magazynie Recovery Services. Kliknij przycisk **Utwórz nowy** , jeśli nie masz istniejącego magazynu.
6. Zaakceptuj sugerowaną nazwę magazynu lub określ swoje własne.
7. Określ lub Utwórz grupę zasobów, w której będzie znajdować się magazyn. Magazyn grupy zasobów może się różnić od grupy zasobów maszyny wirtualnej.

    ![Włączanie kopii zapasowej maszyny wirtualnej](./media/backup-during-vm-creation/enable-backup.png)

8. Zaakceptuj domyślne zasady tworzenia kopii zapasowych lub zmodyfikuj ustawienia.
    - Zasady tworzenia kopii zapasowych określają, jak często mają być wykonywane migawki kopii zapasowej maszyny wirtualnej oraz jak długo mają być przechowywane kopie zapasowe.
    - Zasady domyślne tworzą kopię zapasową maszyny wirtualnej raz dziennie.
    - Możesz dostosować własne zasady tworzenia kopii zapasowej dla maszyny wirtualnej platformy Azure, aby tworzyć kopie zapasowe codziennie lub co tydzień.
    - [Dowiedz się więcej](backup-azure-vms-introduction.md#backup-and-restore-considerations) o zagadnieniach dotyczących kopii zapasowych dla maszyn wirtualnych platformy Azure
    - [Dowiedz się więcej](backup-instant-restore-capability.md) o funkcji natychmiastowego przywracania.

      ![Domyślne zasady kopii zapasowych](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup grupę zasobów dla Virtual Machines

Usługa Backup tworzy oddzielną grupę zasobów (RG), inną niż grupa zasobów maszyny wirtualnej, do przechowywania kolekcji punktów przywracania (RPC). Usługa RPC przechowuje natychmiastowe punkty odzyskiwania zarządzanych maszyn wirtualnych. Domyślny format nazewnictwa grupy zasobów utworzonej przez usługę kopii zapasowej to: `AzureBackupRG_<Geo>_<number>`. Na przykład: *AzureBackupRG_northeurope_1*. Teraz można dostosować nazwę grupy zasobów utworzoną przez Azure Backup.

Informacje, które należy zwrócić uwagę:

1. Możesz użyć domyślnej nazwy RG lub edytować ją zgodnie z wymaganiami firmy.
2. Wzorzec nazwy RG można podać jako dane wejściowe podczas tworzenia zasad kopii zapasowej maszyny wirtualnej. Nazwa RG powinna mieć następujący format: `<alpha-numeric string>* n <alpha-numeric string>`. element "n" jest zastępowany liczbą całkowitą (rozpoczynając od 1) i jest używany do skalowania w górę, jeśli pierwszy RG jest pełny. Jedna RG może mieć maksymalnie 600 wywołań RPC.
              ![wybrać nazwę podczas tworzenia zasad](./media/backup-during-vm-creation/create-policy.png)
3. Wzorzec powinien być zgodny z regułami nazewnictwa RG poniżej, a łączna długość nie powinna przekraczać maksymalnej dozwolonej długości nazwy RG.
    1. Nazwa grupy zasobów zezwala tylko na znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Nie mogą kończyć się kropką.
    2. Nazwy grup zasobów mogą zawierać do 74 znaków, w tym nazwę RG i sufiks.
4. Pierwszy `<alpha-numeric-string>` jest obowiązkowy, podczas gdy drugi po "n" jest opcjonalny. Ma to zastosowanie tylko w przypadku nadania niestandardowej nazwy. Jeśli nie wprowadzisz niczego w żadnej z pól tekstowych, zostanie użyta nazwa domyślna.
5. Nazwę RG można edytować, modyfikując zasady, jeśli są wymagane. Jeśli wzorzec nazwy zostanie zmieniony, w nowym RG zostanie utworzony nowy RPS pliku. Jednak stary RPS pliku nadal będzie znajdował się w starej RG i nie zostanie przeniesiony, ponieważ kolekcja RP nie obsługuje przenoszenia zasobów. Ostatecznie RPS pliku będzie odbierać elementy bezużyteczne w miarę wygasania punktów.
![zmienić nazwy podczas modyfikowania zasad](./media/backup-during-vm-creation/modify-policy.png)
6. Zaleca się, aby nie blokować grupy zasobów utworzonej do użycia przez usługę kopii zapasowej.

## <a name="start-a-backup-after-creating-the-vm"></a>Rozpocznij tworzenie kopii zapasowej po utworzeniu maszyny wirtualnej

Kopia zapasowa maszyny wirtualnej będzie uruchamiana zgodnie z zasadami tworzenia kopii zapasowych. Zaleca się jednak uruchomienie początkowej kopii zapasowej.

Po utworzeniu maszyny wirtualnej wykonaj następujące czynności:

1. We właściwościach maszyny wirtualnej kliknij pozycję **kopia zapasowa**. Stan maszyny wirtualnej to początkowa kopia zapasowa oczekująca na uruchomienie początkowej kopii zapasowej
2. Kliknij pozycję **Wykonaj kopię zapasową teraz** , aby uruchomić kopię zapasową na żądanie.

    ![Uruchamianie kopii zapasowej na żądanie](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Wdrażanie chronionej maszyny wirtualnej przy użyciu szablonu Menedżer zasobów

W poprzednich krokach opisano, jak za pomocą Azure Portal utworzyć maszynę wirtualną i chronić ją w magazynie Recovery Services. Aby szybko wdrożyć co najmniej jedną maszynę wirtualną i chronić ją w magazynie Recovery Services, zapoznaj się z szablonem [Wdrażanie maszyny wirtualnej z systemem Windows i Włączanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz chronioną maszynę wirtualną, Dowiedz się, jak zarządzać nimi i je przywracać.

- [Zarządzanie maszynami wirtualnymi i monitorowanie ich](backup-azure-manage-vms.md)
- [Przywracanie maszyny wirtualnej](backup-azure-arm-restore-vms.md)

Jeśli napotkasz jakiekolwiek problemy, [zapoznaj](backup-azure-vms-troubleshoot.md) się z przewodnikiem rozwiązywania problemów.
