---
title: Instalowanie pakietu Office na głównym obrazie wirtualnego dysku twardego — Azure
description: Jak zainstalować i dostosować pakiet Office na głównym obrazie pulpitu wirtualnego systemu Windows na platformie Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 1637cb7621d6071bbce2af66f3a106770139ad4e
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368822"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalowanie pakietu Office w głównym obrazie wirtualnego dysku twardego

W tym artykule opisano, jak zainstalować pakiet Office 365 ProPlus, OneDrive i inne typowe aplikacje na głównym obrazie wirtualnego dysku twardego (VHD) na potrzeby przekazywania na platformę Azure. Jeśli użytkownicy muszą uzyskać dostęp do niektórych aplikacji biznesowych, zalecamy ich zainstalowanie po zakończeniu instrukcji przedstawionych w tym artykule.

W tym artykule przyjęto założenie, że utworzono już maszynę wirtualną (VM). Jeśli nie, zobacz [Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md#create-a-vm) .

W tym artykule przyjęto również, że masz podwyższony poziom dostępu do maszyny wirtualnej, niezależnie od tego, czy jest on zainicjowany na platformie Azure, czy w Menedżerze funkcji Hyper-V. Jeśli nie, zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Te instrukcje dotyczą konfiguracji specyficznej dla pulpitu wirtualnego systemu Windows, która może być używana z istniejącymi procesami w organizacji.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalowanie pakietu Office w trybie aktywacji komputera udostępnionego

Aktywacja na komputerze udostępnionym pozwala wdrożyć pakiet Office 365 ProPlus na komputerze w organizacji, do którego uzyskuje dostęp wielu użytkowników. Aby uzyskać więcej informacji na temat aktywacji komputera udostępnionego, zobacz [Omówienie aktywacji komputera udostępnionego dla pakietu Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Zainstaluj pakiet Office za pomocą [narzędzia wdrażania pakietu Office](https://www.microsoft.com/download/details.aspx?id=49117) . Wiele sesji systemu Windows 10 Enterprise obsługuje tylko następujące wersje pakietu Office:
- Pakiet Office 365 ProPlus
- Pakiet Office 365 firmy, który jest dostarczany z subskrypcją Microsoft 365 Business

Narzędzie wdrażania pakietu Office wymaga pliku XML konfiguracji. Aby dostosować Poniższy przykład, zobacz [Opcje konfiguracji narzędzia do wdrażania pakietu Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Ten przykładowy plik XML konfiguracji wykona następujące czynności:

- Zainstaluj pakiet Office z miesięcznego kanału i dostarczaj aktualizacje z miesięcznego kanału, gdy są one wykonywane.
- Użyj architektury x64.
- Wyłącz aktualizacje automatyczne.
- Usuń wszystkie istniejące instalacje pakietu Office i Migruj ich ustawienia.
- Włącz aktywację komputera udostępnionego.

>[!NOTE]
>Funkcja wyszukiwania wzornika programu Visio może nie zadziałać zgodnie z oczekiwaniami w programie Virtual Desktop systemu Windows.

Oto co to jest przykładowy kod XML konfiguracji:

- Instalowanie programu Skype dla firm
- Zainstaluj usługę OneDrive w trybie dla użytkownika. Aby dowiedzieć się więcej, zobacz [Instalowanie usługi OneDrive w trybie dla komputera](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktywację komputera udostępnionego można skonfigurować za zasady grupy obiektów zasad grupy (GPO) lub ustawień rejestru. Obiekt zasad grupy znajduje się w obszarze **Konfiguracja komputera\\zasad\\Szablony administracyjne\\Microsoft Office 2016 (Machine)\\ustawienia licencjonowania**

Narzędzie wdrażania pakietu Office zawiera plik Setup. exe. Aby zainstalować pakiet Office, uruchom następujące polecenie w wierszu polecenia:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Przykładowa konfiguracja. XML

Poniższy przykład kodu XML zainstaluje comiesięczne wydanie.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Zespół biura zaleca używanie instalacji 64-bitowej dla parametru **OfficeClientEdition** .

Po zainstalowaniu pakietu Office można zaktualizować domyślne zachowanie biura. Uruchom następujące polecenia pojedynczo lub w pliku wsadowym, aby zaktualizować zachowanie.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instalowanie usługi OneDrive w trybie dla komputera

Usługa OneDrive jest zwykle instalowana dla poszczególnych użytkowników. W tym środowisku należy zainstalować na komputerze.

Poniżej przedstawiono sposób instalowania usługi OneDrive w trybie na maszynę:

1. Najpierw Utwórz lokalizację, aby przemieścić Instalatora usługi OneDrive. Folder dysku lokalnego lub lokalizacji [\\\\UNC] (file://unc) jest prawidłowy.

2. Pobierz OneDriveSetup. exe do lokalizacji przygotowanej za pomocą tego linku: <https://aka.ms/OneDriveWVD-Installer>

3. Jeśli zainstalowano pakiet Office z usługą OneDrive, pomijając **\<EXCLUDEAPP ID = "OneDrive"/\>** , Odinstaluj wszystkie istniejące instalacje usługi OneDrive dla użytkowników z poziomu wiersza polecenia z podwyższonym poziomem uprawnień, uruchamiając następujące polecenie:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Uruchom to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby ustawić wartość rejestru **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Uruchom to polecenie, aby zainstalować usługę OneDrive w trybie dla komputera:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Uruchom to polecenie, aby skonfigurować usługę OneDrive do uruchamiania przy logowaniu dla wszystkich użytkowników:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Włącz **dyskretnie Skonfiguruj konto użytkownika** , uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Przekieruj i Przenieś znane foldery systemu Windows do usługi OneDrive, uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Zespoły i Skype

Pulpit wirtualny systemu Windows nie obsługuje programu Skype dla firm i zespołów.

## <a name="next-steps"></a>Następne kroki

Po dodaniu pakietu Office do obrazu można kontynuować Dostosowywanie głównego obrazu wirtualnego dysku twardego. Zobacz [Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md).
