---
title: Tworzenie i przekazywanie Oracle Linux wirtualnego dysku twardego
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego Oracle Linux system operacyjny.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: mimckitt
ms.openlocfilehash: e0250737f1f2934548a16ee42e9ff582f2403c48
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747727"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Linux w środowisku Oracle dla platformy Azure

W tym artykule założono, że zainstalowano już Oracle Linux system operacyjny na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux uwagi dotyczące instalacji
* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Obsługa funkcji Hyper-V i platformy Azure Oracle Linux z nieprzerwanym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z systemem Red Hat.
* UEK2 firmy Oracle nie jest obsługiwana w przypadku funkcji Hyper-V i platformy Azure, ponieważ nie obejmują one wymaganych sterowników.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Wersje jądra systemu Linux starsze niż 2.6.37 nie obsługują architektury NUMA w funkcji Hyper-V o większych rozmiarach maszyn wirtualnych. Ten problem ma głównie wpływ na starsze dystrybucje przy użyciu wbudowanego jądra Red Hat 2.6.32 i został ustalony w Oracle Linux 6,6 i nowszych
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować tak, aby utworzył plik wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .
* Upewnij się, że repozytorium `Addons` jest włączone. Edytuj plik `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7), a następnie zmień wiersz `enabled=0` na `enabled=1` w obszarze **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 i nowsze
Należy wykonać określone czynności konfiguracyjne w systemie operacyjnym, aby maszyna wirtualna mogła działać na platformie Azure.

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.
3. Odinstaluj program NetworkManager, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Uwaga:** Jeśli pakiet nie jest jeszcze zainstalowany, to polecenie zakończy się niepowodzeniem z komunikatem o błędzie. Jest to oczekiwane.
4. Utwórz plik o nazwie **Sieć** w katalogu `/etc/sysconfig/`, który zawiera następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Utwórz plik o nazwie **ifcfg-eth0** w katalogu `/etc/sysconfig/network-scripts/` zawierającym następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Upewnij się, że usługa sieciowa zacznie działać w czasie rozruchu, uruchamiając następujące polecenie:
   
        # chkconfig network on
8. Zainstaluj środowisko Python-pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
9. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądro zawiera następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
   
   Oprócz powyższych zaleca się *usunięcie* następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
   Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej przez 128 MB lub więcej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.
10. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie. Najnowsza wersja to 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Należy pamiętać, że zainstalowanie pakietu WALinuxAgent spowoduje usunięcie pakietów sieciowych i programu NetworkManager-GNOME, jeśli nie zostały one jeszcze usunięte zgodnie z opisem w kroku 2.
12. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 i nowsze
**Zmiany w Oracle Linux 7**

Przygotowywanie maszyny wirtualnej Oracle Linux 7 na platformie Azure jest bardzo podobne do Oracle Linux 6, jednak istnieje kilka ważnych różnic:

* Platforma Azure obsługuje Oracle Linux z nieprzerwanym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z systemem Red Hat. Zaleca się Oracle Linux z UEK.
* Pakiet programu NetworkManager nie jest już w konflikcie z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zalecamy, aby nie został usunięty.
* GRUB2 jest teraz używany jako domyślne program inicjujący, więc procedura edytowania parametrów jądra została zmieniona (patrz poniżej).
* XFS jest teraz domyślnym systemem plików. W razie potrzeby można nadal używać systemu plików ext4.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Utwórz plik o nazwie **Sieć** w katalogu `/etc/sysconfig/`, który zawiera następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Utwórz plik o nazwie **ifcfg-eth0** w katalogu `/etc/sysconfig/network-scripts/` zawierającym następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Upewnij się, że usługa sieciowa zacznie działać w czasie rozruchu, uruchamiając następujące polecenie:
   
        # sudo chkconfig network on
7. Zainstaluj pakiet Python-pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
8. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zainstalować wszystkie aktualizacje:
   
        # sudo yum clean all
        # sudo yum -y update
9. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/etc/default/grub" w edytorze tekstów i edytuj parametr `GRUB_CMDLINE_LINUX`, na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Spowoduje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Wyłącza również konwencje nazewnictwa dla kart sieciowych w Oracle Linux 7 z nieprzerwanym jądrem przedsiębiorstwa. Oprócz powyższych zaleca się *usunięcie* następujących parametrów:
   
       rhgb quiet crashkernel=auto
   
   Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej przez 128 MB lub więcej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.
10. Po zakończeniu edycji "/etc/default/grub" powyżej, uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
12. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do tworzenia nowych maszyn wirtualnych na platformie Azure za pomocą pliku Oracle Linux. VHD. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).

