---
title: Obciążenia firmy IBM na platformie Azure | Microsoft Docs
description: Użyj emulatora mainframe i innych usług firmy Microsoft Partners do przetestowania obciążeń firmy IBM z/OS przy użyciu Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834587"
---
# <a name="ibm-workloads-on-azure"></a>Obciążenia firmy IBM na platformie Azure

Wiele obciążeń firmy IBM mainframe opartych na systemie z/OS może być replikowanych na platformie Azure bez utraty funkcjonalności, a użytkownicy nawet obserwowanie zmiany w ich podstawowych systemach. Ponowne hostowanie aplikacji na platformie Azure zapewnia funkcje podobne do komputera mainframe oraz elastyczność, dostępność oraz potencjalne oszczędności w chmurze.

Platforma Azure obsługuje integrację z istniejącymi środowiskami firmy IBM mainframe, umożliwiając Migrowanie applicates, które mają sens, uruchamianie rozwiązań hybrydowych, a następnie Migrowanie w czasie. Mimo że można całkowicie ponownie napisać istniejące programy oparte na programie mainframe dla platformy Azure, jest to bardziej powszechne, aby ponownie je hostować. Ponowne zapisywanie powoduje dodanie kosztów, złożoności i czasu do projektów migracji. W przypadku rehostowania można:

- Przenoszenie aplikacji do emulatora opartego na chmurze.

- Przeprowadź migrację bazy danych do bazy danych opartej na chmurze.

- Zastąp moduły i kod przy użyciu aparatów transformacji kodu.

Ponadto oprogramowanie firmy IBM, w tym WebSphere i MQ, znajduje się teraz w portalu Azure Marketplace. Posiadając licencję na oprogramowanie firmy IBM, możesz wykorzystać możliwości skalowania infrastruktury na żądanie, które platforma Azure udostępnia, aby umożliwić szybkie uruchomienie maszyny wirtualnej.

Dostępny jest obszerny ekosystem partnerów, który ułatwia Migrowanie systemów mainframe firmy IBM do platformy Azure. Z tego względu należy stosować metodę pragmatyczną, gdy jest to możliwe, przed rozpoczęciem wdrażania etapowego ponownego zapisywania lub zamiany aplikacji. Uzyskaj więcej wskazówek i pomoc od partnerów w [centrum migracji systemu mainframe platformy Azure](https://azure.microsoft.com/migration/mainframe/).

**Następne kroki**

- [Migracja komputera mainframe: mitów i fakty](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Zainstaluj środowisko IBM zD & w środowisku deweloperskim/testowym na platformie Azure](./install-ibm-z-environment.md)
- [Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD & T v1](./demo.md)
- [IBM DB2 pureScale na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
