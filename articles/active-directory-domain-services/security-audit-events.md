---
title: Włącz inspekcje zabezpieczeń dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak włączyć inspekcje zabezpieczeń w celu scentralizowania rejestrowania zdarzeń na potrzeby analizy i alertów w Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 42ab32e80ef0a1a7f3c02d8a8eedbb8ab13c4b88
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132252"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Włącz inspekcje zabezpieczeń dla Azure Active Directory Domain Services

Inspekcje zabezpieczeń w programie Azure Active Directory Domain Services (Azure AD DS) umożliwiają zdarzenia zabezpieczeń usługi Azure Stream do zasobów. Te zasoby obejmują usługę Azure Storage, obszary robocze platformy Azure Log Analytics lub usługę Azure Event Hub. Po włączeniu zdarzeń inspekcji zabezpieczeń usługa Azure AD DS wysyła wszystkie zdarzenia poddane inspekcji dla wybranej kategorii do zamierzonego zasobu.

Zdarzenia można archiwizować do usługi Azure Storage i przesyłać strumieniowo zdarzenia do oprogramowania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) (lub równoważne) przy użyciu usługi Azure Event Hubs lub do własnej analizy oraz przy użyciu obszarów roboczych usługi Azure Log Analytics z Azure Portal.

> [!IMPORTANT]
> Inspekcje zabezpieczeń AD DS platformy Azure są dostępne tylko dla wystąpień opartych na Azure Resource Manager. Aby uzyskać informacje na temat migracji, zobacz [migrowanie AD DS platformy Azure z klasycznego modelu sieci wirtualnej do Menedżer zasobów][migrate-azure-adds].

## <a name="audit-event-categories"></a>Kategorie zdarzeń inspekcji

Inspekcje zabezpieczeń AD DS systemu Azure są wyrównane z tradycyjnym inspekcją dla tradycyjnych AD DS kontrolerów domeny. W środowiskach hybrydowych można ponownie używać istniejących wzorców inspekcji, aby podczas analizowania zdarzeń można było używać tej samej logiki. W zależności od scenariusza potrzebnego do rozwiązywania problemów lub analizowania należy zastosować różne kategorie zdarzeń inspekcji.

Dostępne są następujące kategorie zdarzeń inspekcji:

| Nazwa kategorii inspekcji | Opis |
|:---|:---|
| Logowanie do konta|Inspekcje próbują uwierzytelnić dane konta na kontrolerze domeny lub w lokalnym Menedżerze kont zabezpieczeń (SAM).</p>Ustawienia zasad logowania i wylogowywania oraz zdarzenia śledzą próby dostępu do określonego komputera. Ustawienia i zdarzenia w tej kategorii koncentrują się na używanej bazie danych kont. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja weryfikacji poświadczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Inspekcja usługi uwierzytelniania Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Inspekcja operacji biletów usługi Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Inspekcja innych zdarzeń logowania/wylogowywania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Zarządzanie kontami|Inspekcja zmian kont użytkowników i komputerów oraz grup. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja zarządzania grupami aplikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Inspekcja zarządzania kontami komputerów](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Inspekcja zarządzania grupami dystrybucyjnymi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Inspekcja zarządzania innymi kontami](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Inspekcja zarządzania grupami zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Inspekcja zarządzania kontami użytkowników](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Śledzenie szczegółów|Przeprowadza inspekcję działań poszczególnych aplikacji i użytkowników na tym komputerze oraz zrozumienie sposobu korzystania z komputera. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja aktywności DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Inspekcja aktywności PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Tworzenie procesu inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Zakończenie procesu inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Inspekcja zdarzeń RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Dostęp do usług katalogowych|Inspekcje próbują uzyskać dostęp i zmodyfikować obiekty w Active Directory Domain Services (AD DS). Te zdarzenia inspekcji są rejestrowane tylko na kontrolerach domeny. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja szczegółowej replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Inspekcja dostępu do usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Inspekcja zmian usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Inspekcja replikacji usługi katalogowej](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logowanie-wylogowywanie|Inspekcje próbują zalogować się na komputerze interaktywnie lub za pośrednictwem sieci. Te zdarzenia są przydatne do śledzenia aktywności użytkownika i identyfikowania potencjalnych ataków na zasoby sieciowe. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja blokady konta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Inspekcja oświadczeń użytkowników/urządzeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Inspekcja trybu rozszerzonego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Inspekcja członkostwa w grupie](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Inspekcja trybu głównego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Inspekcja trybu szybkiego protokołu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Inspekcja wylogowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Inspekcja logowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Inspekcja serwera zasad sieciowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Inspekcja innych zdarzeń logowania/wylogowywania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Inspekcja logowania specjalnego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Dostęp do obiektów| Inspekcje próbuje uzyskać dostęp do określonych obiektów lub typów obiektów w sieci lub na komputerze. Ta kategoria zawiera następujące podkategorie:<ul><li>[Wygenerowano inspekcję aplikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Inspekcja usług certyfikacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Inspekcja szczegółowego udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Inspekcja udziału plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Inspekcja systemu plików](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Inspekcja połączenia platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Porzucanie pakietów na platformie filtrowania inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Inspekcja manipulowania obsługą](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Inspekcja obiektu jądra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Inspekcja innych zdarzeń dostępu do obiektów](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Inspekcja rejestru](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Inspekcja magazynu wymiennego](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Inspekcja — SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Inspekcja centralnych zasad dostępu tymczasowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Zmiana zasad|Inspekcja zmian ważnych zasad zabezpieczeń w systemie lokalnym lub w sieci. Zasady są zwykle określane przez administratorów, aby pomóc w zabezpieczaniu zasobów sieciowych. Monitorowanie zmian lub próba zmiany tych zasad może być ważnym aspektem zarządzania zabezpieczeniami w sieci. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja zmian zasad inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Inspekcja zmian zasad uwierzytelniania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Inspekcja zmiany zasad autoryzacji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Inspekcja zmian zasad platformy filtrowania](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Inspekcja zmian zasad na poziomie reguły MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Inspekcja innych zmian zasad](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Użycie uprawnień| Przeprowadza inspekcję używania określonych uprawnień w jednym lub wielu systemach. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja użycia uprawnień niewrażliwych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Inspekcja użycia uprawnień poufnych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Inspekcja innych zdarzeń użycia uprawnień](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Przeprowadza inspekcję zmian na poziomie systemu na komputerze nieuwzględnionym w innych kategoriach i ma potencjalne konsekwencje dla bezpieczeństwa. Ta kategoria zawiera następujące podkategorie:<ul><li>[Inspekcja sterownika IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Inspekcja innych zdarzeń systemowych](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Inspekcja zmiany stanu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Inspekcja rozszerzenia systemu zabezpieczeń](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Inspekcja integralności systemu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Identyfikatory zdarzeń na kategorię

 Inspekcje zabezpieczeń usługi Azure AD DS rejestruje następujące identyfikatory zdarzeń, gdy określona akcja wyzwala zdarzenie podlegające inspekcji:

| Nazwa kategorii zdarzeń | Identyfikatory zdarzeń |
|:---|:---|
|Zabezpieczenia logowania do konta|4767, 4774, 4775, 4776, 4777|
|Zabezpieczenia zarządzania kontami|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780|
|Szczegóły śledzenia zabezpieczeń|None|
|Zabezpieczenia dostępu do DS|5136, 5137, 5138, 5139, 5141|
|Zabezpieczenia wylogowywania|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Zabezpieczenia dostępu do obiektów|None|
|Zabezpieczenia zmiany zasad|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Zabezpieczenia użycia uprawnień|4985|
|Zabezpieczenia systemu|4612, 4621|

## <a name="security-audit-destinations"></a>Miejsca docelowe inspekcji zabezpieczeń

Możesz użyć obszarów roboczych usługi Azure Storage, Azure Event Hubs lub Azure Log Analytics jako zasobów docelowych dla inspekcji usługi Azure AD DS Security. Te miejsca docelowe można łączyć. Można na przykład użyć usługi Azure Storage do archiwizowania zdarzeń inspekcji zabezpieczeń, ale obszar roboczy Log Analytics platformy Azure umożliwia analizowanie i raportowanie informacji w krótkim czasie.

W poniższej tabeli przedstawiono scenariusze dla każdego typu zasobu docelowego.

> [!IMPORTANT]
> Należy utworzyć zasób docelowy przed włączeniem inspekcji zabezpieczeń usługi Azure AD DS. Te zasoby można utworzyć przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

| Zasób docelowy | Scenariusz |
|:---|:---|
|Azure Storage| Tego celu należy używać, gdy głównym wymaganiem jest przechowywanie zdarzeń inspekcji zabezpieczeń w celach archiwalnych. Inne elementy docelowe mogą być używane do celów archiwalnych, ale te elementy docelowe zapewniają możliwości wykraczające poza podstawową potrzebę archiwizowania. <br /><br />Przed włączeniem zdarzeń inspekcji usługi Azure AD DS Security należy najpierw [utworzyć konto usługi Azure Storage](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Tego celu należy używać, gdy głównym wymaganiem jest udostępnianie zdarzeń inspekcji zabezpieczeń za pomocą dodatkowego oprogramowania, takiego jak oprogramowanie do analizy danych lub informacje o zabezpieczeniach & oprogramowania do zarządzania zdarzeniami (SIEM).<br /><br />Przed włączeniem zdarzeń inspekcji usługi Azure AD DS Security [Utwórz centrum zdarzeń przy użyciu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Obszar roboczy usługi Azure Log Analytics| Tego celu należy używać, gdy głównym wymaganiem jest analizowanie i przeglądanie bezpiecznych inspekcji bezpośrednio z Azure Portal.<br /><br />Przed włączeniem zdarzeń inspekcji usługi Azure AD DS Security należy [utworzyć log Analytics obszar roboczy w Azure Portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Włączanie zdarzeń inspekcji zabezpieczeń przy użyciu Azure Portal

Aby włączyć zdarzenia inspekcji usługi Azure AD DS Security przy użyciu Azure Portal, wykonaj następujące czynności.

> [!IMPORTANT]
> Inspekcje zabezpieczeń AD DS platformy Azure nie są wsteczne. Nie można pobrać ani powtórzyć zdarzeń z przeszłości. Usługa Azure AD DS może wysyłać tylko zdarzenia, które wystąpiły po włączeniu inspekcji zabezpieczeń.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. W górnej części Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aadds.contoso.com*.
1. W oknie AD DS platformy Azure wybierz pozycję **Ustawienia diagnostyczne** po lewej stronie.
1. Żadna Diagnostyka nie jest domyślnie skonfigurowana. Aby rozpocząć, wybierz pozycję **Dodaj ustawienie diagnostyczne**.

    ![Dodaj ustawienie diagnostyczne dla Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Wprowadź nazwę konfiguracji diagnostycznej, na przykład *aadds-Audit*.

    Zaznacz pole wyboru dla docelowej inspekcji zabezpieczeń. Możesz wybrać konto usługi Azure Storage, centrum zdarzeń platformy Azure lub obszar roboczy Log Analytics. Te zasoby docelowe muszą już istnieć w subskrypcji platformy Azure. Nie można utworzyć zasobów docelowych w tym kreatorze.

    ![Włącz wymagane miejsce docelowe i typ zdarzeń inspekcji do przechwycenia](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * Wybierz pozycję **Archiwizuj na koncie magazynu**, a następnie wybierz pozycję **Konfiguruj**.
        * Wybierz **subskrypcję** i **konto magazynu** , którego chcesz użyć do archiwizacji zdarzeń inspekcji zabezpieczeń.
        * Gdy wszystko będzie gotowe, wybierz **przycisk OK**.
    * **Centra zdarzeń platformy Azure**
        * Wybierz pozycję **strumień do centrum zdarzeń**, a następnie wybierz pozycję **Konfiguruj**.
        * Wybierz **subskrypcję** i **przestrzeń nazw centrum zdarzeń**. W razie konieczności należy również wybrać **nazwę centrum zdarzeń** , a następnie **nazwę zasad centrum zdarzeń**.
        * Gdy wszystko będzie gotowe, wybierz **przycisk OK**.
    * **Obszary robocze analityczne dzienników platformy Azure**
        * Wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz **obszar roboczy** **subskrypcja** i log Analytics, który ma być używany do przechowywania zdarzeń inspekcji zabezpieczeń.

1. Wybierz kategorie dzienników, które chcesz uwzględnić dla określonego zasobu docelowego. W przypadku wysyłania zdarzeń inspekcji do konta usługi Azure Storage można także skonfigurować zasady przechowywania, które definiują liczbę dni przechowywania danych. Ustawienie domyślne *0* zachowuje wszystkie dane i nie powoduje rotacji zdarzeń po upływie czasu.

    W ramach jednej konfiguracji można wybrać różne kategorie dzienników dla każdego z zasobów. Ta możliwość umożliwia wybranie kategorii dzienników, które mają być przechowywane dla Log Analytics i które kategorie dzienników mają być archiwizowane.

1. Po zakończeniu wybierz pozycję **Zapisz** , aby zatwierdzić zmiany. Zasoby docelowe zaczynają otrzymywać zdarzenia usługi Azure AD DS Security Audit wkrótce po zapisaniu konfiguracji.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Włączanie zdarzeń inspekcji zabezpieczeń przy użyciu Azure PowerShell

Aby włączyć zdarzenia inspekcji usługi Azure AD DS Security przy użyciu Azure PowerShell, wykonaj następujące czynności. W razie potrzeby najpierw [Zainstaluj moduł Azure PowerShell i nawiąż połączenie z subskrypcją platformy Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Inspekcje zabezpieczeń AD DS platformy Azure nie są wsteczne. Nie można pobrać ani powtórzyć zdarzeń z przeszłości. Usługa Azure AD DS może wysyłać tylko zdarzenia, które wystąpiły po włączeniu inspekcji zabezpieczeń.

1. Uwierzytelnij się w subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . Po wyświetleniu monitu wprowadź poświadczenia konta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Utwórz zasób docelowy dla zdarzeń inspekcji zabezpieczeń.

    * **Usługa Azure storage** - [Tworzenie konta magazynu przy użyciu Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Usługa Azure Event hub** - [utworzyć centrum zdarzeń przy użyciu Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Może być również konieczne użycie polecenia cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) w celu utworzenia reguły autoryzacji, która przyznaje uprawnienia AD DS platformy Azure do *przestrzeni nazw*centrum zdarzeń. Reguła autoryzacji musi zawierać prawa do **zarządzania**, **nasłuchiwania**i **wysyłania** .

        > [!IMPORTANT]
        > Upewnij się, że zasada autoryzacji jest ustawiona w przestrzeni nazw centrum zdarzeń, a nie w centrum zdarzeń.

    * **Obszary robocze analityczne dzienników platformy Azure** - [tworzenia obszaru roboczego Log Analytics przy użyciu Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Pobierz identyfikator zasobu dla domeny zarządzanej platformy Azure AD DS za pomocą polecenia cmdlet [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) . Utwórz zmienną o nazwie *$aadds. ResourceId* do przechowywania wartości:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Skonfiguruj ustawienia diagnostyczne platformy Azure za pomocą polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) , aby użyć zasobu docelowego dla zdarzeń inspekcji zabezpieczeń Azure AD Domain Services. W poniższych przykładach zmienna *$aadds. Identyfikator ResourceId* jest używany w poprzednim kroku.

    * **Azure Storage** — Zamień *storageAccountId* na nazwę konta magazynu:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Centra zdarzeń platformy Azure** — Zamień *eventHubName* na nazwę centrum zdarzeń i *eventHubRuleId* z identyfikatorem reguły autoryzacji:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Obszary robocze analityczne dzienników platformy Azure** — Zamień *Identyfikator obszaru roboczego* na identyfikator obszaru roboczego log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Wykonywanie zapytań i Wyświetlanie zdarzeń inspekcji zabezpieczeń przy użyciu Azure Monitor

Obszary robocze analityczne dzienników umożliwiają wyświetlanie i analizowanie zdarzeń inspekcji zabezpieczeń przy użyciu Azure Monitor i języka zapytań Kusto. Ten język zapytań jest przeznaczony do użycia tylko do odczytu, który boasts możliwości analityczne, dzięki łatwemu do odczytu składni. Aby uzyskać więcej informacji na temat rozpoczynania pracy z językami zapytań Kusto, zobacz następujące artykuły:

* [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Wprowadzenie do Log Analytics w Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Wprowadzenie do zapytań dzienników w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Tworzenie i udostępnianie pulpitów nawigacyjnych Log Analytics danych](../azure-monitor/learn/tutorial-logs-dashboards.md)

Następujące przykładowe zapytania mogą służyć do rozpoczęcia analizowania zdarzeń inspekcji zabezpieczeń z usługi Azure AD DS.

### <a name="sample-query-1"></a>Przykładowe zapytanie 1

Wyświetl wszystkie zdarzenia blokady konta w ciągu ostatnich siedmiu dni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Przykładowe zapytanie 2

Wyświetl wszystkie zdarzenia blokady konta (*4740*) od 3 lutego 2020 o godzinie 9 i 10 lutego 2019 północy, posortowane rosnąco według daty i godziny:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Przykładowe zapytanie 3

Wyświetl zdarzenia logowania do konta siedem dni temu (od teraz) dla konta o nazwie użytkownik:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Przykładowe zapytanie 4

Wyświetl zdarzenia logowania do konta siedem dni temu od teraz dla konta o nazwie użytkownik próbującego zalogować się przy użyciu nieprawidłowego hasła (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Przykładowe zapytanie 5

Wyświetl zdarzenia logowania do konta siedem dni temu od teraz dla konta o nazwie użytkownik próbującego się zalogować, gdy konto zostało zablokowane (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Przykładowe zapytanie 6

Wyświetl liczbę zdarzeń logowania do konta siedem dni temu teraz dla wszystkich prób logowania, które wystąpiły dla wszystkich zablokowanych użytkowników:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat usługi Kusto, zobacz następujące artykuły:

* [Przegląd](/azure/kusto/query/) języka zapytań Kusto.
* [Kusto samouczek](/azure/kusto/query/tutorial) , aby zapoznać się z podstawowymi informacjami o zapytaniach.
* [Przykładowe zapytania](/azure/kusto/query/samples) , które pomagają poznać nowe sposoby wyświetlania danych.
* Kusto [najlepsze rozwiązania](/azure/kusto/query/best-practices) w celu zoptymalizowania zapytań.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
