---
title: Użyj Azure Automation, aby uruchomić zadanie w StorSimple Data Manager
description: Dowiedz się, jak używać Azure Automation do wyzwalania StorSimple Data Manager zadań
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273991"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Użyj Azure Automation, aby wyzwolić zadanie

W tym artykule wyjaśniono, jak można użyć funkcji przekształcania danych w ramach usługi StorSimple Data Manager do przekształcania danych urządzenia StorSimple. Zadanie przekształcania danych można uruchomić na dwa sposoby: 

 - Zastosowanie zestawu SDK dla platformy .NET
 - Użyj Azure Automation elementu Runbook
 
W tym artykule szczegółowo opisano sposób tworzenia elementu Runbook Azure Automation, a następnie użycia go do inicjowania zadania przekształcania danych. Aby dowiedzieć się więcej o tym, jak zainicjować transformację danych za pomocą zestawu .NET SDK, przejdź do [zestawu .NET SDK, aby wyzwolić zadania przekształcania danych](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Azure PowerShell zainstalowany na komputerze klienckim. [Pobierz Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Poprawnie skonfigurowana Definicja zadania w ramach usługi StorSimple Data Manager w grupie zasobów.
*   Pobierz plik [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) z repozytorium GitHub. 
*   Pobierz skrypt [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

### <a name="set-up-the-automation-account"></a>Konfigurowanie konta usługi Automation

1. Utwórz konto usługi Automatyzacja Azure Uruchom jako w Azure Portal. Aby to zrobić, przejdź do **witryny Azure Marketplace, > wszystko** , a następnie wyszukaj **automatyzację**. Wybierz pozycję **konta usługi Automation**.

    ![Utwórz konto usługi Automation as](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Aby dodać nowe konto usługi Automation, kliknij pozycję **+ Dodaj**.

    ![Utwórz konto usługi Automation as](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. W oknie **Dodawanie automatyzacji**:

   1. Podaj **nazwę** konta usługi Automation.
   2. Wybierz **subskrypcję** połączoną z usługą StorSimple Data Manager.
   3. Utwórz nową grupę zasobów lub wybierz ją z istniejącej grupy zasobów.
   4. Wybierz **lokalizację**.
   5. Pozostaw wybraną opcję Domyślne **Utwórz konto Uruchom jako** .
   6. Aby uzyskać link do szybkiego dostępu do pulpitu nawigacyjnego, zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**. Kliknij przycisk **Utwórz**.

      ![Utwórz konto usługi Automation as](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Po pomyślnym utworzeniu konta usługi Automation zostanie wyświetlone powiadomienie.
    
      ![Powiadomienie o wdrożeniu konta usługi Automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Aby uzyskać więcej informacji, przejdź do [obszaru Tworzenie konta Uruchom jako](../automation/automation-create-runas-account.md).

3. W nowo utworzonym koncie przejdź do obszaru **zasoby udostępnione > moduły** i kliknij pozycję **+ Dodaj moduł**.

    ![Importuj moduł 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Przejdź do lokalizacji pliku `DataTransformationApp.zip` z komputera lokalnego, a następnie wybierz i otwórz moduł. Kliknij przycisk **OK** , aby zaimportować moduł.

    ![Importuj moduł 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Gdy Azure Automation importuje moduł do konta, wyodrębnia metadane dotyczące modułu. Ta operacja może potrwać kilka minut.

   ![Importuj moduł 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Otrzymasz powiadomienie, że moduł jest wdrażany, i inne powiadomienie po zakończeniu procesu.  Stan w **modułach** zmienia się na **dostępne**.

    ![Importuj moduł 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie, publikowanie i Uruchamianie elementu Runbook usługi Automation

Wykonaj następujące kroki, aby zaimportować, opublikować i uruchomić element Runbook, aby wyzwolić definicję zadania.

1. W witrynie Azure Portal otwórz konto usługi Automation. Przejdź do **usługi Process Automation > elementów Runbook** , a następnie kliknij pozycję **+ Dodaj element Runbook**.

    ![Dodaj element Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. W obszarze **Dodawanie elementu Runbook**kliknij pozycję **Importuj istniejący element Runbook**.

3. Wskaż Azure PowerShell plik skryptu `Trigger-DataTransformation-Job.ps1` dla **pliku elementu Runbook**. Typ elementu Runbook jest wybierany automatycznie. Podaj nazwę i opcjonalny opis elementu Runbook. Kliknij przycisk **Utwórz**.

    ![Dodaj element Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nowy element Runbook zostanie wyświetlony na liście elementów Runbook dla konta usługi Automation. Wybierz i kliknij ten element Runbook.

    ![Dodaj element Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edytuj element Runbook, a następnie kliknij pozycję okienko **testowania** .

    ![Dodaj element Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Podaj parametry, takie jak nazwa usługi StorSimple Data Manager, skojarzona Grupa zasobów i nazwa definicji zadania. **Rozpocznij** test. Raport jest generowany po zakończeniu przebiegu. Aby uzyskać więcej informacji, przejdź do procedury [testowania elementu Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Dodaj element Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Sprawdź dane wyjściowe z elementu Runbook w okienku testów. Jeśli jest spełniony, zamknij okienko. Kliknij przycisk **Publikuj** i po wyświetleniu monitu o potwierdzenie, Potwierdź i Opublikuj element Runbook.

    ![Dodaj element Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Wróć do **elementów Runbook** i wybierz nowo utworzony element Runbook.

    ![Dodawanie elementu Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Uruchom** element Runbook. W obszarze **Start Runbook**wprowadź wszystkie parametry. Kliknij przycisk **OK** , aby przesłać i uruchomić zadanie przekształcania danych.

10. Aby monitorować postęp zadania w Azure Portal, przejdź do obszaru **zadania** w usłudze StorSimple Data Manager. Wybierz i kliknij zadanie, aby wyświetlić szczegóły zadania.

    ![Dodaj element Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Następne kroki

[Użyj StorSimple Data Manager interfejsu użytkownika do przekształcania danych](storsimple-data-manager-ui.md).
