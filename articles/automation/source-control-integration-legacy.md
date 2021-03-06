---
title: Integracja kontroli źródła w programie Azure Automation — Starsza wersja
description: W tym artykule opisano integrację kontroli źródła z usługą GitHub w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 651b97dabfd3cce858ea1f905a39c10bd7d81c41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417439"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integracja kontroli źródła w programie Azure Automation — Starsza wersja

> [!NOTE]
> Istnieje nowe środowisko kontroli źródła. Aby dowiedzieć się więcej na temat nowego środowiska, zobacz [Kontrola źródła (wersja zapoznawcza)](source-control-integration.md).

Integracja kontroli źródła umożliwia kojarzenie elementów Runbook na koncie usługi Automation z repozytorium kontroli źródła w witrynie GitHub. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzić zmiany i przywracać wcześniejsze wersje elementów Runbook. Na przykład kontrola źródła pozwala synchronizować różne gałęzie w kontroli źródła z kontami automatyzacji, test lub produkcja, ułatwiając podwyższenie poziomu kodu, który został przetestowany w środowisku programistycznym do automatyzacji produkcji koncie.

Kontrola źródła umożliwia wypychanie kodu z Azure Automation do kontroli źródła lub ściąganie elementów Runbook z kontroli źródła do Azure Automation. W tym artykule opisano sposób konfigurowania kontroli źródła w środowisku Azure Automationu. Rozpoczynamy od skonfigurowania Azure Automation, aby uzyskać dostęp do repozytorium GitHub i zapoznać się z różnymi operacjami, które można wykonać przy użyciu integracji kontroli źródła. 

> [!NOTE]
> Kontrola źródła obsługuje ściąganie i wypychanie [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) oraz [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks). [Graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) nie są jeszcze obsługiwane.

Istnieją dwa proste kroki wymagane do skonfigurowania kontroli źródła dla konta usługi Automation i tylko jeden, jeśli masz już konto w usłudze GitHub. Oto one:

## <a name="step-1--create-a-github-repository"></a>Krok 1 — Tworzenie repozytorium GitHub

Jeśli masz już konto usługi GitHub i repozytorium, do którego chcesz utworzyć łącze Azure Automation, zaloguj się do istniejącego konta i zacznij od kroku 2 poniżej. W przeciwnym razie przejdź do witryny [GitHub](https://github.com/), Utwórz nowe konto i [Utwórz nowe repozytorium](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 — Konfigurowanie kontroli źródła w Azure Automation

1. Na stronie konto usługi Automation w Azure Portal w obszarze **Ustawienia konta**kliknij pozycję **Kontrola źródła.**

2. Zostanie otwarta strona **Kontrola źródła** , w której można skonfigurować szczegóły konta usługi GitHub. Poniżej znajduje się lista parametrów do skonfigurowania:  

   | **Parametr** | **Opis** |
   |:--- |:--- |
   | Wybierz źródło |Wybierz źródło. Obecnie obsługiwane są tylko usługi **GitHub** . |
   | Autoryzacja |Kliknij przycisk **Autoryzuj** , aby udzielić Azure Automation dostępu do repozytorium GitHub. Jeśli użytkownik jest już zalogowany do konta usługi GitHub w innym oknie, zostaną użyte poświadczenia tego konta. Po pomyślnym uwierzytelnieniu na stronie zostanie wyświetlona nazwa użytkownika usługi GitHub w obszarze **Właściwości autoryzacji**. |
   | Wybieranie repozytorium |Wybierz repozytorium GitHub z listy dostępnych repozytoriów. |
   | Wybierz gałąź |Wybierz gałąź z listy dostępnych gałęzi. Tylko gałąź **główna** jest wyświetlana, jeśli nie utworzono żadnych gałęzi. |
   | Ścieżka folderu elementów Runbook |Ścieżka folderu elementu Runbook określa ścieżkę w repozytorium GitHub, z którego chcesz wypchnąć lub ściągnąć swój kod. Należy go wpisać w formacie **/FolderName/subfoldername**. Tylko elementy Runbook w ścieżce folderu elementu Runbook zostaną zsynchronizowane z kontem usługi Automation. Elementy Runbook w podfolderach ścieżki folderu elementu Runbook **nie** zostaną zsynchronizowane. Użyj **/** , aby zsynchronizować wszystkie elementy Runbook w ramach repozytorium. |
3. Na przykład jeśli masz repozytorium o nazwie **PowerShellScripts** , które zawiera **folder o nazwie** **RootFolder**, który zawiera folder o nazwie podfolder. Aby synchronizować każdy poziom folderów, można użyć następujących ciągów:

   1. Aby zsynchronizować elementy Runbook z **repozytorium**, ścieżka folderu runbook jest */*
   2. Aby synchronizować elementy Runbook z **RootFolder**, ścieżka folderu Runbook to */RootFolder*
   3. Aby zsynchronizować elementy Runbook z **podfolderu**, ścieżka folderu Runbook to */RootFolder/SubFolder*.
4. Po skonfigurowaniu parametrów są one wyświetlane na stronie **Konfigurowanie kontroli źródła** .  

    ![Strona kontroli źródła pokazująca ustawienia](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Po kliknięciu przycisku **OK**Integracja kontroli źródła jest teraz skonfigurowana dla konta usługi Automation i należy ją zaktualizować za pomocą informacji w witrynie GitHub. Teraz możesz kliknąć tę część, aby wyświetlić całą historię zadania synchronizacji kontroli źródła.  

    ![Wartości dla aktualnie skonfigurowanej konfiguracji kontroli źródła](media/source-control-integration-legacy/automation-RepoValues.png)
6. Po skonfigurowaniu kontroli źródła na koncie usługi Automation tworzone są dwa [zasoby zmiennych](automation-variables.md) . Ponadto autoryzowana aplikacja zostanie dodana do konta usługi GitHub.

   * Zmienna **Microsoft. Azure. Automation. SourceControl. Connection** zawiera wartości parametrów połączenia, jak pokazano poniżej.  

     | **Parametr** | **Wartość** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Ciąg |
     | `Value` |{"Branch":\<*nazwę gałęzi*> "RunbookFolderPath":\<*ścieżka folderu elementu Runbook*> "providertype":\<*ma wartość 1 dla > GitHub*, "Repository":\<*nazwę repozytorium*> "Username":\<*nazwę użytkownika usługi GitHub*>} |

   * Zmienna **Microsoft. Azure. Automation. SourceControl. OAuthToken**zawiera bezpieczną zaszyfrowaną wartość OAuthToken.  

     |**Parametr**            |**Wartość** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Nieznany (zaszyfrowany) |
     | `Value` | <*zaszyfrowanych> OAuthToken* |  

     ![Okno pokazujące zmienne kontroli źródła](media/source-control-integration-legacy/automation-Variables.png)  

   * **Kontrola źródła automatyzacji** jest dodawana jako autoryzowana aplikacja do Twojego konta usługi GitHub. Aby wyświetlić aplikację: ze strony głównej usługi GitHub przejdź do **profilu** > **Ustawienia** > **aplikacji**. Ta aplikacja umożliwia Azure Automation synchronizowanie repozytorium GitHub z kontem usługi Automation.  

     ![Ustawienia aplikacji w serwisie GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Używanie kontroli źródła w automatyzacji

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Zaewidencjonuj element Runbook z Azure Automation do kontroli źródła

Zaewidencjonowanie elementu Runbook umożliwia wypchnięcie zmian wprowadzonych w elemencie Runbook w Azure Automation do repozytorium kontroli źródła. Poniżej przedstawiono procedurę zaewidencjonowania elementu Runbook:

1. Na koncie usługi Automation [Utwórz nowy tekstowy element Runbook](automation-first-runbook-textual.md)lub [Edytuj istniejący, tekstowy element Runbook](automation-edit-textual-runbook.md). Ten element Runbook może być albo przepływem pracy programu PowerShell, albo elementem Runbook skryptu programu PowerShell.  
2. Po przeprowadzeniu edycji elementu Runbook Zapisz go, a następnie kliknij przycisk **Zaewidencjonuj** na stronie **Edycja** .  

    ![Okno przedstawiające ewidencjonowanie na przycisk GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Zaewidencjonowanie z Azure Automation zastępuje kod, który znajduje się obecnie w kontroli źródła. Równoważna instrukcja wiersza polecenia usługi git do zaewidencjonowania to **git add + git commit + git push**  

3. Po kliknięciu przycisku **Zaewidencjonuj**zostanie wyświetlony monit z komunikatem potwierdzającym, a następnie kliknij przycisk **tak** , aby kontynuować.  

    ![Okno dialogowe potwierdzające zaewidencjonowanie do kontroli źródła](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Zaewidencjonowanie uruchamia element Runbook kontroli źródła: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ten element Runbook nawiązuje połączenie z usługą GitHub i wypycha zmiany z Azure Automation do repozytorium. Aby wyświetlić historię wyewidencjonowanych zadań, Wróć do karty **Integracja kontroli źródła** i kliknij, aby otworzyć stronę synchronizacja repozytorium. Na tej stronie są wyświetlane wszystkie zadania kontroli źródła.  Wybierz zadanie, które chcesz wyświetlić, a następnie kliknij, aby wyświetlić szczegóły.  

    ![Okno przedstawiające wyniki zadania synchronizacji](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Elementy Runbook kontroli źródła są specjalnymi elementami Runbook automatyzacji, których nie można wyświetlić ani edytować. Chociaż nie są wyświetlane na liście elementów Runbook, na liście zadań wyświetlane są zadania synchronizacji.

5. Nazwa zmodyfikowanego elementu Runbook jest wysyłana jako parametr wejściowy dla zaewidencjonowanych elementów Runbook. [Szczegóły zadania można wyświetlić](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , rozwijając element Runbook na stronie **synchronizacji repozytorium** .  

    ![Okno pokazujące dane wejściowe dla zadania synchronizacji](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Odśwież repozytorium GitHub po zakończeniu zadania, aby wyświetlić zmiany.  W repozytorium powinno być dostępne zatwierdzenie: **Zaktualizowano *nazwę elementu Runbook* w Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizuj elementy Runbook z kontroli źródła do Azure Automation

Przycisk Synchronizuj na stronie synchronizacja repozytorium umożliwia ściąganie wszystkich elementów Runbook w ścieżce folderu elementów Runbook repozytorium do konta usługi Automation. To samo repozytorium można synchronizować z więcej niż jednym kontem usługi Automation. Poniżej przedstawiono procedurę synchronizacji elementu Runbook:

1. Na koncie usługi Automation, na którym konfigurujesz kontrolę źródła, Otwórz stronę **Synchronizacja integracji/repozytorium kontroli źródła** , a następnie kliknij pozycję **Synchronizuj**.  Zostanie wyświetlony monit z komunikatem potwierdzającym, a następnie kliknij przycisk **tak** , aby kontynuować.  

    ![Przycisk Synchronizuj z komunikatem potwierdzającym, że wszystkie elementy Runbook zostaną zsynchronizowane](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synchronizacja uruchamia element Runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ten element Runbook nawiązuje połączenie z usługą GitHub i pobiera zmiany z repozytorium do Azure Automation. Ta akcja powinna zostać wyświetlona na stronie **synchronizacji repozytorium** . Aby wyświetlić szczegółowe informacje o zadaniu synchronizacji, kliknij, aby otworzyć stronę szczegóły zadania.  

    ![Okno przedstawiające wyniki synchronizacji zadania synchronizacji w repozytorium GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Synchronizacja z kontroli źródła zastępuje wersję roboczą elementów Runbook, które obecnie istnieją na koncie usługi Automation dla **wszystkich** elementów Runbook, które są obecnie w kontroli źródła. Równoważna instrukcja wiersza polecenia usługi git do synchronizacji to **ściąganie git**

![Okno przedstawiające wszystkie dzienniki z wstrzymanego zadania synchronizacji kontroli źródła](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Odłączanie kontroli źródła

Aby rozłączyć się z kontem usługi GitHub, Otwórz stronę synchronizacji repozytorium, a następnie kliknij przycisk **Rozłącz**. Po rozłączeniu kontroli źródła elementy Runbook, które zostały zsynchronizowane wcześniej, nadal pozostają na koncie usługi Automation, ale strona synchronizacji repozytorium nie zostanie włączona.  

  ![Okno pokazujące przycisk rozłączania w celu rozłączenia kontroli źródła](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji kontroli źródła, zobacz następujące zasoby:  

* [Azure Automation: Integracja kontroli źródła w programie Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Azure Automation: Integrowanie kontroli źródła elementu Runbook za pomocą usługi Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
