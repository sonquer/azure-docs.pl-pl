---
title: Tworzenie harmonogramu dla laboratoriów zajęć w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak tworzyć harmonogramy dla laboratoriów stacjonarnych w Azure Lab Services tak, aby maszyny wirtualne w laboratoriach były uruchamiane i zamykane w określonym czasie.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330538"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Twórz harmonogramy dla laboratoriów stacjonarnych w Azure Lab Services i zarządzaj nimi 
Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium zajęć i zarządzanie nimi: 

> [!IMPORTANT]
> Zaplanowany czas działania maszyn wirtualnych nie jest uwzględniany w stosunku do [przydziału przydzielonego dla użytkownika](how-to-configure-student-usage.md#set-quotas-for-users). Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="set-a-schedule-for-the-lab"></a>Ustaw harmonogram dla laboratorium
Utwórz zaplanowane zdarzenie dla laboratorium, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane w określonych godzinach. Określony wcześniej limit przydziału użytkownika to dodatkowy czas przypisany do każdego użytkownika poza zaplanowanym czasem. 

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj wydarzenie zaplanowane** na pasku narzędzi. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Upewnij się, że w polu **Standardowy** wybrano **Typ zdarzenia**. Wybierz pozycję **Rozpocznij tylko** , aby określić tylko godzinę rozpoczęcia dla maszyn wirtualnych. Wybierz pozycję **Zatrzymaj tylko** , aby określić tylko czas zatrzymania dla maszyn wirtualnych. 
7. W sekcji **Repeat (powtarzanie** ) wybierz bieżący harmonogram. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/select-current-schedule.png)
5. W oknie dialogowym **powtarzanie** wykonaj następujące czynności:
    1. Upewnij się, że dla **każdego tygodnia** jest ustawiona wartość pole **powtarzanie** . 
    3. Określ **datę rozpoczęcia**.
    4. Określ **godzinę rozpoczęcia** uruchamiania maszyn wirtualnych.
    5. Określ **czas zatrzymania** , w którym maszyny wirtualne mają zostać zamknięte. 
    6. Określ **strefę czasową** dla określonego czasu rozpoczęcia i zakończenia. 
    2. Wybierz dni, w których harmonogram ma obowiązywać. W poniższym przykładzie wybrano poniedziałek-czwartek. 
    8. Wybierz pozycję **Zapisz**. 

        ![Ustaw harmonogram powtarzania](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Teraz na stronie **Dodawanie zaplanowanego zdarzenia** dla **notatek (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
4. Na stronie **Dodawanie zaplanowanego zdarzenia** wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Wyświetl harmonogramy w kalendarzu
W widoku kalendarza można zobaczyć zaplanowane daty i godziny, jak pokazano na poniższej ilustracji:

![Harmonogramy w widoku kalendarza](../media/how-to-create-schedules/schedules-calendar.png)

Wybierz przycisk **dzisiaj** w prawym górnym rogu, aby przełączyć się na bieżącą datę w kalendarzu. Wybierz **strzałkę w lewo** , aby przełączyć się do poprzedniego tygodnia i **strzałki w prawo** , aby przejść do następnego tygodnia w kalendarzu. 

## <a name="edit-a-schedule"></a>Edytowanie harmonogramu
Po wybraniu wyróżnionego harmonogramu w kalendarzu widoczne są przyciski umożliwiające **Edytowanie** lub **usuwanie** harmonogramu. 

![Edytuj stronę harmonogramu](../media/how-to-create-schedules/schedule-edit-button.png)

Na stronie **Edytowanie zaplanowanego zdarzenia** możesz zaktualizować harmonogram i wybrać pozycję **Zapisz**. 

## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz wyróżniony harmonogram w kalendarzu, a następnie wybierz przycisk ikony kosza (Usuń):

    ![Przycisk Usuń na pasku narzędzi](../media/how-to-create-schedules/schedule-delete-button.png)
2. W oknie dialogowym **usuwanie zaplanowanego zdarzenia** wybierz pozycję **tak** , aby potwierdzić usunięcie. 



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
