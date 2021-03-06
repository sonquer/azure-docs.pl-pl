---
title: Video Indexer ciągłość działania i odzyskiwanie po awarii — Azure
description: Dowiedz się, jak przełączyć się w tryb failover na pomocnicze konto Video Indexer, jeśli wystąpi awaria lub niepowodzenie centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 2f54c340226a9ea78643df8e0a984c8ed8475c94
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513579"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Obsłuż Video Indexer ciągłość działania i odzyskiwanie po awarii

Usługa Azure Media Services Video Indexer nie zapewnia błyskawicznej pracy w trybie failover w przypadku awarii lub awarii centrum danych. W tym artykule wyjaśniono, jak skonfigurować środowisko do pracy w trybie failover w celu zapewnienia optymalnej dostępności aplikacji i zminimalizowanego czasu odzyskiwania w przypadku wystąpienia awarii.

Zalecamy skonfigurowanie odzyskiwania po awarii (BCDR) w różnych regionach regionalnych, aby korzystać z zasad izolacji i dostępności platformy Azure. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Wymagania wstępne 

Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Przełączenie w tryb failover na konto pomocnicze

Aby wdrożyć BCDR, musisz mieć dwa konta Video Indexer, aby obsłużyć nadmiarowość.

1. Utwórz dwa konta Video Indexer połączone z platformą Azure (zobacz [Tworzenie kont](connect-to-azure.md)). Jeden dla regionu podstawowego i drugi dla sparowanego regionu platformy Azure. 
1. Jeśli w regionie podstawowym wystąpi błąd, przełącz się do indeksowania przy użyciu konta pomocniczego.

> [!TIP]
> Możesz zautomatyzować BCDR przez skonfigurowanie alertów dziennika aktywności dla powiadomień o kondycji usługi, jak na potrzeby [tworzenia alertów dziennika aktywności w powiadomieniach usługi](../../service-health/alerts-activity-log-service-notifications.md).

Aby uzyskać informacje o używaniu wielu dzierżawców, zobacz [Zarządzanie wieloma dzierżawcami](manage-multiple-tenants.md). Aby zaimplementować BCDR, wybierz jedną z następujących opcji: [Video Indexer konto na dzierżawcę](manage-multiple-tenants.md#video-indexer-account-per-tenant) lub [subskrypcję platformy Azure dla dzierżawy](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Następne kroki

[Zarządzanie kontem Video Indexer połączonym z platformą Azure](manage-account-connected-to-azure.md).
