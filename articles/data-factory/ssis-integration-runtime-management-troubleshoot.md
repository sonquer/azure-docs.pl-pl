---
title: Rozwiązywanie problemów z usługą SSIS Integration Runtime Management
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z zarządzaniem Integration Runtime SSIS (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 52b1d93935e6428563c72361655893ffddf8a507
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941863"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Rozwiązywanie problemów z usługą SSIS Integration Runtime Management w programie Azure Data Factory

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z zarządzaniem w systemie Azure — SQL Server Integration Services (SSIS) Integration Runtime (IR), znane także jako SSIS IR.

## <a name="overview"></a>Przegląd

W przypadku napotkania dowolnego problemu podczas aprowizacji lub anulowania obsługi usługi SSIS IR zostanie wyświetlony komunikat o błędzie w portalu Microsoft Azure Data Factory lub zwrócony błąd z polecenia cmdlet programu PowerShell. Błąd jest zawsze wyświetlany w formacie kodu błędu z szczegółowym komunikatem o błędzie.

Jeśli kod błędu to InternalServerError, usługa ma problemy przejściowe i należy ponowić próbę wykonania tej operacji później. Jeśli ponowienie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

W przeciwnym razie trzy główne zależności zewnętrzne mogą spowodować błędy: serwer Azure SQL Database lub wystąpienie zarządzane, skrypt instalacji niestandardowej i konfigurację sieci wirtualnej.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemy z serwerem Azure SQL Database lub wystąpieniem zarządzanym

Jeśli aprowizujesz środowisko SSIS IR z bazą danych katalogu usług SSIS, wymagany jest serwer usługi Azure SQL Database lub wystąpienie zarządzane. Środowisko SSIS IR musi mieć dostęp do serwera usługi Azure SQL Database lub wystąpienia zarządzanego. Ponadto konto serwera usługi Azure SQL Database lub wystąpienia zarządzanego powinno mieć uprawnienia do utworzenia bazy danych katalogu usług SSIS (SSISDB). Jeśli wystąpił błąd, w portalu usługi Data Factory będzie widoczny kod błędu oraz szczegółowy komunikat o wyjątku SQL. Rozwiąż problem, korzystając z informacji z poniższej listy kodów błędu.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ten problem może występować podczas aprowizowania nowego środowiska SSIS IR lub po uruchomieniu środowiska IR. Jeśli błąd wystąpi podczas aprowizacji środowiska IR, komunikat o błędzie może zawierać szczegółowy komunikat SqlException, wskazujący na jeden z następujących problemów:

* Problem z połączeniem sieciowym. Sprawdź, czy można uzyskać dostęp do nazwy hosta serwera SQL lub wystąpienia zarządzanego. Sprawdź też, czy dostęp środowiska SSIS IR do serwera nie jest blokowany przez zaporę lub sieciową grupę zabezpieczeń.
* Niepowodzenie logowania podczas uwierzytelniania SQL. Podane konto nie może zalogować się do bazy danych programu SQL Server. Upewnij się, że podano poprawne konto użytkownika.
* Niepowodzenie logowania podczas uwierzytelniania usługi Microsoft Azure Active Directory (Azure AD) — tożsamość zarządzana. Dodaj tożsamość zarządzaną swojej fabryki do grupy usługi AAD i upewnij się, że ta tożsamość zarządzana ma uprawnienia dostępu do serwera bazy danych katalogu.
* Przekroczenie limitu czasu połączenia. Ten błąd jest zawsze spowodowany przez konfigurację związaną z zabezpieczeniami. Zalecamy wykonanie następujących czynności:
  1. Utwórz nową maszynę wirtualną.
  1. Dołącz maszynę wirtualną do tego samego Microsoft Azure Virtual Network, jeśli w sieci wirtualnej znajduje się port IR.
  1. Zainstaluj program SSMS i sprawdź stan serwera Azure SQL Database lub wystąpienia zarządzanego.

W przypadku innych problemów należy rozwiązać problem wskazany w szczegółowym komunikacie o wyjątku SQL. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera usługi Azure SQL Database lub wystąpienia zarządzanego.

Jeśli błąd występuje po uruchomieniu środowiska IR, dostęp węzła roboczego środowiska SSIS IR do serwera usługi Azure SQL Database lub wystąpienia zarządzanego jest prawdopodobnie blokowany przez zmiany w zakresie sieciowej grupy zabezpieczeń lub zapory. Odblokuj węzeł roboczy środowiska SSIS IR, aby mógł uzyskać dostęp do serwera usługi Azure SQL Database lub wystąpienia zarządzanego.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Oto, jak ten rodzaj komunikatu o błędzie może wyglądać następująco: "baza danych" SSISDB "osiągnęła limit przydziału rozmiaru. Partycjonowanie lub usuwanie danych, usuwanie indeksów lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. 

Możliwe rozwiązania są następujące:
* Zwiększ rozmiar przydziału SSISDB.
* Zmień konfigurację bazy danych SSISDB, aby zmniejszyć rozmiar przez:
   * Zmniejszenie okresu przechowywania i liczby wersji projektu.
   * Skrócenie okresu przechowywania dziennika.
   * Zmiana domyślnego poziomu dziennika.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ten błąd oznacza, że serwer usługi Azure SQL Database lub wystąpienie zarządzane ma już bazę danych SSISDB, która jest używana przez inne środowisko IR. Musisz zmienić serwer usługi Azure SQL Database lub wystąpienie zarządzane albo usunąć istniejącą bazę danych SSISDB i ponownie uruchomić nowe środowisko IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ten błąd może mieć jedną z następujących przyczyn:

* Konto użytkownika skonfigurowane na potrzeby środowiska SSIS IR nie ma uprawnień wymaganych do utworzenia bazy danych. Możesz przyznać użytkownikowi uprawnienia do tworzenia baz danych.
* Podczas tworzenia bazy danych nastąpiło przekroczenie limitu czasu, na przykład limitu czasu wykonywania lub limitu czasu operacji bazy danych. Spróbuj ponownie wykonać operację później. Jeśli ponowna próba również kończy się niepowodzeniem, skontaktuj się z zespołem pomocy technicznej serwera usługi Azure SQL Database lub wystąpienia zarządzanego.

W przypadku innych problemów sprawdź komunikat o wyjątku SQL i rozwiąż problem wskazany w szczegółach błędu. Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej serwera usługi Azure SQL Database lub wystąpienia zarządzanego.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Ten rodzaj komunikatu o błędzie wygląda następująco: "Nieprawidłowa nazwa obiektu wykazu. catalog_properties". " W takiej sytuacji masz już bazę danych o nazwie SSISDB, ale nie została ona utworzona przez program SSIS IR lub baza danych znajduje się w nieprawidłowym stanie spowodowanym błędami w ostatniej aprowizacji programu SSIS IR. Możesz usunąć istniejącą bazę danych o nazwie SSISDB albo skonfigurować nowy serwer usługi Azure SQL Database lub nowe wystąpienie zarządzane na potrzeby środowiska IR.

## <a name="custom-setup-issues"></a>Problemy z instalacją niestandardową

Za pomocą interfejsu instalacji niestandardowej można dodawać własne kroki instalacji podczas aprowizowania i ponownej konfiguracji środowiska SSIS IR. Aby uzyskać więcej informacji, zobacz [Dostosowywanie konfiguracji środowiska Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Upewnij się, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej, ponieważ wszystkie pliki z tego kontenera zostaną pobrane do węzła roboczego środowiska SSIS IR. Zalecamy przetestowanie skryptu instalacji niestandardowej na komputerze lokalnym, aby naprawić wszelkie problemy z wykonywaniem skryptu przed jego uruchomieniem w środowisku SSIS IR.

Kontener skryptu instalacji niestandardowej zostanie sprawdzony podczas działania środowiska IR, ponieważ środowisko SSIS IR jest regularnie aktualizowane. Ta aktualizacja wymaga dostępu do kontenera w celu pobrania skryptu instalacji niestandardowej i jego ponownej instalacji. Proces sprawdza także, czy kontener jest dostępny i czy plik main.cmd istnieje.

W przypadku dowolnego błędu, który obejmuje instalację niestandardową, zobaczysz kod błędu CustomSetupScriptFailure z kodem podrzędnym, takim jak CustomSetupScriptBlobContainerInaccessible lub CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ten błąd oznacza, że środowisko SSIS IR nie może uzyskać dostępu do kontenera obiektów blob platformy Azure na potrzeby instalacji niestandardowej. Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego kontenera jest osiągalny i nie wygasł.

Zatrzymaj środowisko IR, jeśli jest ono uruchomione, ponownie je skonfiguruj przy użyciu nowego identyfikatora URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej, a następnie ponownie uruchom środowisko IR.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ten błąd oznacza, że środowisko SSIS IR nie może znaleźć skryptu instalacji niestandardowej (main.cmd) w kontenerze obiektów blob. Upewnij się, że plik main.cmd, który jest punktem wejścia dla instalacji niestandardowej, znajduje się w kontenerze.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ten błąd oznacza, że wykonanie skryptu instalacji niestandardowej (main.cmd) nie powiodło się. Najpierw wypróbuj skrypt na komputerze lokalnym lub sprawdź dzienniki wykonywania instalacji niestandardowej w kontenerze obiektów blob.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ten błąd wskazuje, że upłynął limit czasu wykonywania skryptu instalacji niestandardowej. Upewnij się, że skrypt może być wykonywany w trybie dyskretnym i nie potrzebuje żadnej interakcji. Upewnij się również, że kontener obiektów blob zawiera tylko niezbędne pliki instalacji niestandardowej. Zalecane jest przetestowanie skryptu najpierw na komputerze lokalnym. Należy również sprawdzić dzienniki wykonywania instalacji niestandardowej w kontenerze obiektów blob. Limit czasu instalacji niestandardowej jest przekraczany po 45 minutach, a maksymalny okres obejmuje czas pobierania wszystkich plików z kontenera i instalowania ich w środowisku SSIS IR. Jeśli potrzebujesz dłuższego okresu, zgłoś bilet pomocy technicznej.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ten błąd oznacza, że próba przekazania dzienników wykonywania instalacji niestandardowej do kontenera obiektów blob nie powiodła się. Ten problem występuje, ponieważ środowisko SSIS IR nie ma uprawnień do zapisu w kontenerze obiektów blob lub wystąpiły problemy z magazynem bądź siecią. Jeśli instalacja niestandardowa zakończy się pomyślnie, ten błąd nie wpłynie na żadną funkcję środowiska SSIS. Nie będzie jedynie dzienników. Jeśli instalacja niestandardowa zakończy się niepowodzeniem z powodu innego błędu i dziennik nie zostanie przekazany, najpierw zgłaszamy ten błąd, aby można było przekazać dziennik do analizy. Ponadto po rozwiązaniu tego problemu będziemy zgłaszać bardziej specyficzne problemy. Jeśli ten problem nie zostanie rozwiązany po ponowieniu próby, skontaktuj się z zespołem pomocy technicznej usługi Azure Data Factory.

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Po dołączeniu środowiska SSIS IR do usługi Azure Virtual Network środowisko SSIS IR używa sieci wirtualnej objętej subskrypcją użytkownika. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli wystąpi problem związany z siecią wirtualną, zobaczysz jeden z poniższych błędów.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ten błąd może wystąpić z różnych powodów. Aby rozwiązać ten problem, zobacz sekcje[Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) i [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Forbidden

Ten rodzaj błędu może wyglądać następująco: "SubnetId nie jest włączona dla bieżącego konta. Dostawca zasobów Microsoft. Batch nie jest zarejestrowany w ramach tej samej subskrypcji sieci wirtualnej ".

Te szczegóły oznaczają, że usługa Azure Batch nie może uzyskać dostępu do sieci wirtualnej. Zarejestruj dostawcę zasobów Microsoft.Batch w ramach tej samej subskrypcji co sieć wirtualna.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Ten rodzaj błędu może być podobny do jednego z następujących: 

- "Określona Sieć wirtualna nie istnieje lub usługa Batch nie ma do niej dostępu".
- "Określona podsieć XXX nie istnieje".

Te błędy oznaczają, że sieć wirtualna nie istnieje, usługa Azure Batch nie może uzyskać do niej dostępu lub podana podsieć nie istnieje. Upewnij się, że sieć wirtualna i podsieć istnieją oraz że usługa Azure Batch może uzyskać do nich dostęp.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Ten rodzaj komunikatu o błędzie może wyglądać następująco: "nie można zainicjować obsługi administracyjnej Integration Runtime w sieci wirtualnej. Jeśli skonfigurowano ustawienia serwera DNS lub sieciowej grupy zabezpieczeń, upewnij się, że serwer DNS jest dostępny i sieciowej grupy zabezpieczeń jest prawidłowo skonfigurowany.

W tej sytuacji prawdopodobnie masz niestandardową konfigurację ustawień serwera DNS lub sieciowej grupy zabezpieczeń, co uniemożliwia rozpoznanie lub uzyskanie dostępu do nazwy serwera platformy Azure wymaganej przez środowisko SSIS IR. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja sieci wirtualnej środowiska SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej usługi Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Środowisko SSIS IR będzie automatycznie aktualizowane w regularnych odstępach czasu. Podczas uaktualniania tworzona jest nowa pula usługi Azure Batch, a stara pula usługi Azure Batch jest usuwana. Ponadto usuwane są zasoby związane z siecią wirtualną dla starej puli i tworzone są nowe zasoby związane z siecią wirtualną w ramach subskrypcji. Ten błąd oznacza, że usunięcie zasobów związanych z siecią wirtualną dla starej puli nie powiodło się z powodu blokady usuwania na poziomie subskrypcji lub grupy zasobów. Ponieważ klient kontroluje i ustawia blokadę usuwania, w tej sytuacji musi usunąć blokadę usuwania.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Jeśli aprowizacja środowiska SSIS IR nie powiedzie się, wszystkie utworzone zasoby zostaną usunięte. Jeśli jednak istnieje blokada usuwania zasobów na poziomie subskrypcji lub grupy zasobów, zasoby sieci wirtualnej nie zostaną usunięte zgodnie z oczekiwaniami. Aby naprawić ten błąd, usuń blokadę usuwania i ponownie uruchom środowisko IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Po zatrzymaniu środowiska SSIS IR wszystkie zasoby związane z siecią wirtualną są usuwane. Jednak usuwanie może zakończyć się niepowodzeniem, jeśli istnieje zasób z blokadą usuwania na poziomie subskrypcji lub grupy zasobów. Również w tym miejscu klient kontroluje i ustawia blokadę usuwania. W związku z tym musi on usunąć blokadę usuwania, a następnie ponownie zatrzymać środowisko SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

Ten błąd występuje, gdy środowisko IR jest uruchomione, i oznacza, że kondycja środowiska IR pogorszyła się. Ten błąd jest zawsze spowodowany przez zmianę w konfiguracji serwera DNS lub sieciowej grupy zabezpieczeń, która uniemożliwia środowisku SSIS IR łączenie się z niezbędną usługą. Ponieważ konfiguracja serwera DNS i sieciowej grupy zabezpieczeń jest kontrolowana przez klienta, to klient musi rozwiązać problemy z blokowaniem. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja sieci wirtualnej środowiska SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli nadal występują problemy, skontaktuj się z zespołem pomocy technicznej usługi Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Konfiguracja statycznych publicznych adresów IP

Po dołączeniu Azure-SSIS IR do usługi Azure Virtual Network można również przenieść własne statyczne publiczne adresy IP do środowiska IR, aby umożliwić dostęp do źródeł danych, które ograniczają dostęp do określonych adresów IP. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Poza powyższymi problemami z siecią wirtualną mogą również być zgodne ze statycznymi publicznymi adresami IP. Sprawdź następujące błędy, aby uzyskać pomoc.

### <a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Ten błąd może wystąpić z różnych powodów podczas uruchamiania Azure-SSIS IR:

| Komunikat o błędzie | Rozwiązanie|
|:--- |:--- |
| Podany statyczny publiczny adres IP jest już używany. Podaj dwa nieużywane elementy do Azure-SSIS Integration Runtime. | Należy wybrać dwa nieużywane statyczne publiczne adresy IP lub usunąć bieżące odwołania do określonego publicznego adresu IP, a następnie ponownie uruchomić Azure-SSIS IR. |
| Podany statyczny publiczny adres IP nie ma nazwy DNS, podaj dla Azure-SSIS Integration Runtime dwa z nich nazwę DNS. | Nazwę DNS publicznego adresu IP można skonfigurować w Azure Portal, jak pokazano na poniższym obrazie. Określone czynności są następujące: (1) Otwórz Azure Portal i przejdź do strony zasobów tego publicznego adresu IP; (2) wybierz sekcję **Konfiguracja** i skonfiguruj nazwę DNS, a następnie kliknij przycisk **Zapisz** . (3) Uruchom ponownie Azure-SSIS IR. |
| Podane wirtualne i statyczne adresy IP dla Azure-SSIS Integration Runtime muszą znajdować się w tej samej lokalizacji. | Zgodnie z wymaganiami sieci platformy Azure statyczny publiczny adres IP i Sieć wirtualna powinny znajdować się w tej samej lokalizacji i subskrypcji. Podaj dwa prawidłowe statyczne publiczne adresy IP i ponownie uruchom Azure-SSIS IR. |
| Podany statyczny publiczny adres IP jest podstawowym. Podaj dwa standardowe dla Azure-SSIS Integration Runtime. | Aby uzyskać pomoc, zobacz [jednostki SKU publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) . |

![Środowisko IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

W przypadku niepowodzenia aprowizacji Azure-SSIS IR wszystkie utworzone zasoby zostaną usunięte. Jeśli jednak istnieje blokada usuwania zasobu w ramach subskrypcji lub grupy zasobów (która zawiera statyczny publiczny adres IP), zasoby sieciowe nie zostaną usunięte zgodnie z oczekiwaniami. Aby naprawić ten błąd, Usuń blokadę usuwania i uruchom ponownie środowisko IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Po zatrzymaniu Azure-SSIS IR wszystkie zasoby sieciowe utworzone w grupie zasobów zawierającej publiczny adres IP zostaną usunięte. Jednak usunięcie może zakończyć się niepowodzeniem, jeśli istnieje blokada usuwania zasobu w subskrypcji lub grupie zasobów (która zawiera statyczny publiczny adres IP). Usuń blokadę usuwania i uruchom ponownie środowisko IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR jest regularnie aktualizowana automatycznie. Podczas uaktualniania tworzone są nowe węzły IR, a stare węzły zostaną usunięte. Ponadto zostaną usunięte utworzone zasoby sieciowe (np. moduł równoważenia obciążenia i sieciowa Grupa zabezpieczeń) dla starych węzłów, a nowe zasoby sieciowe zostaną utworzone w ramach subskrypcji. Ten błąd oznacza, że usunięcie zasobów sieciowych dla starych węzłów nie powiodło się z powodu blokady usuwania w ramach subskrypcji lub grupy zasobów (zawierającej poziom statycznego publicznego adresu IP). Usuń blokadę usuwania, aby można było oczyścić stare węzły i zwolnić statyczny publiczny adres IP dla starych węzłów. W przeciwnym razie nie można zwolnić statycznego publicznego adresu IP i nie będzie można jeszcze uaktualnić środowiska IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Jeśli chcesz wprowadzić własne statyczne publiczne adresy IP, należy podać dwa publiczne adresy IP. Jeden z nich zostanie użyty do natychmiastowego utworzenia węzłów podczerwieni, a drugi zostanie użyty podczas uaktualniania środowiska IR. Ten błąd może wystąpić, gdy inny publiczny adres IP nie nadaje się do użycia podczas uaktualniania. Zapoznaj się z [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) , aby uzyskać możliwe przyczyny.