---
title: Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej | Microsoft Docs
description: W tym samouczku dowiesz się, jak dołączać maszynę wirtualną z systemem Windows Server do domeny zarządzanej Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: c40a3b1352c383b8b70a0b14f59265188b77a86d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523689"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Samouczek: dołączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej

Azure Active Directory Domain Services (AD DS platformy Azure) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP, uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Korzystając z domeny zarządzanej AD DS platformy Azure, możesz zapewnić funkcje przyłączania do domeny i zarządzanie maszynami wirtualnymi na platformie Azure. W tym samouczku pokazano, jak utworzyć maszynę wirtualną z systemem Windows Server, a następnie przyłączyć ją do domeny zarządzanej AD DS platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows Server
> * Połącz MASZYNę wirtualną z systemem Windows Server z siecią wirtualną platformy Azure
> * Dołącz maszynę wirtualną do domeny zarządzanej AD DS platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.
    * Upewnij się, że Azure AD Connect synchronizacji skrótów haseł lub samoobsługowego resetowania hasła, aby konto mogło zalogować się do domeny zarządzanej AD DS platformy Azure.
* Host usługi Azure bastionu wdrożony w sieci wirtualnej platformy Azure AD DS.
    * W razie konieczności [Utwórz hosta usługi Azure bastionu][azure-bastion].

Jeśli masz już maszynę wirtualną do przyłączenia do domeny, przejdź do sekcji, aby [dołączyć maszynę wirtualną do domeny zarządzanej AD DS platformy Azure](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz maszynę wirtualną z systemem Windows Server do przyłączenia do domeny zarządzanej AD DS platformy Azure przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Windows Server

Aby dowiedzieć się, jak przyłączyć komputer do domeny zarządzanej AD DS platformy Azure, Utwórz teraz maszynę wirtualną z systemem Windows Server. Ta maszyna wirtualna jest połączona z siecią wirtualną platformy Azure, która zapewnia łączność z domeną zarządzaną platformy Azure AD DS. Proces dołączania do domeny zarządzanej AD DS platformy Azure jest taki sam jak dołączanie zwykłej domeny Active Directory Domain Services lokalnej.

Jeśli masz już maszynę wirtualną do przyłączenia do domeny, przejdź do sekcji, aby [dołączyć maszynę wirtualną do domeny zarządzanej AD DS platformy Azure](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. W obszarze **wprowadzenie**wybierz pozycję **Windows Server 2016 Datacenter**.

    ![Wybierz, aby utworzyć maszynę wirtualną z systemem Windows Server 2016 Datacenter w Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. W oknie **podstawy** Skonfiguruj podstawowe ustawienia dla maszyny wirtualnej. Pozostaw wartości domyślne *opcji dostępności*, *obrazu*i *rozmiaru*.

    | Parametr            | Sugerowana wartość   |
    |----------------------|-------------------|
    | Grupa zasobów       | Wybierz lub Utwórz grupę zasobów, na przykład grupa *zasobów* |
    | Nazwa maszyny wirtualnej | Wprowadź nazwę maszyny wirtualnej, na przykład *myVM* |
    | Region               | Wybierz region, w którym chcesz utworzyć maszynę wirtualną, na przykład *Wschodnie stany USA* |
    | Nazwa użytkownika             | Wprowadź nazwę użytkownika dla konta administratora lokalnego, które ma zostać utworzone na maszynie wirtualnej, na przykład *azureuser* |
    | Hasło             | Wprowadź wartość, a następnie potwierdź hasło do konta administratora lokalnego na maszynie wirtualnej. Nie określaj poświadczeń konta użytkownika domeny. |

1. Domyślnie maszyny wirtualne utworzone na platformie Azure są dostępne z Internetu przy użyciu protokołu RDP. Po włączeniu protokołu RDP mogą wystąpić automatyczne ataki, które mogą wyłączyć konta z nazwami wspólnymi, takimi jak *administrator* czy *administrator* , z powodu wielokrotnych nieudanych prób logowania.

    Protokół RDP powinien być włączony tylko w razie potrzeby i ograniczony do zestawu autoryzowanych zakresów adresów IP. Ta konfiguracja pozwala ulepszyć zabezpieczenia maszyny wirtualnej i zmniejsza obszar pod kątem potencjalnego ataku. Możesz również utworzyć hosta usługi Azure bastionu i użyć Azure Portal go, który umożliwia dostęp tylko za pośrednictwem protokołu SSL. W następnym kroku tego samouczka użyjesz hosta usługi Azure bastionu, aby bezpiecznie połączyć się z maszyną wirtualną.

    Na razie Wyłącz bezpośrednie połączenia RDP z maszyną wirtualną.

    W obszarze **publiczne porty przychodzące**zaznacz opcję *Brak*.

1. Gdy skończysz, wybierz pozycję **Dalej: dyski**.
1. Z menu rozwijanego **typ dysku systemu operacyjnego**wybierz *SSD w warstwie Standardowa*, a następnie wybierz **Dalej: sieć**.
1. Maszyna wirtualna musi nawiązać połączenie z podsiecią sieci wirtualnej platformy Azure, która może komunikować się z podsiecią, w której wdrożono domenę zarządzaną platformy Azure AD DS. Zalecamy, aby domena zarządzana AD DS platformy Azure została wdrożona w swojej własnej dedykowanej podsieci. Nie Wdrażaj maszyny wirtualnej w tej samej podsieci co domena zarządzana AD DS platformy Azure.

    Istnieją dwa główne sposoby wdrożenia maszyny wirtualnej i łączenia się z odpowiednią podsiecią sieci wirtualnej:
    
    * Utwórz lub wybierz istniejącą podsieć w tej samej sieci wirtualnej, w której wdrożono domenę zarządzaną platformy Azure AD DS.
    * Wybierz podsieć w sieci wirtualnej platformy Azure, która jest połączona z nią przy użyciu [komunikacji równorzędnej sieci wirtualnej platformy Azure][vnet-peering].
    
    W przypadku wybrania podsieci sieci wirtualnej, która nie jest połączona z podsiecią dla wystąpienia usługi Azure AD DS, nie można przyłączyć maszyny wirtualnej do domeny zarządzanej. Na potrzeby tego samouczka utworzymy nową podsieć w sieci wirtualnej platformy Azure.

    W okienku **Sieć** wybierz sieć wirtualną, w której wdrożono domenę zarządzaną platformy Azure AD DS, na przykład *aaads-VNET*
1. W tym przykładzie istniejąca *podsieć aaads* jest pokazana, z którą jest połączona domena zarządzana platformy AD DS Azure. Nie łącz maszyny wirtualnej z tą podsiecią. Aby utworzyć podsieć dla maszyny wirtualnej, wybierz pozycję **Zarządzaj konfiguracją podsieci**.

    ![Wybierz, aby zarządzać konfiguracją podsieci w Azure Portal](./media/join-windows-vm/manage-subnet.png)

1. W menu po lewej stronie okna Sieć wirtualna wybierz pozycję **przestrzeń adresowa**. Sieć wirtualna jest tworzona z użyciem pojedynczej przestrzeni adresowej *10.0.1.0/24*, która jest używana przez domyślną podsieć.

    Dodaj dodatkowy zakres adresów IP do sieci wirtualnej. Rozmiar tego zakresu adresów oraz rzeczywisty zakres adresów IP, który ma być używany, zależy od innych już wdrożonych zasobów sieciowych. Zakres adresów IP nie powinien nakładać się na wszystkie istniejące zakresy adresów w środowisku platformy Azure lub lokalnym. Upewnij się, że rozmiar zakresu adresów IP jest wystarczająco duży, aby liczba maszyn wirtualnych, które mają zostać wdrożone w podsieci.

    W poniższym przykładzie dodano dodatkowy zakres adresów IP *10.0.2.0/24* . Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

    ![Dodaj dodatkowy zakres adresów IP sieci wirtualnej w Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Następnie w menu po lewej stronie okna Sieć wirtualna wybierz pozycję **podsieci**, a następnie wybierz pozycję **+ podsieć** , aby dodać podsieć.

1. Wybierz pozycję **+ podsieć**, a następnie wprowadź nazwę podsieci, na przykład *Zarządzanie*. Podaj **zakres adresów (blok CIDR)** , taki jak *10.0.2.0/24*. Upewnij się, że ten zakres adresów IP nie pokrywa się z żadnym innym istniejącymi zakresami adresów platformy Azure lub lokalnymi. Pozostaw wartości domyślne innych opcji, a następnie wybierz przycisk **OK**.

    ![Utwórz konfigurację podsieci w Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Utworzenie podsieci trwa kilka sekund. Po jego utworzeniu wybierz pozycję *X* , aby zamknąć okno podsieć.
1. Wróć do okienka **Sieć** , aby utworzyć maszynę wirtualną, wybierz podsieć utworzoną z menu rozwijanego, na przykład *Zarządzanie*. Upewnij się, że wybrano poprawną podsieć i nie Wdróż maszyny wirtualnej w tej samej podsieci co domena zarządzana AD DS Azure.
1. Pozostaw wartości domyślne innych opcji, a następnie wybierz pozycję **Zarządzanie**.
1. Ustaw wartość ustawienia **Diagnostyka rozruchu** *.* Pozostaw wartości domyślne innych opcji, a następnie wybierz pozycję **Przegląd + Utwórz**.
1. Przejrzyj ustawienia maszyny wirtualnej, a następnie wybierz pozycję **Utwórz**.

Utworzenie maszyny wirtualnej może potrwać kilka minut. Azure Portal pokazuje stan wdrożenia. Gdy maszyna wirtualna jest gotowa, wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu maszyny wirtualnej w Azure Portal po jego pomyślnym utworzeniu](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server

Aby bezpiecznie połączyć się z maszynami wirtualnymi, użyj hosta usługi Azure bastionu. W przypadku usługi Azure bastionu Host zarządzany jest wdrażany w sieci wirtualnej i zapewnia połączenia protokołu RDP lub SSH opartego na sieci Web z maszynami wirtualnymi. Dla maszyn wirtualnych nie są wymagane żadne publiczne adresy IP i nie trzeba otwierać reguł sieciowej grupy zabezpieczeń dla zewnętrznego ruchu zdalnego. Możesz połączyć się z maszynami wirtualnymi przy użyciu Azure Portal z przeglądarki sieci Web.

Aby użyć hosta bastionu do nawiązania połączenia z maszyną wirtualną, wykonaj następujące czynności:

1. W okienku **Przegląd** dla maszyny wirtualnej wybierz pozycję **Połącz**, a następnie **bastionu**.

    ![Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu programu bastionu w Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Wprowadź poświadczenia dla maszyny wirtualnej, która została określona w poprzedniej sekcji, a następnie wybierz pozycję **Połącz**.

   ![Połącz się za pomocą hosta bastionu w Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

W razie potrzeby Zezwól przeglądarce sieci Web na otwieranie wyskakujących okienek w celu wyświetlenia połączenia bastionu. Nawiązywanie połączenia z maszyną wirtualną trwa kilka sekund.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Dołącz maszynę wirtualną do domeny zarządzanej AD DS platformy Azure

Po utworzeniu maszyny wirtualnej i połączeniu RDP opartym na sieci Web za pomocą usługi Azure bastionu Przyjrzyjmy maszynę wirtualną z systemem Windows Server do domeny zarządzanej AD DS platformy Azure. Ten proces jest taki sam jak komputer łączący się z zwykłą lokalną domeną Active Directory Domain Services.

1. Jeśli **Menedżer serwera** nie zostanie otwarta domyślnie po zalogowaniu się do maszyny wirtualnej, wybierz menu **Start** , a następnie wybierz pozycję **Menedżer serwera**.
1. W lewym okienku okna **Menedżer serwera** wybierz pozycję **serwer lokalny**. W obszarze **Właściwości** w okienku po prawej stronie wybierz pozycję **Grupa robocza**.

    ![Otwórz Menedżer serwera na maszynie wirtualnej i edytuj Właściwość grupy roboczej](./media/join-windows-vm/server-manager.png)

1. W oknie **Właściwości systemu** wybierz pozycję **Zmień** , aby dołączyć do domeny zarządzanej AD DS platformy Azure.

    ![Wybierz, aby zmienić właściwości grupy roboczej lub domeny](./media/join-windows-vm/change-domain.png)

1. W polu **domena** Określ nazwę domeny zarządzanej platformy Azure AD DS, na przykład *contoso.com*, a następnie wybierz **przycisk OK**.

    ![Określ domenę zarządzaną AD DS platformy Azure do przyłączenia](./media/join-windows-vm/join-domain.png)

1. Wprowadź poświadczenia domeny w celu przyłączenia do domeny. Użyj poświadczeń dla użytkownika, który należy do grupy *administratorów DC usługi Azure AD* . Tylko członkowie tej grupy mają uprawnienia do przyłączania maszyn do domeny zarządzanej AD DS platformy Azure. Konto musi być częścią domeny zarządzanej platformy Azure AD DS lub dzierżawy usługi Azure AD z zewnętrznych katalogów skojarzonych z dzierżawą usługi Azure AD nie można prawidłowo uwierzytelnić podczas procesu przyłączania do domeny. Poświadczenia konta można określić w jeden z następujących sposobów:

    * **Format nazwy UPN** (zalecane) — wprowadź sufiks głównej nazwy użytkownika (UPN) dla konta użytkownika, zgodnie z konfiguracją w usłudze Azure AD. Na przykład sufiks nazwy UPN *contosoadmin* użytkownika będzie `contosoadmin@contoso.onmicrosoft.com`. Istnieje kilka typowych przypadków użycia, w których format nazwy UPN może być niezawodnie używany do logowania się do domeny, a nie do formatu *sAMAccountName* :
        * Jeśli prefiks nazwy UPN użytkownika jest długi, taki jak *deehasareallylongname* *, może to* być automatycznie generowane.
        * Jeśli wielu użytkowników ma ten sam prefiks nazwy UPN w dzierżawie usługi Azure AD, np. *Dee*, jego format *sAMAccountName* może być automatycznie generowany.
    * **Format sAMAccountName** — wprowadź nazwę konta w formacie *sAMAccountName* . Na przykład nazwa *sAMAccountName* *contosoadmin* użytkownika będzie `CONTOSO\contosoadmin`.

1. Przyłączenie do domeny zarządzanej platformy Azure AD DS może potrwać kilka sekund. Po zakończeniu następujący komunikat powita cię w domenie:

    ![Witamy w domenie](./media/join-windows-vm/join-domain-successful.png)

    Kliknij przycisk **OK**, aby kontynuować.

1. Aby ukończyć proces przyłączenia do domeny zarządzanej usługi Azure AD DS, uruchom ponownie maszynę wirtualną.

> [!TIP]
> Można przyłączyć do domeny maszynę wirtualną przy użyciu programu PowerShell przy użyciu polecenia cmdlet [Add-Computer][add-computer] . Poniższy przykład dołącza do domeny *contoso* , a następnie ponownie uruchamia maszynę wirtualną. Po wyświetleniu monitu wprowadź poświadczenia dla użytkownika, który należy do grupy *administratorów DC usługi Azure AD* :
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Aby przyłączyć do domeny maszynę wirtualną bez nawiązywania z nią połączenia i ręcznie skonfigurować połączenie, można użyć polecenia cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Po ponownym uruchomieniu maszyny wirtualnej z systemem Windows Server wszystkie zasady zastosowane w domenie zarządzanej usługi Azure AD DS są wypychane do maszyny wirtualnej. Teraz możesz także zalogować się do maszyny wirtualnej z systemem Windows Server przy użyciu odpowiednich poświadczeń domeny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W następnym samouczku użyjesz tej maszyny wirtualnej z systemem Windows Server, aby zainstalować narzędzia do zarządzania, które umożliwiają administrowanie domeną zarządzaną platformy Azure AD DS. Jeśli nie chcesz kontynuować korzystania z tej serii samouczków, przejrzyj następujące kroki czyszczenia, aby [usunąć maszynę wirtualną](#delete-the-vm). W przeciwnym razie [Przejdź do następnego samouczka](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Odłączanie maszyny wirtualnej od domeny zarządzanej AD DS platformy Azure

Aby usunąć maszynę wirtualną z domeny zarządzanej AD DS platformy Azure, wykonaj kroki ponownie, aby [dołączyć maszynę wirtualną do domeny](#join-the-vm-to-the-azure-ad-ds-managed-domain). Zamiast przyłączać się do domeny zarządzanej AD DS platformy Azure, wybierz opcję dołączenia do grupy roboczej, takiej jak domyślna *Grupa robocza*. Po ponownym uruchomieniu maszyny wirtualnej obiekt komputera zostanie usunięty z domeny zarządzanej AD DS platformy Azure.

Jeśli [usuniesz maszynę wirtualną](#delete-the-vm) bez rozłączenia z domeną, obiekt oddzielony komputer zostanie pozostawiony na platformie Azure AD DS.

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Jeśli nie korzystasz z tej maszyny wirtualnej systemu Windows Server, Usuń maszynę wirtualną, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów** .
1. Wybierz grupę zasobów, *na przykład grupa zasobów.*
1. Wybierz maszynę wirtualną, na przykład *myVM*, a następnie wybierz pozycję **Usuń**. Wybierz pozycję **tak** , aby potwierdzić usunięcie zasobu. Usunięcie maszyny wirtualnej może potrwać kilka minut.
1. Po usunięciu maszyny wirtualnej wybierz dysk systemu operacyjnego, kartę sieciową i inne zasoby z prefiksem *myVM* i usuń je.

## <a name="troubleshoot-domain-join-issues"></a>Rozwiązywanie problemów z przyłączaniem do domeny

Maszyna wirtualna z systemem Windows Server powinna pomyślnie dołączyć do domeny zarządzanej AD DS platformy Azure, tak jak w przypadku zwykłego komputera lokalnego, dołączenie do domeny Active Directory Domain Services. Jeśli maszyna wirtualna z systemem Windows Server nie może przyłączyć się do domeny zarządzanej usługi Azure AD DS, oznacza to, że występuje problem związany z łącznością lub poświadczeniami. Zapoznaj się z poniższymi sekcjami rozwiązywania problemów, aby pomyślnie dołączyć do domeny zarządzanej.

### <a name="connectivity-issues"></a>Problemy z łącznością

Jeśli nie otrzymasz monitu z prośbą o podanie poświadczeń w celu przyłączenia do domeny, wystąpił problem z łącznością. Maszyna wirtualna nie może nawiązać połączenia z domeną zarządzaną platformy Azure AD DS w sieci wirtualnej.

Po wykonaniu każdego z tych kroków rozwiązywania problemów spróbuj ponownie dołączyć maszynę wirtualną z systemem Windows Server do domeny zarządzanej.

* Sprawdź, czy maszyna wirtualna jest połączona z tą samą siecią wirtualną, w której jest włączona usługa Azure AD DS lub czy ma równorzędne połączenie sieciowe.
* Spróbuj wysłać polecenie ping do nazwy domeny DNS domeny zarządzanej, takiej jak `ping contoso.com`.
    * Jeśli żądanie ping nie powiedzie się, spróbuj wysłać polecenie ping do adresów IP dla domeny zarządzanej, takich jak `ping 10.0.0.4`. Adres IP dla Twojego środowiska jest wyświetlany na stronie *Właściwości* po wybraniu domeny zarządzanej AD DS platformy Azure z listy zasobów platformy Azure.
    * Jeśli można wysłać polecenie ping do adresu IP, ale nie do domeny, może to spowodować niepoprawne skonfigurowanie usługi DNS. Upewnij się, że adresy IP domeny zarządzanej są skonfigurowane jako serwery DNS dla sieci wirtualnej.
* Spróbuj opróżnić pamięć podręczną programu rozpoznawania nazw DNS na maszynie wirtualnej przy użyciu polecenia `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemy związane z poświadczeniami

Jeśli zostanie wyświetlony monit z prośbą o podanie poświadczeń w celu przyłączenia do domeny, ale po wprowadzeniu tych poświadczeń, maszyna wirtualna będzie mogła nawiązać połączenie z domeną zarządzaną platformy Azure AD DS. Podane poświadczenia nie umożliwiają przełączenia maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure.

Po wykonaniu każdego z tych kroków rozwiązywania problemów spróbuj ponownie dołączyć maszynę wirtualną z systemem Windows Server do domeny zarządzanej.

* Upewnij się, że określone konto użytkownika należy do grupy *Administratorzy domeny usługi AAD* .
* Upewnij się, że konto jest częścią domeny zarządzanej usługi Azure AD DS lub dzierżawy usługi Azure AD. Konta z zewnętrznych katalogów skojarzonych z dzierżawą usługi Azure AD nie mogą być prawidłowo uwierzytelniane podczas procesu przyłączania do domeny.
* Spróbuj użyć formatu UPN, aby określić poświadczenia, takie jak `contosoadmin@contoso.onmicrosoft.com`. Jeśli istnieje wielu użytkowników z tym samym prefiksem nazwy UPN w dzierżawie lub jeśli prefiks nazwy UPN jest zbyt długi, zostanie automatycznie wygenerowany *sAMAccountName* dla Twojego konta. W takich przypadkach format *sAMAccountName* dla konta może być inny niż oczekiwany lub używany w domenie lokalnej.
* Sprawdź, czy [włączono synchronizację haseł][password-sync] z domeną zarządzaną. Bez tego kroku konfiguracji wymagane skróty haseł nie będą obecne w domenie zarządzanej AD DS platformy Azure w celu poprawnego uwierzytelnienia próby logowania.
* Poczekaj na zakończenie synchronizacji haseł. Po zmianie hasła konta użytkownika automatyczna synchronizacja w tle z usługi Azure AD aktualizuje hasło w usłudze Azure AD DS. Udostępnienie hasła przez przyłączenie do domeny wymaga pewnego czasu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows Server
> * Nawiązywanie połączenia z MASZYNą wirtualną z systemem Windows Server w sieci wirtualnej platformy Azure
> * Dołącz maszynę wirtualną do domeny zarządzanej AD DS platformy Azure

Aby administrować domeną zarządzaną platformy Azure AD DS, skonfiguruj maszynę wirtualną zarządzania przy użyciu Centrum administracyjne usługi Active Directory (usługach ADAC).

> [!div class="nextstepaction"]
> [Instalowanie narzędzi administracyjnych na maszynie wirtualnej zarządzania](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
