---
title: Konfigurowanie grupy dostępności (szablon szybkiego startu platformy Azure)
description: Użyj szablonów szybkiego startu platformy Azure, aby utworzyć klaster trybu failover systemu Windows, dołączyć do klastra SQL Server maszyny wirtualne, utworzyć odbiornik i skonfigurować wewnętrzny moduł równoważenia obciążenia na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022378"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Korzystanie z szablonów szybkiego startu platformy Azure w celu skonfigurowania grupy dostępności dla SQL Server na maszynie wirtualnej platformy Azure
W tym artykule opisano sposób korzystania z szablonów szybkiego startu platformy Azure w celu częściowo automatyzowania wdrożenia konfiguracji zawsze włączonych grup dostępności dla SQL Server maszyn wirtualnych na platformie Azure. W tym procesie są używane dwa szablony szybkiego startu platformy Azure: 

   | Szablon | Opis |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Tworzy klaster trybu failover systemu Windows i dołącza do niego SQL Server maszyny wirtualne. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Tworzy odbiornik grupy dostępności i konfiguruje wewnętrzny moduł równoważenia obciążenia. Tego szablonu można używać tylko wtedy, gdy klaster trybu failover systemu Windows został utworzony przy użyciu szablonu **101-SQL-VM-AG-Setup** . |
   | &nbsp; | &nbsp; |

Inne części konfiguracji grupy dostępności muszą zostać wykonane ręcznie, takie jak tworzenie grupy dostępności i tworzenie wewnętrznego modułu równoważenia obciążenia. Ten artykuł zawiera sekwencję zautomatyzowanych i ręcznych kroków.
 

## <a name="prerequisites"></a>Wymagania wstępne 
Aby zautomatyzować instalację zawsze włączonych grup dostępności przy użyciu szablonów szybkiego startu, należy spełnić następujące wymagania wstępne: 
- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyna wirtualna przyłączona [do domeny na platformie Azure z systemem SQL Server 2016 (lub nowszym) w wersji Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) , która znajduje się w tym samym zestawie dostępności lub strefie dostępności i która została [zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dwie dostępne adresy IP (nieużywane przez żadną jednostkę): jeden dla wewnętrznego modułu równoważenia obciążenia i jeden dla odbiornika grupy dostępności w tej samej podsieci co grupa dostępności. Jeśli jest używany istniejący moduł równoważenia obciążenia, wymagany jest tylko jeden dostępny adres IP.  

## <a name="permissions"></a>Uprawnienia
Następujące uprawnienia są niezbędne do skonfigurowania grupy dostępności zawsze włączone przy użyciu szablonów szybkiego startu platformy Azure: 

- Istniejące konto użytkownika domeny, które ma uprawnienie **Tworzenie obiektu komputera** w domenie.  Na przykład konto administratora domeny ma zwykle wystarczające uprawnienia (na przykład: account@domain.com). _To konto powinno być również częścią lokalnej grupy administratorów na każdej maszynie wirtualnej w celu utworzenia klastra._
- Konto użytkownika domeny kontrolujące usługę SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Krok 1. Tworzenie klastra trybu failover i dołączanie SQL Server maszyn wirtualnych do klastra przy użyciu szablonu szybkiego startu 
Po zarejestrowaniu maszyn wirtualnych SQL Server z dostawcą zasobów maszyny wirtualnej SQL można przyłączyć SQL Server do maszyn wirtualnych do *SqlVirtualMachineGroups*. Ten zasób definiuje metadane klastra trybu failover systemu Windows. Metadane obejmują wersję, wydanie, w pełni kwalifikowaną nazwę domeny, Active Directory konta do zarządzania zarówno klastrem, jak i usługą SQL Server oraz kontem magazynu jako monitorem chmury. 

Dodanie SQL Server maszyn wirtualnych do grupy zasobów *SqlVirtualMachineGroups* powoduje uruchomienie usługi klastra trybu failover systemu Windows w celu utworzenia klastra, a następnie dołączenie tych SQL Server maszyn wirtualnych do tego klastra. Ten krok jest zautomatyzowany z szablonem szybkiego startu **101-SQL-VM-AG-Setup** . Można go zaimplementować, wykonując następujące czynności:

1. Przejdź do szablonu przewodnika Szybki Start [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) . Następnie wybierz pozycję **Wdróż na platformie Azure** , aby otworzyć szablon szybkiego startu w Azure Portal.
1. Wypełnij pola wymagane, aby skonfigurować metadane dla klastra trybu failover systemu Windows. Pola opcjonalne można pozostawić puste.

   W poniższej tabeli przedstawiono wymagane wartości szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   | **Subskrypcja** |  Subskrypcja, w której znajdują się maszyny wirtualne SQL Server. |
   |**Grupa zasobów** | Grupa zasobów, w której znajdują się maszyny wirtualne SQL Server. | 
   |**Nazwa klastra trybu failover** | Nazwa, która ma być nowym klastrem trybu failover systemu Windows. |
   | **Istniejąca lista maszyn wirtualnych** | SQL Server maszyny wirtualne, które chcesz wziąć udział w grupie dostępności, a które będą częścią tego nowego klastra. Oddziel te wartości przecinkami i spacjami (na przykład: *SQLVM1, SQLVM2*). |
   | **Wersja SQL Server** | SQL Server wersja SQL Server maszyn wirtualnych. Wybierz ją z listy rozwijanej. Obecnie obsługiwane są tylko obrazy w SQL Server 2016 i SQL Server 2017. |
   | **Istniejąca w pełni kwalifikowana nazwa domeny** | Istniejąca nazwa FQDN domeny, w której znajdują się maszyny wirtualne SQL Server. |
   | **Istniejące konto domeny** | Istniejące konto użytkownika domeny, które ma uprawnienie **Tworzenie obiektu komputera** w domenie jako [obiekt nazwy klastra](/windows-server/failover-clustering/prestage-cluster-adds) jest tworzona podczas wdrażania szablonu. Na przykład konto administratora domeny ma zwykle wystarczające uprawnienia (na przykład: account@domain.com). *To konto powinno być również częścią lokalnej grupy administratorów na każdej maszynie wirtualnej w celu utworzenia klastra.*| 
   | **Hasło konta domeny** | Hasło do wyżej wymienionego konta użytkownika domeny. | 
   | **Istniejące konto usługi SQL** | Konto użytkownika domeny, które steruje [usługą SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) podczas wdrażania grupy dostępności (na przykład: account@domain.com). |
   | **Hasło usługi SQL** | Hasło używane przez konto użytkownika domeny, które steruje usługą SQL Server. |
   | **Nazwa monitora chmury** | Nowe konto usługi Azure Storage, które zostanie utworzone i użyte na potrzeby monitora w chmurze. Możesz zmodyfikować tę nazwę. |
   | **\_lokalizacji artefaktów** | To pole jest domyślnie ustawione i nie powinno być modyfikowane. |
   | **Token SAS lokalizacji \_artefaktów** | To pole jest celowo pozostawione puste. |
   | &nbsp; | &nbsp; |

1. Jeśli akceptujesz warunki i postanowienia, zaznacz pole wyboru **Zgadzam się na warunki i postanowienia podane powyżej** . Następnie wybierz pozycję **Kup** , aby zakończyć wdrażanie szablonu szybkiego startu. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z ikony dzwonka **powiadomień** na górnym banerze nawigacji lub przejdź do **grupy zasobów** w Azure Portal. Wybierz pozycję **wdrożenia** w obszarze **Ustawienia**, a następnie wybierz pozycję **Microsoft. Template** Deployment. 

>[!NOTE]
> Poświadczenia podane podczas wdrażania szablonu są przechowywane tylko dla długości wdrożenia. Po zakończeniu wdrażania te hasła zostaną usunięte. Jeśli dodasz więcej SQL Server maszyn wirtualnych do klastra, zostanie wyświetlony monit o ich podanie. 


## <a name="step-2-manually-create-the-availability-group"></a>Krok 2. ręczne tworzenie grupy dostępności 
Ręcznie Utwórz grupę dostępności jak zwykle za pomocą [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)lub [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Nie* Twórz teraz odbiornika, ponieważ szablon **101-SQL-VM-aglistener-Setup** Start jest automatycznie w kroku 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Krok 3. ręczne tworzenie wewnętrznego modułu równoważenia obciążenia
Odbiornik grupy dostępności zawsze włączony wymaga wewnętrznego wystąpienia Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia udostępnia "przestawny" adres IP dla odbiornika grupy dostępności, który umożliwia szybsze przełączanie w tryb failover i ponowne łączenie. Jeśli SQL Server maszyny wirtualne w grupie dostępności są częścią tego samego zestawu dostępności, można użyć podstawowego modułu równoważenia obciążenia. W przeciwnym razie należy użyć standardowego modułu równoważenia obciążenia. 

> [!IMPORTANT]
> Wewnętrzny moduł równoważenia obciążenia powinien znajdować się w tej samej sieci wirtualnej co SQL Server wystąpienia maszyn wirtualnych. 

Wystarczy utworzyć wewnętrzny moduł równoważenia obciążenia. W kroku 4 szablon z przewodnikiem **101-SQL-VM-aglistener-Setup** obsługuje resztę konfiguracji (na przykład pulę zaplecza, sondę kondycji i reguły równoważenia obciążenia). 

1. W Azure Portal Otwórz grupę zasobów zawierającą SQL Server maszyn wirtualnych. 
2. W grupie zasobów wybierz pozycję **Dodaj**.
3. Wyszukaj **moduł równoważenia obciążenia**. W wynikach wyszukiwania wybierz pozycję **Load Balancer**, która jest publikowana przez **firmę Microsoft**.
4. W bloku **Load Balancer** wybierz pozycję **Utwórz**.
5. W oknie dialogowym **Tworzenie usługi równoważenia obciążenia** Skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Wprowadź nazwę tekstową, która reprezentuje moduł równoważenia obciążenia. Na przykład wprowadź **sqlLB**. |
   | **Typ** |**Wewnętrzne**: Większość implementacji korzysta z wewnętrznego modułu równoważenia obciążenia, który umożliwia aplikacjom w ramach tej samej sieci wirtualnej łączenie się z grupą dostępności.  </br> **Zewnętrzny**: umożliwia aplikacjom łączenie się z grupą dostępności za pomocą publicznego połączenia internetowego. |
   | **Sieć wirtualna** | Wybierz sieć wirtualną, w której znajdują się wystąpienia SQL Server. |
   | **Podsieć** | Wybierz podsieć, w której znajdują się wystąpienia SQL Server. |
   | **Przypisanie adresu IP** |**Ruchom** |
   | **Prywatny adres IP** | Określ dostępny adres IP z podsieci. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, to pole może się pojawić. Wybierz subskrypcję, którą chcesz skojarzyć z tym zasobem. Zwykle jest to taka sama subskrypcja jak wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w której znajdują się wystąpienia SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w której znajdują się wystąpienia SQL Server. |
   | &nbsp; | &nbsp; |

6. Wybierz pozycję **Utwórz**. 


>[!IMPORTANT]
> Zasób publicznego adresu IP dla każdej maszyny wirtualnej SQL Server powinien mieć standardową jednostkę SKU zgodną z usługą równoważenia obciążenia w warstwie Standardowa. Aby określić jednostkę SKU publicznego adresu IP maszyny wirtualnej, przejdź do pozycji **Grupa zasobów**, wybierz zasób **publicznego adresu IP** dla maszyny wirtualnej SQL Server i Znajdź wartość w obszarze **jednostka SKU** w okienku **Przegląd** . 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Krok 4. Tworzenie odbiornika grupy dostępności i Konfigurowanie wewnętrznego modułu równoważenia obciążenia przy użyciu szablonu szybkiego startu

Utworzenie odbiornika grupy dostępności i automatyczne skonfigurowanie wewnętrznego modułu równoważenia obciążenia przy użyciu szablonu **101-SQL-VM-aglistener-Setup — konfiguracja** szybkiego startu. Szablon inicjuje zasób Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Szablon **101-SQL-VM-aglistener-Setup** Start, za pośrednictwem dostawcy zasobów maszyny wirtualnej SQL, wykonuje następujące czynności:

- Tworzy nowy zasób adresu IP frontonu (na podstawie wartości adresu IP podanego podczas wdrażania) dla odbiornika. 
- Konfiguruje ustawienia sieci dla klastra i wewnętrznego modułu równoważenia obciążenia. 
- Konfiguruje pulę zaplecza dla wewnętrznego modułu równoważenia obciążenia, sondy kondycji i reguł równoważenia obciążenia.
- Tworzy odbiornik grupy dostępności o podanym adresie IP i nazwie.
 
>[!NOTE]
> Można użyć **101-SQL-VM-aglistener-Setup** tylko wtedy, gdy klaster trybu failover systemu Windows został utworzony przy użyciu szablonu **101-SQL-VM-AG-Setup** .
   
   
Aby skonfigurować wewnętrzny moduł równoważenia obciążenia i utworzyć odbiornik grupy dostępności, wykonaj następujące czynności:
1. Przejdź do szablonu [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) Start i wybierz pozycję **Wdróż na platformie Azure** , aby uruchomić szablon szybkiego startu w Azure Portal.
1. Wypełnij pola wymagane w celu skonfigurowania wewnętrznego modułu równoważenia obciążenia i Utwórz odbiornik grupy dostępności. Pola opcjonalne można pozostawić puste. 

   W poniższej tabeli przedstawiono wymagane wartości szablonu: 

   | **Pole** | Wartość |
   | --- | --- |
   |**Grupa zasobów** | Grupa zasobów, w której istnieje SQL Server maszyny wirtualne i Grupa dostępności. | 
   |**Istniejąca nazwa klastra trybu failover** | Nazwa klastra, do którego są przyłączone maszyny wirtualne SQL Server. |
   | **Istniejąca Grupa dostępności SQL**| Nazwa grupy dostępności, do której należy SQL Server maszyny wirtualne. |
   | **Istniejąca lista maszyn wirtualnych** | Nazwy maszyn wirtualnych SQL Server, które są częścią wspomnianej wcześniej grupy dostępności. Rozdziel nazwy przecinkami i spacjami (na przykład: *SQLVM1, SQLVM2*). |
   | **Odbiornika** | Nazwa DNS, która ma zostać przypisana do odbiornika. Domyślnie ten szablon określa nazwę "aglistener", ale można ją zmienić. Nazwa nie powinna być dłuższa niż 15 znaków. |
   | **Port odbiornika** | Port, który ma być używany przez odbiornik. Zazwyczaj ten port powinien być wartością domyślną 1433. Jest to numer portu określany przez szablon. Ale jeśli port domyślny został zmieniony, port odbiornika powinien używać tej wartości zamiast tego. | 
   | **Adres IP odbiornika** | Adres IP, który ma być używany przez odbiornik. Ten adres zostanie utworzony podczas wdrażania szablonu, więc podaj taki, który nie jest jeszcze używany.  |
   | **Istniejąca podsieć** | Nazwa wewnętrznej podsieci maszyn wirtualnych SQL Server (na przykład: *Domyślna*). Możesz określić tę wartość, przechodząc do **grupy zasobów**, wybierając sieć wirtualną, wybierając **podsieci** w okienku **Ustawienia** i kopiując wartość w polu **Nazwa**. |
   | **Istniejąca Load Balancer wewnętrzna** | Nazwa wewnętrznego modułu równoważenia obciążenia, który został utworzony w kroku 3. |
   | **Port sondy** | Port sondy, który ma być używany przez wewnętrzny moduł równoważenia obciążenia. Szablon domyślnie używa 59999, ale można zmienić tę wartość. |
   | &nbsp; | &nbsp; |

1. Jeśli akceptujesz warunki i postanowienia, zaznacz pole wyboru **Zgadzam się na warunki i postanowienia podane powyżej** . Wybierz pozycję **Kup** , aby zakończyć wdrażanie szablonu szybkiego startu. 
1. Aby monitorować wdrożenie, wybierz wdrożenie z ikony dzwonka **powiadomień** na górnym banerze nawigacji lub przejdź do **grupy zasobów** w Azure Portal. Wybierz pozycję **wdrożenia** w obszarze **Ustawienia**, a następnie wybierz pozycję **Microsoft. Template** Deployment. 

>[!NOTE]
>Jeśli wdrożenie zakończy się niepowodzeniem w połowie, należy ręcznie [usunąć nowo utworzony odbiornik](#remove-the-availability-group-listener) przy użyciu programu PowerShell przed ponownym wdrożeniem szablonu z przewodnikiem szybki start **101-SQL-VM-aglistener-Setup** . 

## <a name="remove-the-availability-group-listener"></a>Usuń odbiornik grupy dostępności
Jeśli później będzie konieczne usunięcie odbiornika grupy dostępności, który został skonfigurowany przez szablon, musisz przejść przez dostawcę zasobów maszyny wirtualnej SQL. Ponieważ odbiornik jest zarejestrowany za pośrednictwem dostawcy zasobów maszyny wirtualnej SQL, usunięcie go za pośrednictwem SQL Server Management Studio jest niewystarczające. 

Najlepszą metodą jest usunięcie go za pośrednictwem dostawcy zasobów maszyny wirtualnej SQL przy użyciu poniższego fragmentu kodu w programie PowerShell. Spowoduje to usunięcie metadanych odbiornika grupy dostępności z dostawcy zasobów maszyny wirtualnej SQL. Również fizycznie usuwa odbiornik z grupy dostępności. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Typowe błędy
W tej sekcji omówiono niektóre znane problemy i ich możliwe rozwiązania. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Odbiornik grupy dostępności dla grupy dostępności "\<AG Name >" już istnieje
Wybrana grupa dostępności używana w szablonie szybkiego startu platformy Azure dla odbiornika grupy dostępności zawiera już odbiornik. Jest ona fizycznie w grupie dostępności lub jej metadane pozostają w ramach dostawcy zasobów maszyny wirtualnej SQL. Przed ponownym wdrożeniem szablonu szybkiego startu **101-SQL-VM-aglistener-Setup** należy usunąć odbiornik przy użyciu [programu PowerShell](#remove-the-availability-group-listener) . 

### <a name="connection-only-works-from-primary-replica"></a>Połączenie działa tylko z repliką podstawową
To zachowanie jest prawdopodobnie z niepowodzenia wdrożenia szablonu **101-SQL-VM-aglistener-Setup** , które pozostawiło konfigurację wewnętrznego modułu równoważenia obciążenia w stanie niespójnym. Sprawdź, czy pula zaplecza zawiera listę zestawu dostępności oraz czy istnieją reguły dla sondy kondycji i reguły równoważenia obciążenia. W przypadku braku jakichkolwiek elementów konfiguracja wewnętrznego modułu równoważenia obciążenia jest niespójna. 

Aby rozwiązać ten problem, Usuń odbiornik przy użyciu [programu PowerShell](#remove-the-availability-group-listener), Usuń wewnętrzny moduł równoważenia obciążenia za pośrednictwem Azure Portal i ponownie rozpocznij od kroku 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Nieprawidłowego żądania — można zaktualizować tylko listę maszyn wirtualnych SQL
Ten błąd może wystąpić podczas wdrażania szablonu **101-SQL-VM-aglistener-Setup** , jeśli odbiornik został usunięty za pośrednictwem SQL Server Management Studio (SSMS), ale nie został usunięty z dostawcy zasobów maszyny wirtualnej SQL. Usunięcie odbiornika za pomocą narzędzia SSMS nie powoduje usunięcia metadanych odbiornika z dostawcy zasobów maszyny wirtualnej SQL. Odbiornik musi zostać usunięty z dostawcy zasobów za pośrednictwem [programu PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Konto domeny nie istnieje
Ten błąd może mieć dwie przyczyny. Podane konto domeny nie istnieje lub nie zawiera danych [głównej nazwy użytkownika (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) . Szablon **101-SQL-VM-AG-Setup** oczekuje konta domeny w formularzu nazwy UPN (czyli *user@domain.com* ), ale niektóre konta domeny mogą go brakować. Zwykle dzieje się tak, gdy użytkownik lokalny został zmigrowany do pierwszego konta administratora domeny, gdy serwer został podwyższony do kontrolera domeny lub gdy użytkownik został utworzony za pomocą programu PowerShell. 

Sprawdź, czy konto istnieje. Jeśli tak, może się zdarzyć, że zadziała w drugiej sytuacji. Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Na kontrolerze domeny Otwórz okno **Active Directory Użytkownicy i komputery** przy użyciu opcji **Narzędzia** w **Menedżer serwera**. 
2. Przejdź do konta, wybierając pozycję **Użytkownicy** w lewym okienku.
3. Kliknij prawym przyciskiem myszy konto, a następnie wybierz polecenie **Właściwości**.
4. Wybierz kartę **konto** . Jeśli pole **Nazwa logowania użytkownika** jest puste, jest to przyczyna błędu. 

    ![Puste konto użytkownika wskazuje brakującą nazwę UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Wypełnij pole **Nazwa logowania użytkownika** , aby dopasować nazwę użytkownika, a następnie wybierz odpowiednią domenę z listy rozwijanej. 
6. Wybierz pozycję **Zastosuj** , aby zapisać zmiany, a następnie zamknij okno dialogowe, wybierając **przycisk OK**. 

Po wprowadzeniu tych zmian spróbuj ponownie wdrożyć szablon szybkiego startu platformy Azure. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Przegląd SQL Server maszyn wirtualnych](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące SQL Server maszyn wirtualnych](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen maszyn wirtualnych SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji dla maszyn wirtualnych SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Przełączanie modeli licencjonowania dla maszyny wirtualnej SQL Server](virtual-machines-windows-sql-ahb.md)



