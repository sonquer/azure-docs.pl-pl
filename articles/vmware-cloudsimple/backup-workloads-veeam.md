---
title: Azure VMware Solutions (Automatyczna synchronizacja) — tworzenie kopii zapasowych maszyn wirtualnych obciążeń w chmurze prywatnej w programie Veeam
description: Opis sposobu tworzenia kopii zapasowych maszyn wirtualnych, które są uruchomione w opartej na platformie Azure chmurze prywatnej, za pomocą Veeam B & R 9,5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d8dc822ec07bdf061121b97384d0e2f9f239d6e2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025133"
---
# <a name="back-up-workload-vms-on-avs-private-cloud-using-veeam-br"></a>Tworzenie kopii zapasowych maszyn wirtualnych obciążeń w chmurze prywatnej o automatycznej synchronizacji przy użyciu Veeam B & R

W tym przewodniku opisano, jak można utworzyć kopię zapasową maszyn wirtualnych działających w chmurze prywatnej "Automatyczna synchronizacja" w oparciu o platformę Azure za pomocą Veeam B & R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Informacje o rozwiązaniu do tworzenia kopii zapasowych i odzyskiwania Veeam

Rozwiązanie Veeam obejmuje następujące składniki.

**Serwer kopii zapasowej**

Serwer kopii zapasowej jest serwerem Windows Server (VM), który służy jako centrum sterowania dla Veeam i wykonuje następujące funkcje: 

* Koordynuje zadania tworzenia kopii zapasowej, replikacji, weryfikacji odzyskiwania i przywracania
* Kontroluje planowanie zadań i przydzielanie zasobów
* Umożliwia konfigurowanie składników infrastruktury kopii zapasowych i zarządzanie nimi oraz określanie ustawień globalnych infrastruktury kopii zapasowych

**Serwery proxy**

Serwery proxy są zainstalowane między serwerem kopii zapasowej a innymi składnikami infrastruktury kopii zapasowych. Zarządzają następującymi funkcjami:

* Pobieranie danych maszyny wirtualnej z magazynu produkcyjnego
* Kompresja
* Deduplikacja
* Szyfrowanie
* Przekazywanie danych do repozytorium kopii zapasowych

**Repozytorium kopii zapasowych**

Repozytorium kopii zapasowych to lokalizacja przechowywania, w której Veeam przechowuje pliki kopii zapasowych, kopie maszyn wirtualnych i metadane dla replikowanych maszyn wirtualnych. Repozytorium może być serwerem z systemem Windows lub Linux z dyskami lokalnymi (lub zainstalowanymi NFS/SMB) lub urządzeniem deduplikacji magazynu sprzętowego.

### <a name="veeam-deployment-scenarios"></a>Scenariusze wdrażania Veeam
Możesz wykorzystać platformę Azure, aby zapewnić repozytorium kopii zapasowych i miejsce docelowe magazynu w celu długotrwałego tworzenia kopii zapasowych i archiwizowania. Wszystkie kopie zapasowe ruchu sieciowego między maszynami wirtualnymi w chmurze prywatnej automatycznej synchronizacji i repozytorium kopii zapasowych na platformie Azure podróżują za pośrednictwem linku o dużej przepustowości i o małym opóźnieniu. Ruch związany z replikacją w różnych regionach jest przenoszony przez wewnętrzną sieć platformy Azure do planowania, co obniża koszty związane z przepustowością użytkowników.

**Podstawowe wdrożenie**

Aby utworzyć kopię zapasową w środowiskach o pojemności poniżej 30 TB, automatyczna synchronizacja zaleca następujące konfiguracje:

* Serwer kopii zapasowej Veeam i serwer proxy zainstalowane na tej samej maszynie wirtualnej w chmurze prywatnej automatycznej synchronizacji.
* Główne repozytorium kopii zapasowej oparte na systemie Linux na platformie Azure skonfigurowane jako element docelowy dla zadań tworzenia kopii zapasowych.
* `azcopy` używany do kopiowania danych z głównego repozytorium kopii zapasowej do kontenera obiektów blob platformy Azure, który jest replikowany do innego regionu.

![Podstawowe scenariusze wdrażania](media/veeam-basicdeployment.png)

**Wdrożenie zaawansowane**

Aby utworzyć kopię zapasową w środowiskach z więcej niż 30 TB, automatyczna synchronizacja zaleca następujące konfiguracje:

* Jeden serwer proxy na węzeł w klastrze sieci vSAN, zgodnie z zaleceniami Veeam.
* Główne repozytorium kopii zapasowej oparte na systemie Windows w chmurze prywatnej automatycznej synchronizacji umożliwia szybkie przywracanie danych z pięciu dni.
* Repozytorium kopii zapasowych systemu Linux na platformie Azure jako element docelowy dla zadań kopii zapasowych w celu dłuższego okresu przechowywania. To repozytorium należy skonfigurować jako repozytorium kopii zapasowych skalowalne w poziomie.
* `azcopy` używany do kopiowania danych z głównego repozytorium kopii zapasowej do kontenera obiektów blob platformy Azure, który jest replikowany do innego regionu.

![Podstawowe scenariusze wdrażania](media/veeam-advanceddeployment.png)

Na powyższym rysunku Zwróć uwagę na to, że serwer proxy kopii zapasowej jest maszyną wirtualną z dostępem do dysku maszyny wirtualnej z możliwością dynamicznego dodawania do obciążeń w magazynie danych sieci vSAN. Veeam używa trybu transportowego proxy usługi Kopia zapasowa urządzenia wirtualnego dla sieci vSAN.

## <a name="requirements-for-veeam-solution-on-avs"></a>Wymagania dotyczące rozwiązania Veeam na potrzeby automatycznej synchronizacji

Rozwiązanie Veeam wymaga wykonania następujących czynności:

* Udostępnianie własnych licencji Veeam.
* Wdrażaj Veeam i zarządzaj nimi, aby tworzyć kopie zapasowe obciążeń uruchomionych w chmurze prywatnej automatycznej synchronizacji.

To rozwiązanie zapewnia pełną kontrolę nad narzędziem kopia zapasowa Veeam i oferuje możliwość korzystania z interfejsu natywnego Veeam lub wtyczki Veeam vCenter do zarządzania zadaniami tworzenia kopii zapasowych maszyn wirtualnych.

Jeśli jesteś istniejącym użytkownikiem Veeam, możesz pominąć sekcję dotyczącą składników rozwiązania Veeam i bezpośrednio przejść do [scenariuszy wdrażania Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-avs-private-cloud"></a>Zainstaluj i skonfiguruj kopie zapasowe Veeam w chmurze prywatnej automatycznej synchronizacji

W poniższych sekcjach opisano sposób instalowania i konfigurowania rozwiązania do tworzenia kopii zapasowych Veeam w chmurze prywatnej automatycznej synchronizacji.

Proces wdrażania obejmuje następujące kroki:

1. [Interfejs użytkownika vCenter: Konfigurowanie usług infrastruktury w chmurze prywatnej automatycznej synchronizacji](#vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud)
2. [Portal automatycznej konfiguracji: Konfigurowanie automatycznej synchronizacji sieci prywatnej chmury dla Veeam](#avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam)
3. [Portal automatycznej synchronizacji: eskalacja uprawnień](#avs-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure Portal: łączenie sieci wirtualnej z chmurą prywatną do automatycznej synchronizacji](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
5. [Azure Portal: Tworzenie repozytorium kopii zapasowych na platformie Azure](#azure-portal-connect-your-virtual-network-to-the-avs-private-cloud)
6. [Azure Portal: Konfigurowanie magazynu obiektów blob platformy Azure na potrzeby długoterminowego przechowywania danych](#configure-azure-blob-storage-for-long-term-data-retention)
7. [Interfejs użytkownika vCenter narzędzia do automatycznej synchronizacji w chmurze prywatnej: Install Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br)
8. [Konsola Veeam: Konfigurowanie kopii zapasowej Veeam & oprogramowanie do odzyskiwania](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portal automatycznej synchronizacji: Konfigurowanie uprawnień dostępu Veeam i cofanie eskalacji](#avs-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem wdrażania Veeam należy wykonać następujące czynności:

* Posiadana subskrypcja platformy Azure
* Wstępnie utworzona grupa zasobów platformy Azure
* Sieć wirtualna platformy Azure w ramach subskrypcji
* Konto usługi Azure Storage
* [Chmura prywatna do automatycznej synchronizacji](create-private-cloud.md) utworzona przy użyciu portalu automatycznej synchronizacji.  

Podczas fazy implementacji są niezbędne następujące elementy:

* Szablony programu VMware dla systemu Windows do zainstalowania Veeam (na przykład obraz bitowy Windows Server 2012 R2-64)
* Jedna dostępna sieć VLAN zidentyfikowana dla sieci kopii zapasowej
* CIDR podsieci, która ma zostać przypisana do sieci kopii zapasowej
* Veeam 9,5 U3 z instalowalnym nośnikiem (ISO) przekazanym do magazynu danych sieci vSAN w chmurze prywatnej automatycznej synchronizacji

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-avs-private-cloud"></a>Interfejs użytkownika vCenter: Konfigurowanie usług infrastruktury w chmurze prywatnej automatycznej synchronizacji

Skonfiguruj usługi infrastruktury w chmurze prywatnej automatycznej synchronizacji, aby ułatwić zarządzanie obciążeniami i narzędziami.

* Można dodać zewnętrznego dostawcę tożsamości, zgodnie z opisem w temacie [Konfigurowanie źródeł tożsamości programu vCenter do użycia Active Directory](set-vcenter-identity.md) , jeśli którykolwiek z następujących warunków ma zastosowanie:
  * Chcesz identyfikować użytkowników z lokalnej Active Directory (AD) w chmurze prywatnej automatycznej synchronizacji.
  * Chcesz skonfigurować usługi AD w chmurze prywatnej automatycznej synchronizacji dla wszystkich użytkowników.
  * Chcesz korzystać z usługi Azure AD.
* Aby zapewnić wyszukiwanie adresów IP, zarządzanie adresami IP oraz usługi rozpoznawania nazw dla obciążeń w chmurze prywatnej automatycznej synchronizacji, należy skonfigurować serwer DHCP i DNS zgodnie z opisem w temacie [Konfigurowanie aplikacji DNS i DHCP oraz obciążeń w chmurze prywatnej automatycznej synchronizacji](dns-dhcp-setup.md).

### <a name="avs-private-cloud-set-up-avs-private-cloud-networking-for-veeam"></a>Automatyczna synchronizacja chmury prywatnej: Konfigurowanie automatycznej synchronizacji sieci prywatnej chmury dla Veeam

Dostęp do portalu automatycznej synchronizacji w celu skonfigurowania automatycznej synchronizacji sieci prywatnej chmury dla rozwiązania Veeam.

Utwórz sieć VLAN dla sieci kopii zapasowej i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md).

Utwórz reguły zapory między podsiecią zarządzania i siecią kopii zapasowej, aby zezwolić na ruch sieciowy na portach używanych przez Veeam. Zobacz temat Veeam [używane porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Instrukcje dotyczące tworzenia reguły zapory znajdują się w temacie [Konfigurowanie tabel i reguł zapory](firewall.md).

Poniższa tabela zawiera listę portów.

| Ikona | Opis | Ikona | Opis |
| ------------ | ------------- | ------------ | ------------- |
| Backup Server  | vCenter  | HTTPS/TCP  | 443 |
| Backup Server <br> *Wymagane do wdrożenia składników Veeam Backup & Replication* | Serwer proxy kopii zapasowej  | TCP/UDP  | 135, 137 do 139 i 445 |
    | Backup Server   | DNS  | UDP  | 53  | 
    | Backup Server   | Serwer powiadomień Veeam Update  | TCP  | 80  | 
    | Backup Server   | Veeam serwer aktualizacji licencji  | TCP  | 443  | 
    | Serwer proxy kopii zapasowej   | vCenter |   |   | 
    | Serwer proxy kopii zapasowej  | Repozytorium kopii zapasowych systemu Linux   | TCP  | 22  | 
    | Serwer proxy kopii zapasowej  | Repozytorium kopii zapasowej systemu Windows  | TCP  | 49152 – 65535   | 
    | Repozytorium kopii zapasowych  | Serwer proxy kopii zapasowej  | TCP  | 2500 – 5000  | 
    | Źródłowe repozytorium kopii zapasowej<br> *Używane na potrzeby zadań kopii zapasowej*  | Docelowe repozytorium kopii zapasowych  | TCP  | 2500 – 5000  | 

Utwórz reguły zapory między podsiecią obciążenia i siecią kopii zapasowej zgodnie z opisem w temacie [Konfigurowanie tabel i reguł zapory](firewall.md). W przypadku tworzenia kopii zapasowych i przywracania z uwzględnieniem aplikacji należy otworzyć [dodatkowe porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) na maszynach wirtualnych obciążenia, które obsługują określone aplikacje.

Domyślnie automatyczna synchronizacja zapewnia link 1Gbps ExpressRoute. W przypadku większych rozmiarów środowiska może być wymagane łącze o większej przepustowości. Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać więcej informacji o większej przepustowości.

Aby kontynuować instalację, wymagany jest identyfikator URI klucza autoryzacji i obwodu równorzędnego oraz dostęp do subskrypcji platformy Azure. Te informacje są dostępne na stronie połączenie Virtual Network w portalu automatycznej synchronizacji. Aby uzyskać instrukcje, zobacz temat [Uzyskiwanie informacji o komunikacji równorzędnej dla usługi Azure Virtual Network w celu nawiązania połączenia automatycznej](virtual-network-connection.md)wersji. Jeśli masz problemy z uzyskaniem informacji, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="avs-private-cloud-escalate-privileges-for-cloudowner"></a>Automatyczna synchronizacja chmury prywatnej: eskalacja uprawnień dla **cloudowner**

Domyślny użytkownik "cloudowner" nie ma wystarczających uprawnień w chmurze prywatnej chmury programu vCenter do instalacji VEEAM, więc uprawnienia użytkownika vCenter muszą zostać przeeskalacjne. Aby uzyskać więcej informacji, zobacz temat [Eskalacja uprawnień](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-avs-private-cloud"></a>Azure Portal: łączenie sieci wirtualnej z chmurą prywatną do automatycznej synchronizacji

Połącz sieć wirtualną z chmurą prywatną do automatycznej synchronizacji, postępując zgodnie z instrukcjami w temacie [Azure Virtual Network Connection przy użyciu ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: Tworzenie maszyny wirtualnej repozytorium kopii zapasowej

1. Utwórz standardową maszynę wirtualną D2 v3 z (2 procesorów wirtualnych vCPU i 8 GB pamięci).
2. Wybierz obraz oparty na CentOS 7,4.
3. Skonfiguruj grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dla maszyny wirtualnej. Sprawdź, czy maszyna wirtualna nie ma publicznego adresu IP i nie jest dostępna z publicznego Internetu.
4. Utwórz konto użytkownika oparte na nazwie użytkownika i hasła dla nowej maszyny wirtualnej. Aby uzyskać instrukcje, zobacz temat [Tworzenie maszyny wirtualnej z systemem Linux w Azure Portal](../virtual-machines/linux/quick-create-portal.md).
5. Utwórz dysk twardy 1x512 GiB Standard i dołącz go do maszyny wirtualnej repozytorium. Aby uzyskać instrukcje, zobacz [jak dołączyć dysk danych zarządzanych do maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Utwórz wolumin XFS na dysku zarządzanym](https://www.digitalocean.com/docs/volumes/how-to/). Zaloguj się do maszyny wirtualnej przy użyciu wyżej wspomnianych poświadczeń. Wykonaj następujący skrypt, aby utworzyć wolumin logiczny, dodać do niego dysk, utworzyć [partycję](https://www.digitalocean.com/docs/volumes/how-to/partition/) systemu plików XFS i [zainstalować](https://www.digitalocean.com/docs/volumes/how-to/mount/) partycję pod ścieżką/backup1.

    Przykładowy skrypt:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Udostępnienie/backup1 jako punktu instalacji systemu plików NFS na serwerze kopii zapasowej Veeam, który działa w chmurze prywatnej automatycznej synchronizacji. Aby uzyskać instrukcje, zobacz artykuł dotyczący oceanu cyfrowego, [jak skonfigurować instalację systemu plików NFS w systemie CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Użyj tej nazwy udziału NFS podczas konfigurowania repozytorium kopii zapasowych na serwerze kopii zapasowych Veeam.

8. Skonfiguruj reguły filtrowania w sieciowej grupy zabezpieczeń dla maszyny wirtualnej repozytorium kopii zapasowych, aby jawnie zezwolić na cały ruch sieciowy do i z maszyny wirtualnej.

> [!NOTE]
> Veeam Backup & replikacja używa protokołu SSH do komunikowania się z repozytoriami kopii zapasowych systemu Linux i wymaga narzędzia SCP dla repozytoriów systemu Linux. Sprawdź, czy demon SSH został prawidłowo skonfigurowany i czy punkt połączenia usługi jest dostępny na hoście z systemem Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurowanie magazynu obiektów blob platformy Azure na potrzeby długoterminowego przechowywania danych

1. Utwórz konto magazynu ogólnego przeznaczenia (GPv2) typu standardowego i kontenera obiektów blob, zgodnie z opisem w Wprowadzenie wideo firmy Microsoft [za pomocą usługi Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Utwórz kontener usługi Azure Storage, zgodnie z opisem w temacie Tworzenie odwołania do [kontenera](https://docs.microsoft.com/rest/api/storageservices/create-container) .
2. Pobierz narzędzie wiersza polecenia `azcopy` dla systemu Linux firmy Microsoft. W CentOS 7,5 można użyć następujących poleceń w powłoce bash.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Użyj `azcopy` polecenia, aby skopiować pliki kopii zapasowej do i z kontenera obiektów BLOB. Zobacz [transfer danych za pomocą AzCopy w systemie Linux](../storage/common/storage-use-azcopy-linux.md) , aby uzyskać szczegółowe polecenia.

### <a name="vcenter-console-of-avs-private-cloud-install-veeam-br"></a>Konsola programu vCenter do automatycznej synchronizacji w chmurze prywatnej: Install Veeam B & R

Uzyskaj dostęp do programu vCenter z chmury prywatnej automatycznej synchronizacji, aby utworzyć konto usługi Veeam, zainstaluj Veeam B & R 9,5 i skonfiguruj Veeam za pomocą konta usługi.

1. Utwórz nową rolę o nazwie "Veeam Backup role" i przypisz do niej wymagane uprawnienia zgodnie z zaleceniami Veeam. Aby uzyskać szczegółowe informacje, zobacz temat Veeam [wymagane uprawnienia](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Utwórz nową grupę "Veeam grupy użytkowników" w programie vCenter i przypisz ją do roli "Veeam Backup".
3. Utwórz nowego użytkownika "konto usługi Veeam" i dodaj go do grupy użytkowników "Veeam".

    ![Tworzenie konta usługi Veeam](media/veeam-vcenter01.png)

4. Utwórz grupę portów rozproszonych w programie vCenter przy użyciu kopii zapasowej sieci VLAN. Aby uzyskać szczegółowe informacje, zobacz film wideo VMware [tworzenia rozproszonej grupy portów w kliencie sieci Web vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Utwórz maszyny wirtualne dla serwerów kopii zapasowych Veeam i proxy w programie vCenter zgodnie z [wymaganiami systemu Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Możesz użyć systemu Windows 2012 R2 lub Linux. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące korzystania z repozytoriów kopii zapasowych systemu Linux](https://www.veeam.com/kb2216).
6. Zainstaluj instalowalny Veeam ISO jako urządzenie CDROM na maszynie wirtualnej serwera kopii zapasowych Veeam.
7. Przy użyciu sesji protokołu RDP z maszyną z systemem Windows 2012 R2 (obiektem docelowym instalacji Veeam) [Zainstaluj Veeam B & R 9,5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) na maszynie wirtualnej z systemem Windows 2012 R2.
8. Znajdź wewnętrzny adres IP maszyny wirtualnej serwera kopii zapasowej Veeam i skonfiguruj adres IP jako statyczny na serwerze DHCP. Dokładne kroki wymagane do wykonania tego procesu zależą od serwera DHCP. Na przykład w artykule dotyczącego <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statycznego mapowania protokołu DHCP</a> w temacie opisano sposób KONFIGUROWANIA serwera DHCP przy użyciu routera PFSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Konsola Veeam: Instalowanie oprogramowania do tworzenia kopii zapasowych i odzyskiwania programu Veeam

Za pomocą konsoli Veeam Skonfiguruj oprogramowanie do tworzenia kopii zapasowych i odzyskiwania oprogramowania Veeam. Aby uzyskać szczegółowe informacje, zobacz [Veeam Backup & Replication V9-Installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Dodaj VMware vSphere jako środowisko serwera zarządzanego. Po wyświetleniu monitu podaj poświadczenia konta usługi Veeam, które zostało utworzone na początku [konsoli programu vCenter w ramach automatycznej synchronizacji w chmurze prywatnej: Install Veeam B & R](#vcenter-console-of-avs-private-cloud-install-veeam-br).

    * Użyj domyślnych ustawień kontroli obciążenia i domyślnych ustawień zaawansowanych.
    * Ustaw lokalizację serwera instalacji jako serwer zapasowy.
    * Zmień lokalizację kopii zapasowej konfiguracji serwera Veeam na repozytorium zdalne.

2. Dodaj serwer z systemem Linux na platformie Azure jako repozytorium kopii zapasowych.

    * Użyj domyślnych ustawień kontroli obciążenia i ustawień zaawansowanych. 
    * Ustaw lokalizację serwera instalacji jako serwer zapasowy.
    * Zmień lokalizację kopii zapasowej konfiguracji serwera Veeam na repozytorium zdalne.

3. Włącz szyfrowanie kopii zapasowej konfiguracji przy użyciu **ustawień Narzędzia główne > Konfiguracja kopii zapasowej**.

4. Dodaj maszynę wirtualną z systemem Windows Server jako serwer proxy dla środowiska VMware. Przy użyciu "reguł ruchu" dla serwera proxy Szyfruj dane kopii zapasowej za pośrednictwem sieci.

5. Skonfiguruj zadania tworzenia kopii zapasowej.
    * Aby skonfigurować zadania tworzenia kopii zapasowej, postępuj zgodnie z instrukcjami w temacie [Tworzenie zadania tworzenia kopii zapasowej](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Włącz szyfrowanie plików kopii zapasowych w obszarze **Ustawienia zaawansowane > magazyn**.

6. Skonfiguruj zadania kopii zapasowej.

    * Aby skonfigurować zadania kopii zapasowej, postępuj zgodnie z instrukcjami wyświetlanymi w obszarze wideo [Tworzenie kopii zapasowej zadania](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Włącz szyfrowanie plików kopii zapasowych w obszarze **Ustawienia zaawansowane > magazyn**.

### <a name="avs-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portal automatycznej synchronizacji: Konfigurowanie uprawnień dostępu Veeam i cofanie eskalacji
Utwórz publiczny adres IP dla Veeam kopii zapasowych i serwera odzyskiwania. Aby uzyskać instrukcje, zobacz [przydzielanie publicznych adresów IP](public-ips.md).

Utwórz regułę zapory za pomocą programu, aby umożliwić serwerowi Veeam kopii zapasowej Tworzenie połączenia wychodzącego do witryny internetowej Veeam w celu pobierania aktualizacji/poprawek na porcie TCP 80. Aby uzyskać instrukcje, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

Aby cofnąć eskalację uprawnień, zobacz Cofanie [eskalacji uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Informacje

### <a name="avs-references"></a>Informacje o automatycznej synchronizacji

* [Utwórz chmurę prywatną do automatycznej synchronizacji](create-private-cloud.md)
* [Tworzenie sieci VLAN/podsieci i zarządzanie nimi](create-vlan-subnet.md)
* [Źródła tożsamości vCenter](set-vcenter-identity.md)
* [Obciążenie usługi DNS i DHCP](dns-dhcp-setup.md)
* [Eskalacja uprawnień](escalate-privileges.md)
* [Konfigurowanie tabel i reguł zapory](firewall.md)
* [Automatyczna synchronizacja uprawnień chmury prywatnej](learn-private-cloud-permissions.md)
* [Przydziel publiczne adresy IP](public-ips.md)

### <a name="veeam-references"></a>Odwołania Veeam

* [Używane porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Wymagane uprawnienia](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Wymagania systemowe](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalowanie & kopii zapasowej Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Wymagane moduły i uprawnienia dla obsługi wielooperacyjnego FLR i repozytorium dla systemu Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & replikacja V9 — instalacja i wdrażanie — wideo](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam V9 Tworzenie zadania tworzenia kopii zapasowej — wideo](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam V9 tworzenia zadania kopii zapasowej — wideo](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Odwołania platformy Azure

* [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z inną subskrypcją obwodu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Utwórz maszynę wirtualną z systemem Linux w Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Jak dołączyć dysk danych zarządzanych do maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Wprowadzenie z usługą Azure Storage — wideo](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Utwórz kontener](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Transferowanie danych za pomocą narzędzia AzCopy w systemie Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Odwołania VMware

* [Tworzenie rozproszonej grupy portów w programie vSphere Web Client — wideo](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Inne odwołania

* [Tworzenie woluminu XFS na dysku zarządzanym — RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Jak skonfigurować instalację systemu plików NFS w systemie CentOS 7 — HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurowanie serwera DHCP-bramy](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
