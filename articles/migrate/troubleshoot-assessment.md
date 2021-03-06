---
title: Rozwiązywanie problemów dotyczących oceny i wizualizacji zależności w programie Azure Migrate
description: Uzyskaj pomoc dotyczącą oceny rozwiązywania problemów i wizualizacji zależności w programie Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: fb1e32d9f67febb09eadfb5d31221db33504eb05
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289479"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Rozwiązywanie problemów z wizualizacją zależności/oceny

Ten artykuł pomaga w rozwiązywaniu problemów z oceną i wizualizacją zależności przy użyciu [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemy z gotowością do oceny

Rozwiąż problemy z gotowością do oceny w następujący sposób:

**Problem** | **Fix**
--- | ---
Nieobsługiwany typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zalecamy przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Do obsługi migracji takich maszyn wirtualnych można użyć migracji serwera Azure Migrate. Spowoduje to przekonwertowanie typu rozruchowego maszyny wirtualnej na system BIOS podczas migracji.
Warunkowo obsługiwany system operacyjny Windows | System operacyjny przeszedłł datę końca okresu obsłudze i potrzebuje niestandardowej umowy pomocy technicznej (CSA) w celu uzyskania [pomocy technicznej na platformie Azure](https://aka.ms/WSosstatement). Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie.
Nieobsługiwany system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](https://aka.ms/WSosstatement). Rozważ uaktualnienie maszyny przed przeprowadzeniem migracji na platformę Azure.
Warunkowo potwierdzony system operacyjny Linux | Platforma Azure poświadcza tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md). Rozważ uaktualnienie maszyny przed przeprowadzeniem migracji na platformę Azure.
Niepotwierdzony system operacyjny Linux | Maszyna może zaczynać się na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. Przed przeprowadzeniem migracji na platformę Azure Rozważ uaktualnienie do [zatwierdzonej wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) .
Nieznany system operacyjny | System operacyjny maszyny wirtualnej został określony jako "inny" w vCenter Server. To zachowanie uniemożliwia Azure Migrate weryfikacji gotowości maszyny wirtualnej platformy Azure. Przed przeprowadzeniem migracji maszyny upewnij się, że system operacyjny jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwana wersja bitowa | Maszyny wirtualne z 32-bitowymi systemami operacyjnymi mogą przeprowadzić rozruch na platformie Azure, ale zalecamy uaktualnienie do wersji 64-bitowej przed migracją na platformę Azure.
Wymaga subskrypcji Microsoft Visual Studio | Na komputerze jest uruchomiony system operacyjny klienta systemu Windows, który jest obsługiwany tylko przez subskrypcję programu Visual Studio.
Nie znaleziono maszyny wirtualnej wymaganej wydajności magazynu | Wydajność magazynu (operacje wejścia/wyjścia na sekundę [IOPS] i przepływność) wymagane dla maszyny przekraczają obsługę maszyny wirtualnej platformy Azure. Przed migracją Zmniejsz wymagania dotyczące magazynu maszyny.
Nie znaleziono maszyny wirtualnej dla wymaganej wydajności sieci | Wydajność sieci (WE/wychodzącej) wymagana przez maszynę przekracza obsługę maszyny wirtualnej platformy Azure. Zmniejsz wymagania dotyczące sieci dla maszyny.
Nie znaleziono maszyny wirtualnej w określonej lokalizacji | Użyj innej lokalizacji docelowej przed migracją.
Co najmniej jeden niewłaściwy dysk | Co najmniej jeden dysk dołączony do maszyny wirtualnej nie spełnia wymagań platformy Azure. Z<br/><br/> Azure Migrate: Ocena serwera obecnie nie obsługuje dysków SSD w warstwie Ultra i ocenia dyski w oparciu o limity dysków dla dysków zarządzanych w warstwie Premium (32 TB).<br/><br/> Dla każdego dysku podłączonego do maszyny wirtualnej upewnij się, że rozmiar dysku to < 64 TB (obsługiwane przez dyski SSD w warstwie Ultra).<br/><br/> Jeśli tak nie jest, zmniejsz rozmiar dysku przed przeprowadzeniem migracji na platformę Azure lub Użyj wielu dysków [na platformie Azure, aby uzyskać](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) wyższe limity magazynu. Należy upewnić się, że wydajność (IOPS i przepustowość) wymagana przez poszczególne dyski są obsługiwane przez [maszyny wirtualne zarządzane](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)przez platformę Azure.
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją Usuń nieużywane karty sieciowe z maszyny.
Liczba dysków przekracza limit | Usuń nieużywane dyski z maszyny przed migracją.
Rozmiar dysku przekracza limit | Azure Migrate: Ocena serwera obecnie nie obsługuje dysków SSD w warstwie Ultra i ocenia dyski w oparciu o limity dysku Premium (32 TB).<br/><br/> Platforma Azure obsługuje jednak dyski o rozmiarze do 64 TB (obsługiwane przez dyski SSD w warstwie Ultra). Zmniejsz liczbę dysków do mniej niż 64 TB przed migracją lub Użyj wielu [dysków na platformie Azure, aby uzyskać](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) wyższe limity magazynu.
Dysk niedostępny w określonej lokalizacji | Przed przeprowadzeniem migracji upewnij się, że dysk znajduje się w lokalizacji docelowej.
Dysk niedostępny dla określonej nadmiarowości | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny wirtualnej z wymaganymi rdzeniami i pamięcią | Platforma Azure nie może znaleźć odpowiedniego typu maszyny wirtualnej. Przed migracją Zmniejsz ilość pamięci i liczbę rdzeni maszyny lokalnej.
Nie można określić przydatności maszyny wirtualnej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednego dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla co najmniej jednej karty sieciowej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.

## <a name="linux-vms-are-conditionally-ready"></a>Maszyny wirtualne z systemem Linux są "warunkowo gotowe"

Ocena serwera oznacza maszyny wirtualne z systemem Linux jako "warunkowo gotowe" ze względu na znaną przerwę w ocenie serwera.

- Przerwy uniemożliwiają wykrycie pomocniczej wersji systemu operacyjnego Linux zainstalowanej na lokalnych maszynach wirtualnych.
- Na przykład w przypadku RHEL 6,10 obecnie Ocena serwera wykrywa tylko RHEL 6 jako wersję systemu operacyjnego.
-  Ponieważ platforma Azure poświadcza tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako gotowe do oceny serwera.
- Możesz określić, czy system operacyjny Linux uruchomiony na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [Pomoc techniczną platformy Azure](https://aka.ms/migrate/selfhost/azureendorseddistros)w systemie Linux.
-  Po zweryfikowaniu poświadczonej dystrybucji można zignorować to ostrzeżenie.

## <a name="azure-skus-bigger-than-on-premises"></a>Jednostki SKU platformy Azure większe niż lokalne

Ocena serwera Azure Migrate może zalecać jednostki SKU maszyny wirtualnej platformy Azure z większą liczbą rdzeni i pamięci niż bieżąca alokacja lokalna na podstawie typu oceny:


- Zalecenie dotyczące jednostki SKU maszyny wirtualnej zależy od właściwości oceny.
- Ma to wpływ na typ oceny wykonywanej w ocenie serwera: *oparty na wydajności*lub w środowisku *lokalnym*.
- W przypadku ocen opartych na wydajności Ocena serwera traktuje dane użycia lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia odpowiedniej docelowej jednostki SKU maszyny wirtualnej dla lokalnych maszyn wirtualnych. Dodaje również współczynnik komfortu podczas określania efektywnego wykorzystania.
- W przypadku lokalnego określania wielkości dane wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana na podstawie przydziału lokalnego.

Aby pokazać, jak to może mieć wpływ na zalecenia, przyjrzyjmy się przykładowi:

Mamy lokalną maszynę wirtualną z czterema rdzeniami i 8 GB pamięci, z użyciem procesora CPU 50% i 50% wykorzystania pamięci, a określonym czynnikiem komfortu wynoszącym 1,3.

-  Jeśli ocena jest przeprowadzana **lokalnie**, zaleca się używanie jednostki SKU maszyny wirtualnej platformy Azure z czterema rdzeniami i 8 GB pamięci.
- Jeśli ocena jest oparta na wydajności, w oparciu o efektywne wykorzystanie procesora CPU i pamięci (50% z 4 rdzeniami * 1,3 = 2,6 rdzenie i 50% 8 GB pamięci * 1,3 = 5,3 GB pamięci), najtańszą jednostką SKU maszyny wirtualnej czterech rdzeni (najbliższą obsługiwaną liczbą rdzeni) i osiem GB pamięci (najbliższe obsługiwane rozmiar pamięci) jest zalecany.
- [Dowiedz się więcej](concepts-assessment-calculation.md#assessments-in-server-assessment) o ustalaniu wielkości ocen.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Jednostki SKU dysku platformy Azure większe niż lokalne

Ocena serwera Azure Migrate może zalecić większy dysk na podstawie typu oceny.
- Ustalanie rozmiarów dysków w ocenie serwera zależy od dwóch właściwości oceny: ustalania rozmiarów kryteriów i typu magazynu.
- Jeśli kryteria ustalania wielkości są **oparte na wydajności**, a typ magazynu jest ustawiony na wartość **Automatyczne**, operacje we/wy i przepływność dysku są brane pod uwagę podczas identyfikowania docelowego typu dysku (HDD w warstwie Standardowa, SSD w warstwie Standardowa lub Premium). Zalecana jest jednostka SKU dysku z typu dysku, a zalecenie uwzględnia wymagania dotyczące rozmiaru dysku lokalnego.
- Jeśli kryterium ustalania rozmiaru jest **oparte na wydajności**, a typ magazynu to **Premium**, zalecana jest jednostka SKU dysku Premium na platformie Azure na podstawie wymagań operacji we/wy na sekundę, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do przeprowadzania ustalania rozmiarów dysków, gdy kryteria ustalania wielkości są **jako lokalne** , a typ magazynu to **HDD w warstwie Standardowa**, **SSD w warstwie Standardowa**lub **Premium**.

Jeśli na przykład masz dysk lokalny z 32 GB pamięci, ale zagregowane liczby operacji we/wy odczytu i zapisu dla dysku to 800 IOPS, Ocena serwera zaleca dysk w warstwie Premium (z powodu wyższych wymagań IOPS), a następnie zaleca użycie dysku SKU, który może obsługiwać język r wymagane i rozmiar operacji we/wy. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). Mimo że rozmiar wymagany przez dysk lokalny to 32 GB, Ocena serwera zaleca większy dysk z powodu dużego wymagania IOPS dysku lokalnego.

## <a name="utilized-corememory-percentage-missing"></a>Brak użytego procentu rdzenia/pamięci

Raporty oceny serwera "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing", gdy urządzenie Azure Migrate nie może zbierać danych wydajności dla odpowiednich lokalnych maszyn wirtualnych.

- Taka sytuacja może wystąpić, jeśli maszyny wirtualne są wyłączone w czasie trwania oceny. Urządzenie nie może zbierać danych wydajności dla maszyny wirtualnej, gdy jest ona wyłączona.
- Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy na tych maszynach wirtualnych jest włączona pamięć dynamiczna. Istnieje znany problem dotyczący tylko maszyn wirtualnych funkcji Hyper-V, w których urządzenie Azure Migrate nie może zbierać danych użycia pamięci dla maszyn wirtualnych, dla których nie włączono pamięci dynamicznej.
- Jeśli brakuje któregokolwiek z liczników wydajności, Azure Migrate oceny serwera powróci do przyznanych rdzeni i pamięci, a następnie zaleca rozmiar maszyny wirtualnej.
- Jeśli brakuje wszystkich liczników wydajności, upewnij się, że zostały spełnione wymagania dotyczące dostępu do portów dla oceny. Dowiedz się więcej o wymaganiach dostępu do portów dla oprogramowania [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access), [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) i oceny serwera [fizycznego](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) .

## <a name="is-the-operating-system-license-included"></a>Czy uwzględniono licencję systemu operacyjnego?

Ocena serwera Azure Migrate obecnie uwzględnia koszt licencji systemu operacyjnego tylko dla maszyn z systemem Windows. Koszty licencji dla maszyn z systemem Linux nie są obecnie uwzględniane.

## <a name="how-does-performance-based-sizing-work"></a>Jak działa Wymiarowanie oparte na wydajności?

Narzędzie Server Assessment nieprzerwanie zbiera dane wydajności maszyn lokalnych i używa ich do określania zalecanych jednostek SKU maszyn wirtualnych i jednostek SKU dysków na platformie Azure. [Dowiedz się, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) zbierane są dane oparte na wydajności.


## <a name="dependency-visualization-in-azure-government"></a>Wizualizacja zależności w Azure Government

Azure Migrate zależy od Service Map funkcji wizualizacji zależności. Ponieważ Service Map jest obecnie niedostępna w Azure Government, ta funkcja nie jest dostępna w Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Zależności nie są wyświetlane po zainstalowaniu agenta

Po zainstalowaniu agentów wizualizacji zależności na lokalnych maszynach wirtualnych Azure Migrate zwykle trwa 15-30 minut, aby wyświetlić zależności w portalu. Jeśli zaczekasz dłużej niż 30 minut, upewnij się, że Microsoft Monitoring Agent (MMA) może nawiązać połączenie z obszarem roboczym Log Analytics.

Dla maszyn wirtualnych z systemem Windows:
1. W panelu sterowania uruchom MMA.
2. We **właściwościach Microsoft Monitoring Agent** > **Azure log Analytics (OMS)** upewnij się, że **stan** obszaru roboczego to zielony.
3. Jeśli stan nie jest zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do MMA.

    ![Stan MMA](./media/troubleshoot-assessment/mma-properties.png)

W przypadku maszyn wirtualnych z systemem Linux upewnij się, że polecenia instalacji programu MMA i agenta zależności powiodły się.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- **Agent MMS**: Zapoznaj się z obsługiwanymi systemami operacyjnymi [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)i [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) .
- **Agent zależności**: obsługiwane systemy operacyjne [Windows i Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="visualize-dependencies-for--hour"></a>Wizualizuj zależności dla > godziny

Chociaż Azure Migrate pozwala na powrót do określonej daty w ostatnim miesiącu, maksymalny czas trwania wizualizacji zależności wynosi godzinę.

Można na przykład użyć funkcji czas trwania w mapie zależności, aby wyświetlić zależności dla wczoraj, ale można je wyświetlić tylko dla jednego okresu.

Można jednak użyć dzienników Azure Monitor, aby [wykonać zapytanie o dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas.

## <a name="visualized-dependencies-for--10-machines"></a>Wizualne zależności dla maszyn > 10

W ocenie serwera Azure Migrate można [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) z maksymalnie 10 maszynami wirtualnymi. W przypadku większych grup zalecamy dzielenie maszyn wirtualnych na mniejsze grupy w celu wizualizacji zależności.

## <a name="machines-show-install-agent"></a>Na maszynach zostanie wyświetlona wartość "Zainstaluj agenta"

Po przeprowadzeniu migracji maszyn z włączoną wizualizacją zależności na platformę Azure, na maszynach może zostać wyświetlona akcja "Zainstaluj agenta" zamiast "Wyświetl zależności" z powodu następujących zachowań:


- Po migracji na platformę Azure maszyny lokalne są wyłączone i równoważne maszyny wirtualne są na platformie Azure. Te maszyny uzyskują inny adres MAC.
- Maszyny mogą także mieć inny adres IP w zależności od tego, czy lokalny adres IP został zachowany.
- Jeśli adresy MAC i IP różnią się od lokalnych, Azure Migrate nie kojarzy maszyn lokalnych z dowolnymi Service Map danymi zależności. W takim przypadku zostanie wyświetlona opcja instalacji agenta zamiast wyświetlania zależności.
- Po przeprowadzeniu migracji testowej na platformę Azure maszyny lokalne pozostają włączone zgodnie z oczekiwaniami. Równoważne komputery z systemem na platformie Azure uzyskują różne adresy MAC i mogą uzyskiwać różne adresów IP. Jeśli nie zablokujesz wychodzącego ruchu dziennika Azure Monitor z tych maszyn, Azure Migrate nie będzie kojarzyć maszyn lokalnych z dowolnymi Service Map danymi zależności i w ten sposób będzie wyświetlana opcja instalacji agentów, a nie do wyświetlania zależności.


## <a name="capture-network-traffic"></a>Przechwyć ruch sieciowy

Zbierz dzienniki ruchu sieciowego w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Naciśnij klawisz F12, aby rozpocząć Narzędzia deweloperskie. W razie konieczności Wyczyść ustawienie **Wyczyść wpisy przy nawigacji** .
3. Wybierz kartę **Sieć** i Rozpocznij przechwytywanie ruchu sieciowego:
   - W programie Chrome wybierz opcję **Zachowaj dziennik**. Nagrywanie powinno być uruchamiane automatycznie. Czerwony okrąg wskazuje na to, że ruch jest przechwytywany. Jeśli czerwony okrąg nie jest wyświetlany, wybierz czarny okrąg, aby rozpocząć.
   - W programie Microsoft Edge i Internet Explorer nagrywanie powinno być uruchamiane automatycznie. Jeśli tak nie jest, wybierz zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas rejestrowania, zatrzymywania rejestrowania i zapisywania kopii zapisanego działania:
   - W programie Chrome kliknij prawym przyciskiem myszy i wybierz pozycję **Zapisz jako Har z zawartością**. Ta akcja kompresuje i eksportuje dzienniki jako plik. HAR.
   - W przeglądarce Microsoft Edge lub Internet Explorer wybierz opcję **Eksportuj ruch przechwycony** . Ta akcja kompresuje i eksportuje dziennik.
6. Wybierz kartę **konsola** , aby sprawdzić, czy występują ostrzeżenia lub błędy. Aby zapisać dziennik konsoli:
   - W programie Chrome kliknij prawym przyciskiem myszy w dowolnym miejscu w dzienniku konsoli. Wybierz pozycję **Zapisz jako**, aby wyeksportować i zip log.
   - W przeglądarce Microsoft Edge lub Internet Explorer, kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystko**.
7. Zamknij Narzędzia deweloperskie.

## <a name="next-steps"></a>Następne kroki

[Utwórz](how-to-create-assessment.md) lub [Dostosuj](how-to-modify-assessment.md) ocenę.
