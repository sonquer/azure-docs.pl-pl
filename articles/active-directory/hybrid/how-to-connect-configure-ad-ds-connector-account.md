---
title: 'Azure AD Connect: Skonfiguruj uprawnienia konta łącznika AD DS | Microsoft Docs'
description: Ten dokument zawiera szczegółowe informacje dotyczące konfigurowania konta łącznika AD DS przy użyciu nowego modułu programu PowerShell w programie ADSyncConfig
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515823"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Skonfiguruj uprawnienia konta łącznika AD DS 

Moduł programu PowerShell o nazwie [ADSyncConfig. PSM1](reference-connect-adsyncconfig.md) został wprowadzony z kompilacją 1.1.880.0 (wydana w sierpniu 2018) zawierającym kolekcję poleceń cmdlet, które ułatwiają konfigurowanie odpowiednich uprawnień Active Directory dla wdrożenia Azure AD Connect. 

## <a name="overview"></a>Przegląd 
Następujące polecenia cmdlet programu PowerShell mogą służyć do konfigurowania Active Directory uprawnień konta łącznika AD DS dla każdej funkcji wybranej do włączenia w Azure AD Connect. Aby zapobiec wszelkim problemom, należy przygotować Active Directory uprawnień z wyprzedzeniem, jeśli chcesz zainstalować Azure AD Connect przy użyciu niestandardowego konta domeny w celu nawiązania połączenia z lasem. Tego modułu ADSyncConfig można także użyć w celu skonfigurowania uprawnień po wdrożeniu Azure AD Connect.

![Przegląd konta usług AD DS](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

W przypadku instalacji programu Azure AD Connect Express automatycznie generowane konto (MSOL_nnnnnnnnnn) jest tworzone w Active Directory ze wszystkimi niezbędnymi uprawnieniami, więc nie ma potrzeby korzystania z tego modułu ADSyncConfig, chyba że masz zablokowane uprawnienia dziedziczenie w jednostkach organizacyjnych lub na określonych obiektach Active Directory, które mają być synchronizowane z usługą Azure AD. 
 
### <a name="permissions-summary"></a>Podsumowanie uprawnień 
Poniższa tabela zawiera podsumowanie uprawnień wymaganych w przypadku obiektów usługi AD: 

| Funkcja | Uprawnienia |
| --- | --- |
| Funkcja MS-DS-ConsistencyGuid |Uprawnienia do odczytu i zapisu do atrybutu MS-DS-ConsistencyGuid udokumentowanego w [koncepcji projektowania — przy użyciu MS-ds-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótów haseł |<li>Replikowanie zmian w katalogu</li>  <li>Replikuj wszystkie zmiany katalogu |
| Wdrożenie hybrydowe programu Exchange |Uprawnienia do odczytu i zapisu w odniesieniu do atrybutów przedstawionych w funkcji [zapisywania zwrotnego hybrydowego programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontaktów. |
| Folder publiczny poczty programu Exchange |Uprawnienia Odczyt do atrybutów przedstawionych w [folderze publicznym poczty programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do odczytu i zapisu w odniesieniu do atrybutów opisanych w temacie [wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia do odczytu i zapisu dla obiektów urządzeń i kontenerów udokumentowanych w funkcji [zapisywania zwrotnego urządzeń](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Odczytuj, twórz, Aktualizuj i usuwaj obiekty grupy do zsynchronizowanych **grup pakietu Office 365**.  Aby uzyskać więcej informacji, zobacz [zapisywanie zwrotne grup](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Korzystanie z modułu ADSyncConfig PowerShell 
Moduł ADSyncConfig wymaga [Narzędzia administracji zdalnej serwera (RSAT) dla AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) , ponieważ zależy on od modułu i narzędzi AD DS PowerShell. Aby zainstalować narzędzia RSAT dla AD DS, Otwórz okno programu Windows PowerShell za pomocą polecenia "Uruchom jako administrator" i wykonaj: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfiguracja](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Możesz również skopiować plik **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.PSM1** do kontrolera domeny, który ma już narzędzia RSAT dla AD DS zainstalowane i korzystać z tego modułu programu PowerShell.

Aby rozpocząć korzystanie z ADSyncConfig, należy załadować moduł w oknie programu Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Aby sprawdzić wszystkie polecenia cmdlet zawarte w tym module, możesz wpisać:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaznacz](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Każde polecenie cmdlet ma te same parametry, aby wprowadzić konto łącznika AD DS i przełącznik AdminSDHolder. Aby określić konto łącznika AD DS, można podać nazwę konta i domenę lub tylko nazwę wyróżniającą konta (DN).

np.:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Oraz

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Upewnij się, że `<ADAccountName>`, `<ADDomainName>` i `<ADAccountDN>` z prawidłowymi wartościami dla danego środowiska.

Jeśli nie chcesz modyfikować uprawnień w kontenerze AdminSDHolder, użyj przełącznika `-SkipAdminSdHolders`. 

Domyślnie wszystkie polecenia cmdlet ustawiania uprawnień będą podejmować próby ustawienia uprawnień AD DS w katalogu głównym każdej domeny w lesie, co oznacza, że użytkownik uruchamiający sesję programu PowerShell wymaga uprawnień administratora domeny w każdej domenie w lesie.  Z powodu tego wymagania zaleca się użycie administratora przedsiębiorstwa z katalogu głównego lasu. Jeśli wdrożenie Azure AD Connect ma wiele łączników AD DS, będzie wymagane uruchomienie tego samego polecenia cmdlet w każdym lesie zawierającym łącznik AD DS. 

Możesz również ustawić uprawnienia dla konkretnej jednostki organizacyjnej lub obiektu AD DS przy użyciu parametru `-ADobjectDN` po którym następuje nazwa wyróżniająca obiektu docelowego, w którym chcesz ustawić uprawnienia. W przypadku korzystania z ADobjectDN docelowego polecenie cmdlet ustawi uprawnienia tylko dla tego obiektu, a nie w katalogu głównym domeny lub kontenerze AdminSDHolder. Ten parametr może być przydatny w przypadku niektórych jednostek organizacyjnych lub AD DS obiektów, które mają wyłączone dziedziczenie uprawnień (Zobacz Lokalizowanie obiektów AD DS z wyłączonym dziedziczeniem uprawnień) 

Wyjątkami od tych typowych parametrów jest polecenie cmdlet `Set-ADSyncRestrictedPermissions`, które służy do ustawiania uprawnień na samym koncie łącznika AD DS, a polecenie cmdlet `Set-ADSyncPasswordHashSyncPermissions`, ponieważ uprawnienia wymagane do synchronizacji skrótów haseł są ustawiane tylko w katalogu głównym domeny. , dlatego to polecenie cmdlet nie obejmuje parametrów `-ObjectDN` ani `-SkipAdminSdHolders`.

### <a name="determine-your-ad-ds-connector-account"></a>Określanie konta łącznika AD DS 
W przypadku Azure AD Connect jest już zainstalowany i chcesz sprawdzić, co to jest konto łącznika AD DS aktualnie używane przez Azure AD Connect, można wykonać polecenie cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Lokalizowanie obiektów AD DS z wyłączonym dziedziczeniem uprawnień 
Jeśli chcesz sprawdzić, czy istnieje AD DS obiekt z wyłączonym dziedziczeniem uprawnień, możesz uruchomić następujące polecenie: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Domyślnie to polecenie cmdlet wyszuka tylko jednostki organizacyjne z wyłączonym dziedziczeniem, ale można określić inne klasy obiektów AD DS w parametrze `-ObjectClass` lub użyć znaku "*" dla wszystkich klas obiektów w następujący sposób: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Wyświetlanie uprawnień AD DS obiektu 
Możesz użyć poniższego polecenia cmdlet, aby wyświetlić listę uprawnień aktualnie ustawionych na obiekcie Active Directory, podając jego wyróżniającą: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurowanie uprawnień konta łącznika AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Skonfiguruj podstawowe uprawnienia tylko do odczytu 
Aby ustawić podstawowe uprawnienia tylko do odczytu dla konta łącznika AD DS, gdy nie jest używana żadna funkcja Azure AD Connect, uruchom polecenie: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Oraz 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


To polecenie cmdlet ustawi następujące uprawnienia: 
 

|Typ |Nazwa |Dostęp |Dotyczy| 
|-----|-----|-----|-----|
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Obiekty podrzędne urządzenia| 
|Zezwól |Konto łącznika AD DS|Odczyt wszystkich właściwości |Podrzędne obiekty InetOrgPerson| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Obiekty podrzędne komputera| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Podrzędne obiekty foreignSecurityPrincipal| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Obiekty grupy podrzędnej| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Obiekty podrzędne użytkownika| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Podrzędne obiekty Contact| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurowanie uprawnień usługi MS-DS-spójności-identyfikatora GUID 
Aby ustawić uprawnienia dla konta łącznika AD DS przy użyciu atrybutu MS-ds-Consistency-GUID jako kotwicy źródłowej ("Zezwól na platformę Azure zarządzać zakotwiczeniem źródła dla mnie"), uruchom polecenie: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Oraz 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól|Konto łącznika AD DS|Właściwość odczytu/zapisu|Obiekty podrzędne użytkownika|

### <a name="permissions-for-password-hash-synchronization"></a>Uprawnienia do synchronizacji skrótów haseł 
Aby ustawić uprawnienia dla konta łącznika AD DS podczas korzystania z synchronizacji skrótów haseł, uruchom polecenie: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Oraz 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |Konto łącznika AD DS |Replikowanie zmian w katalogu |Tylko ten obiekt (katalog główny domeny)| 
|Zezwól |Konto łącznika AD DS |Wszystkie zmiany katalogu replikowanego |Tylko ten obiekt (katalog główny domeny)| 
  
### <a name="permissions-for-password-writeback"></a>Uprawnienia do zapisywania zwrotnego haseł 
Aby ustawić uprawnienia dla konta łącznika AD DS podczas korzystania z funkcji zapisywania zwrotnego haseł, uruchom polecenie: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Oraz

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |Konto łącznika AD DS |Resetowanie hasła |Obiekty podrzędne użytkownika| 
|Zezwól |Konto łącznika AD DS |Zapisz Właściwość lockoutTime |Obiekty podrzędne użytkownika| 
|Zezwól |Konto łącznika AD DS |Zapisz Właściwość pwdLastSet |Obiekty podrzędne użytkownika| 

### <a name="permissions-for-group-writeback"></a>Uprawnienia do zapisywania zwrotnego grup 
Aby ustawić uprawnienia dla konta łącznika AD DS podczas korzystania z funkcji zapisywania zwrotnego grup, uruchom polecenie: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Oraz 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |Konto łącznika AD DS |Ogólny Odczyt/zapis |Wszystkie atrybuty grupy typów obiektów i podobiektów| 
|Zezwól |Konto łącznika AD DS |Tworzenie/usuwanie obiektu podrzędnego |Wszystkie atrybuty grupy typów obiektów i podobiektów| 
|Zezwól |Konto łącznika AD DS |Usuwanie/usuwanie obiektów drzewa|Wszystkie atrybuty grupy typów obiektów i podobiektów|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Uprawnienia do wdrożenia hybrydowego programu Exchange 
Aby ustawić uprawnienia dla konta łącznika AD DS podczas korzystania z wdrożenia hybrydowego programu Exchange, uruchom polecenie: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Oraz 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia:  
 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |Konto łącznika AD DS |Odczyt/zapis wszystkich właściwości |Obiekty podrzędne użytkownika| 
|Zezwól |Konto łącznika AD DS |Odczyt/zapis wszystkich właściwości |Podrzędne obiekty InetOrgPerson| 
|Zezwól |Konto łącznika AD DS |Odczyt/zapis wszystkich właściwości |Obiekty grupy podrzędnej| 
|Zezwól |Konto łącznika AD DS |Odczyt/zapis wszystkich właściwości |Podrzędne obiekty Contact| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Uprawnienia dla folderów publicznych poczty programu Exchange (wersja zapoznawcza) 
Aby ustawić uprawnienia dla konta łącznika AD DS podczas korzystania z funkcji folderów publicznych poczty programu Exchange, uruchom polecenie: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Oraz 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |Konto łącznika AD DS |Odczyt wszystkich właściwości |Podrzędne obiekty PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Ogranicz uprawnienia na koncie łącznika AD DS 
Ten skrypt programu PowerShell zwiększy uprawnienia do konta łącznika usługi AD dostarczonego jako parametr. Podwyższenie poziomu uprawnień obejmuje następujące kroki: 

- Wyłącz dziedziczenie dla określonego obiektu 
- Usuń wszystkie wpisy kontroli dostępu do określonego obiektu, z wyjątkiem kontroli dostępu do własnych, gdy chcemy zachować domyślne uprawnienia, które są niezmienione, gdy nastąpi samodzielne. 
 
  Parametr-ADConnectorAccountDN jest kontem usługi AD, którego uprawnienia należy wzmocnić. Jest to zazwyczaj konto domeny MSOL_nnnnnnnnnnnn, które jest skonfigurowane w łączniku AD DS (zobacz Określanie konta łącznika AD DS). Parametr-Credential jest wymagany do określenia konta administratora, które ma niezbędne uprawnienia, aby ograniczyć uprawnienia Active Directory do docelowego obiektu usługi AD. Jest to zwykle administrator przedsiębiorstwa lub domeny.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Na przykład: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwól |SYSTEMAMI |Pełna kontrola |Ten obiekt 
|Zezwól |Administratorzy przedsiębiorstwa |Pełna kontrola |Ten obiekt 
|Zezwól |Administratorzy domeny |Pełna kontrola |Ten obiekt 
|Zezwól |Administratorzy |Pełna kontrola |Ten obiekt 
|Zezwól |Kontrolery domeny przedsiębiorstwa |Wyświetl zawartość |Ten obiekt 
|Zezwól |Kontrolery domeny przedsiębiorstwa |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwól |Kontrolery domeny przedsiębiorstwa |Uprawnienia do odczytu |Ten obiekt 
|Zezwól |Uwierzytelnieni użytkownicy |Wyświetl zawartość |Ten obiekt 
|Zezwól |Uwierzytelnieni użytkownicy |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwól |Uwierzytelnieni użytkownicy |Uprawnienia do odczytu |Ten obiekt 

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Accounts and permissions](reference-connect-accounts-permissions.md) (Azure AD Connect: konta i uprawnienia)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Odwołanie ADSyncConfig](reference-connect-adsyncconfig.md)

