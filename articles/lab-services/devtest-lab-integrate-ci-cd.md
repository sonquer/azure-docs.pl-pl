---
title: Integruj Azure DevTest Labs z Azure Pipelines
description: Dowiedz się, jak zintegrować usługi Azure DevTest Labs w sieci Web Azure potoków ciągłej integracji i potoku dostaw
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293219"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integracja Azure DevTest Labs Azure Pipelines z potokiem ciągłej integracji/ciągłego wdrażania

Za pomocą rozszerzenia *Azure DevTest Labs Tasks* można zintegrować kompilację Azure Pipelines ciągłej integracji i ciągłego dostarczania (Ci/CD) oraz potoków wydań z programem Azure DevTest Labs. Rozszerzenie instaluje kilka zadań, w tym: 

- Tworzenie maszyny wirtualnej
- Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej
- Usuwanie maszyny wirtualnej 

Te zadania ułatwiają, na przykład, szybko wdrażać maszynę wirtualną z *obrazem złota* dla określonego zadania testowego, a następnie usunąć maszynę wirtualną po zakończeniu testu.

W tym artykule pokazano, jak za pomocą zadań Azure DevTest Labs utworzyć i wdrożyć maszynę wirtualną, utworzyć obraz niestandardowy, a następnie usunąć maszynę wirtualną, a wszystko to w jednym potoku wydania. Zwykle zadania są wykonywane indywidualnie we własnych niestandardowych potokach kompilowania, testowania i wdrażania.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zarejestruj się lub Zaloguj w organizacji [usługi Azure DevOps](https://dev.azure.com) i [Utwórz projekt](/vsts/organizations/projects/create-project) w organizacji. 
  
- Zainstaluj rozszerzenie zadania Azure DevTest Labs z Visual Studio Marketplace.
  
  1. Przejdź do [usługi Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Wybierz pozycję **Pobierz bezpłatnie**.
  1. Wybierz z listy rozwijanej organizację usługi Azure DevOps, a następnie wybierz pozycję **Zainstaluj**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Tworzenie szablonu w celu utworzenia maszyny wirtualnej platformy Azure 

W tej sekcji opisano sposób tworzenia szablonu Azure Resource Manager, który służy do tworzenia maszyny wirtualnej platformy Azure na żądanie.

1. Aby utworzyć szablon Menedżer zasobów w ramach subskrypcji, wykonaj procedurę opisaną w temacie [Korzystanie z szablonu Menedżer zasobów](devtest-lab-use-resource-manager-template.md).
   
1. Przed wygenerowaniem szablonu usługi Resource Manager, należy dodać [artefaktu WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako część tworzenia maszyny Wirtualnej. Zadania wdrażania, takie jak *Kopiowanie plików platformy Azure* i program *PowerShell na komputerach docelowych* , wymagają dostępu do usługi WinRM. Artefakt usługi WinRM wymaga nazwy hosta jako parametru, który powinien być w pełni kwalifikowaną nazwą domeny (FQDN) maszyny wirtualnej. 
   
   > [!NOTE]
   > Gdy używasz usługi WinRM przy użyciu współużytkowanego adresu IP, należy dodać regułę translatora adresów Sieciowych w celu mapowania portu zewnętrznego port usługi WinRM. Reguła NAT nie jest potrzebna, jeśli utworzysz maszynę wirtualną z publicznym adresem IP.
   
   
1. Zapisz szablon na komputerze jako plik o nazwie *CreateVMTemplate. JSON*.
   
1. Zaewidencjonuj szablon w systemie kontroli źródła.

## <a name="create-a-script-to-get-vm-properties"></a>Utwórz skrypt, aby uzyskać właściwości maszyny wirtualnej

Po uruchomieniu kroków zadań, takich jak *Kopiowanie plików platformy Azure* lub program *PowerShell na maszynach docelowych* w potoku wydania, Poniższy skrypt zbiera wartości, które są potrzebne do wdrożenia aplikacji na maszynie wirtualnej. Zwykle te zadania są używane do wdrażania aplikacji na maszynie wirtualnej platformy Azure. Zadania wymagają wartości, takich jak nazwa grupy zasobów maszyny wirtualnej, adres IP i nazwa FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby utworzyć plik skryptu:

1. Otwórz Edytor tekstów, a następnie wklej poniższy skrypt.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Zapisz plik o nazwie takiej jak *GetLabVMParams. ps1*, a następnie Zaewidencjonuj go w systemie kontroli źródła. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Tworzenie potoku wydania w usłudze Azure Pipelines

Aby utworzyć nowy potok wydania:

1. Na stronie projektu usługi Azure DevOps wybierz pozycję **potoki** > **wersje** w lewym okienku nawigacji.
1. Wybierz pozycję **Nowy potok**.
1. W obszarze **Wybierz szablon**przewiń w dół i wybierz pozycję **puste zadanie**, a następnie wybierz pozycję **Zastosuj**.

### <a name="add-and-set-variables"></a>Dodawanie i Ustawianie zmiennych

Zadania potoku używają wartości przypisanych do maszyny wirtualnej podczas tworzenia szablonu Menedżer zasobów w Azure Portal. 

Aby dodać zmienne dla wartości: 

1. Na stronie potok wybierz kartę **zmienne** .
   
1. Dla każdej zmiennej wybierz pozycję **Dodaj** , a następnie wprowadź nazwę i wartość:
   
   |Nazwa|Wartość|
   |---|---|
   |*vmName*|Nazwa maszyny wirtualnej, która została przypisana w szablonie Menedżer zasobów|
   |*Uż*|Nazwa użytkownika do uzyskiwania dostępu do maszyny wirtualnej|
   |*Hasło*|Hasło dla nazwy użytkownika. Wybierz ikonę kłódki, aby ukryć i zabezpieczyć hasło.

### <a name="create-a-devtest-labs-vm"></a>Tworzenie maszyny wirtualnej z systemem DevTest Labs

Następnym krokiem jest utworzenie maszyny wirtualnej z obrazem złota do użycia w przyszłych wdrożeniach. Możesz utworzyć maszynę wirtualną w wystąpieniu Azure DevTest Labs przy użyciu zadania *Azure DevTest Labs Utwórz maszynę wirtualną* .

1. Na karcie **potok** potoku wydania wybierz hiperłącze w obszarze **etap 1** , aby **wyświetlić zadania etapów**, a następnie wybierz znak plus **+** obok **zadania agenta**. 
   
1. W obszarze **Dodaj zadania**wybierz pozycję **Azure DevTest Labs Utwórz maszynę wirtualną**, a następnie wybierz pozycję **Dodaj**. 
   
1. W lewym okienku wybierz pozycję **Utwórz maszynę wirtualną Azure DevTest Labs** . 

1. W okienku po prawej stronie wypełnij formularz w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Subskrypcja usługi Azure RM**|Wybierz połączenie z usługą lub subskrypcję z **dostępnych połączeń usługi platformy Azure** lub **dostępne subskrypcje platformy Azure** na liście rozwijanej, a następnie w razie potrzeby wybierz pozycję **Autoryzuj** .<br /><br />**Uwaga:** Aby uzyskać informacje na temat tworzenia bardziej ograniczonego połączenia z subskrypcją platformy Azure, zobacz [punkt końcowy usługi Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Nazwa laboratorium**|Wybierz nazwę istniejącego laboratorium, w którym zostanie utworzona maszyna wirtualna laboratorium.|
   |**Nazwa szablonu**|Wprowadź pełną ścieżkę i nazwę pliku szablonu zapisanego w repozytorium kodu źródłowego. Można używać wbudowanych właściwości do uproszczenia ścieżki, na przykład:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parametry szablonu**|Wprowadź parametry dla zdefiniowanych wcześniej zmiennych:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Zmienne wyjściowe** > **Identyfikator maszyny wirtualnej laboratorium**|Wprowadź zmienną dla utworzonego identyfikatora maszyny wirtualnej laboratorium. W przypadku użycia domyślnej **labVMId**można odwołać się do zmiennej w kolejnych zadaniach jako *$ (labVMId)* .<br /><br />Można utworzyć nazwę inną niż domyślna, ale pamiętaj, aby użyć poprawnej nazwy w kolejnych zadaniach. Identyfikator maszyny wirtualnej laboratorium można napisać w następującej postaci:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Zbierz szczegóły maszyny wirtualnej z DevTest Labs

Uruchom skrypt został utworzony wcześniej, aby zbierać szczegółowe informacje o maszynie Wirtualnej DevTest Labs. 

1. Na karcie **potok** potoku wydania wybierz hiperłącze w obszarze **etap 1** , aby **wyświetlić zadania etapów**, a następnie wybierz znak plus **+** obok **zadania agenta**. 
   
1. W obszarze **Dodaj zadania**wybierz pozycję **Azure PowerShell**, a następnie wybierz pozycję **Dodaj**. 
   
1. Wybierz pozycję **Azure PowerShell skrypt: FilePath** w lewym okienku. 
   
1. W okienku po prawej stronie wypełnij formularz w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Typ połączenia platformy Azure**|Wybierz **Azure Resource Manager**.|
   |**Subskrypcja platformy Azure**|Wybierz połączenie lub subskrypcję usługi.| 
   |**Typ skryptu**|Wybierz **ścieżkę pliku skryptu**.|
   |**Ścieżka skryptu**|Wprowadź pełną ścieżkę i nazwę skryptu programu PowerShell zapisanego w repozytorium kodu źródłowego. Można używać wbudowanych właściwości do uproszczenia ścieżki, na przykład:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumenty skryptu**|Wprowadź nazwę zmiennej *labVmId* , która została wypełniona przez poprzednie zadanie, na przykład:<br /><br />`-labVmId '$(labVMId)'`|

Skrypt zbiera wymagane wartości i zapisuje je w zmiennych środowiskowych w potoku wydania, dzięki czemu można łatwo odwoływać się do nich w kolejnych krokach.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Tworzenie obrazu maszyny wirtualnej na podstawie maszyny wirtualnej DevTest Labs

Następnym zadaniem jest utworzenie obrazu nowo wdrożonej maszyny wirtualnej w wystąpieniu Azure DevTest Labs. Obrazu można następnie użyć do tworzenia kopii maszyny wirtualnej na żądanie, w każdym przypadku, gdy chcesz wykonać zadanie deweloperów lub niektóre testy. 

1. Na karcie **potok** potoku wydania wybierz hiperłącze w obszarze **etap 1** , aby **wyświetlić zadania etapów**, a następnie wybierz znak plus **+** obok **zadania agenta**. 
   
1. W obszarze **Dodaj zadania**wybierz pozycję **Azure DevTest Labs Utwórz obraz niestandardowy**, a następnie wybierz pozycję **Dodaj**. 
   
1. Skonfiguruj zadania w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Subskrypcja usługi Azure RM**|Wybierz połączenie lub subskrypcję usługi.|
   |**Nazwa laboratorium**|Wybierz nazwę istniejącego laboratorium, w którym zostanie utworzony obraz.|
   |**Nazwa obrazu niestandardowego**|Wprowadź nazwę obrazu niestandardowego.|
   |**Opis** (opcjonalnie)|Wprowadź opis, aby ułatwić wybranie poprawnego obrazu później.|
   |**Identyfikator maszyny** wirtualnej laboratorium **źródłowego laboratorium źródłowego > **|Jeśli zmieniono nazwę domyślną zmiennej LabVMId, wprowadź ją w tym miejscu. Wartość domyślna to **$(labVMId)** .|
   |**Zmienne wyjściowe** > **niestandardowego identyfikatora obrazu**|W razie potrzeby można edytować domyślną nazwę zmiennej.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Wdróż aplikację na maszynie wirtualnej z DevTest Labs (opcjonalnie)

Możesz dodać zadania do wdrożenia aplikacji na nowej maszynie wirtualnej z DevTest Labs. Zadania, które zwykle są używane do wdrażania aplikacji, to *Kopiowanie plików na platformę Azure* i program *PowerShell na komputerach docelowych*.

Informacje o maszynie wirtualnej potrzebne do parametrów tych zadań są przechowywane w trzech zmiennych konfiguracyjnych o nazwie **labVmRgName**, **labVMIpAddress**i **labVMFqdn** w potoku wydania. Jeśli chcesz wypróbować tworzenie maszyny Wirtualnej usługi DevTest Labs i obraz niestandardowy bez wdrażanie aplikacji, możesz pominąć ten krok.

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Ostatnim zadaniem jest usunięcie maszyny wirtualnej wdrożonej w wystąpieniu Azure DevTest Labs. Zazwyczaj można usuwać maszyny Wirtualnej po wykonywanie zadań deweloperskich lub uruchomić testy, które należy wdrożonej maszyny Wirtualnej. 

1. Na karcie **potok** potoku wydania wybierz hiperłącze w obszarze **etap 1** , aby **wyświetlić zadania etapów**, a następnie wybierz znak plus **+** obok **zadania agenta**. 
   
1. W obszarze **Dodaj zadania**wybierz pozycję **Azure DevTest Labs Usuń maszynę wirtualną**, a następnie wybierz pozycję **Dodaj**. 
   
1. Skonfiguruj zadania w następujący sposób:
   
   - W obszarze **subskrypcja usługi Azure RM**wybierz połączenie lub subskrypcję usługi. 
   - W przypadku **identyfikatora maszyny wirtualnej laboratorium**w przypadku zmiany nazwy domyślnej zmiennej LabVMId wprowadź ją w tym miejscu. Wartość domyślna to **$(labVMId)** .
   
### <a name="save-the-release-pipeline"></a>Zapisz potok wydania

Aby zapisać nowy potok wydania:

1. Wybierz pozycję Nazwa **Nowy potok wydania** na stronie potoku wydania, a następnie wprowadź nową nazwę potoku. 
   
1. Wybierz ikonę **Zapisz** w prawym górnym rogu. 

## <a name="create-and-run-a-release"></a>Utwórz i uruchom wydanie

Aby utworzyć i uruchomić wydanie przy użyciu nowego potoku:

1. Wybierz pozycję **Utwórz wydanie** w prawym górnym rogu na stronie potoku wydania. 
   
1. W obszarze **artefakty**wybierz najnowszą kompilację, a następnie wybierz pozycję **Utwórz**.
   
1. W każdym etapie wydania Odśwież widok wystąpienia DevTest Labs w Azure Portal, aby wyświetlić informacje o tworzeniu maszyny wirtualnej, tworzeniu obrazu i usunięciu maszyny wirtualnej.

Możesz użyć obrazu niestandardowego do tworzenia maszyn wirtualnych za każdym razem, gdy będą potrzebne.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [tworzenie środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
- Poznaj więcej szablonów szybkiego startu usługi Resource Manager, związane z automatyzacją DevTest Labs z [publicznego repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-quickstart-templates).
- W razie potrzeby Zobacz stronę [Rozwiązywanie problemów z usługą Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) .
 
