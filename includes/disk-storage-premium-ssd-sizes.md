---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837565"
---
| Rozmiary SSD w warstwie Premium | P1 | P2 | Przeznaczone | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Rozmiar dysku w GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2 048 | 4 096 | 8192 | 16 384 | 32,767 |
| Liczba operacji we/wy na sekundę na dysk | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2300 | 5000 | 7500 | 7500 | 16,000 | 18 000 | 20,000 |
| Przepływność na dysk | 25 MiB/s | 25 MiB/s | 25 MiB/s | 25 MiB/s | 50 MiB/s | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s| 500 MiB/s | 750 MiB/s | 900 MiB/s |
| Maksymalna liczba operacji we/wy na sekundę na dysk * * | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Maksymalna przepływność serii na dysk * * | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s |
| Maksymalny czas trwania szeregu * * | 30 minut  | 30 minut  | 30 minut  | 30 minut  | 30 minut  | 30 minut  | 30 minut  | 30 minut  |
| Kwalifikujące się do rezerwacji | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku |

\*wskazuje rozmiar dysku, który jest obecnie dostępny w wersji zapoznawczej, aby uzyskać informacje o dostępności regionalnej, zobacz [nowe rozmiary dysków: zarządzane i niezarządzane](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*oznacza funkcję, która jest obecnie dostępna w wersji zapoznawczej, aby uzyskać więcej informacji, zobacz artykuł dotyczący obciążeń [dysków](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) .
