---
title: Omówienie agenta maszyny wirtualnej platformy Azure z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować agenta systemu Linux (waagent) w celu zarządzania interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej Azure.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073853"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Zrozumienie i używanie agenta systemu Linux platformy Azure

Agent Microsoft Azure Linux (waagent) zarządza obsługą systemu Linux & FreeBSD, a interakcja maszyny wirtualnej z kontrolerem sieci szkieletowej Azure. Oprócz agenta systemu Linux dostarczającego funkcje inicjowania obsługi, platforma Azure udostępnia również opcję korzystania z usługi Cloud-init dla niektórych systemów operacyjnych systemu Linux. Agent systemu Linux zapewnia następujące funkcje wdrożeń systemu Linux i FreeBSD IaaS:

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [plik Readme](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Inicjowanie obsługi obrazu**
  
  * Tworzenie konta użytkownika
  * Konfigurowanie typów uwierzytelniania SSH
  * Wdrażanie kluczy publicznych SSH i par kluczy
  * Ustawianie nazwy hosta
  * Publikowanie nazwy hosta na platformie DNS platformy
  * Raportowanie odcisku palca klucza hosta SSH do platformy
  * Zarządzanie dyskami zasobów
  * Formatowanie i Instalowanie dysku zasobów
  * Konfigurowanie obszaru wymiany
* **Sieć**
  
  * Zarządza trasami w celu poprawy zgodności z serwerami DHCP platformy
  * Zapewnia stabilność nazwy interfejsu sieciowego
* **Kernel**
  
  * Konfiguruje wirtualną architekturę NUMA (Wyłącz dla jądra <`2.6.37`)
  * Zużywa entropię funkcji Hyper-V dla/dev/Random
  * Konfiguruje limity czasu SCSI dla urządzenia głównego (co może być zdalne)
* **Diagnostyka**
  
  * Przekierowanie konsoli do portu szeregowego
* **Wdrożenia SCVMM**
  
  * Wykrywa i uruchamia Bootstrap agenta programu VMM dla systemu Linux w przypadku uruchamiania w środowisku System Center Virtual Machine Manager 2012 R2
* **Rozszerzenie maszyny wirtualnej**
  
  * Wsuń składnik utworzony przez firmę Microsoft i partnerów na maszynę wirtualną z systemem Linux (IaaS) w celu umożliwienia automatyzacji oprogramowania i konfiguracji
  * Implementacja odwołania do rozszerzenia maszyny wirtualnej na [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Komunikacja
Przepływ informacji z platformy do agenta odbywa się za pośrednictwem dwóch kanałów:

* Dysk DVD z dołączonym czasem rozruchu dla wdrożeń IaaS. Ten dysk DVD zawiera plik konfiguracji zgodny z OVF, który zawiera wszystkie informacje o aprowizacji inne niż rzeczywiste pary kluczy SSH.
* Punkt końcowy TCP uwidaczniający interfejs API REST używany do uzyskiwania konfiguracji wdrożenia i topologii.

## <a name="requirements"></a>Wymagania
Następujące systemy zostały przetestowane i wiadomo, że współpracują z agentem systemu Azure Linux:

> [!NOTE]
> Ta lista może się różnić od oficjalnych list obsługiwanych systemów na platformie Microsoft Azure, zgodnie z opisem w tym miejscu: [https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0 +
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4 +

Inne obsługiwane systemy:

* FreeBSD 10 + (Azure Linux Agent v 2.0.10 +)

Agent systemu Linux zależy od niektórych pakietów systemowych w celu poprawnego działania:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Narzędzia systemu plików: sfdisk, fdisk, mkfs, częściowo
* Narzędzia do haseł: chpasswd, sudo
* Narzędzia do przetwarzania tekstu: SED, grep
* Narzędzia sieciowe: trasa IP
* Obsługa jądra na potrzeby instalowania systemów plików UDF.

## <a name="installation"></a>Instalacja
Instalacja przy użyciu programu RPM lub pakietu DEB z repozytorium pakietu dystrybucji jest preferowaną metodą instalowania i uaktualniania agenta systemu Azure Linux. Wszyscy [pozatwierdzeni dostawcy dystrybucji](../linux/endorsed-distros.md) integrują pakiet agenta platformy Azure z systemem Linux z obrazami i repozytoriami.

Zapoznaj się z dokumentacją w [repozytorium agenta systemu Azure Linux w witrynie GitHub](https://github.com/Azure/WALinuxAgent) w celu uzyskania zaawansowanych opcji instalacji, takich jak instalowanie ze źródła lub do lokalizacji niestandardowych lub prefiksów.

## <a name="command-line-options"></a>Opcje wiersza polecenia
### <a name="flags"></a>flagi
* verbose: Zwiększ poziom szczegółowości określonego polecenia
* Wymuś: Pomiń interaktywne potwierdzenie niektórych poleceń

### <a name="commands"></a>Polecenia
* Pomoc: Wyświetla listę obsługiwanych poleceń i flag.
* Cofanie aprowizacji: podjęto próbę oczyszczenia systemu i nastąpi jego ponowne udostępnienie. Następująca operacja usuwa:
  
  * Wszystkie klucze hosta SSH (jeśli inicjowanie obsługi. RegenerateSshHostKeyPair ma wartość "y" w pliku konfiguracyjnym)
  * Konfiguracja serwer nazw w/etc/resolv.conf
  * Hasło główne z/etc/shadow (jeśli inicjowanie obsługi. DeleteRootPassword ma wartość "y" w pliku konfiguracyjnym)
  * Buforowane dzierżawy klientów DHCP
  * Zresetowanie nazwy hosta na wartość localhost.localdomain

> [!WARNING]
> Anulowanie aprowizacji nie gwarantuje, że obraz jest czyszczony z uwzględnieniem wszystkich poufnych informacji i odpowiednich do ponownej dystrybucji.
> 
> 

* Anulowanie aprowizacji + użytkownik: wykonuje wszystkie czynności w ramach anulowania aprowizacji (powyżej), a także usuwa ostatnio zainicjowane konto użytkownika (uzyskane z/var/lib/waagent) i powiązane dane. Ten parametr jest niezależny od zainicjowania obsługi obrazu, który został wcześniej zainicjowany na platformie Azure, aby można go było przechwycić i ponownie użyć.
* Wersja: Wyświetla wersję waagent
* serialconsole: konfiguruje GRUB, aby oznaczyć ttyS0 (pierwszy port szeregowy) jako konsolę rozruchową. Dzięki temu dzienniki rozruchu jądra są wysyłane do portu szeregowego i udostępniane do debugowania.
* Demon: Uruchom waagent jako demona, aby zarządzać interakcją z platformą. Ten argument jest określony do waagent w skrypcie init waagent.
* Uruchom: Uruchom waagent jako proces w tle

## <a name="configuration"></a>Konfiguracja
Plik konfiguracji (/etc/waagent.conf) kontroluje akcje waagent. Poniżej przedstawiono przykładowy plik konfiguracji:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

Opisane są następujące różne opcje konfiguracji. Opcje konfiguracji są trzy typy: Wartość logiczna, String lub Integer. Opcje konfiguracji wartości logicznych można określić jako "y" lub "n". Specjalne słowo kluczowe "none" może być używane dla niektórych wpisów konfiguracji typu String jako następujące szczegóły:

**Inicjowanie obsługi administracyjnej. włączone:**  
```
Type: Boolean  
Default: y
```
Dzięki temu użytkownik może włączyć lub wyłączyć funkcję aprowizacji w agencie. Prawidłowe wartości to "y" lub "n". Jeśli Inicjowanie obsługi jest wyłączone, hosty SSH i klucze użytkownika w obrazie są zachowywane i jakakolwiek konfiguracja określona w interfejsie API aprowizacji platformy Azure zostanie zignorowana.

> [!NOTE]
> Parametr `Provisioning.Enabled` domyślnie przyjmuje wartość "n" w obrazach w chmurze Ubuntu, które korzystają z inicjowania obsługi administracyjnej w chmurze.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Jeśli ta wartość jest ustawiona, hasło główne w pliku/etc/shadow zostanie wymazane podczas procesu aprowizacji.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Jeśli ta wartość jest ustawiona, wszystkie pary kluczy hosta SSH (ECDSA, DSA i RSA) są usuwane podczas procesu aprowizacji z/etc/ssh/. I generowana jest pojedyncza para kluczy.

Typ szyfrowania pary kluczy można skonfigurować przy użyciu wpisu aprowizacji. SshHostKeyPairType. Niektóre dystrybucje ponownie tworzą pary kluczy SSH dla wszelkich brakujących typów szyfrowania po ponownym uruchomieniu demona SSH (na przykład po ponownym uruchomieniu).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Można ustawić ten typ algorytmu szyfrowania, który jest obsługiwany przez demona SSH na maszynie wirtualnej. Zazwyczaj obsługiwane są wartości "RSA", "DSA" i "ECDSA". "w systemie Windows" nie obsługuje "ECDSA". Tak więc, jeśli zamierzasz użyć w systemie Windows programu w celu nawiązania połączenia z wdrożeniem systemu Linux, użyj "RSA" lub "DSA".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Jeśli ta wartość jest ustawiona, waagent monitoruje maszynę wirtualną z systemem Linux pod kątem zmian nazw hostów (zwracanych przez polecenie "hostname") i automatycznie aktualizuje konfigurację sieci w obrazie, aby odzwierciedlić zmianę. Aby można było wypchnąć zmianę nazwy na serwery DNS, Sieć jest ponownie uruchamiana na maszynie wirtualnej. Powoduje to krótką utratę łączności z Internetem.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli jest ustawiona, waagent dekoduje CustomData z base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Jeśli ta wartość jest ustawiona, waagent wykonuje CustomData po aprowizacji.

**Inicjowanie obsługi administracyjnej. AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ta opcja umożliwia resetowanie hasła dla użytkownika sys; wartość domyślna jest wyłączona.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algorytm używany przez Crypt podczas generowania skrótu hasła.  
 1 - MD5  
 2a — Blowfish  
 5 — SHA-256  
 6 — SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Długość losowej soli używanej podczas generowania skrótu hasła.

**ResourceDisk. Format:**  
```
Type: Boolean  
Default: y
```
Jeśli ta wartość jest ustawiona, dysk zasobów dostarczony przez platformę jest sformatowany i instalowany przez waagent, jeśli typ systemu plików żądany przez użytkownika w "ResourceDisk. FileSystem" jest inny niż "NTFS". Na dysku są udostępniane pojedynczej partycji typu Linux (83). Ta partycja nie jest sformatowana, jeśli może zostać pomyślnie zainstalowana.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Określa typ systemu plików dla dysku zasobu. Obsługiwane wartości różnią się w zależności od dystrybucji systemu Linux. Jeśli ciąg to X, a następnie mkfs. Symbol X powinien być obecny w obrazie systemu Linux. Obrazy SLES 11 powinny zwykle korzystać z "ext3". W tym miejscu obrazy FreeBSD powinny używać "UFS2".

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Określa ścieżkę, w której zainstalowano dysk zasobu. Dysk zasobu jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Określa opcje instalacji dysków, które mają zostać przesłane do polecenia instalacji-o. Jest to rozdzielona przecinkami lista wartości, np. 'nodev,nosuid'. Aby uzyskać szczegółowe informacje, zobacz Instalowanie (8).

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Jeśli ta wartość jest ustawiona, plik wymiany (/swapfile) jest tworzony na dysku zasobu i dodawany do obszaru wymiany w systemie.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Rozmiar pliku wymiany w megabajtach.

**Logs. verbose:**  
```
Type: Boolean  
Default: n
```
W przypadku ustawienia opcji szczegółowości dziennika jest zwiększana. Waagent dzienniki do/var/log/waagent.log i wykorzystuje funkcjonalność System logrotate do rotacji dzienników.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Jeśli ta wartość jest ustawiona, Agent podejmie próbę instalacji, a następnie załadowania sterownika jądra RDMA zgodnego z wersją oprogramowania układowego na źródłowym sprzęcie.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
To ustawienie umożliwia skonfigurowanie limitu czasu interfejsu SCSI w sekundach na dysku systemu operacyjnego i na dyskach danych. Jeśli nie zostanie ustawiona, są używane wartości domyślne systemu.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
Tego ustawienia można użyć do określenia alternatywnej ścieżki pliku binarnego OpenSSL, który ma być używany na potrzeby operacji kryptograficznych.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
W przypadku ustawienia agent używa tego serwera proxy w celu uzyskania dostępu do Internetu. 

**Funkcja Aktualizacje automatyczne. włączona**
```
Type: Boolean
Default: y
```
Włącza lub wyłącza funkcję autoaktualizowania dla przetwarzania stanu celu. wartość domyślna jest włączona.



## <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Images
Obrazy w chmurze Ubuntu wykorzystują funkcję [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) do wykonywania wielu zadań konfiguracyjnych, które w przeciwnym razie byłyby zarządzane przez agenta systemu Linux platformy Azure. Obowiązują następujące różnice:

* **Inicjowanie obsługi administracyjnej. włączono** domyślnie dla "n" obrazów w chmurze Ubuntu, które używają funkcji Cloud-init do wykonywania zadań aprowizacji.
* Następujące parametry konfiguracji nie mają wpływu na obrazy w chmurze Ubuntu korzystające z usługi Cloud-init do zarządzania dyskami zasobów i przestrzenią wymiany:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Aby uzyskać więcej informacji, zapoznaj się z poniższymi zasobami w celu skonfigurowania punktu instalacji dysku zasobu i zamiany miejsca na obrazy w chmurze Ubuntu podczas aprowizacji:
  
  * [Ubuntu wiki: Konfigurowanie partycji wymiany](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Wprowadzanie danych niestandardowych do maszyny wirtualnej platformy Azure](../windows/classic/inject-custom-data.md)

