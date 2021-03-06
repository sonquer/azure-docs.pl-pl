---
title: Pulpit zdalny rozłącza często z maszyną wirtualną platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z częstymi połączeniami Pulpit zdalny na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 636973110e11770e33c635e312c86b25110705da
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981349"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Pulpit zdalny rozłącza często z maszyną wirtualną platformy Azure

W tym artykule wyjaśniono, jak rozwiązywać problemy z częstymi połączeniami z maszyną wirtualną platformy Azure za pomocą protokołu Remote Desktop Protocol RDP).

> [!NOTE] 
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano użycie Menedżer zasobów model wdrażania. Zalecamy używanie tego modelu w przypadku nowych wdrożeń zamiast korzystania z klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw

Podczas sesji są używane sporadyczne problemy z łącznością RDP. Możesz na początku połączyć się z maszyną wirtualną, ale połączenie zostanie porzucane.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli odbiornik RDP jest niepoprawnie skonfigurowany. Zazwyczaj ten problem występuje w przypadku maszyny wirtualnej, która używa obrazu niestandardowego.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy [wykonać migawkę dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) , którego dotyczy dana maszyna wirtualna, jako kopii zapasowej. 

Aby rozwiązać ten problem, należy użyć kontrolki serial lub [naprawić maszynę wirtualną w trybie offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny wirtualnej odzyskiwania.

### <a name="serial-control"></a>Kontrolka szeregowa

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md). Następnie uruchom następujące polecenia, aby zresetować konfiguracje protokołu RDP. Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do następnego kroku.
2. Obniż poziom warstwy zabezpieczeń protokołu RDP do wartości 0. W przypadku tego ustawienia komunikacja między serwerem a klientem jest używana przez natywne szyfrowanie protokołu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Obniż poziom szyfrowania do minimalnego ustawienia, aby umożliwić nawiązywanie połączeń ze starszymi klientami RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Ustaw protokół RDP, aby załadować konfigurację użytkownika komputera klienckiego.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Włącz kontrolkę Keep-Alive protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Ustaw kontrolkę reconnect RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Ustaw kontrolę czasu sesji RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Ustaw kontrolę czasu odłączenia RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Ustaw kontrolę czasu połączenia RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Ustaw kontrolę czasu bezczynności sesji RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Ustaw kontrolkę "Ogranicz maksymalną liczbę równoczesnych połączeń":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Uruchom ponownie maszynę wirtualną, a następnie spróbuj ponownie nawiązać połączenie za pomocą protokołu RDP.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Po dołączeniu dysku systemu operacyjnego do maszyny Wirtualnej odzyskiwania, upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
3. Na dołączonym dysku systemu operacyjnego przejdź do folderu **\Windows\System32\Config** . Skopiuj wszystkie pliki w tym folderze jako kopię zapasową, w przypadku gdy wymagane jest wycofanie.
4. Uruchom Edytor rejestru (regedit. exe).
5. Wybierz klucz **HKEY_LOCAL_MACHINE** . W menu wybierz kolejno pozycje **plik** > **ładowanie Hive**:
6. Przejdź do folderu **\windows\system32\config\SYSTEM** na dysku systemu operacyjnego, który został podłączony. W polu Nazwa gałęzi wpisz **BROKENSYSTEM**. W kluczu **HKEY_LOCAL_MACHINE** zostanie wyświetlona nowa gałąź rejestru. Następnie załaduj **\windows\system32\config\SOFTWARE** Hive oprogramowania w kluczu **HKEY_LOCAL_MACHINE** . W polu Nazwa oprogramowania Hive wprowadź **BROKENSOFTWARE**. 
7. Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**) i uruchom polecenia w pozostałych krokach, aby zresetować konfiguracje protokołu RDP. 
8. Obniż poziom warstwy zabezpieczeń protokołu RDP do wartości 0, aby komunikacja między serwerem a klientem była używana przez natywne szyfrowanie RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Obniż poziom szyfrowania do minimalnego ustawienia, aby umożliwić nawiązywanie połączeń ze starszymi klientami RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Ustaw protokół RDP, aby załadować konfigurację użytkownika komputera klienckiego.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Włącz kontrolkę Keep-Alive protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Ustaw kontrolkę reconnect RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Ustaw kontrolę czasu sesji RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Ustaw kontrolę czasu odłączenia RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Ustaw kontrolę czasu połączenia RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Ustaw kontrolę czasu bezczynności sesji RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp"/v "fInheritMaxIdleTime"/t REG_DWORD/d 1/f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Ustaw kontrolkę "Ogranicz maksymalną liczbę równoczesnych połączeń":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Uruchom ponownie maszynę wirtualną, a następnie spróbuj ponownie nawiązać połączenie za pomocą protokołu RDP.

## <a name="need-help"></a>Potrzebujesz pomocy? 
Skontaktuj się z pomocą techniczną. Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.





