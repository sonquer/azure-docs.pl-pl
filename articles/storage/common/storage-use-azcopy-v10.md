---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą AzCopy v10 | Microsoft Docs
description: AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł pomaga pobrać AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c4e2681121a15e0b84a11c7cf35119c3f1b69f11
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443719"
---
# <a name="get-started-with-azcopy"></a>Wprowadzenie do narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob lub plików do lub z konta magazynu. Ten artykuł pomaga pobrać AzCopy, nawiązać połączenie z kontem magazynu, a następnie przenieść pliki.

> [!NOTE]
> AzCopy **v10** jest obecnie obsługiwaną wersją AzCopy.
>
> Jeśli musisz użyć AzCopy **v 8.1**, zobacz sekcję [Używanie poprzedniej wersji AzCopy](#previous-version) w tym artykule.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Pobierz AzCopy

Najpierw Pobierz plik wykonywalny AzCopy v10 do dowolnego katalogu na komputerze. AzCopy v10 to tylko plik wykonywalny, więc nie ma nic do zainstalowania.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Te pliki są kompresowane jako plik zip (Windows i Mac) lub plik tar (Linux). Aby pobrać i zdekompresować plik tar w systemie Linux, zapoznaj się z dokumentacją dystrybucji systemu Linux.

> [!NOTE]
> Jeśli chcesz skopiować dane do i z usługi [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) , zainstaluj program [AzCopy w wersji 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Uruchom AzCopy

Dla wygody należy rozważyć dodanie lokalizacji katalogu wykonywalnego AzCopy do ścieżki systemowej w celu ułatwienia użycia. W ten sposób można wpisać `azcopy` z dowolnego katalogu w systemie.

W przypadku wybrania opcji nie dodawaj katalogu AzCopy do ścieżki należy zmienić katalogi na lokalizację pliku wykonywalnego AzCopy i wpisać `azcopy` lub `.\azcopy` w wierszach polecenia programu Windows PowerShell.

Aby wyświetlić listę poleceń, wpisz `azcopy -h` a następnie naciśnij klawisz ENTER.

Aby dowiedzieć się więcej na temat konkretnego polecenia, należy dołączyć nazwę polecenia (na przykład: `azcopy list -h`).

![Wbudowana pomoc](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Aby znaleźć szczegółową dokumentację referencyjną dla każdego polecenia i parametru polecenia, zobacz [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Przed wprowadzeniem jakichkolwiek istotnych informacji w usłudze AzCopy należy określić sposób dostarczania poświadczeń autoryzacji do usługi magazynu. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybierz sposób dostarczania poświadczeń autoryzacji

Poświadczenia autoryzacji można podawać przy użyciu Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

Użyj tej tabeli jako przewodnika:

| Typ usługi Storage | Obecnie obsługiwana metoda autoryzacji |
|--|--|
|**Blob Storage** | Usługa Azure AD & SAS |
|**BLOB Storage (hierarchiczna przestrzeń nazw)** | Usługa Azure AD & SAS |
|**File Storage** | Tylko SAS |

### <a name="option-1-use-azure-active-directory"></a>Opcja 1: Użyj Azure Active Directory

Za pomocą Azure Active Directory można podać poświadczenia jeden raz zamiast konieczności dołączania tokenu sygnatury dostępu współdzielonego do każdego polecenia.  

> [!NOTE]
> W bieżącej wersji, jeśli planujesz skopiować obiekty blob między kontami magazynu, musisz dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. Token sygnatury dostępu współdzielonego można pominąć tylko z docelowego adresu URL. Aby zapoznać się z przykładami, zobacz [Kopiuj obiekty blob między kontami magazynu](storage-use-azcopy-blobs.md).

Wymagany poziom autoryzacji zależy od tego, czy planujesz przekazywanie plików czy po prostu pobierasz je.

Jeśli chcesz tylko pobrać pliki, sprawdź, czy [czytnik danych BLOB magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) został przypisany do tożsamości użytkownika, tożsamości zarządzanej lub nazwy głównej usługi.

> Tożsamości użytkowników, zarządzane tożsamości i nazwy główne usług to każdy typ *podmiotu zabezpieczeń*, dlatego będziemy używać warunkowego *podmiotu zabezpieczeń* dla pozostałej części tego artykułu.

Jeśli chcesz przekazać pliki, sprawdź, czy jedna z tych ról została przypisana do podmiotu zabezpieczeń:

- [Współautor danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Właściciel danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Role te można przypisać do podmiotu zabezpieczeń w dowolnym z następujących zakresów:

- Kontener (system plików)
- Konto magazynu
- Grupa zasobów
- Subskrypcja

Aby dowiedzieć się, jak weryfikować i przypisywać role, zobacz [udzielanie dostępu do obiektów blob platformy Azure i danych z kolejki przy użyciu RBAC w Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Należy pamiętać, że propagacja ról RBAC może potrwać do 5 minut.

Nie trzeba mieć jednej z tych ról przypisanych do podmiotu zabezpieczeń, jeśli podmiot zabezpieczeń zostanie dodany do listy kontroli dostępu (ACL) docelowego kontenera lub katalogu. Na liście ACL podmiot zabezpieczeń musi mieć uprawnienia do zapisu w katalogu docelowym i uprawnienia do wykonywania w kontenerze i w każdym katalogu nadrzędnym.

Aby dowiedzieć się więcej, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Uwierzytelnianie tożsamości użytkownika

Po sprawdzeniu, czy tożsamość użytkownika ma wymagany poziom autoryzacji, Otwórz wiersz polecenia, wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login
```

Jeśli użytkownik należy do więcej niż jednej organizacji, Uwzględnij identyfikator dzierżawy organizacji, do której należy konto magazynu.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Zastąp symbol zastępczy `<tenant-id>` IDENTYFIKATORem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal.

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, podaj kod, a następnie wybierz przycisk **dalej** .

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie Zaloguj się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu możesz zamknąć okno przeglądarki i zacząć korzystać z AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Uwierzytelnianie jednostki usługi

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który działa bez interakcji z użytkownikiem, szczególnie w przypadku uruchamiania lokalnego. Jeśli planujesz uruchamiać AzCopy na maszynach wirtualnych działających na platformie Azure, zarządzana tożsamość usługi jest łatwiejsza do administrowania. Aby dowiedzieć się więcej, zobacz sekcję [uwierzytelnianie tożsamości zarządzanej](#managed-identity) w tym artykule.

Przed uruchomieniem skryptu należy zalogować się interaktywnie co najmniej jeden raz, aby zapewnić AzCopy z poświadczeniami nazwy głównej usługi.  Te poświadczenia są przechowywane w zabezpieczonym i zaszyfrowanym pliku, dzięki czemu skrypt nie musi podawać informacji poufnych.

Możesz zalogować się do konta przy użyciu klucza tajnego klienta lub przy użyciu hasła certyfikatu skojarzonego z rejestracją aplikacji jednostki usługi.

Aby dowiedzieć się więcej na temat tworzenia nazwy głównej usługi, zobacz [How to: Use the Portal, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Aby dowiedzieć się więcej na temat ogólnych nazw głównych usług, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Korzystanie z klucza tajnego klienta

Zacznij od ustawienia zmiennej środowiskowej `AZCOPY_SPA_CLIENT_SECRET` na klucz tajny klienta rejestracji aplikacji jednostki usługi.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiona w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. Dzięki temu wartość jest dostępna tylko dla bieżącej sesji.

Ten przykład pokazuje, jak można to zrobić w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Rozważ użycie monitu, jak pokazano w tym przykładzie. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń konsoli.  

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Zastąp symbol zastępczy `<application-id>` IDENTYFIKATORem aplikacji rejestracji aplikacji jednostki usługi. Zastąp symbol zastępczy `<tenant-id>` IDENTYFIKATORem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal. 

##### <a name="using-a-certificate"></a>Korzystanie z certyfikatu

Jeśli wolisz użyć własnych poświadczeń do autoryzacji, możesz przekazać certyfikat do rejestracji aplikacji, a następnie użyć tego certyfikatu do logowania.

Oprócz przekazywania certyfikatu do rejestracji aplikacji trzeba również mieć kopię certyfikatu zapisaną na komputerze lub maszynie wirtualnej, na której będzie działać AzCopy. Ta kopia certyfikatu powinna znajdować się w temacie. PFX lub. Format PEM i musi zawierać klucz prywatny. Klucz prywatny powinien być chroniony hasłem. Jeśli używasz systemu Windows, a certyfikat istnieje tylko w magazynie certyfikatów, upewnij się, że ten certyfikat został wyeksportowany do pliku PFX (w tym klucza prywatnego). Aby uzyskać wskazówki, zobacz [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Następnie ustaw dla zmiennej środowiskowej `AZCOPY_SPA_CERT_PASSWORD` hasło certyfikatu.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiona w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. Dzięki temu wartość jest dostępna tylko dla bieżącej sesji.

Ten przykład pokazuje, jak to zadanie można wykonać w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Zastąp symbol zastępczy `<path-to-certificate-file>` ścieżką względną lub w pełni kwalifikowaną do pliku certyfikatu. AzCopy zapisuje ścieżkę do tego certyfikatu, ale nie zapisuje kopii certyfikatu, więc pamiętaj, aby zachować ten certyfikat w miejscu. Zastąp symbol zastępczy `<tenant-id>` IDENTYFIKATORem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal.

> [!NOTE]
> Rozważ użycie monitu, jak pokazano w tym przykładzie. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń konsoli. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Uwierzytelnianie tożsamości zarządzanej

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który jest uruchamiany bez interakcji z użytkownikiem, a skrypt jest uruchamiany z maszyny wirtualnej platformy Azure. W przypadku korzystania z tej opcji nie będzie konieczne przechowywanie żadnych poświadczeń na maszynie wirtualnej.

Możesz zalogować się do konta przy użyciu tożsamości zarządzanej na poziomie systemu, która została włączona na maszynie wirtualnej, lub przy użyciu identyfikatora klienta, identyfikatora obiektu lub identyfikatora zasobu tożsamości zarządzanej przypisanej przez użytkownika, która została przypisana do maszyny wirtualnej.

Aby dowiedzieć się więcej na temat włączania tożsamości zarządzanej na poziomie systemu lub tworzenia tożsamości zarządzanej przypisanej przez użytkownika, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Korzystanie z tożsamości zarządzanej na poziomie systemu

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana na poziomie systemu. Zobacz [tożsamość zarządzana przypisana przez system](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Następnie w konsoli poleceń wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Korzystanie z tożsamości zarządzanej przypisanej przez użytkownika

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana przypisana przez użytkownika. Zobacz [tożsamość zarządzana przypisana przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Następnie w konsoli poleceń wpisz dowolne z poniższych poleceń, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Zastąp symbol zastępczy `<client-id>` IDENTYFIKATORem klienta tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Zastąp symbol zastępczy `<object-id>` IDENTYFIKATORem obiektu tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Zastąp symbol zastępczy `<resource-id>` IDENTYFIKATORem zasobu tożsamości zarządzanej przypisanej przez użytkownika.

### <a name="option-2-use-a-sas-token"></a>Opcja 2: używanie tokenu SAS

Do każdego źródłowego lub docelowego adresu URL, który będzie używany w poleceniach AzCopy, można dołączyć token sygnatury dostępu współdzielonego.

To przykładowe polecenie cyklicznie kopiuje dane z katalogu lokalnego do kontenera obiektów BLOB. Fikcyjny token sygnatury dostępu współdzielonego jest dołączany na końcu adresu URL kontenera.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Aby dowiedzieć się więcej o tokenach SAS i sposobach ich uzyskania, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Transfer plików

Po uwierzytelnieniu tożsamości lub uzyskaniu tokenu SAS można rozpocząć transfer plików.

Aby znaleźć przykładowe polecenia, zapoznaj się z dowolnym z tych artykułów.

- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Transferowanie danych za pomocą AzCopy i magazynu Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Używanie AzCopy w skrypcie

### <a name="obtain-a-static-download-link"></a>Uzyskaj statyczny link pobierania

W miarę upływu czasu [link pobierania](#download-and-install-azcopy) AzCopy będzie wskazywał nowe wersje AzCopy. Jeśli skrypt pobiera AzCopy, skrypt może przestać działać, jeśli nowsza wersja AzCopy modyfikuje funkcje, od których zależy skrypt.

Aby uniknąć tych problemów, Uzyskaj statyczny (niezmieniony) link do bieżącej wersji AzCopy. Dzięki temu skrypt pobiera tę samą dokładną wersję AzCopy przy każdym uruchomieniu.

Aby uzyskać link, uruchom następujące polecenie:

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> W przypadku systemu Linux `--strip-components=1` na `tar` polecenie usuwa folder najwyższego poziomu, który zawiera nazwę wersji, a następnie wyodrębnia plik binarny bezpośrednio do bieżącego folderu. Dzięki temu skrypt będzie aktualizowany przy użyciu nowej wersji `azcopy` przez zaktualizowanie `wget` adresu URL.

W danych wyjściowych tego polecenia zostanie wyświetlony adres URL. Skrypt może następnie pobrać AzCopy przy użyciu tego adresu URL.

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Znaki specjalne ucieczki w tokenach SAS

W plikach wsadowych, które mają rozszerzenie `.cmd` należy wypróbować znaki `%`, które pojawiają się w tokenach sygnatury dostępu współdzielonego. Można to zrobić, dodając znak `%` znaku obok istniejących znaków `%` w ciągu tokenu sygnatury dostępu współdzielonego.

### <a name="run-scripts-by-using-jenkins"></a>Uruchamianie skryptów przy użyciu Jenkins

Jeśli planujesz używać [Jenkins](https://jenkins.io/) do uruchamiania skryptów, pamiętaj, aby umieścić następujące polecenie na początku skryptu.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Używanie AzCopy w Eksplorator usługi Azure Storage

[Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) używa AzCopy do wykonywania wszystkich operacji transferu danych. [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) można użyć, jeśli chcesz wykorzystać zalety wydajności AzCopy, ale wolisz używać graficznego interfejsu użytkownika, a nie wiersza polecenia, aby pracować z plikami.

Eksplorator usługi Storage używa klucza konta do wykonywania operacji, więc po zalogowaniu się do Eksplorator usługi Storage nie trzeba podawać dodatkowych poświadczeń autoryzacji.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Użyj poprzedniej wersji programu AzCopy

Jeśli musisz użyć poprzedniej wersji programu AzCopy (AzCopy v 8.1), zobacz jedno z następujących linków:

- [AzCopy w systemie Windows (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy w systemie Linux (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy

Zobacz [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania, problemy lub ogólne informacje zwrotne, prześlij je [na](https://github.com/Azure/azure-storage-azcopy) stronę usługi GitHub.
