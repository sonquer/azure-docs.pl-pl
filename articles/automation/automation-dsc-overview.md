---
title: Przegląd konfiguracji stanu Azure Automation
description: Omówienie konfiguracji stanu Azure Automation (DSC), jej warunków i znanych problemów
keywords: PowerShell DSC, Konfiguracja żądanego stanu, środowisko PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850894"
---
# <a name="azure-automation-state-configuration-overview"></a>Przegląd konfiguracji stanu Azure Automation

Azure Automation konfiguracja stanu to usługa platformy Azure, która umożliwia pisanie i kompilowanie konfiguracji konfiguracji żądanego stanu ( [DSC) programu PowerShell, zarządzanie](/powershell/scripting/dsc/configurations/configurations)nimi oraz przypisywanie konfiguracji do węzłów [docelowych, a](/powershell/scripting/dsc/resources/resources)wszystko to w chmurze.

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto używać konfiguracji stanu Azure Automation

Azure Automation konfiguracja stanu zapewnia kilka korzyści z używania DSC poza platformą Azure.

### <a name="built-in-pull-server"></a>Wbudowany serwer ściągania

Azure Automation konfiguracja stanu udostępnia serwer ściągania DSC podobny do [usługi Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver) , dzięki czemu węzły docelowe automatycznie odbierają konfiguracje, są zgodne z żądanym stanem i raportują zgodność z powrotem. Wbudowany serwer ściągania w programie Azure Automation eliminuje konieczność skonfigurowania i utrzymania własnego serwera ściągania. Azure Automation mogą kierować wirtualne lub fizyczne maszyny z systemem Windows lub Linux, w chmurze lub w środowisku lokalnym.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie wszystkimi artefaktami DSC

Azure Automation konfiguracja stanu umożliwia korzystanie z tej samej warstwy zarządzania do [konfiguracji żądanego stanu programu PowerShell](/powershell/scripting/dsc/overview/overview) jako Azure Automation ofert dla skryptów programu PowerShell.

Za pomocą Azure Portal lub z programu PowerShell można zarządzać wszystkimi konfiguracjami DSC, zasobami i węzłami docelowymi.

![Zrzut ekranu przedstawiający stronę Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importowanie danych raportowania do dzienników Azure Monitor

Węzły, które są zarządzane za pomocą Azure Automation stanu Configuration, wysyłają szczegółowe dane stanu raportowania do wbudowanego serwera ściągania. Konfigurację stanu Azure Automation można skonfigurować w celu wysłania tych danych do Log Analytics obszaru roboczego. Aby dowiedzieć się, jak wysyłać dane stanu konfiguracji stanu do obszaru roboczego Log Analytics, zobacz [przesyłanie dalej Azure Automation dane konfiguracji stanu do dzienników Azure monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Wymagania wstępne

Podczas korzystania z konfiguracji stanu Azure Automation (DSC) należy wziąć pod uwagę poniższe wymagania.

### <a name="operating-system-requirements"></a>Wymagania systemu operacyjnego

W przypadku węzłów z systemem Windows obsługiwane są następujące wersje:

- Windows Server 2019
- Windows Server 2016
- 2012R2 systemu Windows Server
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Autonomiczna jednostka SKU produktu [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) nie zawiera implementacji żądanego stanu konfiguracją, więc nie może być zarządzana przez program PowerShell DSC lub konfigurację stanu Azure Automation.

W przypadku węzłów z systemem Linux obsługiwane są następujące dystrybucje/wersje:

Rozszerzenie DSC systemu Linux obsługuje wszystkie dystrybucje systemu Linux wymienione w obszarze [obsługiwane dystrybucje systemu Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Wymagania DSC

W przypadku wszystkich węzłów systemu Windows działających na platformie Azure podczas dołączania zostanie zainstalowany program [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) .  W przypadku węzłów z systemami Windows Server 2012 i Windows 7 [usługa WinRM zostanie włączona](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

W przypadku wszystkich węzłów systemu Linux działających na platformie Azure podczas dołączania zostanie zainstalowana [Konfiguracja DSC programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .

### <a name="network-planning"></a>Konfigurowanie sieci prywatnych

Jeśli węzły znajdują się w sieci prywatnej, do komunikacji z automatyzacją (DSC) wymagane są następujące porty i adresy URL:

* Port: dla wychodzącego dostępu do Internetu wymagany jest tylko protokół TCP 443.
* Globalny adres URL: *. azure-automation.net
* Globalny adres URL US Gov Wirginia: *. azure-automation.us
* Usługa agenta: https://\<identyfikator obszaru roboczego\>. agentsvc.azure-automation.net

Zapewnia to łączność sieciową dla węzła zarządzanego w celu komunikowania się z Azure Automation.
W przypadku korzystania z zasobów DSC, które komunikują się między węzłami, takimi jak [* zasoby](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), należy również zezwolić na ruch między węzłami.
Zapoznaj się z dokumentacją poszczególnych zasobów DSC, aby poznać te wymagania sieciowe.

#### <a name="proxy-support"></a>Obsługa serwera proxy

Obsługa serwera proxy dla agenta DSC jest dostępna w systemie Windows w wersji 1809 i nowszych.
Aby skonfigurować tę opcję, należy ustawić wartość **ProxyURL** i **ProxyCredential** w [skrypcie konfiguracji](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) kodera używanym do rejestrowania węzłów.
Serwer proxy nie jest dostępny w usłudze DSC dla wcześniejszych wersji systemu Windows.

W przypadku węzłów systemu Linux Agent DSC obsługuje serwer proxy i użyje zmiennej http_proxy, aby określić adres URL.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Zakresy i przestrzeń nazw sieci konfiguracji stanu platformy Azure

Zaleca się użycie adresów wymienionych podczas definiowania wyjątków. Dla adresów IP można pobrać zakresy adresów [IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację z tym regionalnym centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Południowo-środkowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Aby uzyskać listę adresów IP regionów zamiast nazw regionów, Pobierz plik XML [adresu IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) z centrum pobierania Microsoft.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera zakresy adresów IP, które są używane w centrach danych Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i magazynu.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień.
>
> Dobrym pomysłem jest pobranie nowego pliku XML co tydzień. Następnie zaktualizuj swoją witrynę, aby prawidłowo identyfikować usługi działające na platformie Azure. Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizacji anonsu usługi Border Gateway Protocol (BGP) w pierwszym tygodniu każdego miesiąca.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączyć węzły, zobacz sekcję dołączanie [maszyn w celu zarządzania według konfiguracji stanu Azure Automation](automation-dsc-onboarding.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
