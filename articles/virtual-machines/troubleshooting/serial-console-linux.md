---
title: Konsola szeregowa platformy Azure dla systemu Linux | Microsoft Docs
description: Dwukierunkowa konsola szeregowa dla Virtual Machines i Virtual Machine Scale Sets platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167016"
---
# <a name="azure-serial-console-for-linux"></a>Konsola szeregowa platformy Azure dla systemu Linux

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Linux i wystąpień zestawów skalowania maszyn wirtualnych. To połączenie szeregowe łączy się z portem szeregowym ttyS0 maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Aby uzyskać dokumentację konsoli szeregowej dla systemu Windows, zobacz [konsola szeregowa dla systemu Windows](../windows/serial-console.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w Azure Government. Nie jest jeszcze dostępna w chmurze platformy Azure w Chinach.


## <a name="prerequisites"></a>Wymagania wstępne

- W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany model wdrażania zarządzania zasobami. W przypadku wdrożeń klasycznych nie są obsługiwane.

- Twoje konto używające konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć użytkownika opartego na hasłach. Można go utworzyć za pomocą funkcji [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny wirtualnej. Wybierz pozycję **zresetuj hasło** w sekcji **Pomoc techniczna i rozwiązywanie problemów** .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć włączoną [diagnostykę rozruchu](boot-diagnostics.md) .

    ![Ustawienia diagnostyki rozruchu](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Aby poznać ustawienia dotyczące dystrybucji systemu Linux, zobacz [konsola szeregowa dostępność dystrybucji systemu Linux](#serial-console-linux-distribution-availability).

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych musi być skonfigurowane pod kątem danych wyjściowych seryjnych w `ttys0`. Jest to wartość domyślna dla obrazów platformy Azure, ale zawarto to dokładnie sprawdzić na obrazach niestandardowych. Szczegóły [poniżej](#custom-linux-images).


> [!NOTE]
> Konsola szeregowa wymaga użytkownika lokalnego ze skonfigurowanym hasłem. Maszyny wirtualne lub zestawy skalowania maszyn wirtualnych skonfigurowane tylko przy użyciu klucza publicznego SSH nie będą mogły zalogować się do konsoli szeregowej. Aby utworzyć użytkownika lokalnego przy użyciu hasła, użyj [rozszerzenia VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), które jest dostępne w portalu, wybierając pozycję **zresetuj hasło** w Azure Portal i Utwórz użytkownika lokalnego z hasłem.
> Możesz również zresetować hasło administratora na koncie przy [użyciu grub, aby przeprowadzić rozruch w trybie jednego użytkownika](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostępność dystrybucji w konsoli szeregowej systemu Linux
Aby konsola szeregowa działała poprawnie, system operacyjny gościa musi być skonfigurowany do odczytu i zapisu komunikatów konsoli na porcie szeregowym. Większość [poświadczonej dystrybucji systemu Linux platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ma domyślnie skonfigurowaną konsolę szeregowaną. Wybranie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal zapewnia dostęp do konsoli szeregowej.

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że na maszynie wirtualnej jest włączona Diagnostyka rozruchu. Naciśnięcie **klawisza ENTER** często rozwiązuje problemy, w przypadku których nic nie pojawia się w konsoli szeregowej.

Dystrybucja      | Dostęp do konsoli szeregowej
:-----------|:---------------------
Red Hat Enterprise Linux    | Konsola szeregowa dostęp domyślnie włączony.
CentOS      | Konsola szeregowa dostęp domyślnie włączony.
Debian      | Konsola szeregowa dostęp domyślnie włączony.
Ubuntu      | Konsola szeregowa dostęp domyślnie włączony.
CoreOS      | Konsola szeregowa dostęp domyślnie włączony.
SUSE        | Nowsze obrazy SLES dostępne na platformie Azure mają domyślnie włączony dostęp do konsoli szeregowej. Jeśli używasz starszych wersji (10 lub wcześniejszych) SLES na platformie Azure, zapoznaj się z [artykułem KB](https://www.novell.com/support/kb/doc.php?id=3456486) , aby włączyć konsolę szeregową.
Oracle Linux        | Konsola szeregowa dostęp domyślnie włączony.

### <a name="custom-linux-images"></a>Niestandardowe obrazy systemu Linux
Aby włączyć konsolę szeregową dla niestandardowego obrazu maszyny wirtualnej z systemem Linux, Włącz dostęp do konsoli w pliku */etc/inittab* , aby uruchomić terminal na `ttyS0`. Na przykład: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Może być również konieczne zduplikowanie Getty na ttyS0. Można to zrobić za pomocą `systemctl start serial-getty@ttyS0.service`.

Warto również dodać ttyS0 jako lokalizację docelową dla danych wyjściowych seryjnych. Aby uzyskać więcej informacji na temat konfigurowania niestandardowego obrazu do pracy z konsolą szeregową, zobacz Ogólne wymagania systemowe na stronie [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Jeśli tworzysz jądro niestandardowe, rozważ włączenie tych flag jądra: `CONFIG_SERIAL_8250=y` i `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Plik konfiguracji zazwyczaj znajduje się w ścieżce */Boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Uszkodzony plik *fstab* | Naciśnij klawisz **Enter** , aby kontynuować, a następnie użyj edytora tekstów, aby naprawić plik *fstab* . Być może trzeba będzie działać w trybie jednego użytkownika. Aby uzyskać więcej informacji, zobacz sekcję konsola szeregowa w sprawie [rozwiązywania problemów z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) i [używania konsoli szeregowej do uzyskiwania dostępu do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).
Reguły zapory niepoprawne |  Jeśli skonfigurowano dołączenie iptables do blokowania łączności SSH, można użyć konsoli szeregowej do współpracy z maszyną wirtualną bez konieczności używania protokołu SSH. Więcej szczegółów można znaleźć na [stronie dołączenie iptables Man](https://linux.die.net/man/8/iptables).<br>Podobnie, Jeśli zapora blokuje dostęp SSH, można uzyskać dostęp do maszyny wirtualnej za pośrednictwem konsoli szeregowej i ponownie skonfigurować zaporę. Więcej informacji można znaleźć w [dokumentacji zapory](https://firewalld.org/documentation/).
System plików uszkodzenie/wyboru | Aby uzyskać więcej informacji na temat rozwiązywania problemów z uszkodzonymi systemami plików przy użyciu konsoli szeregowej, zobacz sekcję dotyczącą konsoli szeregowej [maszyny wirtualnej](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) z systemem Linux systemu Azure.
Problemy z konfiguracją SSH | Dostęp do konsoli szeregowej i zmienić ustawienia. Konsola szeregowa można użyć niezależnie od konfiguracji SSH maszyny wirtualnej, ponieważ nie wymaga ona, aby maszyna wirtualna mogła działać. Przewodnik rozwiązywania problemów jest dostępny w [przypadku rozwiązywania problemów z połączeniami SSH z maszyną wirtualną platformy Azure z systemem Linux, która kończy się niepowodzeniem, wystąpiła błędy](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) Więcej szczegółów można znaleźć w [szczegółowych krokach rozwiązywania problemów SSH w przypadku problemów z połączeniem z maszyną wirtualną z systemem Linux na platformie Azure](./detailed-troubleshoot-ssh-connection.md)
Interakcja z programu inicjującego | Uruchom ponownie maszynę wirtualną z poziomu bloku konsoli szeregowej, aby uzyskać dostęp do usługi GRUB na maszynie wirtualnej z systemem Linux. Aby uzyskać szczegółowe informacje i informacje dotyczące dystrybucji, zobacz [Korzystanie z konsoli szeregowej w celu uzyskania dostępu do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Wyłącz konsolę seryjną

Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowej. Konsolę szeregową można wyłączyć na poziomie subskrypcji lub na maszynie wirtualnej/zestawie skalowania maszyn wirtualnych. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie i wyłączanie konsoli szeregowej platformy Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają rolę dostępu [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub wyższą dla maszyny wirtualnej. Jeśli dzierżawa Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), dostęp do konsoli szeregowej będzie wymagał również usługi MFA, ponieważ dostęp do konsoli szeregowej odbywa się za pomocą [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane, które są wysyłane w obie strony są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Wszystkie prawa dostępu do konsoli szeregowej są obecnie rejestrowane w dziennikach [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Żadne hasła dostępu do konsoli są rejestrowane. Jednak jeśli polecenia uruchamiane w ramach konsoli zawierają lub danych wyjściowych haseł, kluczy tajnych, nazwy użytkowników lub jakąkolwiek inną formę identyfikowalne dane osobowe (PII), te będą zapisywane do dzienników diagnostyki rozruchu maszyny Wirtualnej. One będą zapisywane wraz z wszystkich innych widocznych tekstu, jako część wykonania wstecz przewijania konsoli szeregowej funkcji. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mieli do nich dostęp. W przypadku umieszczania jakichkolwiek poleceń dotyczących danych, które zawierają wpisy tajne lub dane OSOBowe, zalecamy użycie protokołu SSH, chyba że konsola szeregowa jest absolutnie konieczna.

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik nawiązał połączenie z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który został odłączony, nie zostanie wylogowany. Możliwość wymuszenia wylogowania przy rozłączeniu (przy użyciu mechanizmu SIGHUP lub podobnego) nadal znajduje się w planie. W przypadku systemu Windows w specjalnej konsoli administracyjnej (SAC) jest włączony automatyczny limit czasu. Jednak w przypadku systemu Linux można skonfigurować ustawienie limitu czasu terminalu. Aby to zrobić, Dodaj `export TMOUT=600` w pliku *. bash_profile* lub *. profilu* dla użytkownika, którego używasz do logowania się w konsoli programu. To ustawienie spowoduje przekroczenie limitu czasu sesji po 10 minutach.

## <a name="accessibility"></a>Ułatwienia dostępu
Ułatwienia dostępu to kluczowy fokus dla konsoli szeregowej platformy Azure. W tym celu upewnij się, że konsola szeregowa jest w pełni dostępna.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj klawisza **Tab** na klawiaturze, aby przejść do interfejsu konsoli szeregowej z Azure Portal. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokus okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-serial-console-with-a-screen-reader"></a>Korzystanie z konsoli szeregowej z czytnikiem ekranu
Konsoli szeregowej ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="known-issues"></a>Znane problemy
Mamy świadomość niektórych problemów z konsolą szeregową i systemem operacyjnym maszyny wirtualnej. Poniżej znajduje się lista tych problemów i kroków związanych z ograniczeniami dla maszyn wirtualnych z systemem Linux. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych. Jeśli nie są one zgodne z wyświetlonym błędem, zobacz Typowe błędy usługi konsoli szeregowej w przypadku [typowych błędów konsoli szeregowej](./serial-console-errors.md).

Problem                           |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po banerze połączenia nie spowoduje wyświetlenia monitu logowania. | GRUB może nie być poprawnie skonfigurowana. Uruchom następujące polecenia: `grub2-mkconfig -o /etc/grub2-efi.cfg` i/lub `grub2-mkconfig -o /etc/grub2.cfg`. Aby uzyskać więcej informacji, zobacz [naciśnięcie klawisza ENTER nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten problem może wystąpić, jeśli używasz niestandardowej maszyny wirtualnej, urządzenia z ograniczeniami lub konfiguracji GRUB, która powoduje, że system Linux nie może nawiązać połączenia z portem szeregowym.
Konsola szeregowa tekst pobiera tylko część rozmiaru ekranu (często po użyciu edytora tekstów). | Konsole szeregowe nie obsługują negocjowania rozmiaru okna o rozmiarze ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), co oznacza, że sygnał SIGWINCH nie zostanie wysłany do aktualizacji rozmiaru ekranu, a maszyna wirtualna nie będzie miała informacji o rozmiarze terminalu. Zainstaluj xterm lub podobne narzędzie, aby udostępnić polecenie `resize`, a następnie uruchom `resize`.
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.
Błędne dane wejściowe klawiatury w obrazach SLES BYOS. Dane wejściowe z klawiatury są tylko sporadycznie rozpoznawane. | Jest to problem z pakietem Plymouth. Nie należy uruchamiać Plymouth na platformie Azure, ponieważ nie jest potrzebny ekran powitalny, a Plymouth zakłóca możliwości platformy do korzystania z konsoli szeregowej. Usuń Plymouth z `sudo zypper remove plymouth` a następnie uruchom ponownie. Alternatywnie możesz zmodyfikować wiersz jądra konfiguracji GRUB, dołączając `plymouth.enable=0` na końcu wiersza. Można to zrobić, [edytując wpis rozruchu w czasie rozruchu](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)lub edytując wiersz GRUB_CMDLINE_LINUX w `/etc/default/grub`, przebudować GRUB z `grub2-mkconfig -o /boot/grub2/grub.cfg`, a następnie ponownie uruchomić.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P. Jak mogę wysłać opinię?**

A. Prześlij opinię, tworząc problem w usłudze GitHub w https://aka.ms/serialconsolefeedback. Alternatywnie (mniej preferowany) można wysłać opinię za pośrednictwem azserialhelp@microsoft.com lub z kategorii https://feedback.azure.commaszyny wirtualnej.

**P. czy konsola szeregowa obsługuje kopiowanie/wklejanie?**

A. Tak. Użyj **klawiszy ctrl**+**SHIFT**+**C** i **Ctrl**+**SHIFT**+**V** , aby skopiować i wkleić do terminalu.

**P. Czy można używać konsoli szeregowej zamiast połączenia SSH?**

A. Mimo że takie użycie może wydawać się technicznie możliwe, konsola szeregowa jest przeznaczona głównie jako narzędzie do rozwiązywania problemów w sytuacjach, gdy łączność za pośrednictwem protokołu SSH nie jest możliwa. Zalecamy używanie konsoli szeregowej jako zamiennika protokołu SSH z następujących powodów:

- Konsola szeregowa nie ma tak dużej przepustowości jak SSH. Ponieważ jest to połączenie tylko do tekstu, trudne są duże interakcje ze zbyt dużą graficznym interfejsem użytkownika.
- Konsola szeregowa dostęp jest obecnie możliwy tylko przy użyciu nazwy użytkownika i hasła. Ponieważ klucze SSH są znacznie bardziej bezpieczne niż kombinacje nazwy użytkownika/hasła, z punktu widzenia zabezpieczeń logowania zalecamy używanie protokołu SSH za pośrednictwem konsoli szeregowej.

**P. kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

**P. kto może uzyskać dostęp do konsoli szeregowej dla maszyny wirtualnej/zestawu skalowania maszyn wirtualnych?**

A. Aby można było uzyskać dostęp do konsoli szeregowej, należy mieć rolę współautor maszyny wirtualnej lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

**P. Moja konsola szeregowa nie wyświetla niczego, co mam zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Aby uzyskać informacje o konfigurowaniu obrazu w celu włączenia konsoli szeregowej, zobacz [konsola szeregowa dostępność dystrybucji systemu Linux](#serial-console-linux-distribution-availability).

**P. czy konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Tak! Zobacz [konsolę szeregowa dla Virtual Machine Scale Sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**P. w przypadku skonfigurowania maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych za pomocą tylko uwierzytelniania przy użyciu klucza SSH można nadal używać konsoli szeregowej do nawiązywania połączenia z maszyną wirtualną/wystąpieniem zestawu skalowania maszyn wirtualnych?**

A. Tak. Ponieważ konsola szeregowa nie wymaga kluczy SSH, wystarczy skonfigurować kombinację nazwy użytkownika/hasła. Możesz to zrobić, wybierając pozycję **zresetuj hasło** w Azure Portal i używając tych poświadczeń, aby zalogować się do konsoli szeregowej.

## <a name="next-steps"></a>Następne kroki
* Użyj konsoli szeregowej, aby [uzyskać dostęp do grub i trybu jednego użytkownika](serial-console-grub-single-user-mode.md).
* Użyj konsoli szeregowej dla [wywołań NMI i sysrq](serial-console-nmi-sysrq.md).
* Dowiedz się, jak [włączyć Grub w różnych dystrybucjeach](serial-console-grub-proactive-configuration.md) za pomocą konsoli szeregowej
* Konsola szeregowa jest również dostępna dla [maszyn wirtualnych z systemem Windows](../windows/serial-console.md).
* Dowiedz się więcej na temat [diagnostyki rozruchu](boot-diagnostics.md).

