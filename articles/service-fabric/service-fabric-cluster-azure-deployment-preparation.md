---
title: Planowanie wdrożenia klastra Service Fabric platformy Azure
description: Dowiedz się więcej o planowaniu i przygotowaniu dla środowiska produkcyjnego wdrożenia klastra Service Fabric na platformie Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 32d48f9ffa056d252bdf762304340f245d80fd26
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834454"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planowanie i przygotowywanie wdrożenia klastra

Planowanie i przygotowywanie wdrożenia klastra produkcyjnego jest bardzo ważne.  Istnieje wiele czynników, które należy wziąć pod uwagę.  W tym artykule przedstawiono kroki przygotowywania wdrożenia klastra.

## <a name="read-the-best-practices-information"></a>Przeczytaj informacje o najlepszych rozwiązaniach
Aby pomyślnie zarządzać aplikacjami i klastrami platformy Azure Service Fabric, istnieją operacje, dla których zdecydowanie zalecamy przeprowadzenie optymalizacji niezawodności środowiska produkcyjnego.  Aby uzyskać więcej informacji, Przeczytaj [Service Fabric najlepszych rozwiązań dotyczących aplikacji i klastrów](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Wybierz system operacyjny dla klastra
Service Fabric umożliwia tworzenie klastrów Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux.  Przed wdrożeniem klastra należy wybrać system operacyjny: Windows lub Linux.  W każdym węźle (maszynie wirtualnej) w klastrze działa ten sam system operacyjny, nie można mieszać maszyn wirtualnych z systemami Windows i Linux w tym samym klastrze.

## <a name="capacity-planning"></a>Planowanie pojemności
W przypadku każdego wdrożenia produkcyjnego planowanie wydajności jest ważnym krokiem. Poniżej przedstawiono niektóre zagadnienia, które należy uwzględnić w ramach tego procesu.

* Początkowa liczba typów węzłów klastra 
* Właściwości każdego typu węzła (rozmiar, liczba wystąpień, podstawowe, połączenie z Internetem, liczba maszyn wirtualnych itp.)
* Charakterystyka niezawodności i trwałości klastra

### <a name="select-the-initial-number-of-node-types"></a>Wybierz początkową liczbę typów węzłów
Najpierw należy ustalić, co tworzony klaster ma być używany dla programu. Jakiego rodzaju aplikacje są planowane do wdrożenia w tym klastrze? Czy Twoja aplikacja ma wiele usług i czy wszystkie z nich muszą być dostępne publicznie czy z Internetu? Czy usługi (które tworzą aplikację) mają różne potrzeby związane z infrastrukturą, takie jak większa ilość pamięci RAM czy więcej cykli procesora CPU? Klaster Service Fabric może składać się z więcej niż jednego typu węzła: podstawowego typu węzła i jednego lub więcej typów węzłów innych niż podstawowe. Każdy typ węzła jest mapowany na zestaw skalowania maszyn wirtualnych. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. [Właściwości węzła i ograniczenia umieszczania][placementconstraints] można skonfigurować w celu ograniczenia określonych usług do określonych typów węzłów.  Aby uzyskać więcej informacji, zapoznaj [się z liczbą typów węzłów, których klaster potrzebuje do uruchomienia](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Wybierz właściwości węzła dla każdego typu węzła
Typy węzłów definiują jednostkę SKU, liczbę i właściwości maszyn wirtualnych w skojarzonym zestawie skalowania.

Minimalny rozmiar maszyn wirtualnych dla każdego typu węzła jest określany na podstawie wybranej [warstwy trwałości][durability] dla typu węzła.

Minimalna liczba maszyn wirtualnych dla typu węzła podstawowego jest określana na podstawie wybranej [warstwy niezawodności][reliability] .

Zapoznaj się z minimalnymi zaleceniami dotyczącymi [typów węzłów podstawowych](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [obciążeń stanowych dla typów węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)i [bezstanowych obciążeń dla typów węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Każda większa niż minimalna liczba węzłów powinna być oparta na liczbie replik aplikacji/usług, które mają być uruchamiane w tym typie węzła.  [Planowanie wydajności aplikacji Service Fabric](service-fabric-capacity-planning.md) ułatwia oszacowanie zasobów potrzebnych do uruchamiania aplikacji. Klaster można zawsze skalować w górę lub w dół w celu dostosowania się do zmieniających się obciążeń aplikacji. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Używanie tymczasowych dysków systemu operacyjnego dla zestawów skalowania maszyn wirtualnych

*Dyski tymczasowe systemu operacyjnego* są magazynem utworzonym na lokalnej maszynie wirtualnej i nie zostały zapisane w zdalnej usłudze Azure Storage. Są one zalecane dla wszystkich typów węzłów Service Fabric (podstawowych i pomocniczych), w porównaniu do tradycyjnych stałych dysków systemu operacyjnego, dysków tymczasowych systemu operacyjnego:

* Zmniejsz opóźnienie odczytu/zapisu na dysku systemu operacyjnego
* Włącz szybsze operacje zarządzania węzłami resetowania/ponownego obrazu
* Obniżenie kosztów ogólnych (dyski są bezpłatne i nie wiążą się z dodatkowym kosztem magazynowania)

Tymczasowe dyski systemu operacyjnego nie są konkretną funkcją Service Fabric, ale raczej funkcją *zestawów skalowania maszyn wirtualnych* platformy Azure, które są mapowane do typów węzłów Service Fabric. Użycie ich z Service Fabric wymaga następujących w szablonie Azure Resource Manager klastra:

1. Upewnij się, że typy węzłów określają [obsługiwane rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/windows/ephemeral-os-disks.md) dla dysków tymczasowych systemu operacyjnego i że rozmiar maszyny wirtualnej ma wystarczający rozmiar pamięci podręcznej, aby zapewnić obsługę rozmiaru dysku systemu operacyjnego (patrz *Uwaga* poniżej). Na przykład:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Pamiętaj, aby wybrać rozmiar maszyny wirtualnej z rozmiarem pamięci podręcznej równym lub większym niż rozmiar dysku systemu operacyjnego maszyny wirtualnej. w przeciwnym razie wdrożenie platformy Azure może spowodować błąd (nawet jeśli jest to początkowo zaakceptowane).

2. Określ wersję zestawu skalowania maszyn wirtualnych (`vmssApiVersion`) `2018-06-01` lub nowszego:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. W sekcji zestaw skalowania maszyn wirtualnych szablonu wdrożenia Określ `Local` opcję dla `diffDiskSettings`:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "vhdContainers": ["[concat(reference(concat('Microsoft.Storage/storageAccounts/', parameters('vmStorageAccountName')), variables('storageApiVersion')).primaryEndpoints.blob, parameters('vmStorageAccountContainerName'))]"],
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Aby uzyskać więcej informacji i więcej opcji konfiguracji, zobacz [dyski tymczasowe systemu operacyjnego dla maszyn wirtualnych platformy Azure](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Wybierz poziomy trwałości i niezawodności dla klastra
Warstwa trwałości służy do wskazywania, że system ma uprawnienia do programu z podstawową infrastrukturą platformy Azure. W przypadku typu węzła podstawowego to uprawnienie umożliwia Service Fabric wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny wirtualnej (takich jak ponowne uruchomienie maszyny wirtualnej, odtwarzanie maszyn wirtualnych lub migracja maszyny wirtualnej), które mają wpływ na wymagania dotyczące kworum usług systemowych i usług stanowych. W przypadku typów węzłów innych niż podstawowe to uprawnienie umożliwia Service Fabric wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny wirtualnej (takich jak ponowne uruchomienie maszyny wirtualnej, odtwarzanie maszyn wirtualnych i migracja maszyn wirtualnych), które mają wpływ na wymagania dotyczące kworum dla usług stanowych.  Aby uzyskać zalety różnych poziomów i zaleceń dotyczących poziomu użycia i czasu, zobacz [charakterystyki trwałości klastra][durability].

Warstwa niezawodności służy do ustawiania liczby replik usług systemowych, które mają być uruchamiane w tym klastrze w typie węzła podstawowego. Im większa liczba replik, tym bardziej niezawodna usługa systemowa znajduje się w klastrze.  Aby uzyskać zalety różnych poziomów i zaleceń dotyczących poziomu użycia i, zobacz [Charakterystyka niezawodności klastra][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Włączanie zwrotnego serwera proxy i/lub systemu DNS
Usługi łączące się ze sobą w klastrze zwykle mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. Aby ułatwić łączenie się między usługami, Service Fabric zapewnia dodatkowe usługi: [usługi DNS](service-fabric-dnsservice.md) i [odwrotnej usługi serwera proxy](service-fabric-reverseproxy.md).  Obie usługi mogą być włączane podczas wdrażania klastra.

Ponieważ wiele usług, szczególnie usługi kontenerowe, może mieć istniejącą nazwę adresu URL, można je rozwiązać przy użyciu standardowego protokołu DNS (zamiast protokołu Usługa nazewnictwa) jest wygodne, szczególnie w scenariuszach aplikacji "Unieś i Shift". Jest to dokładnie działanie usługi DNS. Umożliwia mapowanie nazw DNS na nazwę usługi, a tym samym rozwiązanie adresów IP punktów końcowych.

Zwrotny serwer proxy odnosi się do usług w klastrze, które uwidaczniają punkty końcowe HTTP (w tym HTTPS). Zwrotny serwer proxy znacznie upraszcza wywoływanie innych usług przez podanie określonego formatu identyfikatora URI.  Zwrotny serwer proxy obsługuje również kroki rozwiązywania, nawiązywania połączenia i ponawiania próby wymagane dla jednej usługi do komunikacji z inną.

## <a name="prepare-for-disaster-recovery"></a>Przygotowywanie do odzyskiwania awaryjnego
Krytyczna część dostarczania wysokiej dostępności zapewnia, że usługi mogą przetrwać wszystkie różne typy awarii. Jest to szczególnie ważne w przypadku nieplanowanych awarii i poza formantem. [Przygotowanie do odzyskiwania po awarii](service-fabric-disaster-recovery.md) zawiera opis niektórych typowych trybów awarii, które mogą być awariami, jeśli nie są poprawnie modelowane i zarządzane. Omówiono w nim również środki zaradcze i działania podejmowane w przypadku wystąpienia awarii.

## <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji
Czy Twoja aplikacja i klaster są gotowe do przetworzenia ruchu produkcyjnego? Przed wdrożeniem klastra w środowisku produkcyjnym należy wykonać czynności z [listy kontrolnej gotowości produkcyjnej](service-fabric-production-readiness-checklist.md). Utrzymuj bezproblemowe działanie aplikacji i klastra, wykonując czynności wykonywane przez elementy z tej listy kontrolnej. Zdecydowanie zalecamy, aby wszystkie te elementy były wyszukiwane przed przejściem do środowiska produkcyjnego.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie klastra Service Fabric z systemem Windows](service-fabric-best-practices-overview.md)
* [Tworzenie klastra Service Fabric z systemem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster