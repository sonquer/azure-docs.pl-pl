---
title: Rozwiązywanie problemów Azure Automation konfiguracji żądanego stanu (DSC)
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją żądanego stanu (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430537"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Rozwiązywanie problemów z Azure Automation konfiguracją żądanego stanu (DSC)

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracją żądanego stanu (DSC).

## <a name="diagnosing-an-issue"></a>Diagnozowanie problemu

Jeśli występują błędy podczas kompilowania lub wdrażania konfiguracji w usłudze Azure State Configuration, należy wykonać kilka kroków, aby ułatwić zdiagnozowanie problemu.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Upewnij się, że konfiguracja została pomyślnie skompilowana na komputerze lokalnym

Konfiguracja stanu platformy Azure jest oparta na usłudze PowerShell DSC. Dokumentację dotyczącą języka i składni DSC można znaleźć w dokumentacji [DSC programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Kompilując konfigurację DSC na komputerze lokalnym, można odnajdywać i rozwiązywać typowe błędy, takie jak:

   - Brakujące moduły
   - Błędy składniowe
   - Błędy logiki

### <a name="2-view-dsc-logs-on-your-node"></a>2. Wyświetl dzienniki DSC w Twoim węźle

Jeśli konfiguracja została pomyślnie skompilowana, ale nie powiedzie się w przypadku zastosowania do węzła, można znaleźć szczegółowe informacje w dziennikach DSC. Informacje o tym, gdzie znaleźć te dzienniki, można znaleźć w temacie [gdzie znajdują się dzienniki zdarzeń DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Moduł [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) może pomóc w analizie szczegółowych informacji z dzienników DSC. Jeśli skontaktujesz się z pomocą techniczną, wymagają one tych dzienników do zdiagnozowania problemu.

Moduł xDscDiagnostics można zainstalować na maszynie lokalnej, korzystając z instrukcji znajdujących się w temacie [Instalowanie stabilnej wersji modułu](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Aby zainstalować moduł xDscDiagnostics na maszynie platformy Azure, użyj polecenie [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Można również użyć opcji **Uruchom polecenie** z portalu, wykonując czynności opisane w sekcji [Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Uruchom](../../virtual-machines/windows/run-command.md).

Aby uzyskać informacje na temat korzystania z programu xDscDiagnostics, zobacz [Używanie xDscDiagnostics do analizowania dzienników DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zobacz również [polecenia cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły

Konfiguracja DSC zależy od modułów zainstalowanych w węźle. W przypadku korzystania z konfiguracji stanu Azure Automation należy zaimportować wszystkie wymagane moduły do konta usługi Automation, korzystając z procedury wymienionej w temacie [Import modules](../shared-resources/modules.md#import-modules). Konfiguracje mogą również mieć zależność od określonych wersji modułów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Typowe błędy podczas pracy z usługą DSC

### <a name="unsupported-characters"></a>Scenariusz: nie można usunąć konfiguracji z użyciem znaków specjalnych z portalu

#### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC z portalu zostanie wyświetlony następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowym problemem, który został zaplanowany do rozwiązania.

#### <a name="resolution"></a>Rozwiązanie

* Aby usunąć konfigurację, użyj polecenia AZ cmdlet "Remove-AzAutomationDscConfiguration".
* Dokumentacja tego polecenia cmdlet nie została jeszcze zaktualizowana.  Do tego momentu zapoznaj się z dokumentacją modułu AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenariusz: nie można zarejestrować agenta DSC

#### <a name="issue"></a>Problem

Podczas próby uruchomienia `Set-DscLocalConfigurationManager` lub innego polecenia cmdlet DSC zostanie wyświetlony komunikat o błędzie:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest zwykle spowodowany przez zaporę, komputer za serwerem proxy lub inne błędy sieciowe.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy maszyna ma dostęp do odpowiednich punktów końcowych Azure Automation DSC i spróbuj ponownie. Aby uzyskać listę wymaganych portów i adresów, zobacz [Planowanie sieci](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a>Scenariusz <a/><a name="unauthorized">: raporty o stanie zwracają kod odpowiedzi "nieautoryzowane"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła z konfiguracją stanu (DSC) pojawia się jeden z następujących komunikatów o błędach:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zły lub wygasły certyfikat.  Aby uzyskać więcej informacji, zobacz [wygaśnięcie i rejestrację certyfikatu](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration).

### <a name="resolution"></a>Rozwiązanie

Wykonaj kroki opisane poniżej, aby ponownie zarejestrować węzeł DSC zakończony niepowodzeniem.

Najpierw Wyrejestruj węzeł, wykonując poniższe kroki.

1. Z poziomu Azure Portal w obszarze **główne** -> **konta usługi Automation**> {Twoje konto usługi Automation} — > **State Configuration (DSC)**
2. Kliknij pozycję "węzły" i kliknij węzeł z problemami.
3. Kliknij pozycję "Wyrejestruj", aby wyrejestrować węzeł.

Następnie Odinstaluj rozszerzenie DSC z węzła.

1. Z poziomu Azure Portal w obszarze **Narzędzia główne** -> **maszyna wirtualna** -> {niepowodzenie węzła} — **rozszerzenia** >
2. Kliknij pozycję "Microsoft. PowerShell. DSC".
3. Kliknij przycisk "Odinstaluj", aby odinstalować rozszerzenie DSC programu PowerShell.

Po trzecie Usuń wszystkie złe lub wygasłe certyfikaty z węzła.

W przypadku niepowodzenia węzła w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenie:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Na koniec Zarejestruj ponownie węzeł zakończony niepowodzeniem, wykonując poniższe kroki.

1. Z poziomu Azure Portal w obszarze **główne** -> **konta usługi Automation** > {Twoje konto usługi Automation} — > **State Configuration (DSC)**
2. Kliknij pozycję "węzły".
3. Kliknij przycisk "Dodaj".
4. Wybierz węzeł z niepowodzeniem.
5. Kliknij pozycję "Połącz" i wybierz odpowiednie opcje.

### <a name="failed-not-found"></a>Scenariusz: węzeł jest w stanie niepowodzenia z powodu błędu "nie znaleziono"

#### <a name="issue"></a>Problem

Węzeł zawiera raport ze stanem **Niepowodzenie** i zawierający błąd:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy węzeł jest przypisany do nazwy konfiguracji (na przykład ABC) zamiast nazwy konfiguracji węzła (na przykład ABC. Serwer WebServer).

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł "nazwa konfiguracji węzła", a nie "nazwa konfiguracji".
* Konfigurację węzła można przypisać do węzła przy użyciu Azure Portal lub za pomocą polecenia cmdlet programu PowerShell.

  * Aby przypisać konfigurację węzła do węzła przy użyciu Azure Portal, Otwórz stronę **węzły DSC** , a następnie wybierz węzeł i kliknij przycisk **Przypisz konfigurację węzła** .
  * Aby przypisać konfigurację węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj polecenia cmdlet **Set-AzureRmAutomationDscNode**

### <a name="no-mof-files"></a>Scenariusz: podczas kompilowania konfiguracji nie zostały utworzone żadne konfiguracje węzłów (pliki MOF)

#### <a name="issue"></a>Problem

Zadanie kompilacji DSC zawiesza się z błędem:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Przyczyna

Gdy wyrażenie zgodne z słowem kluczowym **Node** w konfiguracji DSC zostanie oszacowane jako `$null`, nie są generowane konfiguracje węzłów.

#### <a name="resolution"></a>Rozwiązanie

Niektóre z poniższych rozwiązań rozwiązują ten problem:

* Upewnij się, że wyrażenie obok słowa kluczowego **Node** w definicji konfiguracji nie ocenia się do $null.
* Jeśli przekazujesz ConfigurationData podczas kompilowania konfiguracji, upewnij się, że przechodzą oczekiwane wartości wymagane przez konfigurację z [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC zostanie zablokowany w stanie "w toku"

#### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Przyczyna

Uaktualniono wersję WMF i usunięto uszkodzoną usługę WMI.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami podanymi w artykule [znane problemy i ograniczenia DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Scenariusz: nie można użyć poświadczenia w konfiguracji DSC

#### <a name="issue"></a>Problem

Zadanie kompilacji DSC zostało wstrzymane z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Przyczyna

Użyto poświadczenia w konfiguracji, ale nie podano odpowiednich **ConfigurationData** , aby ustawić **PSDscAllowPlainTextPassword** na true dla każdej konfiguracji węzła.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że w odpowiedniej **ConfigurationData** określono wartość true dla każdej konfiguracji węzła, która **została określona w** konfiguracji. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](../automation-dsc-compile.md).

### <a name="failure-processing-extension"></a>Scenariusz: dołączanie z rozszerzenia DSC, błąd "niepowodzenie przetwarzania rozszerzenia"

#### <a name="issue"></a>Problem

Podczas dołączania przy użyciu rozszerzenia DSC wystąpi błąd zawierający błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj, gdy do węzła jest przypisana nazwa konfiguracji węzła, która nie istnieje w usłudze.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przypiszesz węzeł z nazwą konfiguracji węzła, która dokładnie pasuje do nazwy w usłudze.
* Można zrezygnować z uwzględnienia nazwy konfiguracji węzła, która spowoduje dołączenie węzła, ale nie przypisanie konfiguracji węzła

### <a name="cross-subscription"></a>Scenariusz: rejestrowanie węzła przy użyciu programu PowerShell zwraca błąd "wystąpił co najmniej jeden błąd"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła przy użyciu `Register-AzAutomationDSCNode` lub `Register-AzureRMAutomationDSCNode`pojawia się następujący błąd.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby zarejestrowania węzła, który znajduje się w oddzielnej subskrypcji niż konto usługi Automation.

#### <a name="resolution"></a>Rozwiązanie

Traktuj węzeł między subskrypcjami, tak jakby był on w osobnej chmurze lub lokalnie.

Wykonaj poniższe kroki, aby zarejestrować węzeł.

* Windows — [fizyczne/wirtualne maszyny z systemem Windows lokalnie lub w chmurze innej niż Azure/AWS](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux — [fizyczne/wirtualne maszyny z systemem Linux w środowisku lokalnym lub w chmurze innej niż Azure](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="agent-has-a-problem"></a>Scenariusz: komunikat o błędzie — "Inicjowanie obsługi nie powiodło się"

#### <a name="issue"></a>Problem

Podczas rejestrowania węzła zostanie wyświetlony komunikat o błędzie:

```error
Provisioning has failed
```

#### <a name="cause"></a>Przyczyna

Ten komunikat występuje, gdy występuje problem z łącznością między węzłem i platformą Azure.

#### <a name="resolution"></a>Rozwiązanie

Ustal, czy węzeł znajduje się w prywatnej sieci wirtualnej, czy też ma inne problemy z nawiązywaniem połączenia z platformą Azure.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów podczas](onboarding.md)dołączania rozwiązań.

### <a name="failure-linux-temp-noexec"></a>Scenariusz: zastosowanie konfiguracji w systemie Linux powoduje wystąpienie błędu z ogólnym błędem

#### <a name="issue"></a>Problem

W przypadku stosowania konfiguracji w systemie Linux Wystąpił błąd z powodu błędu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Przyczyna

Klienci zidentyfikowali, że jeśli `/tmp` lokalizacji jest ustawiona na `noexec`, bieżąca wersja DSC nie będzie stosowała konfiguracji.

#### <a name="resolution"></a>Rozwiązanie

* Usuń opcję `noexec` z lokalizacji `/tmp`.

### <a name="compilation-node-name-overlap"></a>Scenariusz: nazwy konfiguracji węzłów, które pokrywają się, mogą spowodować utratę prawidłowej wersji

#### <a name="issue"></a>Problem

Jeśli jeden skrypt konfiguracji jest używany do generowania konfiguracji wielu węzłów, a niektóre konfiguracje węzłów mają nazwę, która jest podzbiorem innych, problem w usłudze kompilacji może skutkować przypisaniem niewłaściwej konfiguracji.  Dzieje się tak tylko w przypadku użycia jednego skryptu do generowania konfiguracji z danymi konfiguracyjnymi na węzeł i tylko wtedy, gdy nazwa się pokrywa na początku ciągu.

Przykładowo, jeśli jeden skrypt konfiguracji jest używany do generowania konfiguracji na podstawie danych węzła przekazaną jako tablicę skrótów za pomocą poleceń cmdlet, a dane węzła zawierają serwer o nazwie "serwer" i "1server".

#### <a name="cause"></a>Przyczyna

Znany problem dotyczący usługi kompilacji.

#### <a name="resolution"></a>Rozwiązanie

Najlepszym rozwiązaniem jest skompilowanie lokalnie lub w potoku ciągłej integracji/ciągłego dostarczania i przekazanie plików MOF bezpośrednio do usługi.  Jeśli kompilacja w usłudze jest wymagana, następnym najlepszym rozwiązaniem jest podzielenie zadań kompilacji, aby nie nakładały się nazw.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
