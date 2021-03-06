---
title: Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure
description: Pobierz dysk VHD systemu Windows przy użyciu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940450"
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego (VHD) systemu Windows z platformy Azure przy użyciu Azure Portal.

## <a name="optional-generalize-the-vm"></a>Opcjonalne: Uogólnij maszynę wirtualną

Jeśli chcesz użyć wirtualnego dysku twardego jako [obrazu](tutorial-custom-images.md) do tworzenia innych maszyn wirtualnych, użyj programu [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) do uogólnienia systemu operacyjnego. 

Aby użyć wirtualnego dysku twardego jako obrazu do tworzenia innych maszyn wirtualnych, Uogólnij maszynę wirtualną.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. [Nawiąż połączenie z maszyną wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Na maszynie wirtualnej Otwórz okno wiersza polecenia jako administrator.
4. Zmień katalog na *%windir%\System32\Sysprep* i uruchom plik Sysprep. exe.
5. W oknie dialogowym Narzędzie przygotowywania systemu wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i upewnij się, że opcja **generalize** jest zaznaczona.
6. W opcje zamykania wybierz pozycję **Zamknij**, a następnie kliknij przycisk **OK**. 


## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Musisz zatrzymać maszynę wirtualną, aby pobrać dysk VHD. 

1. W menu centrum w Azure Portal kliknij pozycję **Virtual Machines**.
1. Z listy wybierz maszynę wirtualną.
1. W bloku maszyny wirtualnej kliknij pozycję **Zatrzymaj**.


## <a name="generate-download-url"></a>Generuj adres URL pobierania

Aby pobrać plik VHD, należy wygenerować adres URL [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po wygenerowaniu adresu URL zostanie do niego przypisany czas wygaśnięcia.

1. Na stronie maszyny wirtualnej kliknij pozycję **dyski** w menu po lewej stronie.
1. Wybierz dysk systemu operacyjnego dla maszyny wirtualnej.
1. Na stronie dysku wybierz pozycję **eksport dysku** z menu po lewej stronie.
1. Domyślny czas wygaśnięcia adresu URL to *3600* sekund. Zwiększ ten poziom do **36000** dla dysków systemu operacyjnego Windows.
1. Kliknij pozycję **Generuj adres URL**.

> [!NOTE]
> Czas wygaśnięcia zostanie zwiększony z ustawienia domyślnego, aby zapewnić wystarczającą ilość czasu na pobranie dużego pliku VHD dla systemu operacyjnego Windows Server. Można oczekiwać, że plik VHD zawierający system operacyjny Windows Server zostanie pobrany do kilku godzin w zależności od połączenia. Jeśli pobierasz dysk VHD dla dysku z danymi, wystarczy domyślny czas. 
> 
> 

## <a name="download-vhd"></a>Pobierz dysk VHD

1. W obszarze wygenerowanego adresu URL kliknij pozycję Pobierz plik VHD.
1. Może być konieczne kliknięcie przycisku **Zapisz** w przeglądarce, aby rozpocząć pobieranie. Domyślna nazwa pliku VHD jest *ABCD*.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać plik VHD na platformę Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Utwórz dyski zarządzane na podstawie dysków niezarządzanych na koncie magazynu](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Zarządzanie dyskami platformy Azure za pomocą programu PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

