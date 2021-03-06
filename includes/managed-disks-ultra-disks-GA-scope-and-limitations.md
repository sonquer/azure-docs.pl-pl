---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935891"
---
Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

- Są obsługiwane w następujących regionach o różnej liczbie stref dostępności na region:
    - Wschodnie stany USA 2
    - Wschodnie stany USA
    - Zachodnie stany USA 2
    - Azja Południowo-Wschodnia
    - Europa Północna
    - Europa Zachodnia
    - Południowe Zjednoczone Królestwo 
- Może być używany tylko z strefami dostępności (zbiory dostępności i pojedyncze wdrożenia maszyn wirtualnych poza strefami nie będą miały możliwości dołączenia Ultra Disk)
- Są obsługiwane tylko przez następującą serię maszyn wirtualnych:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny wirtualnej jest dostępny w każdym obsługiwanym regionie za pomocą Ultra disks
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4 KB. Ze względu na rozmiar sektora macierzystego 4K dla Ultra dysku istnieją aplikacje, które nie będą zgodne z Ultra Disks. Przykładem może być Oracle Database, który wymaga wersji 12,2 lub nowszej, aby można było obsługiwać Ultra Disks.  
- Można utworzyć tylko jako puste dyski  
- Nie obsługują jeszcze migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności i usługi Azure Disk Encryption
- Nie należy jeszcze obsługiwać integracji z programem Azure Backup lub Azure Site Recovery
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.
- Jeśli chcesz wziąć udział w ograniczonej wersji zapoznawczej maszyny wirtualnej, która może wykonywać 160 000 operacji we/wy na bardzo dyskach, Wyślij wiadomość e-mail UltraDiskFeedback@microsoft. com