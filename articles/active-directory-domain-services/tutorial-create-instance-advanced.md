---
title: Samouczek — Tworzenie wystąpienia Azure Active Directory Domain Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować wystąpienie Azure Active Directory Domain Services i określić zaawansowane opcje konfiguracji przy użyciu Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5e969ed4f525d0b3d17339b9f9a6111ad81b0125
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931639"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Samouczek: Tworzenie i Konfigurowanie wystąpienia Azure Active Directory Domain Services z zaawansowanymi opcjami konfiguracji

Azure Active Directory Domain Services (AD DS platformy Azure) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP, uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Te usługi domenowe są używane bez konieczności samodzielnego wdrażania kontrolerów domeny i zarządzania nimi. Platforma Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta Integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych, a także umożliwia korzystanie z istniejących grup i kont użytkowników w celu zabezpieczenia dostępu do zasobów.

Można [utworzyć domenę zarządzaną przy użyciu opcji konfiguracji domyślnej][tutorial-create-instance] sieci i synchronizacji lub ręcznie zdefiniować te ustawienia. W tym samouczku pokazano, jak definiować zaawansowane opcje konfiguracji, aby utworzyć i skonfigurować wystąpienie usługi Azure AD DS przy użyciu Azure Portal.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie ustawień systemu DNS i sieci wirtualnej dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
> * Dodawanie użytkowników administracyjnych do zarządzania domeną
> * Włączanie synchronizacji skrótów haseł

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Aby włączyć usługę Azure AD DS, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usługi Azure AD DS, musisz mieć uprawnienia *współautora* w ramach subskrypcji platformy Azure.

Chociaż nie jest to wymagane w przypadku usługi Azure AD DS, zaleca się [skonfigurowanie funkcji samoobsługowego resetowania hasła (SSPR)][configure-sspr] dla dzierżawy usługi Azure AD. Użytkownicy mogą zmieniać swoje hasła bez SSPR, ale SSPR pomagają w przypadku zapomnienia hasła i konieczności jego zresetowania.

> [!IMPORTANT]
> Po utworzeniu domeny zarządzanej AD DS platformy Azure nie można przenieść wystąpienia do innej grupy zasobów, sieci wirtualnej, subskrypcji itd. Podczas wdrażania wystąpienia usługi Azure AD DS należy zadbać o wybranie najbardziej odpowiedniej subskrypcji, grupy zasobów, regionu i sieci wirtualnej.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz i skonfigurujesz wystąpienie usługi Azure AD DS przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Utwórz wystąpienie i skonfiguruj ustawienia podstawowe

Aby uruchomić kreatora **włączania Azure AD Domain Services** , wykonaj następujące czynności:

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.
1. Wprowadź *usługi domenowe* na pasku wyszukiwania, a następnie wybierz *Azure AD Domain Services* z sugestii wyszukiwania.
1. Na stronie Azure AD Domain Services wybierz pozycję **Utwórz**. Zostanie uruchomiony Kreator **włączania Azure AD Domain Services** .
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć domenę zarządzaną.
1. Wybierz **grupę zasobów** , do której powinna należeć domena zarządzana. Wybierz opcję **Utwórz nową** lub wybierz istniejącą grupę zasobów.

Podczas tworzenia wystąpienia usługi Azure AD DS należy określić nazwę DNS. Po wybraniu tej nazwy DNS należy wziąć pod uwagę następujące kwestie:

* **Nazwa wbudowanej domeny:** Domyślnie używana jest wbudowana nazwa domeny katalogu (sufiks *. onmicrosoft.com* ). Jeśli chcesz włączyć bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu, nie możesz utworzyć certyfikatu cyfrowego, aby zabezpieczyć połączenie z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *. onmicrosoft.com* , więc urząd certyfikacji (CA) nie wystawia certyfikatu.
* **Niestandardowe nazwy domen:** Najbardziej typowym podejściem jest określenie niestandardowej nazwy domeny, która zwykle jest już posiadana i ma Routing. W przypadku używania routingu, domeny niestandardowej, ruch może prawidłowo przepływać w miarę potrzeb do obsługi aplikacji.
* **Sufiksy domen bez routingu:** Zwykle zalecamy uniknięcie sufiksu nazwy domeny bez obsługi routingu, takiego jak *contoso. Local*. Sufiks *. Local* nie jest w stanie routingu i może powodować problemy z ROZPOZNAWANIEM nazw DNS.

> [!TIP]
> Jeśli utworzysz niestandardową nazwę domeny, weź pod uwagę istniejące przestrzenie nazw DNS. Zaleca się dołączenie unikatowego prefiksu dla nazwy domeny. Na przykład jeśli nazwa główna DNS to *contoso.com*, Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu niestandardowej nazwy domeny *Corp.contoso.com* lub *ds.contoso.com*. W środowisku hybrydowym z lokalnym środowiskiem AD DS te prefiksy mogą być już używane. Użyj unikatowego prefiksu dla usługi Azure AD DS.
>
> Możesz użyć głównej nazwy DNS dla domeny zarządzanej platformy Azure AD DS, ale może być konieczne utworzenie dodatkowych rekordów DNS dla innych usług w środowisku. Jeśli na przykład uruchomisz serwer sieci Web, który hostuje lokację przy użyciu nazwy głównej DNS, mogą wystąpić konflikty nazw, które wymagają dodatkowych wpisów DNS.
>
> W tych samouczkach i artykułach z przewodnikiem jest używana jako krótki przykład domeny niestandardowej *aadds.contoso.com* . We wszystkich poleceniach należy określić własną nazwę domeny, która może zawierać unikatowy prefiks.
>
> Aby uzyskać więcej informacji, zobacz [Wybieranie prefiksu nazewnictwa dla domeny] [prefiks nazewnictwa].

Obowiązują również następujące ograniczenia nazw DNS:

* **Ograniczenia prefiksu domeny:** Nie można utworzyć domeny zarządzanej z prefiksem dłuższym niż 15 znaków. Prefiks określonej nazwy domeny (np. *contoso* w nazwie domeny *contoso.com* ) musi zawierać co najwyżej 15 znaków.
* **Konflikty nazw sieciowych:** Nazwa domeny DNS dla domeny zarządzanej nie powinna już istnieć w sieci wirtualnej. W celu sprawdzenia następujących scenariuszy, które mogłyby prowadzić do konfliktu nazw:
    * Jeśli masz już domenę Active Directory z tą samą nazwą domeny DNS w sieci wirtualnej platformy Azure.
    * Jeśli sieć wirtualna, w której planujesz włączyć domenę zarządzaną, ma połączenie sieci VPN z siecią lokalną. W tym scenariuszu upewnij się, że nie masz domeny z tą samą nazwą domeny DNS w sieci lokalnej.
    * Jeśli masz istniejącą usługę w chmurze platformy Azure o tej nazwie w sieci wirtualnej platformy Azure.

Wypełnij pola w oknie *podstawy* Azure Portal, aby utworzyć wystąpienie usługi Azure AD DS:

1. Wprowadź **nazwę domeny DNS** dla domeny zarządzanej, biorąc pod uwagę poprzednie punkty.
1. Wybierz **lokalizację** platformy Azure, w której ma zostać utworzona domena zarządzana. W przypadku wybrania regionu, który obsługuje Strefy dostępności, zasoby AD DS platformy Azure są dystrybuowane między strefami w celu zapewnienia dodatkowej nadmiarowości.

    Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

    Nie ma niczego do skonfigurowania na potrzeby dystrybuowania AD DS platformy Azure między strefami. Platforma Azure automatycznie obsługuje dystrybucję zasobów. Aby uzyskać więcej informacji i sprawdzić dostępność regionów, zobacz [co to są strefy dostępności na platformie Azure?][availability-zones]

1. **Jednostka SKU** określa wydajność, częstotliwość tworzenia kopii zapasowych i maksymalną liczbę relacji zaufania lasów, które można utworzyć. Jednostkę SKU można zmienić po utworzeniu domeny zarządzanej, jeśli Twoje wymagania biznesowe lub zmiany zostały zmienione. Aby uzyskać więcej informacji, zobacz [pojęcia związane z usługą Azure AD DS SKU][concepts-sku].

    Na potrzeby tego samouczka wybierz *standardową* jednostkę SKU.
1. *Las* to konstrukcja logiczna używana przez Active Directory Domain Services do grupowania jednej lub wielu domen. Domyślnie domena zarządzana AD DS platformy Azure jest tworzona jako Las *użytkownika* . Ten typ lasu służy do synchronizowania wszystkich obiektów z usługi Azure AD, w tym wszystkich kont użytkowników utworzonych w środowisku lokalnym AD DS. Las *zasobów* synchronizuje tylko użytkowników i grupy utworzone bezpośrednio w usłudze Azure AD. Lasy zasobów są obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji o lasach *zasobów* , w tym o tym, dlaczego można korzystać z jednej z nich i jak utworzyć relacje zaufania lasów z lokalnymi domenami AD DS, zobacz [Omówienie lasów zasobów platformy Azure AD DS][resource-forests].

    Na potrzeby tego samouczka wybierz opcję utworzenia lasu *użytkownika* .

    ![Konfigurowanie ustawień podstawowych dla wystąpienia Azure AD Domain Services](./media/tutorial-create-instance-advanced/basics-window.png)

1. Aby ręcznie skonfigurować dodatkowe opcje, wybierz pozycję **dalej — sieć**. W przeciwnym razie wybierz pozycję **Przegląd + Utwórz** , aby zaakceptować domyślne opcje konfiguracji, a następnie przejdź do sekcji, aby [wdrożyć domenę zarządzaną](#deploy-the-managed-domain). Po wybraniu tej opcji Utwórz konfigurowane są następujące wartości domyślne:

    * Tworzy sieć wirtualną o nazwie *aadds-VNET* , która używa zakresu adresów IP *10.0.1.0/24*.
    * Tworzy podsieć o nazwie *aadds-Subnet* przy użyciu zakresu adresów IP *10.0.1.0/24*.
    * Synchronizuje *wszystkich* użytkowników z usługi Azure AD do domeny zarządzanej AD DS platformy Azure.

## <a name="create-and-configure-the-virtual-network"></a>Utwórz i skonfiguruj sieć wirtualną

Aby zapewnić łączność, wymagana jest sieć wirtualna platformy Azure i dedykowana podsieć. Usługa Azure AD DS jest włączona w tej podsieci sieci wirtualnej. W tym samouczku utworzysz sieć wirtualną, ale zamiast tego można wybrać użycie istniejącej sieci wirtualnej. W obu przypadkach należy utworzyć dedykowaną podsieć do użycia przez usługę Azure AD DS.

Niektóre zagadnienia dotyczące tej dedykowanej podsieci sieci wirtualnej obejmują następujące obszary:

* Podsieć musi mieć co najmniej 3-5 dostępnych adresów IP w zakresie adresów, aby umożliwić obsługę zasobów AD DS platformy Azure.
* Nie wybieraj podsieci *bramy* na potrzeby wdrażania usługi Azure AD DS. Wdrożenie AD DS platformy Azure w podsieci *bramy* nie jest obsługiwane.
* Nie należy wdrażać żadnych innych maszyn wirtualnych w podsieci. Aplikacje i maszyny wirtualne często używają sieciowych grup zabezpieczeń do zabezpieczania łączności. Uruchomienie tych obciążeń w oddzielnej podsieci pozwala zastosować te sieciowe grupy zabezpieczeń bez zakłócania łączności z domeną zarządzaną.
* Po włączeniu usługi Azure AD DS nie można przenieść domeny zarządzanej do innej sieci wirtualnej.

Aby uzyskać więcej informacji na temat planowania i konfigurowania sieci wirtualnej, zobacz [zagadnienia dotyczące sieci Azure Active Directory Domain Services][network-considerations].

Wypełnij pola w oknie *sieci* w następujący sposób:

1. Na stronie **Sieć** wybierz sieć wirtualną, w której chcesz wdrożyć AD DS platformy Azure, z menu rozwijanego lub wybierz pozycję **Utwórz nowy**.
    1. Jeśli wybierzesz opcję utworzenia sieci wirtualnej, wprowadź nazwę sieci wirtualnej, na przykład *myVnet*, a następnie podaj zakres adresów, na przykład *10.0.1.0/24*.
    1. Utwórz dedykowaną podsieć z nieprawidłową nazwą, taką jak *DomainServices*. Podaj zakres adresów, taki jak *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Upewnij się, że wybrano zakres adresów należący do zakresu prywatnych adresów IP. Zakresy adresów IP, których nie ma w publicznej przestrzeni adresowej, powodują błędy w usłudze Azure AD DS.

1. Wybierz podsieć sieci wirtualnej, na przykład *DomainServices*.
1. Gdy wszystko będzie gotowe, wybierz pozycję **dalej Administracja**.

## <a name="configure-an-administrative-group"></a>Skonfiguruj grupę administracyjną

Specjalna grupa administracyjna o nazwie *Administratorzy usługi AAD* jest używana do zarządzania domeną AD DS platformy Azure. Członkowie tej grupy mają uprawnienia administracyjne na maszynach wirtualnych, które są przyłączone do domeny zarządzanej. Na maszynach wirtualnych przyłączonych do domeny ta grupa jest dodawana do lokalnej grupy administratorów. Członkowie tej grupy mogą również używać Pulpit zdalny do zdalnego nawiązywania połączenia z maszynami wirtualnymi przyłączonymi do domeny.

Nie masz uprawnień *administratora domeny* ani *administratora przedsiębiorstwa* w domenie zarządzanej przy użyciu usługi Azure AD DS. Te uprawnienia są zastrzeżone przez usługę i nie są dostępne dla użytkowników w ramach dzierżawy. Zamiast tego grupa *Administratorzy usługi AAD DC* umożliwia wykonywanie niektórych operacji uprzywilejowanych. Te operacje obejmują Przyłączanie komputerów do domeny, należących do grupy administracyjnej na maszynach wirtualnych przyłączonych do domeny i Konfigurowanie zasady grupy.

Kreator automatycznie tworzy grupę *administratorów DC w usłudze AAD* w katalogu usługi Azure AD. Jeśli masz istniejącą grupę o tej nazwie w katalogu usługi Azure AD, Kreator wybierze tę grupę. Opcjonalnie możesz wybrać opcję dodawania dodatkowych użytkowników do tej grupy *administratorów domeny usługi AAD* w procesie wdrażania. Kroki te można wykonać później.

1. Aby dodać kolejnych użytkowników do tej grupy *administratorów domeny usługi AAD* , wybierz pozycję **Zarządzaj członkostwem w grupie**.

    ![Konfigurowanie członkostwa w grupie Administratorzy domeny usługi AAD](./media/tutorial-create-instance-advanced/admin-group.png)

1. Wybierz przycisk **Dodaj członków** , a następnie wyszukaj i wybierz użytkowników z katalogu usługi Azure AD. Na przykład wyszukaj własne konto i Dodaj je do grupy *administratorów kontrolera domeny usługi AAD* .
1. W razie potrzeby należy zmienić lub dodać dodatkowych adresatów dla powiadomień, gdy w domenie zarządzanej platformy Azure AD DS są wyświetlane alerty wymagające uwagi.
1. Gdy wszystko będzie gotowe, wybierz pozycję **następna synchronizacja**.

## <a name="configure-synchronization"></a>Konfigurowanie synchronizacji

Usługa Azure AD DS pozwala synchronizować *wszystkich* użytkowników i grupy dostępne w usłudze Azure AD albo synchronizację z *zakresem* tylko określonych grup. W przypadku wybrania opcji synchronizowanie *wszystkich* użytkowników i grup nie można później przeprowadzić synchronizacji z zakresem. Aby uzyskać więcej informacji na temat synchronizacji z zakresem, zobacz [Azure AD Domain Services synchronizacji w zakresie][scoped-sync].

1. Na potrzeby tego samouczka wybierz opcję Synchronizuj **wszystkich** użytkowników i grupy. Ten wybór synchronizacji jest opcją domyślną.

    ![Wykonaj pełną synchronizację użytkowników i grup z usługi Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Wybierz pozycję **Przegląd + utwórz**.

## <a name="deploy-the-managed-domain"></a>Wdróż domenę zarządzaną

Na stronie **Podsumowanie** kreatora przejrzyj ustawienia konfiguracji dla domeny zarządzanej. Możesz wrócić do dowolnego kroku kreatora, aby wprowadzić zmiany. Aby ponownie wdrożyć domenę zarządzaną platformy Azure AD DS w innej dzierżawie usługi Azure AD w spójny sposób przy użyciu tych opcji konfiguracji, można również **pobrać szablon do automatyzacji**.

1. Aby utworzyć domenę zarządzaną, wybierz pozycję **Utwórz**. Zostanie wyświetlona informacja o tym, że nie można zmienić pewnych opcji konfiguracji, takich jak nazwa DNS lub Sieć wirtualna, gdy zarządzana AD DS platformy Azure została utworzona. Aby kontynuować, wybierz **przycisk OK**.
1. Proces aprowizacji domeny zarządzanej może potrwać do godziny. W portalu zostanie wyświetlone powiadomienie pokazujące postęp wdrożenia usługi Azure AD DS. Wybierz powiadomienie, aby zobaczyć szczegółowy postęp wdrażania.

    ![Powiadomienie w Azure Portal wdrożenia jest w toku](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Wybierz grupę zasobów, *na przykład grupa zasobów, a*następnie wybierz wystąpienie AD DS platformy Azure z listy zasobów platformy Azure, takich jak *aadds.contoso.com*. Karta **Przegląd** pokazuje, że obecnie trwa *wdrażanie*domeny zarządzanej. Nie można skonfigurować domeny zarządzanej, dopóki nie zostanie ona w pełni zainicjowana.

    ![Stan usług domenowych w trakcie aprowizacji](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Gdy domena zarządzana jest w pełni obsługiwana, na karcie **Przegląd** zostanie wyświetlony stan domeny jako *uruchomiony*.

    ![Stan usług domenowych po pomyślnym zainicjowaniu obsługi administracyjnej](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Domena zarządzana jest skojarzona z dzierżawą usługi Azure AD. Podczas procesu aprowizacji usługa Azure AD DS tworzy dwie aplikacje dla przedsiębiorstw o nazwie *usługi kontrolera domeny* i *AzureActiveDirectoryDomainControllerServices* w dzierżawie usługi Azure AD. Te aplikacje przedsiębiorstwa są konieczne do obsługi domeny zarządzanej. Nie usuwaj tych aplikacji.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure

Pomyślnie wdrożono usługę Azure AD DS, teraz skonfigurujesz sieć wirtualną tak, aby zezwalała na inne połączone maszyny wirtualne i aplikacje do korzystania z domeny zarządzanej. Aby zapewnić tę łączność, zaktualizuj ustawienia serwera DNS dla sieci wirtualnej w taki sposób, aby wskazywały dwa adresy IP, na których wdrożono platformę Azure AD DS.

1. Karta **Przegląd** dla domeny zarządzanej zawiera kilka **wymaganych czynności konfiguracyjnych**. Pierwszym krokiem konfiguracji jest aktualizacja ustawień serwera DNS dla sieci wirtualnej. Po poprawnym skonfigurowaniu ustawień DNS ten krok nie jest już pokazywany.

    Wymienione adresy są kontrolerami domeny do użycia w sieci wirtualnej. W tym przykładzie te adresy to *10.1.0.4* i *10.1.0.5*. Te adresy IP można później znaleźć na karcie **Właściwości** .

    ![Skonfiguruj ustawienia DNS dla sieci wirtualnej przy użyciu Azure AD Domain Services adresów IP](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Aby zaktualizować ustawienia serwera DNS dla sieci wirtualnej, wybierz przycisk **Konfiguruj** . Ustawienia DNS są automatycznie konfigurowane dla sieci wirtualnej.

> [!TIP]
> Jeśli w poprzednich krokach została wybrana istniejąca sieć wirtualna, wszystkie maszyny wirtualne podłączone do sieci pobierzą nowe ustawienia DNS po ponownym uruchomieniu. Możesz ponownie uruchomić maszyny wirtualne przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Włącz konta użytkowników dla AD DS platformy Azure

Aby można było uwierzytelniać użytkowników w domenie zarządzanej, usługa Azure AD DS musi używać skrótów haseł w formacie, który jest odpowiedni dla protokołu NT LAN Manager (NTLM) i uwierzytelniania Kerberos. Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie zostanie włączona usługa Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie generować tych skrótów uwierzytelniania NTLM lub Kerberos w oparciu o istniejące poświadczenia użytkownika.

> [!NOTE]
> Po odpowiednim skonfigurowaniu skróty do przydatnych haseł są przechowywane w domenie zarządzanej AD DS platformy Azure. Jeśli usuniesz domenę zarządzaną platformy Azure AD DS, wszystkie skróty haseł przechowywane w tym punkcie również zostaną usunięte. Nie można ponownie użyć synchronizowanych informacji poświadczeń w usłudze Azure AD, jeśli później utworzysz domenę zarządzaną platformy Azure AD DS — musisz ponownie skonfigurować synchronizację skrótów haseł w celu ponownego przechowywania skrótów haseł. Wcześniej przyłączone do domeny maszyny wirtualne lub użytkownicy nie będą mogli od razu przeprowadzić uwierzytelniania — usługa Azure AD musi generować i przechowywać skróty haseł w nowej domenie zarządzanej AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [proces synchronizacji skrótów haseł dla platformy Azure AD DS i Azure AD Connect][password-hash-sync-process].

Instrukcje generowania i przechowywania tych skrótów haseł są różne dla kont użytkowników tylko w chmurze utworzonych w usłudze Azure AD, a konta użytkowników, które są synchronizowane z katalogu lokalnego przy użyciu Azure AD Connect. Konto użytkownika tylko w chmurze to konto, które zostało utworzone w katalogu usługi Azure AD przy użyciu witryny Azure Portal lub poleceń cmdlet programu Azure AD PowerShell. Te konta użytkowników nie są synchronizowane z katalogu lokalnego. W tym samouczku Przyjrzyjmy się podstawowemu kontu użytkownika tylko w chmurze. Aby uzyskać więcej informacji na temat dodatkowych kroków wymaganych do użycia Azure AD Connect, zobacz [Synchronize hash haseł dla kont użytkowników synchronizowanych z lokalnej usługi AD do domeny zarządzanej][on-prem-sync].

> [!TIP]
> Jeśli dzierżawa usługi Azure AD ma kombinację użytkowników tylko w chmurze i użytkowników z lokalnej usługi AD, należy wykonać oba zestawy kroków.

W przypadku kont użytkowników tylko w chmurze użytkownicy muszą zmienić swoje hasła, zanim będą mogli korzystać z usługi Azure AD DS. Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów haseł dla uwierzytelniania Kerberos i NTLM. Możesz wygasnąć hasła dla wszystkich użytkowników w dzierżawie, którzy muszą korzystać z usługi Azure AD DS, co wymusza zmianę hasła przy następnym logowaniu, lub poinstruuj ich, aby ręcznie zmiany haseł. Na potrzeby tego samouczka ręcznie zmienimy hasło użytkownika.

Aby użytkownik mógł zresetować swoje hasło, dzierżawa usługi Azure AD musi być [skonfigurowana do samoobsługowego resetowania hasła][configure-sspr].

Aby zmienić hasło dla użytkownika tylko w chmurze, użytkownik musi wykonać następujące czynności:

1. Przejdź do strony panelu dostępu usługi Azure AD w [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. W prawym górnym rogu wybierz swoją nazwę, a następnie wybierz pozycję **profil** z menu rozwijanego.

    ![Wybieranie profilu](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na stronie **profil** wybierz pozycję **Zmień hasło**.
1. Na stronie **Zmienianie hasła** wprowadź istniejące hasło (stare), a następnie wprowadź i Potwierdź nowe hasło.
1. Wybierz pozycję **Prześlij**.

Po zmianie hasła nowego hasła do użycia w usłudze Azure AD DS i pomyślnym zalogowaniu się do komputerów przyłączonych do domeny zarządzanej zajmie kilka minut.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie ustawień systemu DNS i sieci wirtualnej dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
> * Dodawanie użytkowników administracyjnych do zarządzania domeną
> * Włącz konta użytkowników dla AD DS platformy Azure i Generuj skróty haseł

Aby wyświetlić tę domenę zarządzaną w działaniu, należy utworzyć maszynę wirtualną i przyłączyć ją do domeny.

> [!div class="nextstepaction"]
> [Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
