---
title: Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku maszyny wirtualne są dostępne w laboratorium skonfigurowanym przez nauczyciela.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 2ac9e8b8d0635eceb7d4f85ad867b102f7d064f5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585161"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano sposób rejestracji w laboratorium zajęć, wyświetlania wszystkich laboratoriów, do których można uzyskać dostęp, uruchamiania/zatrzymywania maszyny wirtualnej w laboratorium oraz nawiązywania połączenia z maszyną wirtualną. 

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego od nauczyciela. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 

    > [!NOTE]
    > Do korzystania z Azure Lab Services jest wymagany konto Microsoft. Jeśli próbujesz zalogować się do portalu przy użyciu innych konto Microsoft, takich jak Yahoo lub konta Google, postępuj zgodnie z instrukcjami, aby utworzyć konto Microsoft, który zostanie połączony z niekonto Microsoft. Następnie postępuj zgodnie z instrukcjami, aby ukończyć proces rejestracji. 
1. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 
1. Zaczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny wirtualnej Zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zostanie wyświetlona ikona reprezentująca **system operacyjny (OS)** maszyny wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Zobaczysz ikonę/przyciski w dolnej części kafelka, aby uruchomić/zatrzymać maszynę wirtualną, a następnie nawiązać połączenie z maszyną wirtualną. 
    1. Na prawo od przycisków zobaczysz stan maszyny wirtualnej. Upewnij się, że stan maszyny wirtualnej jest **zatrzymany**.

        ![Maszyna wirtualna w stanie zatrzymania](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Uruchamianie lub zatrzymywanie maszyny wirtualnej
1. **Uruchom** maszynę wirtualną, wybierając pierwszy przycisk, jak pokazano na poniższej ilustracji. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Upewnij się, że stan maszyny wirtualnej to **uruchomiono**. 

    ![Maszyna wirtualna w stanie uruchomienia](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Zauważ, że ikona pierwszego przycisku została zmieniona, aby reprezentować operację **zatrzymania** . Możesz wybrać ten przycisk, aby zatrzymać maszynę wirtualną. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz drugi przycisk, jak pokazano na poniższej ilustracji, aby **nawiązać połączenie** z maszyną wirtualną laboratorium. 

    ![Łączenie z maszyną wirtualną](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. W przypadku maszyn wirtualnych z **systemem Windows** Zapisz plik **RDP** na dysku twardym. Otwórz plik RDP, aby nawiązać połączenie z maszyną wirtualną. Zaloguj się na komputerze przy użyciu **nazwy użytkownika** i **hasła** uzyskanych z nauczycieli/profesor. 
    3. W przypadku maszyn wirtualnych z **systemem Linux** można użyć protokołu **SSH** lub **RDP** (jeśli jest włączony), aby połączyć się z nimi. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Jeśli używasz **komputera Mac** do nawiązywania połączenia z maszyną wirtualną laboratorium, postępuj zgodnie z instrukcjami w następnej sekcji. 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac
W tej sekcji pokazano, jak student może połączyć się z maszyną wirtualną z komputera Mac przy użyciu protokołu RDP.

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>Krok 1. Instalowanie Pulpit zdalny Microsoft na komputerze Mac
1. Otwórz sklep App Store na komputerze Mac, a następnie wyszukaj **pulpit zdalny Microsoft**.

    ![Pulpit zdalny Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Zainstaluj najnowszą wersję programu Pulpit zdalny Microsoft. 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>Krok 2. Uzyskiwanie dostępu do maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputerze z zainstalowanym **pulpit zdalny Microsoft** . Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z maszyną wirtualną](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Wybierz pozycję **Kontynuuj** , jeśli zostanie wyświetlone następujące ostrzeżenie. 

    ![Ostrzeżenie dotyczące certyfikatu](../media/how-to-use-classroom-lab/certificate-error.png)
1. Powinna zostać wyświetlona maszyna wirtualna. 

    > [!NOTE]
    > Poniższy przykład dotyczy maszyny wirtualnej z systemem CentOS Linux. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Pasek postępu 
Pasek postępu na kafelku pokazuje liczbę godzin użycia w odniesieniu do liczby godzin [przypisanych](how-to-configure-student-usage.md#set-quotas-for-users) do użytkownika. Ten czas jest dodatkowym czasem przydzielonym do czasu zaplanowanym dla laboratorium. Kolor paska postępu i tekstu pod paskiem postępu różnią się w zależności od następujących scenariuszy:

- Jeśli klasa jest w toku (w ramach harmonogramu klasy), pasek postępu jest wyszarzony do reprezentowania godzin przydziału nie jest używany. 

    ![Pasek postępu w kolorze szarym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Jeśli limit przydziału nie zostanie przypisany (zero godzin), tekst **dostępny podczas klas** jest pokazywany zamiast paska postępu. 
    
    ![Stan, gdy nie ustawiono limitu przydziału](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Jeśli wykorzystano **limit przydziału**, kolor paska postępu jest **czerwony**. 

    ![Pasek postępu w kolorze czerwonym](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Kolor paska postępu jest **niebieski** , gdy jest on poza zaplanowanym terminem dla laboratorium i został użyty jakiś czas przydziału. 

    ![Pasek postępu w kolorze niebieskim](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Wyświetlanie wszystkich laboratoriów
Po zarejestrowaniu się w laboratoriach możesz wyświetlić wszystkie laboratoria na potrzeby zajęć, wykonując następujące czynności: 

1. Przejdź do strony [https://labs.azure.com](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Zaloguj się w usłudze przy użyciu konta użytkownika, którego użyto do zarejestrowania się do laboratorium. 
3. Upewnij się, że są wyświetlone wszystkie laboratoria, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
 