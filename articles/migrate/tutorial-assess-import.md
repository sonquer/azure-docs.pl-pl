---
title: Ocenianie serwerów za pomocą zaimportowanych danych serwera za pomocą oceny serwera Azure Migrate
description: Opisuje sposób oceny serwerów lokalnych na potrzeby migracji na platformę Azure z użyciem oceny serwera Azure Migrate przy użyciu zaimportowanych danych.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 060399952545c903fec8ecf08d99e438883c9fd1
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902536"
---
# <a name="assess-servers-by-using-imported-data"></a>Ocenianie serwerów za pomocą zaimportowanych danych

W tym artykule wyjaśniono, jak oceniać serwery lokalne za pomocą narzędzia do [oceny serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-assessment-tool) , importując metadane serwera w formacie wartości rozdzielanych przecinkami (CSV). Ta metoda oceny nie wymaga skonfigurowania urządzenia Azure Migrate w celu utworzenia oceny. Jest to przydatne, jeśli:

- Przed wdrożeniem urządzenia należy utworzyć szybką, początkową ocenę.
- Nie można wdrożyć urządzenia Azure Migrate w organizacji.
- Nie można udostępniać poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie na platformę Azure. Możesz kontrolować dane, które udostępniasz w zaimportowanym pliku. Ponadto większość danych (na przykład dostarczając adresy IP) jest opcjonalna.

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać o następujących kwestiach:

- W jednym pliku CSV można dodać maksymalnie 20 000 serwerów.
- Można dodać do 20 000 serwerów w projekcie Azure Migrate przy użyciu woluminu CSV.
- Informacje o serwerze można przekazać do oceny serwera wiele razy przy użyciu woluminu CSV.
- Zbieranie informacji o aplikacji jest przydatne w ocenie środowiska lokalnego do migracji. Jednak Ocena serwera nie przeprowadza obecnie oceny poziomu aplikacji ani nie uwzględnia aplikacji podczas tworzenia oceny.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj projekt Azure Migrate.
> * Wypełnij plik CSV informacjami o serwerze.
> * Zaimportuj plik, aby dodać informacje o serwerze do oceny serwera.
> * Utwórz i przejrzyj ocenę.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z przewodnikami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="set-azure-permissions-for-azure-migrate"></a>Ustawianie uprawnień platformy Azure dla Azure Migrate

Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto, a następnie wybierz je, aby wyświetlić uprawnienia.
3. Upewnij się, że masz uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

Aby skonfigurować nowy projekt Azure Migrate:

1. W Azure Portal w obszarze **wszystkie usługi**wyszukaj pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**w obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-import/assess-migrate.png)

4. W obszarze **wprowadzenie**wybierz pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Więcej informacji:

    - Przejrzyj [obsługiwane lokalizacje geograficzne](migrate-support-matrix.md#supported-geographies). Lokalizacja geograficzna projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Wybierz opcję **Dalej**.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena serwera** > **dalej**.

    ![Tworzenie oceny Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie wybierz pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="prepare-the-csv"></a>Przygotowywanie woluminu CSV

Pobierz szablon CSV i Dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, wybierz pozycję **odkryj**.
2. W obszarze **odnajdywanie maszyn**wybierz pozycję **Importuj przy użyciu woluminu CSV**.
3. Wybierz pozycję **Pobierz** , aby pobrać szablon woluminu CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobierz szablon CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i Dodaj je do pliku CSV.

- Aby zebrać dane, można wyeksportować je z narzędzi używanych do zarządzania lokalnym serwerem, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, Pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

Poniższa tabela zawiera podsumowanie pól plików do wypełnienia:

**Nazwa pola** | **Wypełnione** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Tak | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzeni** | Tak | Liczba rdzeni procesora przypisanych do serwera.
**Pamięć** | Tak | Całkowita ilość pamięci RAM w MB przypisana do serwera.
**Nazwa systemu operacyjnego** | Tak | System operacyjny serwera.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegółowe szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (w GB).<br/>Możesz dodać szczegóły do większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu z dysku 1** | Nie | Operacje odczytu z dysku na sekundę.
**Operacje zapisu na dysku 1** | Nie | Operacje zapisu na dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę, w MEGABAJTach na sekundę.
**Przepływność zapisu na dysku 1** | Nie | Dane zapisywane na dysku na sekundę, w MEGABAJTach na sekundę.
**Procent użycia procesora CPU** | Nie | Procent użycia procesora CPU.
**Procent wykorzystania pamięci** | Nie | Procent używanej pamięci RAM.
**Całkowita liczba odczytywanych dysków** | Nie | Operacje odczytu z dysku na sekundę.
**Całkowita liczba dysków — operacje zapisu** | Nie | Operacje zapisu na dysku na sekundę.
**Całkowita liczba dysków do odczytu** | Nie | Dane odczytane z dysku (MB na sekundę).
**Całkowita przepustowość zapisu dysków** | Nie | Dane zapisywane na dysku, w MEGABAJTach na sekundę.
**Sieć w przepływności** | Nie | Dane odebrane przez serwer, w MEGABAJTach na sekundę.
**Przepływność w sieci** | Nie | Dane przesyłane przez serwer, w MEGABAJTach na sekundę.
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości mogą być "BIOS" lub "UEFI".
**Typ serwera** | Nie | Wartości mogą być "fizyczne" lub "wirtualne".
**Funkcji hypervisor** | Nie | Funkcja hypervisor, w której jest uruchomiona maszyna. <br/> Możliwe wartości to "VMware", "Hyper-V", "Xen", "AWS", "GCP" lub "inne".
**Numer wersji funkcji hypervisor** | Nie | Wersja funkcji hypervisor.
**Identyfikator maszyny wirtualnej** | Nie | Identyfikator maszyny wirtualnej. Jest to wartość **InstanceUUid** dla maszyny wirtualnej VMware VCENTER lub **Identyfikator maszyny wirtualnej funkcji Hyper-** v dla funkcji Hyper-w.
**Identyfikator programu Virtual Machine Manager** | Nie | Jest to wartość **InstanceUUid** dla programu VMware vCenter. Nie jest to konieczne w przypadku funkcji Hyper-V.
**Adres MAC**| Nie | Adres MAC serwera.
**IDENTYFIKATOR SYSTEMU BIOS** | Nie | Identyfikator systemu BIOS serwera.
**Identyfikator serwera niestandardowego** | Nie | Lokalny, unikatowy identyfikator serwera lokalnie. <br/> Przydatne do śledzenia zaimportowanego serwera według identyfikatora lokalnego.
**Nazwa aplikacji 1** | Nie | Nazwa obciążenia uruchomionego na serwerze.<br/>Możesz dodać szczegóły do większej liczby aplikacji, [dodając kolumny](#add-multiple-applications) w szablonie. Można dodać maksymalnie pięć aplikacji.
**Typ aplikacji 1** | Nie | Typ obciążenia uruchomionego na serwerze
**Wersja 1 aplikacji** | Nie | Wersja obciążenia uruchomionego na serwerze.
**Wygaśnięcie licencji aplikacji 1** | Nie | Wygaśnięcie licencji (jeśli dotyczy).
**Jednostka biznesowa** | Nie | Jednostka biznesowa, do której należy serwer.
**Właściciel firmy** | Nie | Właściciel jednostki biznesowej.
**Nazwa aplikacji biznesowej** | Nie | Nazwa aplikacji, do której należy aplikacja.
**Lokalizacja** | Nie | Centrum danych, w którym znajduje się serwer programu.
**Data likwidacji serwera** | Nie | Data likwidacji serwera fizycznego lub podstawowego serwera fizycznego serwera wirtualnego.

### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Ocena rozpoznaje nazwy określonych systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów z [listy obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodaj wiele dysków

Szablon zawiera domyślne pola dla pierwszego dysku. Możesz dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, Dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu z dysku 2
- Operacje zapisu na dysku 2
- Przepływność odczytu dysku 2
- Przepływność zapisu na dysku 2

### <a name="add-multiple-applications"></a>Dodaj wiele aplikacji

Szablon zawiera pola dla pojedynczej aplikacji. Możesz dodać podobne kolumny dla maksymalnie pięciu aplikacji.  

Na przykład aby określić wszystkie pola dla drugiej aplikacji, Dodaj następujące kolumny:

- Nazwa aplikacji 2
- Typ aplikacji 2
- Wersja 2 aplikacji
- Wygaśnięcie licencji aplikacji 2

> [!NOTE]
> Informacje o aplikacji są przydatne podczas oceniania środowiska lokalnego na potrzeby migracji. Jednak Ocena serwera Azure Migrate nie przeprowadza obecnie oceny poziomu aplikacji ani nie uwzględnia aplikacji podczas tworzenia oceny.

## <a name="import-the-server-information"></a>Importuj informacje o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj serwery do oceny serwera.

1. W Azure Migrate w obszarze **odnajdywanie maszyn**przejdź do ukończonego szablonu.
2. Wybierz pozycję **Import** (Importuj).
3. Stan importowania jest pokazywany.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je rozwiązać lub kontynuować bez rozwiązywania problemów.
    - Aby poprawić dokładność oceny, Popraw informacje o serwerze zgodnie z opisem w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz pozycję **Pobierz szczegóły ostrzeżenia. Wolumin CSV**. Ta operacja pobiera wolumin CSV z zawartymi ostrzeżeniami. Przejrzyj ostrzeżenia i rozwiąż problemy zgodnie z wymaganiami.
    - Jeśli błędy pojawiają się w stanie, w związku z czym stan importu **nie powiodło się**, należy usunąć te błędy przed kontynuowaniem importowania:
        1. Pobierz plik CSV, który zawiera teraz szczegóły błędu.
        1. Przejrzyj błędy i Rozwiąż je w razie potrzeby. 
        1. Ponownie Przekaż zmodyfikowany plik.
4. Po **zakończeniu**stanu importu informacje o serwerze zostały zaimportowane.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Możesz zaktualizować informacje o serwerze, importując dane dla serwera z tą samą **nazwą serwera**. Nie można zmodyfikować pola **Nazwa serwera** . Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w Azure Portal po przeprowadzeniu odnajdywania:

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. Na stronie **Azure Migrate serwerów** > **Azure Migrate: Ocena serwera** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Importuj na podstawie** .

## <a name="set-up-and-run-an-assessment"></a>Konfigurowanie i uruchamianie oceny

Za pomocą oceny serwera można utworzyć dwa typy ocen.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny na podstawie określonych wartości danych wydajności. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)** : na podstawie danych wejściowych/wyjściowych na sekundę (IOPS) i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie określonego rozmiaru serwera.<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu, które można wybrać do oceny.

Aby uruchomić ocenę:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
2. Na karcie **serwery** w kafelku **Azure Migrate: Ocena serwera** wybierz pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

3. W obszarze **ocenianie serwerów**Określ nazwę oceny.
4. W obszarze **Źródło odnajdywania**wybierz pozycję **maszyny dodane za pośrednictwem opcji Importuj do Azure Migrate**.
5. Wybierz pozycję **Wyświetl wszystko** , aby przejrzeć właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

6. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. Grupa zbiera co najmniej jedną maszynę wirtualną w celu oceny.
7. W obszarze **Dodawanie maszyn do grupy**wybierz serwery, które mają zostać dodane do grupy.
8. Wybierz pozycję **Utwórz ocenę** , aby utworzyć grupę, a następnie uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

9. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate: Ocena serwera** > **ocen**.
10. Wybierz pozycję **Ocena eksportu** , aby pobrać ją jako plik programu Microsoft Excel.

## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy serwery są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania serwerów na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W obszarze **cele migracji** > **serwery**wybierz pozycję **oceny** w **Azure Migrate: Ocena serwera**.
2. W obszarze **oceny**Wybierz ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość platformy Azure**Ustal, czy serwery są gotowe do migracji na platformę Azure.
2. Sprawdź stan:
    - **Gotowe do platformy Azure**: Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: pokazuje problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

3. Wybierz stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły dotyczące gotowości serwera i przejść do szczegółów, aby zobaczyć szczegóły serwera, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure. Przekonaj się:

- Zapoznaj się z miesięcznymi kosztami obliczeniowymi i magazynem. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów opierają się na zaleceń dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Szacowany koszt umożliwia uruchamianie serwerów lokalnych jako maszyn wirtualnych typu "infrastruktura jako usługa" (IaaS). Ocena serwera nie uwzględnia kosztów platformy jako usługi (PaaS) lub oprogramowania jako usługi (SaaS).

- Przejrzyj miesięczny magazyn — szacunki kosztów. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone między różne typy dysków magazynu.
- Przejdź do szczegółów, aby zobaczyć szczegóły dotyczące określonych maszyn wirtualnych.

> [!NOTE]
> Klasyfikacje zaufania nie są przypisywane do ocen serwerów zaimportowanych do oceny serwera przy użyciu woluminu CSV.

## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemu operacyjnego

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A – H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I-R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SYSTEM OPERACYJNY IBM OS/2
   :::column-end:::
   :::column span="":::
      systemu
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S-T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO dla systemu UNIX 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U-Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      System Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      System Windows NT<br/>
      System Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Próg systemu Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Zaimportowane serwery do Azure Migrate: Ocena serwera przy użyciu woluminu CSV.
> * Utworzono i sprawdzono ocenę.

Teraz należy [wdrożyć urządzenie](./migrate-appliance.md) pod kątem dokładniejszych ocen oraz zebrać serwery w grupach w celu uzyskania dokładniejszej oceny przy użyciu [analizy zależności](./concepts-dependency-visualization.md).
