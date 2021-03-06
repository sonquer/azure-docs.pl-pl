---
title: Skonfiguruj laboratorium działanie hakerskie etyczne z Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium przy użyciu Azure Lab Services do uczenia etycznych działanie hakerskie.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133179"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Konfigurowanie laboratorium do uczenia się klasy działanie hakerskie etycznych 
W tym artykule opisano sposób konfigurowania klasy, która koncentruje się na dowodowych etycznej działanie hakerskie. Testowanie penetracji, metoda stosowana przez społeczność etyczną działanie hakerskie, występuje, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci w celu zademonstrowania luk w zabezpieczeniach. 

W klasie etycznej działanie hakerskie studenci mogą uczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux](https://www.kali.org/) . Maszyna wirtualna Metasploitable jest używana na potrzeby wykorzystywania i Kali maszyny wirtualnej zapewnia dostęp do narzędzi wymaganych do wykonywania zadań śledczej.

Ten artykuł zawiera dwa główne sekcje. W pierwszej sekcji opisano, jak utworzyć laboratorium klasowe. Druga sekcja zawiera informacje na temat tworzenia maszyny szablonu z włączoną funkcją wirtualizacji zagnieżdżonej oraz wymaganych narzędzi i obrazów. W takim przypadku obraz Metasploitable i obraz systemu Linux w systemie Kali na maszynie z włączoną funkcją Hyper-V do obsługi obrazów.

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta. Zapoznaj się z poniższym samouczkiem dotyczącym tworzenia nowego konta laboratorium: [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).

Postępuj zgodnie z [tym samouczkiem](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium, a następnie Zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Image (Obraz) |
| -------------------- | ----- | 
| Średni (Wirtualizacja zagnieżdżona) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Komputer szablonu 

Po utworzeniu komputera szablonu Uruchom maszynę i Połącz się z nią, aby wykonać trzy następujące zadania. 
 
1. Skonfiguruj maszynę do wirtualizacji zagnieżdżonej. Umożliwia ona dostęp do wszystkich odpowiednich funkcji systemu Windows, takich jak funkcja Hyper-V, i konfiguruje sieci dla obrazów funkcji Hyper-V, które będą mogły komunikować się ze sobą i z Internetem.
2. Skonfiguruj obraz [Kali](https://www.kali.org/) systemu Linux. Kali to dystrybucja systemu Linux obejmująca narzędzia do testowania i inspekcji zabezpieczeń.
3. Skonfiguruj obraz Metasploitable. Na potrzeby tego przykładu zostanie użyty obraz [Metasploitable3](https://github.com/rapid7/metasploitable3) . Ten obraz jest tworzony w celu uzyskania luk w zabezpieczeniach.

Skrypt, który automatyzuje zadania opisane powyżej, jest dostępny w przypadku [skryptów działanie hakerskie etycznych usług laboratoryjnych](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Przygotuj komputer szablonu do wirtualizacji zagnieżdżonej
Postępuj zgodnie z instrukcjami w [tym artykule](how-to-enable-nested-virtualization-template-vm.md) , aby przygotować maszynę wirtualną szablonu do wirtualizacji zagnieżdżonej. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Skonfiguruj zagnieżdżoną maszynę wirtualną przy użyciu obrazu Kali systemu Linux
Kali to dystrybucja systemu Linux obejmująca narzędzia do testowania i inspekcji zabezpieczeń.

1. Pobierz obraz z [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  
    1. Pobierz **Bit 64 dla funkcji Hyper-v Kali Linux** dla funkcji Hyper-v.
    1. Wyodrębnij plik. 7z.  Jeśli nie masz jeszcze 7 pliku zip, Pobierz go z [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). Zapamiętaj lokalizację wyodrębnionego folderu, ponieważ będzie on potrzebny później.
2. Otwórz **Menedżera funkcji Hyper-V** z poziomu narzędzi administracyjnych.
1. Wybierz pozycję **Akcja**, a następnie wybierz pozycję **Importuj maszynę wirtualną**. 
1. Na stronie **lokalizowanie folderu** kreatora **importu maszyny wirtualnej** wybierz lokalizację wyodrębnionego folderu, który zawiera obraz Kali systemu Linux.

    ![Okno dialogowe lokalizowania folderu](../media/class-type-ethical-hacking/locate-folder.png)
1. Na stronie **Wybierz maszynę wirtualną** wybierz obraz Kali systemu Linux.  W takim przypadku obraz jest **Kali-Linux-2019,3-HyperV**.

    ![Wybierz obraz Kali](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Na stronie **Wybierz typ importu** wybierz opcję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)** .

    ![Wybierz typ importu](../media/class-type-ethical-hacking/choose-import-type.png)
1. Zaakceptuj ustawienia domyślne na stronie **Wybieranie folderów dla plików maszyny wirtualnej** i **Wybierz foldery do przechowywania stron wirtualnych dysków twardych** , a następnie wybierz przycisk **dalej**.
1. Na stronie **Połącz sieć** wybierz pozycję **LabServicesSwitch** utworzone wcześniej w sekcji **przygotowanie szablonu do wirtualizacji zagnieżdżonej** w tym artykule, a następnie wybierz przycisk **dalej**.

    ![Strona łączenia sieci](../media/class-type-ethical-hacking/connect-network.png)
1. Na stronie **Podsumowanie** wybierz pozycję **Zakończ** . Zaczekaj na zakończenie operacji kopiowania i importowania. Maszyna wirtualna z systemem Kali Linux będzie teraz dostępna w funkcji Hyper-V.
1. W **Menedżerze funkcji Hyper-V**wybierz pozycję **Akcja** -> **Start**, a następnie wybierz pozycję **Akcja** -> **Połącz** się z maszyną wirtualną.  
12. Domyślna nazwa użytkownika to `root`, a hasło jest `toor`. 

    > [!NOTE]
    > Jeśli zachodzi potrzeba odblokowania obrazu, naciśnij klawisz CTRL i przeciągnij myszą w górę.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Skonfiguruj zagnieżdżoną maszynę wirtualną przy użyciu obrazu Metasploitable  
Obraz Rapid7 Metasploitable to obraz skonfigurowany do celów z lukami w zabezpieczeniach. Ten obraz będzie używany do testowania i znajdowania problemów. Poniższe instrukcje przedstawiają sposób użycia wstępnie utworzonego obrazu Metasploitable. Jeśli jednak jest wymagana nowsza wersja obrazu Metasploitable, zobacz [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Przejdź do strony [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Wypełnij formularz, aby pobrać obraz, a następnie wybierz przycisk **Prześlij** .
1. Wybierz przycisk **Pobierz Metasploitable teraz** .
1. Po pobraniu pliku zip Wyodrębnij plik zip i Zapamiętaj lokalizację.
1. Przekonwertuj wyodrębniony plik VMDK na plik VHDX, aby można było używać go z funkcją Hyper-V. Aby to zrobić, Otwórz program PowerShell z uprawnieniami administracyjnymi i przejdź do folderu, w którym znajduje się plik VMDK, i wykonaj następujące instrukcje:
    1. Pobierz [konwertera maszyn wirtualnych firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=42497)i uruchom plik mvmc_setup. msi po wyświetleniu monitu.
    1. Zaimportuj moduł programu PowerShell.  Domyślna lokalizacja, w której zainstalowano moduł, to C:\Program Files\Microsoft Virtual Machine Converter \

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Przekonwertuj dysk VMDK na plik VHD, który może być używany przez funkcję Hyper-V. Ta operacja może potrwać kilka minut.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Skopiuj nowo utworzony plik metasploitable. vhdx do C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Utwórz nową maszynę wirtualną funkcji Hyper-V.
    1. Otwórz **Menedżera funkcji Hyper-V**.
    1. Wybierz **akcję** -> **nową** -> ą **maszynę wirtualną**.
    1. Na stronie **zanim rozpoczniesz** **Kreatora nowej maszyny wirtualnej**kliknij przycisk **dalej**.
    1. Na stronie **Określanie nazwy i lokalizacji** wprowadź **Metasploitable** jako **nazwę**, a następnie wybierz przycisk **dalej**.

        ![Kreator nowego obrazu maszyny wirtualnej](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na stronie **Określanie generacji** zaakceptuj wartości domyślne, a następnie wybierz przycisk **dalej**.
    1. Na stronie **przypisywanie pamięci** wprowadź **512 MB** dla **pamięci początkowej**, a następnie wybierz przycisk **dalej**. 

        ![Strona przypisywanie pamięci](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na stronie **Konfigurowanie sieci** pozostaw połączenie jako **niepołączone**. Później skonfigurujesz kartę sieciową.
    1. Na stronie **Podłączanie wirtualnego dysku twardego** wybierz opcję **Użyj istniejącego wirtualnego dysku twardego**. Przejdź do lokalizacji pliku **metasploitable. vhdx** , który został utworzony w poprzednim kroku, a następnie wybierz przycisk **dalej**. 

        ![Strona łączenie dysku sieci wirtualnej](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na stronie **Kończenie pracy Kreatora nowej maszyny wirtualnej** , a następnie wybierz pozycję **Zakończ**.
    1. Po utworzeniu maszyny wirtualnej wybierz ją w Menedżerze funkcji Hyper-V. Nie włączaj jeszcze maszyny.  
    1. Wybierz pozycję **akcja** -> **Ustawienia**.
    1. W oknie dialogowym **Ustawienia dla Metasploitable** wybierz pozycję **Dodaj sprzęt**. 
    1. Wybierz opcję **Starsza karta sieciowa**, a następnie wybierz pozycję **Dodaj**.

        ![Strona karty sieciowej](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na **starszej karcie sieciowej** wybierz opcję **LabServicesSwitch** dla ustawienia **przełącznika wirtualnego** , a następnie wybierz **przycisk OK**. LabServicesSwitch został utworzony podczas przygotowywania maszyny szablonu dla funkcji Hyper-V w sekcji **Prepare szablonu dla zagnieżdżonej wirtualizacji** .

        ![Strona starszej karty sieciowej](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Obraz Metasploitable jest teraz gotowy do użycia. W **Menedżerze funkcji Hyper-V**wybierz pozycję **Akcja** -> **Start**, a następnie wybierz pozycję **Akcja** -> **Połącz** się z maszyną wirtualną.  Domyślna nazwa użytkownika to **msfadmin** , a hasło to **msfadmin**. 


Szablon jest teraz aktualizowany i zawiera obrazy potrzebne do testowania działanie hakerskiej penetracji etycznej, obrazu z narzędziami do testowania penetracji i innego obrazu z lukami w zabezpieczeniach do odnalezienia. Obraz szablonu można teraz opublikować w klasie. Wybierz przycisk **Publikuj** na stronie szablon, aby opublikować szablon w laboratorium.
  

## <a name="cost"></a>Koszty  
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu: 
 
W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów, Cena za laboratorium to: 

25 studentów * (20 + 10) godz. * 55 jednostek laboratoryjnych * 0,01 USD za godzinę = 412,50 USD. 

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono procedurę tworzenia laboratorium dla klasy działanie hakerskie etycznej. Obejmuje to kroki konfigurowania zagnieżdżonej wirtualizacji na potrzeby tworzenia dwóch maszyn wirtualnych w ramach maszyny wirtualnej hosta w celu przetestowania.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne do konfigurowania dowolnego laboratorium:

- [Dodawanie użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users). 

