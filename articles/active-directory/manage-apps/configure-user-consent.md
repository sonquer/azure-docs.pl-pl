---
title: Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje za pomocą usługi Azure AD
description: Dowiedz się, jak i kiedy użytkownicy mogą wyrazić zgodę na aplikacje, które będą miały dostęp do danych organizacji.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443391"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje

Aplikacje można zintegrować z platformą tożsamości firmy Microsoft, aby umożliwić użytkownikom logowanie się przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) i uzyskiwanie dostępu do danych organizacji w celu zapewnienia zaawansowanych środowisk opartych na danych. Różne uprawnienia umożliwiają aplikacji różnego poziomu dostępu do danych użytkowników i organizacji.

Domyślnie użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych organizacji, ale tylko w przypadku niektórych uprawnień. Na przykład Domyślnie użytkownik może wyrazić zgodę na zezwolenie aplikacji na dostęp do własnej skrzynki pocztowej lub zespołów, do których należy użytkownik, ale nie może wyrazić zgody na odczytanie i zapisanie we wszystkich witrynach programu SharePoint w organizacji. Zezwalanie użytkownikom na zgodę samodzielnie pozwala użytkownikom na łatwe uzyskiwanie przydatnych aplikacji, które integrują się z Microsoft 365, platformą Azure i innymi usługami, ale może to stanowić ryzyko, jeśli nie są one używane i monitorowane uważnie.

Firma Microsoft zaleca wyłączenie w przyszłości operacji wyrażania zgody użytkowników na zmniejszenie obszaru powierzchni i uniknięcie tego ryzyka. Jeśli użytkownik wyraża zgodę na wyłączenie, poprzednie dotacje zgody nadal będą honorowane, ale wszystkie przyszłe operacje wyrażania zgody muszą być wykonywane przez administratora. Użytkownicy mogą żądać zgody administratora w całej dzierżawie za pomocą [przepływu pracy zintegrowanego żądania zgody administratora](configure-admin-consent-workflow.md) lub przez własne procesy obsługi. Aby uzyskać więcej informacji [, zobacz pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md) .

## <a name="configure-user-consent-to-applications"></a>Konfigurowanie zgody użytkownika na aplikacje
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Wyłączenie lub włączenie zgody użytkownika z Azure Portal

Za pomocą Azure Portal można wyłączyć lub umożliwić użytkownikom wyrażanie zgody na aplikacje uzyskujące dostęp do danych organizacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Wybierz **Azure Active Directory**, następnie **aplikacje dla przedsiębiorstw**, a następnie **Ustawienia użytkownika**.
3. Włączać lub wyłączać wyrażanie zgody użytkownika z kontrolką z etykietą **Użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu**.
4. Obowiązkowe Skonfiguruj [przepływ pracy żądania zgody administratora](configure-admin-consent-workflow.md) , aby upewnić się, że użytkownicy, którzy nie mogą wyrazić zgody na aplikację, mogą zażądać zatwierdzenia.

> [!TIP]
> Aby umożliwić użytkownikom zażądanie przeglądu aplikacji, do której użytkownik nie może wyrazić zgody (na przykład ponieważ zgoda użytkownika została wyłączona lub aplikacja żąda uprawnień, których użytkownik nie może udzielić), należy rozważyć [skonfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Wyłączanie lub Włączanie zgody użytkownika przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell V1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)), aby włączyć lub wyłączyć możliwość wyrażania zgody na aplikacje uzyskujące dostęp do danych organizacji.

1. Zaloguj się do swojej organizacji, uruchamiając to polecenie cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Sprawdź, czy jest włączona zgoda użytkownika przez uruchomienie tego polecenia cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Włączać lub wyłączać wyrażanie zgody użytkownika. Na przykład aby wyłączyć wyrażanie zgody użytkownika, uruchom następujące polecenie cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurowanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych grupy

> [!IMPORTANT]
> Poniższe informacje dotyczą przyszłych funkcji, dzięki którym właściciele grup mogą udzielać aplikacjom dostępu do danych ich grup. Po wydaniu tej funkcji będzie ona domyślnie włączona. Mimo że ta funkcja nie jest jeszcze ogólnie dostępna, można użyć tych instrukcji, aby z wyprzedzeniem wyłączyć tę możliwość.

Właściciele grupy mogą autoryzować aplikacje (na przykład aplikacje opublikowane przez innych dostawców) w celu uzyskania dostępu do danych organizacji skojarzonych z grupą. Na przykład właściciel zespołu (który jest właścicielem grupy Office 365 dla zespołu) może zezwalać aplikacji na odczytywanie wszystkich komunikatów zespołów w zespole lub listę profilu podstawowego członków grupy.

> [!NOTE]
> Niezależnie od tego ustawienia Właściciel grupy zawsze może dodawać innych użytkowników lub aplikacje bezpośrednio jako właściciele grupy.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurowanie zgody właściciela grupy przy użyciu programu PowerShell

Możesz użyć modułu Azure AD PowerShell w wersji zapoznawczej ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)), aby włączyć lub wyłączyć możliwość wyrażania zgody dla aplikacji uzyskujących dostęp do danych organizacji w odniesieniu do posiadanych przez nich grup.

1. Upewnij się, że używasz modułu [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (ten krok jest istotny, jeśli zainstalowano moduł [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) i moduł [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) ).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Nawiązywanie połączenia z usługą Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Pobierz bieżącą wartość ustawień *zasad* dotyczących ustawień katalogu w dzierżawie. Wymaga to sprawdzenia, czy zostały utworzone ustawienia katalogu dla tej funkcji, a jeśli nie, użyj wartości z odpowiedniego szablonu ustawień katalogu.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Zapoznaj się z wartościami ustawień. Istnieją dwie wartości ustawień, które definiują, którzy użytkownicy będą mogli zezwolić aplikacji na dostęp do danych swojej grupy:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Wartość logiczna |  Flaga oznaczająca, czy właściciele grup mogą udzielać uprawnień specyficznych dla grupy. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Identyfikator GUID | Jeśli _EnableGroupSpecificConsent_ jest ustawiona na wartość "true", a ta wartość jest ustawiona na identyfikator obiektu grupy, członkowie identyfikowanej grupy będą uprawnieni do przyznawania uprawnień specyficznych dla grupy do grup, których są właścicielami. |

5. Zaktualizuj wartości ustawień dla żądanej konfiguracji:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Zapisz ustawienia.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)

[Przyznaj administratorowi zgodę na dostęp do aplikacji](grant-admin-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w systemie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
