---
title: Zarządzanie serwerami VMware vCenter w Azure Site Recovery
description: W tym artykule opisano, jak dodawać i zarządzać programem VMware vCenter na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: ba5f31049b599cd55a4a9a4261080c1672d336b1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495352"
---
# <a name="manage-vmware-vcenter-server"></a>Zarządzanie programem VMware vCenter Server

W tym artykule podsumowano akcje zarządzania na VMware vCenter Server w [Azure Site Recovery](site-recovery-overview.md). 

## <a name="verify-prerequisites-for-vcenter-server"></a>Sprawdź wymagania wstępne dotyczące vCenter Server

Wymagania wstępne dotyczące serwerów vCenter i maszyn wirtualnych podczas odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure znajdują się w [macierzy obsługi](vmware-physical-azure-support-matrix.md#replicated-machines).


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta do automatycznego odnajdywania

Po skonfigurowaniu odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware Site Recovery musi mieć dostęp do hosta vCenter Server/vSphere, aby serwer przetwarzania Site Recovery mógł automatycznie odnajdywać maszyny wirtualne i w razie potrzeby przełączać je w tryb failover. Domyślnie serwer przetwarzania działa na serwerze konfiguracji Site Recovery. Dodaj konto serwera konfiguracji, aby nawiązać połączenie z hostem vCenter Server/vSphere w następujący sposób:

1. Zaloguj się do komputera serwera konfiguracji.
2. Otwórz narzędzie serwera konfiguracji (cspsconfigtool. exe) przy użyciu skrótu na pulpicie.
3. Na karcie **Zarządzanie kontem** kliknij pozycję **Dodaj konto**. 

   ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
4. Podaj szczegóły konta, a następnie kliknij przycisk **OK** , aby go dodać.  Konto powinno mieć uprawnienia podsumowane w poniższej tabeli. 

Synchronizacja informacji o koncie z Site Recovery trwa około 15 minut.

### <a name="account-permissions"></a>Uprawnienia konta

|**Zadanie podrzędne** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Odnajdywanie/migracja maszyny wirtualnej (bez powrotu po awarii)** | Co najmniej konto użytkownika tylko do odczytu. | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).|
|**Replikacja/tryb failover** | Co najmniej konto użytkownika tylko do odczytu. | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).<br/><br/> Przydatne do migracji, ale nie pełna replikacja, tryb failover i powrót po awarii.|
|**Replikacja/tryb failover/powrót po awarii** | Sugerujemy utworzenie roli (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisanie roli do użytkownika lub grupy programu VMware. | Obiekt centrum danych — > propagowania do obiektu podrzędnego, rola = AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).|


## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware do magazynu

Po skonfigurowaniu odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware należy dodać hosta vCenter Server/vSphere, na którym są odnajdywane maszyny wirtualne do magazynu Site Recovery, w następujący sposób:

1. W obszarze magazyn > **Site Recovery infrastruktura** > **serwery konfiguracji**, Otwórz serwer konfiguracji.
2. Na stronie **szczegółów** kliknij przycisk **vCenter**.
3. W obszarze **Dodawanie programu vCenter**Określ przyjazną nazwę hosta vSphere lub serwera vCenter.
4. Określ adres IP lub nazwę FQDN serwera.
5. Pozostaw port ustawiony na 443, chyba że Twoje serwery VMware są skonfigurowane do nasłuchiwania żądań na innym porcie.
6. Wybierz konto używane do nawiązania połączenia z serwerem VMware vCenter lub vSphere ESXi. Następnie kliknij przycisk **OK**.



## <a name="modify-credentials"></a>Modyfikuj poświadczenia

W razie potrzeby można zmodyfikować poświadczenia używane do nawiązywania połączenia z hostem vCenter Server/vSphere w następujący sposób:

1. Zaloguj się do konfiguracji.
2. Otwórz narzędzie serwera konfiguracji (cspsconfigtool. exe) przy użyciu skrótu na pulpicie.
2. Kliknij pozycję **Dodaj konto** na karcie **Zarządzanie kontem** .

   ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
   
3. Podaj szczegóły nowego konta, a następnie kliknij przycisk **OK**. Konto wymaga uprawnień wymienionych [powyżej](#account-permissions).
4. W magazynie > **Site Recovery infrastruktury** > **serwery konfiguracji**, Otwórz serwer konfiguracji.
5. W obszarze **szczegóły**kliknij przycisk **Odśwież serwer**.
6. Po zakończeniu zadania odświeżania serwera wybierz vCenter Server.
7. W obszarze **Podsumowanie**wybierz nowo dodane konto w **centrum serwer/vSphere konto hosta**, a następnie kliknij przycisk **Zapisz**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Usuwanie vCenter Server 

1. W magazynie > **Site Recovery infrastruktury** > **serwery konfiguracji**, Otwórz serwer konfiguracji.
2. Na stronie **szczegóły** wybierz serwer vCenter.
3. Kliknij przycisk **Usuń** .

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modyfikowanie adresu IP i portu

Można zmodyfikować adres IP vCenter Server lub porty używane do komunikacji między serwerem i Site Recovery. Domyślnie Site Recovery uzyskuje dostęp do informacji o hoście vCenter Server/vSphere za pomocą portu 443.

1. W magazynie > **Site Recovery infrastruktury** > **serwery konfiguracji**, kliknij serwer konfiguracji, do którego zostanie dodany vCenter Server.
2. W obszarze **serwery vCenter**kliknij vCenter Server, które chcesz zmodyfikować.
5. W obszarze **Podsumowanie**zaktualizuj adres IP i port, a następnie Zapisz zmiany.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Aby zmiany zaczęły obowiązywać, poczekaj 15 minut lub [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).


## <a name="migrate-all-vms-to-a-new-server"></a>Migruj wszystkie maszyny wirtualne na nowy serwer

Jeśli chcesz zmigrować wszystkie maszyny wirtualne, aby użyć nowej vCenter Server, wystarczy zaktualizować adres IP przypisany do vCenter Server. Nie dodawaj kolejnego konta VMware, ponieważ może to prowadzić do duplikowania wpisów. Zaktualizuj adres w następujący sposób:

1. W magazynie > **Site Recovery infrastruktury** > **serwery konfiguracji**Lick na serwerze konfiguracji, do którego zostanie dodany vCenter Server.
2. W sekcji **serwery vCenter** kliknij vCenter Server, z których chcesz przeprowadzić migrację.
5. W obszarze **Podsumowanie**zaktualizuj adres IP do nowego vCenter Server i Zapisz zmiany.
6. Gdy tylko adres IP zostanie zaktualizowany, Site Recovery rozpocznie otrzymywanie informacji o odnajdywaniu maszyn wirtualnych z nowego vCenter Server. Nie ma to wpływu na bieżące działania związane z replikacją.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrowanie kilku maszyn wirtualnych na nowy serwer

Jeśli chcesz tylko migrować kilka z replikowanych maszyn wirtualnych do nowego serwera vCenter, wykonaj następujące czynności:

1. [Dodaj](#add-vmware-server-to-the-vault) nowy vCenter Server do serwera konfiguracji.
2. [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla maszyn wirtualnych, które zostaną przeniesione na nowy serwer.
3. W programie VMware dokonaj migracji maszyn wirtualnych do nowego vCenter Server. 
4. [Włącz ponownie replikację](vmware-azure-tutorial.md#enable-replication) zmigrowanych maszyn wirtualnych, wybierając nowe vCenter Server.


## <a name="migrate-most-vms-to-a-new-server"></a>Migruj większość maszyn wirtualnych na nowy serwer
 Jeśli liczba maszyn wirtualnych, które mają zostać poddane migracji do nowego vCenter Server, jest większa niż liczba maszyn wirtualnych, które pozostaną w oryginalnym vCenter Server, wykonaj następujące czynności:

1. [Zaktualizuj adres IP](#modify-the-ip-address-and-port) przypisany do vCenter Server w ustawieniach serwera konfiguracji na adres nowej vCenter Server.
2. [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla kilku maszyn wirtualnych pozostałych na starym serwerze.
3. [Dodaj stary vCenter Server](#add-vmware-server-to-the-vault) i jego adres IP do serwera konfiguracji.
4. [Ponownie Włącz replikację](vmware-azure-tutorial.md#enable-replication) dla maszyn wirtualnych, które pozostają na starym serwerze.
 
 ## <a name="next-steps"></a>Następne kroki
W przypadku wystąpienia jakichkolwiek problemów [Rozwiązywanie problemów](vmware-azure-troubleshoot-vcenter-discovery-failures.md) vCenter Server.
