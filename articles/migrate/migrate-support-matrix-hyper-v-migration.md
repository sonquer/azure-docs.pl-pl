---
title: Obsługa migracji funkcji Hyper-V w Azure Migrate
description: Dowiedz się więcej o obsłudze migracji funkcji Hyper-V za pomocą Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064465"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Macierz obsługi dla migracji funkcji Hyper-V

Ten artykuł zawiera podsumowanie ustawień i ograniczeń dotyczących migracji maszyn wirtualnych funkcji Hyper-V z [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceny maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure, zapoznaj się z [matrycą pomocy technicznej](migrate-support-matrix-hyper-v.md)dotyczącej oceny.

## <a name="migration-limitations"></a>Ograniczenia migracji

Można wybrać maksymalnie 10 maszyn wirtualnych na potrzeby replikacji. Jeśli chcesz migrować więcej maszyn, Replikuj w grupach o wartości 10.


## <a name="hyper-v-hosts"></a>Hosty funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. <br/>Na hostach funkcji Hyper-V należy zainstalować oprogramowanie do replikacji Azure Migrate (dostawca replikacji funkcji Hyper-V).|
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2. |
| **Dostęp do adresu URL** | Oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebować dostępu do tych adresów URL:<br/><br/> -login.microsoftonline.com: Kontrola dostępu i zarządzanie tożsamościami przy użyciu Active Directory.<br/><br/> -*. backup.windowsazure.com: transfer i koordynacja danych replikacji. Migrowanie adresów URL usługi.<br/><br/> -*. blob.core.windows.net: przekazywanie danych do kont magazynu.<br/><br/> -dc.services.visualstudio.com: Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.<br/><br/> -time.windows.com: sprawdza synchronizację czasu między systemem i czasem globalnym.
| **Dostęp do portu** |  Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej.

## <a name="hyper-v-vms"></a>Maszyny wirtualne funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |
| **Wymagane zmiany dotyczące platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Przed migracją musisz ręcznie wprowadzić zmiany. Odpowiednie artykuły zawierają instrukcje, jak to zrobić. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków. |
| **Rozruch z interfejsem UEFI**                  | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS. Na maszynie wirtualnej powinien działać system Windows Server 2012 lub nowszy. Dysk systemu operacyjnego powinien mieć maksymalnie pięć partycji lub mniej, a rozmiar dysku systemu operacyjnego musi być mniejszy niż 300 GB.
  |
| **Rozmiar dysku**                  | 2 TB dla dysku systemu operacyjnego, 4 TB dla dysków z danymi.
| **Numer dysku** | Maksymalnie 16 dysków na maszynę wirtualną.
| **Zaszyfrowane dyski/woluminy**    | Migracja nie jest obsługiwana. |
| **RDM/przekazywanie dysków**      | Migracja nie jest obsługiwana. |
| **Dysk udostępniony** | Maszyny wirtualne korzystające z dysków udostępnionych nie są obsługiwane na potrzeby migracji.
| **NFS**                        | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane. |
| **MAGAZYNU**                      | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane na potrzeby migracji.
| **Dysk docelowy**                | Można przeprowadzić migrację do maszyn wirtualnych platformy Azure tylko z dyskami zarządzanymi. |
| **If** | Nieobsługiwane.
| **Tworzenie zespołu kart interfejsu sieciowego** | Nieobsługiwane.
| **Azure Site Recovery** | Nie można przeprowadzić replikacji przy użyciu migracji serwera Azure Migrate, jeśli maszyna wirtualna ma włączoną replikację z Azure Site Recovery.
| **Porty** | Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 16 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku danych | Do 4 095 GB | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwane są wiele kart. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
BitLocker | Nieobsługiwane. | Aby włączyć replikację dla maszyny, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w Site Recovery.
Połącz po migracji — Windows | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> -Przed migracją włącza protokół RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> W przypadku dostępu do sieci VPN typu lokacja-lokacja Włącz protokół RDP i Zezwalaj na używanie protokołu RDP w **zaporze systemu Windows** -> **dozwolonych aplikacji i funkcji** dla sieci **,** w których są dozwolone. Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md). |
Połącz po migracji — system Linux | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure po migracji przy użyciu protokołu SSH:<br/> Przed migracją na maszynie lokalnej Sprawdź, czy usługa Secure Shell jest ustawiona do uruchamiania, oraz czy reguły zapory zezwalają na połączenie SSH.<br/> Po przejściu w tryb failover na maszynie wirtualnej platformy Azure Zezwól na połączenia przychodzące do portu SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover oraz dla podsieci platformy Azure, do której jest podłączona. Dodatkowo Dodaj publiczny adres IP dla maszyny wirtualnej. |  

## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) do migracji.
