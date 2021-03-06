---
title: Przekazywanie pliku VHD do Azure DevTest Labs przy użyciu programu PowerShell | Microsoft Docs
description: W tym artykule omówiono sposób przekazywania pliku VHD do Azure DevTest Labs przy użyciu programu PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759759"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu programu PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W Azure DevTest Labs pliki VHD mogą służyć do tworzenia obrazów niestandardowych, które są używane do obsługi maszyn wirtualnych. Poniższe kroki umożliwiają przekazanie pliku VHD do konta magazynu laboratorium przy użyciu programu PowerShell. Po przekazaniu pliku VHD [sekcja następne kroki](#next-steps) zawiera listę artykułów, które ilustrują, jak utworzyć obraz niestandardowy na podstawie przekazanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [wprowadzenie do usługi Managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przeprowadzą Cię przez proces przekazywania pliku VHD do Azure DevTest Labs przy użyciu programu PowerShell. 

1. Zaloguj się do [portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.  

1. W bloku laboratorium wybierz pozycję **Konfiguracja**. 

1. W bloku **Konfiguracja** laboratorium wybierz pozycję **obrazy niestandardowe (VHD)** .

1. W bloku **obrazy niestandardowe** wybierz pozycję **+ Dodaj**. 

1. W bloku **obrazu niestandardowego** wybierz pozycję **wirtualny dysk twardy**.

1. W bloku **wirtualnego dysku twardego** wybierz pozycję **Przekaż dysk VHD przy użyciu programu PowerShell**.

    ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. W bloku **przekazywanie obrazu przy użyciu programu PowerShell** Skopiuj wygenerowany skrypt programu PowerShell do edytora tekstu.

1. Zmodyfikuj parametr **LocalFilePath** polecenia cmdlet **Add-AzureVhd** , aby wskazywał lokalizację pliku VHD, który chcesz przekazać.

1. W wierszu polecenia programu PowerShell uruchom polecenie cmdlet **Add-AzureVhd** (z zmodyfikowanym parametrem **LocalFilePath** ).

> [!WARNING] 
> 
> Proces przekazywania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu Azure Portal](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
