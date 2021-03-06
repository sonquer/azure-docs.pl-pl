---
title: Wymagania systemowe urządzenia Microsoft Azure Data Box Disk | Microsoft Docs
description: Informacje na temat wymagań dotyczących oprogramowania i sieci dla urządzenia Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fb2fd89664517e44cf5128a5c82e583f03087061
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307696"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Wymagania systemowe urządzenia Azure Data Box Disk

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania Microsoft Azure Data Box Disk i klientów nawiązujących połączenie z urządzeniem Data Box Disk. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem usługi Data Box Disk, a następnie odwoływanie się do nich w miarę potrzeb podczas wdrażania i kolejnej operacji.

Wymagania systemowe obejmują obsługiwane platformy dla klientów łączących się z dyskami, obsługiwane konta magazynu i typy magazynów.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Przegląd wymagań wstępnych

1. Zamówienie urządzenia Data Box Disk przy użyciu [Samouczka: Zamawianie urządzenia Azure Data Box Disk](data-box-disk-deploy-ordered.md). Odebranie dysków i jednego kabla łączącego dla każdego dysku.
2. Masz dostępny komputer kliencki, z którego możesz skopiować dane. Na komputerze klienckim wymagane jest:

    - Korzystanie z obsługiwanego systemu operacyjnego.
    - Zainstalowanie innego wymaganego oprogramowania.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej znajduje się lista obsługiwanych systemów operacyjnych dla operacji odblokowywania dysków i kopiowania danych za pośrednictwem klientów połączonych z urządzeniem Data Box Disk.

| **System operacyjny** | **Przetestowane wersje** |
| --- | --- |
| Windows Server |2008 R2 z dodatkiem SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64-bitowy) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Inne wymagane oprogramowanie dla klientów z systemem Windows

W przypadku klienta z systemem Windows należy również zainstalować następujące oprogramowanie:

| **Oprogramowanie**| **Wersja** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Inne wymagane oprogramowanie dla klientów z systemem Linux

W przypadku klienta z systemem Linux zestaw narzędzi dla dysków Data Box Disk zainstaluje następujące wymagane oprogramowanie:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Obsługiwane połączenie

Komputer kliencki zawierający dane musi mieć port USB 3.0 lub nowszy. Dyski łączą się z tym klientem przy użyciu dostarczonego kabla.

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

Poniżej znajduje się lista obsługiwanych typów magazynów dla urządzenia Data Box Disk.

| **Konto magazynu** | **Uwagi** |
| --- | --- |
| Wdrożenie klasyczne | Standardowa (Standard) |
| Ogólnego przeznaczenia  |Standardowe; obsługiwane są wersje V1 i V2. Obsługiwane są warstwy Gorąca i Chłodna. |
| Konto magazynu obiektów blob | |

>[!NOTE]
> Konta usługi Azure Data Lake Storage Gen 2 nie są obsługiwane.


## <a name="supported-storage-types-for-upload"></a>Obsługiwane typy magazynu dla przekazywania

Poniżej znajduje się lista typów magazynu obsługiwanych na potrzeby przekazywania na platformę Azure przy użyciu urządzenia Data Box Disk.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Blokowy obiekt blob platformy Azure | |
| Stronicowy obiekt blob platformy Azure  | |
| Azure Files  | |
| Dyski zarządzane | |

::: zone target="docs"

## <a name="next-step"></a>Następny krok

* [Wdrażanie urządzenia Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

