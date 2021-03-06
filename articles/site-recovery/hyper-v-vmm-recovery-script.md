---
title: Dodawanie skryptu do planu odzyskiwania w Azure Site Recovery
description: Dowiedz się, jak dodać skrypt programu VMM do planu odzyskiwania na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084870"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Dodawanie skryptu programu VMM do planu odzyskiwania

W tym artykule opisano sposób tworzenia skryptu System Center Virtual Machine Manager (VMM) i dodawania go do planu odzyskiwania w programie [Azure Site Recovery](site-recovery-overview.md).

Opublikuj wszelkie komentarze lub pytania w dolnej części tego artykułu lub na [forum Recovery Services platformy Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

W planach odzyskiwania można używać skryptów programu PowerShell. Aby można było uzyskać dostęp z planu odzyskiwania, należy utworzyć skrypt i umieścić skrypt w bibliotece programu VMM. Podczas pisania skryptu pamiętaj o następujących kwestiach:

* Upewnij się, że skrypty używają bloków try-catch, dzięki czemu wyjątki są poprawnie obsługiwane.
    - Jeśli w skrypcie wystąpi wyjątek, skrypt przestanie działać, a zadanie pokazuje jako niepowodzenie.
    - Jeśli wystąpi błąd, pozostała część skryptu nie zostanie uruchomiona.
    - Jeśli wystąpi błąd podczas uruchamiania nieplanowanego trybu failover, plan odzyskiwania będzie kontynuowany.
    - Jeśli wystąpi błąd podczas uruchamiania planowanej pracy w trybie failover, plan odzyskiwania zostanie zatrzymany. Napraw skrypt, sprawdź, czy działa zgodnie z oczekiwaniami, a następnie ponownie uruchom plan odzyskiwania.
        - Polecenie `Write-Host` nie działa w skrypcie planu odzyskiwania. Jeśli użyjesz polecenia `Write-Host` w skrypcie, skrypt zakończy się niepowodzeniem. Aby utworzyć dane wyjściowe, Utwórz skrypt serwera proxy, który z kolei uruchamia główny skrypt. Aby upewnić się, że wszystkie dane wyjściowe są potokowe, użyj polecenia **\>\>** .
        - Skrypt przekracza limit czasu, jeśli nie zwróci w ciągu 600 sekund.
        - Jeśli coś jest zapisywana w STDERR, skrypt jest sklasyfikowany jako niepowodzenie. Te informacje są wyświetlane w szczegółach wykonywania skryptu.

* Skrypty w planie odzyskiwania są uruchamiane w kontekście konta usługi programu VMM. Upewnij się, że to konto ma uprawnienia do odczytu zdalnego udziału, w którym znajduje się skrypt. Przetestuj skrypt, aby uruchomić ten sam poziom uprawnień użytkownika co konto usługi programu VMM.
* Polecenia cmdlet programu VMM są dostarczane w module programu Windows PowerShell. Moduł jest instalowany podczas instalowania konsoli programu VMM. Aby załadować moduł do skryptu, użyj następującego polecenia w skrypcie: 

    `Import-Module -Name virtualmachinemanager`

    Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Windows PowerShell i programu VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Upewnij się, że w wdrożeniu programu VMM istnieje co najmniej jeden serwer biblioteki. Domyślnie ścieżka udziału biblioteki serwera programu VMM znajduje się lokalnie na serwerze programu VMM. Nazwa folderu to MSCVMMLibrary.

  Jeśli ścieżka udziału biblioteki jest zdalna (lub jeśli jest lokalna, ale nie została udostępniona przy użyciu MSCVMMLibrary), skonfiguruj udział w następujący sposób, używając \\libserver2. contoso. com\share\ jako przykładu:
  
  1. Otwórz Edytor rejestru, a następnie przejdź do **HKEY_LOCAL_MACHINE \Software\microsoft\azure site Recovery\Registration**.

  1. Zmień wartość parametru **ScriptLibraryPath** na **\\\libserver2.contoso.com\share\\** . Określ pełną nazwę FQDN. Podaj uprawnienia do lokalizacji udziału. Jest to węzeł główny udziału. Aby sprawdzić węzeł główny, w programie VMM przejdź do węzła głównego w bibliotece. Otwierana ścieżka jest katalogiem głównym ścieżki. Jest to ścieżka, której należy użyć w zmiennej.

  1. Przetestuj skrypt przy użyciu konta użytkownika o takim samym poziomie jak konto usługi programu VMM. Korzystając z tych praw użytkownika, program sprawdza, czy te autonomiczne testy są uruchamiane tak samo, jak w planach odzyskiwania. Na serwerze programu VMM Ustaw zasady wykonywania do obejścia w następujący sposób:

     a. Otwórz **64-bitową konsolę programu Windows PowerShell** jako administrator.
     
     b. Wprowadź **obejście Set-executionpolicy**. Aby uzyskać więcej informacji, zobacz [Korzystanie z polecenia cmdlet Set-executionpolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ustaw **obejście Set-executionpolicy** tylko w konsoli programu PowerShell 64-bitowej. Jeśli ustawisz ją na 32-bitową konsolę programu PowerShell, skrypty nie są uruchamiane.

## <a name="add-the-script-to-the-vmm-library"></a>Dodawanie skryptu do biblioteki programu VMM

Jeśli masz lokację źródłową programu VMM, możesz utworzyć skrypt na serwerze programu VMM. Następnie Dołącz skrypt do planu odzyskiwania.

1. W udziale biblioteki Utwórz nowy folder. Na przykład \<nazwy serwera programu VMM > \MSSCVMMLibrary\RPScripts. Umieść folder na źródłowym i docelowym serwerze programu VMM.
1. Utwórz skrypt. Na przykład Nazwij skrypt RPScript. Sprawdź, czy skrypt działa zgodnie z oczekiwaniami.
1. Umieść skrypt w \<nazwa serwera programu VMM > folderze \MSSCVMMLibrary na źródłowym i docelowym serwerze programu VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania

Po dodaniu maszyn wirtualnych lub grup replikacji do planu odzyskiwania i utworzeniu planu można dodać skrypt do grupy.

1. Otwórz plan odzyskiwania.
1. Z listy **krok** wybierz element. Następnie wybierz opcję **skrypt** lub **czynność ręczna**.
1. Określ, czy dodać skrypt lub akcję przed lub po wybranym elemencie. Aby przenieść pozycję skryptu w górę lub w dół, wybierz przyciski **Przenieś w górę** i **Przenieś w dół** .
1. W przypadku dodawania skryptu programu VMM wybierz pozycję **tryb failover do skryptu programu VMM**. W polu **ścieżka skryptu**wprowadź ścieżkę względną do udziału. Na przykład wprowadź **\RPScripts\RPScript.ps1**.
1. W przypadku dodawania elementu Runbook Azure Automation Określ konto usługi Automation, w którym znajduje się element Runbook. Następnie wybierz skrypt elementu Runbook platformy Azure, którego chcesz użyć.
1. Aby upewnić się, że skrypt działa zgodnie z oczekiwaniami, wykonaj test pracy w trybie failover w ramach planu odzyskiwania.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [uruchamianiu trybu failover](site-recovery-failover.md).

