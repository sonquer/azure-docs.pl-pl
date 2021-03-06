---
title: Włączanie Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — Omówienie | Microsoft Docs
description: Dowiedz się, jak wdrażać i konfigurować Azure Monitor dla maszyn wirtualnych. Zapoznaj się z wymaganiami systemowymi.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: ce95223e9501ad7bec2bd260a8fe6f1537db5593
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400616"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Włączanie Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) — Omówienie

Ten artykuł zawiera omówienie opcji dostępnych do skonfigurowania Azure Monitor dla maszyn wirtualnych. Użyj Azure Monitor dla maszyn wirtualnych do monitorowania kondycji i wydajności. Wykrywaj zależności aplikacji, które są uruchamiane na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych, lokalnych maszynach wirtualnych lub maszynach wirtualnych hostowanych w innym środowisku chmury.  

Aby skonfigurować Azure Monitor dla maszyn wirtualnych:

* Włącz pojedynczą maszynę wirtualną platformy Azure lub zestaw skalowania maszyn wirtualnych, wybierając pozycję **Insights (wersja zapoznawcza)** bezpośrednio z poziomu maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.
* Włącz co najmniej dwie maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych za pomocą Azure Policy. Ta metoda zapewnia, że dla istniejących i nowych maszyn wirtualnych i zestawów skalowania wymagane zależności są zainstalowane i poprawnie skonfigurowane. Zgłoszono niezgodne maszyny wirtualne i zestawy skalowania, dzięki czemu można zdecydować, czy włączyć je i skorygować.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure lub na maszynie wirtualnej skalować zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.
* Umożliwia Azure Monitor dla maszyn wirtualnych monitorowania maszyn wirtualnych lub komputerów fizycznych hostowanych w sieci firmowej lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz, informacje w poniższych sekcjach. 

>[!NOTE]
>Poniższe informacje opisane w tej sekcji dotyczą również [rozwiązania Service map](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor dla maszyn wirtualnych obsługuje obszar roboczy Log Analytics w następujących regionach:

- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Południowo-środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowe stany USA
- Północno-środkowe stany USA
- Kanada Środkowa
- Południowe Zjednoczone Królestwo
- Europa Północna
- Europa Zachodnia
- Azja Wschodnia
- Azja Południowo-Wschodnia
- Indie Środkowe
- Japonia Wschodnia
- Australia Wschodnia
- Australia Południowo-Wschodnia

>[!NOTE]
>Maszyny wirtualne platformy Azure można wdrażać z dowolnego regionu. Te maszyny wirtualne nie są ograniczone do regionów obsługiwanych przez obszar roboczy Log Analytics.
>

Jeśli nie masz obszaru roboczego, możesz go utworzyć przy użyciu jednego z następujących zasobów:
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Program PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Witryna Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Możesz również utworzyć obszar roboczy podczas włączania monitorowania dla jednej maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych w Azure Portal.

Aby skonfigurować scenariusz skalowania w poziomie, który używa szablonów Azure Policy, Azure PowerShell lub Azure Resource Manager, w obszarze roboczym Log Analytics:

* Zainstaluj ServiceMap i InfrastructureInsights rozwiązań. Instalację można wykonać przy użyciu dostarczonego szablonu Azure Resource Manager. Na karcie **wprowadzenie** wybierz pozycję **Konfiguruj obszar roboczy**.
* Konfiguruj obszar roboczy usługi Log Analytics, można zebrać liczników wydajności.

Aby skonfigurować obszar roboczy na potrzeby scenariusza w skali, użyj jednej z następujących metod:

* Użyj [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na stronie [**pokrycie zasad**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) Azure monitor dla maszyn wirtualnych wybierz pozycję **Konfiguruj obszar roboczy**. 

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux obsługiwane przez Azure Monitor dla maszyn wirtualnych. W dalszej części tej sekcji znajdziesz pełną listę zawierającą szczegóły głównej i pomocniczej wersji systemu operacyjnego Linux oraz obsługiwane wersje jądra.

|Wersja systemu operacyjnego |Wydajność |Mapy |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|System Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 w wersji 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 z dodatkiem SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko z usługi Azure Monitor. Nie jest ona dostępna bezpośrednio z okienka po lewej stronie maszyny wirtualnej platformy Azure.

>[!NOTE]
>W systemie operacyjnym Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku dowolnej dystrybucji systemu Linux. Na przykład system z ciągu wersji *2.6.16.21-0.8-xen* nie jest obsługiwane.
> - Niestandardowe jądra, w tym ponowne kompilacje standardowych jądra, nie są obsługiwane.
> - CentOSPlus jądro jest obsługiwane.
> - Dla luki w zabezpieczeniach Spectre należy zainstalować jądro systemu Linux. Aby uzyskać więcej informacji, skontaktuj się z dostawcą dystrybucji systemu Linux.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.10 | 2.6.32 — 754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 18,04 | 5,0 (obejmuje jądro dostosowane do platformy Azure)<br>4,18 *<br>4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
|12 SP4 | 4,12. * (obejmuje jądro dostosowane do platformy Azure) |
|12 Z DODATKIEM SP3 | 4.4. * |
|12 Z DODATKIEM SP2 | 4.4. * |

#### <a name="debian"></a>Debian 

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>Program Microsoft Dependency agent

Funkcja map w Azure Monitor dla maszyn wirtualnych pobiera swoje dane z programu Microsoft Dependency Agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. Dlatego w systemie musi być zainstalowany i skonfigurowany Agent Log Analytics przy użyciu agenta zależności.

Niezależnie od tego, czy włączysz Azure Monitor dla maszyn wirtualnych dla jednej maszyny wirtualnej platformy Azure, czy korzystasz z metody wdrażania na skalę, użyj rozszerzenia agenta zależności maszyny wirtualnej platformy Azure dla [systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md) lub [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) , aby zainstalować agenta w ramach środowiska.

>[!NOTE]
>Poniższe informacje opisane w tej sekcji dotyczą również [rozwiązania Service map](service-map.md).  

W środowisku hybrydowym można pobrać i zainstalować agenta zależności ręcznie lub przy użyciu metody zautomatyzowanej.

W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Tak | Wraz z [agentem log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md)agenci systemu Windows potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Tak | Wraz z [agentem log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md)agenci systemu Linux potrzebują agenta zależności. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Agenta zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby włączyć funkcje w Azure Monitor dla maszyn wirtualnych i uzyskać do nich dostęp, musisz mieć rolę *współautora log Analytics* . Aby wyświetlić dane dotyczące wydajności, kondycji i mapy, musisz mieć rolę *czytnika monitorowania* dla maszyny wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics, musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Jak włączyć Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Włącz Azure Monitor dla maszyn wirtualnych przy użyciu jednej z metod opisanych w tej tabeli:

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Pojedyncza maszyna wirtualna platformy Azure lub zestaw skalowania maszyn wirtualnych | [Włącz z poziomu maszyny wirtualnej](vminsights-enable-single-vm.md) | Pojedynczą maszynę wirtualną platformy Azure można włączyć, wybierając pozycję **Insights (wersja zapoznawcza)** bezpośrednio z poziomu maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. |
| Wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych | [Włącz przez Azure Policy](vminsights-enable-at-scale-policy.md) | Możesz włączyć wiele maszyn wirtualnych platformy Azure przy użyciu Azure Policy i dostępnych definicji zasad. |
| Wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych | [Włącz za poorednictwem szablonów Azure PowerShell lub Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Za pomocą szablonów Azure PowerShell lub Azure Resource Manager można włączyć wiele maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych w ramach określonej subskrypcji lub grupy zasobów. |
| Chmura hybrydowa | [Włącz dla środowiska hybrydowego](vminsights-enable-hybrid-cloud.md) | Można je wdrożyć na maszynach wirtualnych lub komputerach fizycznych hostowanych w centrum danych lub w innych środowiskach chmurowych. |

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone 

Azure Monitor dla maszyn wirtualnych konfiguruje Log Analytics obszaru roboczego do zbierania liczników wydajności, z których korzysta. W poniższej tabeli wymieniono obiekty i liczniki, które są zbierane co 60 sekund.

>[!NOTE]
>Poniższa lista liczników wydajności włączonych przez Azure Monitor dla maszyn wirtualnych nie ogranicza możliwości włączania dodatkowych liczników potrzebnych do zbierania danych z maszyn wirtualnych, które są raportowane do obszaru roboczego. Ponadto, Jeśli wyłączysz te liczniki, uniemożliwi to zestaw wykresów wydajności dołączonych do funkcji wydajności, które pokazują wykorzystanie zasobów z maszyn wirtualnych.

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |% Wolnego miejsca |
|Dysk logiczny |Średni czas dysku w s/odczyt |
|Dysk logiczny |Średni czas dysku w s/transfer |
|Dysk logiczny |Średni czas dysku w s/zapis |
|Dysk logiczny |Bajty dysku/s |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Pamięć |Dostępna pamięć (MB) |
|Karta sieciowa |Bajty odebrane/s |
|Karta sieciowa |Bajty wysłane/s |
|Procesor |Czas procesora (%) |

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |Procent wykorzystania miejsca |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Dysk logiczny |Bajty dysku logicznego/s |
|Pamięć |Dostępna pamięć (MB) |
|Network (Sieć) |Całkowita liczba odebranych bajtów |
|Network (Sieć) |Całkowita liczba przesłanych bajtów |
|Procesor |Czas procesora (%) |

## <a name="management-packs"></a>Pakiety administracyjne

Gdy Azure Monitor dla maszyn wirtualnych jest włączona i skonfigurowana z obszarem roboczym Log Analytics, pakiet administracyjny zostanie przekazany do wszystkich komputerów z systemem Windows, które są raportowane do tego obszaru roboczego. Jeśli [Grupa zarządzania System Center Operations Manager została zintegrowana](../../azure-monitor/platform/om-agents.md) z obszarem roboczym log Analytics, pakiet administracyjny Service map zostanie wdrożony z grupy zarządzania na komputerach z systemem Windows zgłaszanych do grupy zarządzania.  

Pakiet administracyjny ma nazwę *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Zapisany w folderze `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. Źródło danych używane przez pakiet administracyjny jest `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawienia jakości, bezpieczeństwa i integralności usługi. 

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, funkcja map zawiera dane dotyczące konfiguracji oprogramowania. Dane zawierają informacje takie jak system operacyjny, wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie zbiera nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Teraz, gdy włączono monitorowanie dla maszyny wirtualnej, informacje o monitorowaniu są dostępne do analizy w Azure Monitor dla maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z funkcji monitorowania wydajności, zobacz [wyświetlanie Azure monitor dla maszyn wirtualnych Performance](vminsights-performance.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
