---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907580"
---
## <a name="steps-to-enable-single-sign-on"></a>Procedura włączania logowania jednokrotnego
Inicjowanie obsługi administracyjnej w chmurze współpracuje z logowaniem jednokrotnym.  Obecnie nie jest dostępna opcja włączania logowania jednokrotnego w przypadku zainstalowania agenta. można jednak wykonać poniższe kroki, aby włączyć logowanie jednokrotne i użyć go. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Krok 1. Pobieranie i wyodrębnianie plików Azure AD Connect
1.  Najpierw pobierz najnowszą wersję programu [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Otwórz wiersz polecenia przy użyciu uprawnień administracyjnych i przejdź do pobranego pliku msi.
3.  Uruchom następujące czynności: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Zmień ścieżki FilePath i extractfolder, aby odpowiadały ścieżce pliku i nazwie folderu wyodrębniania.  Zawartość powinna teraz znajdować się w folderze wyodrębniania.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Krok 2. zaimportowanie bezproblemowego modułu programu PowerShell logowania jednokrotnego

1. Pobierz i zainstaluj program [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowy moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 3. Pobieranie listy Active Directory lasów, w których włączono bezproblemowe logowanie jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj `New-AzureADSSOAuthenticationContext`. Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Wywołaj `Get-AzureADSSOStatus`. To polecenie umożliwia wyświetlenie listy Active Directory lasów (Zobacz listę "domeny"), na której włączono tę funkcję.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4. Włączanie bezproblemowego logowania jednokrotnego dla każdego lasu Active Directory

1. Wywołaj `Enable-AzureADSSOForest`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu Active Directory.

   > [!NOTE]
   >Należy podać nazwę użytkownika poświadczeń administratora domeny w formacie nazwy konta SAM (contoso\johndoe lub contoso. com\johndoe). Używamy części nazwy użytkownika do lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Użyte konto administratora domeny nie może być członkiem grupy Chronieni użytkownicy. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Powtórz poprzedni krok dla każdego lasu Active Directory, w którym chcesz skonfigurować funkcję.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włączanie funkcji w dzierżawie

Aby włączyć funkcję w dzierżawie, wywołaj `Enable-AzureADSSO -Enable $true`.
