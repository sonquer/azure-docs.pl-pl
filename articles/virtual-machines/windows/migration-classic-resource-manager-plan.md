---
title: Planowanie migracji z wersji klasycznej do Azure Resource Manager
description: Planowanie migracji zasobów IaaS z klasycznej do Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 2d7eca56556c8a1097b625b8f4b0e2de2facbc60
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061611"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planowanie migracji zasobów IaaS z klasycznej do Azure Resource Manager
Chociaż Azure Resource Manager oferuje wiele niezwykłych funkcji, ważne jest zaplanowanie podróży migracji, aby upewnić się, że elementy są bezproblemowo. Czas wydatków na planowanie zapewnia niespotykanie problemów podczas wykonywania działań migracji.

Istnieją cztery ogólne etapy podróży migracji:<br>

![Etapy migracji](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planowanie

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i kompromisy

W zależności od wielkości wymagań technicznych, lokalizacje geograficzne i praktyk operacyjnych warto rozważyć następujące kwestie:

1. Dlaczego Azure Resource Manager być wymagana w organizacji?  Jakie są przyczyny biznesowe migracji?
2. Jakie są przyczyny techniczne Azure Resource Manager?  Co (jeśli istnieją) dodatkowe usługi platformy Azure chcesz wykorzystać?
3. Która aplikacja (lub zestawy maszyn wirtualnych) jest dołączona do migracji?
4. Które scenariusze są obsługiwane przez interfejs API migracji?  Zapoznaj się z [nieobsługiwanymi funkcjami i konfiguracjami](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Czy zespoły operacyjne będą teraz obsługiwać aplikacje/maszyny wirtualne w klasycznym i Azure Resource Manager?
6. Jak (Jeśli w ogóle) Azure Resource Manager zmieniać procesów wdrożenia maszyny wirtualnej, zarządzania, monitorowania i raportowania?  Czy należy zaktualizować skrypty wdrażania?
7. Co to jest plan komunikacji dotyczący zgłaszania udziałowców (użytkowników końcowych, właścicieli aplikacji i właścicieli infrastruktury)?
8. W zależności od stopnia złożoności środowiska, w przypadku, gdy aplikacja jest niedostępna dla użytkowników końcowych i właścicieli aplikacji?  Jeśli tak, na czas?
9. Co to jest plan szkoleniowy, aby upewnić się, że zainteresowane strony są merytoryczne i bardziej pomocne w Azure Resource Manager?
10. Co to jest zarządzanie programem lub plan zarządzania projektami migracji?
11. Jakie są osie czasu dla migracji Azure Resource Manager i innych powiązanych map dróg technologicznych?  Czy są one optymalnie wyrównane?

### <a name="patterns-of-success"></a>Wzorce sukcesu

Pomyślni klienci mają szczegółowe plany, w których omawiane są opisane powyżej pytania, udokumentowane i regulowane.  Upewnij się, że plany migracji są szeroko przekazywane do sponsorów i udziałowców.  Wyposażaj się w wiedzę na temat opcji migracji; Więcej informacji na temat tego zestawu dokumentów migracji poniżej jest zdecydowanie zalecane.

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Pułapek, aby uniknąć

- Niepowodzenie planu.  Kroki technologiczne tej migracji są sprawdzone, a wyniki są przewidywalne.
- Założenie, że interfejs API migracji obsługiwanej przez platformę będzie uwzględniać wszystkie scenariusze. Zapoznaj się z [nieobsługiwanymi funkcjami i konfiguracjami](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) , aby zrozumieć, jakie scenariusze są obsługiwane.
- Nie należy planować potencjalnej awarii aplikacji dla użytkowników końcowych.  Zaplanuj wystarczającą liczbę buforów, aby odpowiednio ostrzec użytkowników końcowych o potencjalnie niedostępnym czasie aplikacji.


## <a name="lab-test"></a>Test laboratoryjny

**Replikowanie środowiska i przeprowadzenie migracji testowej**
  > [!NOTE]
  > Dokładna replikacja istniejącego środowiska jest wykonywana przy użyciu narzędzia współtworzonego przez społeczność, które nie jest oficjalnie obsługiwane przez pomoc techniczna firmy Microsoft. W związku z tym jest to krok **opcjonalny** , ale najlepszym sposobem na znalezienie problemów bez dotykania środowisk produkcyjnych. W przypadku korzystania z narzędzia współtworzonego przez społeczność nie jest to opcja, Przeczytaj o zaleceniu Validate/Prepare/Abort.
  >

  Przeprowadzenie testu laboratoryjnego dla dokładnego scenariusza (obliczeń, sieci i magazynu) jest najlepszym sposobem na zapewnienie płynnej migracji. Dzięki temu:

- Całkowicie oddzielne laboratorium lub istniejące nieprodukcyjne środowisko do przetestowania. Zalecamy przeprowadzenie wielokrotnego migrowania w całości i można je zmodyfikować.  Poniżej znajdują się skrypty zbierające/wodne metadanych z prawdziwych subskrypcji.
- Dobrym pomysłem jest utworzenie laboratorium w oddzielnej subskrypcji. Przyczyną jest to, że laboratorium zostanie kilkukrotnie rozłączone i będzie mieć oddzielną, odizolowaną subskrypcję, która zmniejszy prawdopodobieństwo, że coś prawdziwego zostanie przypadkowo usunięte.

  Można to osiągnąć za pomocą narzędzia AsmMetadataParser. [Więcej informacji o tym narzędziu można znaleźć tutaj](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Wzorce sukcesu

W wielu większych migracjach wykryto następujące problemy. Nie jest to pełna lista i należy zapoznać się z [nieobsługiwanymi funkcjami i konfiguracjami](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) , aby uzyskać więcej szczegółów.  Te problemy techniczne mogą wystąpić lub mogą nie występować, ale w przypadku ich rozwiązania przed podjęciem próby migracji zapewni to bezproblemowe działanie.

- **Wykonaj walidację/przygotowanie/przerwanie przebiegu suchego** — jest to najważniejszy krok, aby upewnić się, że klasyczne Azure Resource Manager migracji zakończyło się pomyślnie. Interfejs API migracji ma trzy główne kroki: Weryfikuj, przygotuj i zatwierdź. Sprawdzanie poprawności spowoduje odczytanie stanu środowiska klasycznego i zwrócenie wyniku wszystkich problemów. Ponieważ jednak niektóre problemy mogą występować w stosie Azure Resource Manager, walidacja nie będzie przechwytywać wszystkiego. Następnym krokiem w procesie migracji, przygotowanie pomoże Ci uwidocznić te problemy. Przygotuj spowoduje przeniesienie metadanych z klasycznego do Azure Resource Manager, ale nie spowoduje to zatwierdzeń przenoszenia i nie spowoduje usunięcia ani zmiany elementów po stronie klasycznej. Przebieg suchy obejmuje przygotowywanie migracji, a następnie przerywanie (**bez zatwierdzania**) przygotowania do migracji. Celem sprawdzenia poprawności/przygotowania/przerwania przebiegu suchego jest wyświetlenie wszystkich metadanych w stosie Azure Resource Manager, sprawdzenie go (*programowo lub w portalu*) i sprawdzenie, czy wszystko jest poprawnie migrowane i działa przez problemy techniczne.  Zapewni to również czas trwania migracji, aby można było odpowiednio zaplanować przestoje.  Weryfikacja/przygotowanie/przerwanie nie powoduje przestoju użytkownika; w związku z tym nie zakłóca użycia aplikacji.
  - Poniższe elementy będą musiały zostać rozwiązane przed uruchomieniem suchego, ale przeprowadzony test przebiegu będzie również bezpiecznie opróżniał te kroki przygotowania, jeśli zostaną pominięte. Podczas migracji przedsiębiorstwa znaleźliśmy suchy przebieg, który jest bezpiecznym i niecennym sposobem zapewnienia gotowości do migracji.
  - Gdy jest uruchomiona Metoda przygotowywania, płaszczyzna kontroli (operacje zarządzania platformy Azure) zostanie zablokowana dla całej sieci wirtualnej, dlatego nie można wprowadzać zmian w metadanych maszyny wirtualnej podczas walidacji/przygotowywania/przerywania.  Ale w przeciwnym razie nie wpłynie to na żadną funkcję aplikacji (RD, Usage VM itp.).  Użytkownicy maszyn wirtualnych nie będą wiedzieli, że przebiegi suche są wykonywane.

- **Obwody usługi Express Route i sieci VPN**. Obecnie nie można migrować bram Express Gateway z linkami autoryzacji bez przestojów. Aby obejść ten temat, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania do Menedżer zasobów](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozszerzenia** maszyn wirtualnych — rozszerzenia maszyny wirtualnej mogą być jednym z największych przeszkody do migracji uruchomionych maszyn wirtualnych. Korygowanie rozszerzeń maszyn wirtualnych może potrwać do 1-2 dni, więc należy odpowiednio zaplanować.  Działający Agent platformy Azure jest wymagany do raportowania stanu rozszerzenia maszyny wirtualnej z uruchomionymi maszynami wirtualnymi. Jeśli stan jest nieprawidłowy dla uruchomionej maszyny wirtualnej, spowoduje to zatrzymanie migracji. Sam Agent nie musi znajdować się w kolejności roboczej, aby umożliwić migrację, ale jeśli rozszerzenia istnieją na maszynie wirtualnej, do przechodzenia do przodu będzie potrzebna zarówno Agent roboczy, jak i wychodzące połączenie internetowe (z systemem DNS).
  - Jeśli podczas migracji nastąpi utrata łączności z serwerem DNS, wszystkie rozszerzenia maszyn wirtualnych z wyjątkiem BGInfo w wersji 1.\* należy najpierw usunąć z każdej maszyny wirtualnej przed przygotowaniem migracji, a następnie ponownie dodać z powrotem do maszyny wirtualnej po przeprowadzeniu migracji Azure Resource Manager.  **Jest to tylko w przypadku maszyn wirtualnych, na których działa program.**  Jeśli maszyny wirtualne zostaną zatrzymane bez przydziału, nie trzeba usuwać rozszerzeń maszyn wirtualnych.

  > [!NOTE]
  > Wiele rozszerzeń, takich jak diagnostyka platformy Azure i monitorowanie usługi Security Center, zostanie ponownie zainstalowane po migracji, więc usunięcie ich nie jest problemem.

  - Ponadto upewnij się, że sieciowe grupy zabezpieczeń nie ograniczają dostępu do Internetu. Może się to zdarzyć w przypadku niektórych konfiguracji sieciowych grup zabezpieczeń. Aby rozszerzenia maszyny wirtualnej zostały zmigrowane do Azure Resource Manager, wymagany jest wychodzący dostęp do Internetu (i system DNS).
  - Istnieją dwie wersje rozszerzenia BGInfo i są one nazywane wersjami 1 i 2.  

      - Jeśli maszyna wirtualna używa rozszerzenia BGInfo w wersji 1, można pozostawić to rozszerzenie jako. Interfejs API migracji pomija to rozszerzenie. Rozszerzenie BGInfo można dodać po migracji.
      - Jeśli maszyna wirtualna używa rozszerzenia BGInfo w wersji 2 opartej na notacji JSON, maszyna wirtualna została utworzona przy użyciu Azure Portal. Interfejs API migracji dołącza to rozszerzenie do Azure Resource Manager, pod warunkiem, że agent działa i ma wychodzący dostęp do Internetu (i system DNS).

  - **Opcja korygowania 1**. Jeśli wiesz, że maszyny wirtualne nie będą miały wychodzącego dostępu do Internetu, działającej usługi DNS i działającego agentów platformy Azure na maszynach wirtualnych, Odinstaluj wszystkie rozszerzenia maszyn wirtualnych w ramach migracji przed przygotowaniem, a następnie zainstaluj ponownie rozszerzenia maszyn wirtualnych po migracji.
  - **Opcja korygowania 2**. Jeśli rozszerzenia maszyn wirtualnych są zbyt duże dla progu, kolejną opcją jest zamknięcie/Cofnięcie alokacji wszystkich maszyn wirtualnych przed migracją. Przeprowadź migrację cofniętych maszyn wirtualnych, a następnie uruchom je ponownie po stronie Azure Resource Manager. Korzyścią jest to, że rozszerzenia maszyn wirtualnych zostaną zmigrowane. Minusem polega na tym, że wszystkie publiczne wirtualne adresy IP zostaną utracone (może to być niestarter), a oczywiście maszyny wirtualne zostaną zamknięte, powodując znacznie większy wpływ na działanie aplikacji.

    > [!NOTE]
    > Jeśli zasady Azure Security Center są skonfigurowane dla migracji uruchomionych maszyn wirtualnych, zasady zabezpieczeń muszą zostać zatrzymane przed usunięciem rozszerzeń. w przeciwnym razie rozszerzenie monitorowania zabezpieczeń zostanie automatycznie zainstalowane na maszynie wirtualnej po jej usunięciu.

- **Zestawy dostępności** — dla sieci wirtualnej (vNet), które mają zostać zmigrowane do Azure Resource Manager, wdrożenie klasyczne (tj. usługa w chmurze) zawiera wszystkie maszyny wirtualne muszą znajdować się w jednym zestawie dostępności lub wszystkie maszyny wirtualne nie muszą znajdować się w żadnym zestawie dostępności. Posiadanie więcej niż jednego zestawu dostępności w usłudze w chmurze jest niezgodne z usługą Azure Resource Manager i wstrzymanie migracji.  Ponadto nie może istnieć kilka maszyn wirtualnych w zestawie dostępności i niektóre maszyny wirtualne, które nie znajdują się w zestawie dostępności. Aby rozwiązać ten problem, należy skorygować lub ponownie rozprowadzić usługę w chmurze.  Zaplanuj odpowiednio, ponieważ może to być czasochłonne.

- **Wdrożenia roli sieci Web/procesu roboczego** — Cloud Services zawierające role sieci Web i procesu roboczego nie mogą migrować do Azure Resource Manager. Aby przeprowadzić migrację zawartości ról sieci Web i procesów roboczych, należy przeprowadzić migrację samego kodu do nowszej PaaS App Services (ta dyskusja jest poza zakresem tego dokumentu). Jeśli chcesz pozostawić role sieci Web/proces roboczy jako, ale Przeprowadź migrację klasycznych maszyn wirtualnych do modelu wdrażania Menedżer zasobów, należy najpierw usunąć role sieci Web/proces roboczy z sieci wirtualnej, aby można było rozpocząć migrację.  Typowym rozwiązaniem jest tylko przeniesienie wystąpień roli sieci Web/procesu roboczego do oddzielnej klasycznej sieci wirtualnej, która jest również połączona z obwodem ExpressRoute. W poprzednim przypadku ponownego wdrożenia Utwórz nową klasyczną sieć wirtualną, Przenieś/Wdróż ponownie role sieci Web/procesu roboczego do nowej sieci wirtualnej, a następnie usuń wdrożenia z przenoszonej sieci wirtualnej. Nie są wymagane żadne zmiany w kodzie. Nowe [Virtual Network możliwości komunikacji równorzędnej](../../virtual-network/virtual-network-peering-overview.md) mogą być używane do komunikacji równorzędnej z klasyczną siecią wirtualną zawierającą role sieci Web/procesu roboczego i inne sieci wirtualne w tym samym regionie świadczenia usługi Azure, na przykład w przypadku migrowania sieci wirtualnej (**po zakończeniu migracji sieci wirtualnej, gdy nie można migrować równorzędnych sieci wirtualnych**), co zapewnia te same możliwości bez utraty wydajności i bez opóźnień i przepustowości. Po dodaniu [komunikacji równorzędnej Virtual Network](../../virtual-network/virtual-network-peering-overview.md)wdrożenia roli sieć Web/proces roboczy można teraz łatwo ograniczyć i nie zablokować migracji do Azure Resource Manager.

- **Przydziały Azure Resource Manager** — regiony platformy Azure mają oddzielne limity przydziału/ograniczenia zarówno dla klasycznych, jak i Azure Resource Manager. Mimo że w scenariuszu migracji nowy sprzęt nie jest używany *(zamieniamy istniejące maszyny wirtualne z klasycznej do Azure Resource Manager)* , przydziały Azure Resource Manager nadal muszą mieć wystarczającą ilość miejsca, zanim będzie można rozpocząć migrację. Poniżej wymieniono główne limity, które wystąpiły problemy.  Otwórz bilet pomocy technicznej dotyczącej limitu przydziału, aby zgłosić limity.

    > [!NOTE]
    > Te limity muszą zostać zgłoszone w tym samym regionie co bieżące środowisko do migracji.
    >

  - Interfejsy sieciowe
  - Moduły równoważenia obciążenia
  - Publiczne adresy IP
  - Statyczne publiczne adresy IP
  - Rdzenie
  - Grupy zabezpieczeń sieci
  - Tabele tras

    Bieżące przydziały Azure Resource Manager można sprawdzić przy użyciu następujących poleceń z najnowszą wersją Azure PowerShell.



    **Obliczenia** *(rdzenie, zestawy dostępności)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Sieć** *(sieci wirtualne, statyczne publiczne adresy IP, publiczne adresy IP, sieciowe grupy zabezpieczeń, interfejsy sieciowe, moduły równoważenia obciążenia, tabele tras)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Magazyn** *(konto magazynu)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Limity ograniczania interfejsu API Azure Resource Manager** — Jeśli masz wystarczająco dużo środowiska (np.). > maszyny wirtualne 400 w sieci wirtualnej), można napotkać domyślne limity ograniczania liczby interfejsów API dla operacji zapisu (obecnie `1200 writes/hour`) w Azure Resource Manager. Przed rozpoczęciem migracji należy zgłosić bilet pomocy technicznej w celu zwiększenia tego limitu dla subskrypcji.


- **Przekroczono limit czasu inicjowania obsługi administracyjnej stanu maszyny wirtualnej** — Jeśli dowolna maszyna wirtualna ma stan `provisioning timed out`, należy ją rozwiązać przed migracją. Jedynym sposobem, aby to zrobić, jest wyłączenie aprowizacji lub ponowne Inicjowanie obsługi administracyjnej maszyny wirtualnej (usuń ją, Zachowaj dysk i Utwórz ponownie maszynę wirtualną).

- **Stan maszyny wirtualnej RoleStateUnknown** — jeśli migracja zostanie zatrzymana ze względu na komunikat o błędzie `role state unknown`, ZBADAj maszynę wirtualną za pomocą portalu i upewnij się, że jest uruchomiona. Ten błąd zwykle oddala się od siebie (bez konieczności korygowania) po kilku minutach i jest często typem przejściowym często widocznym podczas `start`maszyny wirtualnej, `stop``restart` operacji. **Zalecane rozwiązanie:** ponownie spróbuj przeprowadzić migrację po kilku minutach.

- **Klaster sieci szkieletowej nie istnieje** — w niektórych przypadkach nie można migrować niektórych maszyn wirtualnych z różnych przyczyn nieparzystych. Jednym z tych znanych przypadków jest to, że maszyna wirtualna została niedawno utworzona (w ciągu ostatniego tygodnia lub tak) i wystąpiła w przypadku klastra platformy Azure, który nie jest jeszcze wyposażony dla obciążeń Azure Resource Manager.  Zostanie wyświetlony komunikat o błędzie z informacją `fabric cluster does not exist` i nie można migrować maszyny wirtualnej. Oczekiwanie na kilka dni będzie zazwyczaj rozwiązywać ten konkretny problem, ponieważ klaster wkrótce uzyska Azure Resource Manager włączony. Jednak natychmiastowe obejście ma na celu `stop-deallocate` maszyny wirtualnej, a następnie kontynuowanie migracji i rozpoczęcie tworzenia kopii zapasowej maszyny wirtualnej w Azure Resource Manager po migracji.

### <a name="pitfalls-to-avoid"></a>Pułapek, aby uniknąć

- Nie wykonuj skrótów i Pomijaj migracje z przeprowadzeniem walidacji/przygotowania/przerwania.
- Większość, jeśli nie wszystkie, potencjalne problemy pojawią się podczas kroków walidacji/przygotowania/przerwania.

## <a name="migration"></a>Migracja

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i kompromisy

Teraz możesz przystąpić do pracy przez znane problemy z Twoim środowiskiem.

W przypadku rzeczywistych migracji warto rozważyć następujące kwestie:

1. Planowanie i planowanie sieci wirtualnej (najmniejsza jednostka migracji) z rosnącym priorytetem.  Najpierw należy wykonać proste sieci wirtualne i postępować z bardziej skomplikowanymi sieciami wirtualnymi.
2. Większość klientów będzie miała środowiska nieprodukcyjne i produkcyjne.  Utwórz plan produkcji jako ostatni.
3. **(Opcjonalnie)** Zaplanuj przestoje konserwacji z dużą ilością buforu w przypadku wystąpienia nieoczekiwanych problemów.
4. Komunikuj się z zespołami pomocy technicznej i wyrównuje je w razie wystąpienia problemów.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Wskazówki techniczne z sekcji _test laboratoryjny_ należy rozważyć i wyeliminować przed rzeczywistą migracją.  W przypadku odpowiedniego testowania migracja jest w rzeczywistości niezdarzeń.  W przypadku środowisk produkcyjnych warto mieć dodatkową pomoc techniczną, taką jak zaufany partner firmy Microsoft lub usługi Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Pułapek, aby uniknąć

Nie w pełni testowanie może powodować problemy i opóźnienia migracji.  

## <a name="beyond-migration"></a>Poza migracją

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i kompromisy

Teraz, gdy jesteś w Azure Resource Manager, zmaksymalizuj platformę.  Zapoznaj się z [omówieniem Azure Resource Manager](../../azure-resource-manager/management/overview.md) , aby dowiedzieć się więcej o dodatkowych korzyściach.

Zagadnienia, które należy wziąć pod uwagę:

- Zgrupowanie migracji z innymi działaniami.  Większość klientów wybiera dla okna obsługi aplikacji.  Jeśli tak, możesz użyć tego przestoju, aby umożliwić innym Azure Resource Manager możliwości, takie jak szyfrowanie i migracja do Managed Disks.
- Należy ponownie odwiedzić przyczyny techniczne i biznesowe Azure Resource Manager; Włącz dodatkowe usługi dostępne tylko dla Azure Resource Manager, które mają zastosowanie do danego środowiska.
- Modernizacja środowiska za pomocą usług PaaS Services.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Nigdy wykonywane celowo usługi, które chcesz teraz włączyć w Azure Resource Manager.  Wielu klientów znalazł poniżej atrakcyjny dla środowisk platformy Azure:

- [Access Control oparte na rolach](../../role-based-access-control/overview.md).
- [Azure Resource Manager szablonów dla łatwiejszego i bardziej kontrolowanego wdrożenia](../../azure-resource-manager/templates/overview.md).
- [Tagi](../../azure-resource-manager/management/tag-resources.md).
- [Kontrolka działania](../../azure-resource-manager/management/view-activity-logs.md)
- [Zasady platformy Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Pułapek, aby uniknąć

Należy pamiętać, że ten klasyczny został uruchomiony w celu Azure Resource Manager podróży migracji.  Jakie były oryginalne przyczyny biznesowe? Czy osiągnięto przyczynę biznesową?


## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway klasyczny do Menedżer zasobów migracji](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migruj obwody usługi ExpressRoute i skojarzone sieci wirtualne z klasycznego modelu wdrażania Menedżer zasobów](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
