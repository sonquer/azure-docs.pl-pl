---
title: Usuń projekt Azure Migrate
description: Opisuje sposób tworzenia projektu Azure Migrate i dodawania narzędzia do oceny/migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512732"
---
# <a name="delete-an-azure-migrate-project"></a>Usuń projekt Azure Migrate

W tym artykule opisano sposób usuwania projektu [Azure Migrate](migrate-overview.md) .


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed usunięciem projektu:

- Po usunięciu projektu, projektu i odnalezionych metadanych maszyn są usuwane.
- Jeśli dołączono obszar roboczy Log Analytics do narzędzia do oceny zależności serwera, należy zdecydować, czy chcesz usunąć obszar roboczy. 
    - Obszar roboczy nie jest automatycznie usuwany. Usuń je ręcznie.
    - Przed usunięciem należy sprawdzić, do czego służy obszar roboczy. Ten sam obszar roboczy Log Analytics może być używany w wielu scenariuszach.
    - Przed usunięciem projektu można znaleźć link do obszaru roboczego w obszarze **Azure Migrate — serwery** > **oceny Azure Migrate serwera**, w obszarze **roboczym pakietu OMS**.
    - Aby usunąć obszar roboczy po usunięciu projektu, Znajdź obszar roboczy w odpowiedniej grupie zasobów i postępuj zgodnie z [tymi instrukcjami](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Usuwanie projektu


1. W Azure Portal Otwórz grupę zasobów, w której został utworzony projekt.
2. Na stronie Grupa zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt i skojarzone zasoby, które chcesz usunąć.
    - Typ zasobu dla projektów Azure Migrate to **Microsoft. migruje/migrateprojects**.
    - W następnej sekcji zapoznaj się z zasobami utworzonymi na potrzeby odnajdywania, oceny i migracji w projekcie Azure Migrate.
    - Jeśli grupa zasobów zawiera tylko projekt Azure Migrate, można usunąć całą grupę zasobów.
    - Jeśli chcesz usunąć projekt z poprzedniej wersji Azure Migrate, kroki są takie same. Typ zasobu dla tych projektów to **projekt migracji**.


## <a name="created-resources"></a>Utworzone zasoby

Te tabele zawierają podsumowanie zasobów utworzonych na potrzeby odnajdywania, oceny i migracji w projekcie Azure Migrate.

> [!NOTE]
> Usuń Magazyn kluczy z zachowaniem ostrożności, ponieważ może on zawierać klucze zabezpieczeń.

### <a name="vmwarephysical-server"></a>VMware/serwer fizyczny

**Zasób** | **Typ**
--- | ---
"Urządzeniename" KV | Magazyn kluczy
Lokacja "urządzeniename" | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. migrowana/migrateprojects
Projekt "ProjectName" | Microsoft. migrowana/assessmentProjects
"ProjectName" rsvault | Magazyn usługi Recovery Services
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services
migrateappligwsa* | Konto magazynu
migrateapplilsa* | Konto magazynu
migrateapplicsa* | Konto magazynu
migrateapplikv* | Magazyn kluczy
migrateapplisbns16041 | Przestrzeń nazw usługi Service Bus

### <a name="hyper-v-vm"></a>Maszyna wirtualna funkcji Hyper-V 

**Zasób** | **Typ**
--- | ---
ProjectName | Microsoft. migrowana/migrateprojects
Projekt "ProjectName" | Microsoft. migrowana/assessmentProjects
HyperV * KV | Magazyn kluczy
Lokacja funkcji Hyper * | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dodatkowe narzędzia do [oceny](how-to-assess.md) i [migracji](how-to-migrate.md) . 
