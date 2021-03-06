---
title: Wysoka dostępność i niezawodność — usługa Azure Scheduler
description: Dowiedz się więcej o wysokiej dostępności i niezawodności w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 6a729df7eb08f8dacff4b0d35d011854208510ff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979284"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Wysoka dostępność i niezawodność usługi Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe. 

Usługa Azure Scheduler zapewnia [wysoką dostępność](https://docs.microsoft.com/azure/architecture/framework/#resiliency) i niezawodność zadań. Aby uzyskać więcej informacji, zobacz [Umowa SLA dla usługi Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Wysoka dostępność

Usługa Azure Scheduler to [Wysoka dostępność] i używa zarówno geograficznie nadmiarowego wdrożenia usługi, jak i geograficznej replikacji zadań.

### <a name="geo-redundant-service-deployment"></a>Wdrożenie usługi geograficznie nadmiarowej

Usługa Azure Scheduler jest dostępna w Azure Portal w niemal [każdym regionie geograficznym obecnie obsługiwanym przez platformę Azure](https://azure.microsoft.com/global-infrastructure/regions/#services). W związku z tym, jeśli centrum danych platformy Azure w hostowanym regionie stanie się niedostępne, nadal możesz korzystać z usługi Azure Scheduler, ponieważ możliwości pracy w trybie failover w usłudze udostępniają harmonogram z innego centrum danych.

### <a name="geo-regional-job-replication"></a>Geograficzna replikacja zadań

Twoje własne zadania w usłudze Azure Scheduler są replikowane w regionach platformy Azure. Jeśli więc w jednym regionie wystąpi awaria, usługa Azure Scheduler przejdzie w tryb failover i upewni się, że zadanie jest uruchamiane z innego centrum danych w sparowanym regionie geograficznym.

Jeśli na przykład utworzysz zadanie w regionie Południowo-środkowe stany USA, usługa Azure Scheduler automatycznie zreplikuje to zadanie w Północno-środkowe stany USA. Jeśli wystąpi awaria w Południowo-środkowe stany USA, usługa Azure Scheduler uruchamia to zadanie w Północno-środkowe stany USA. 

![Geograficzna replikacja zadań](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Usługa Azure Scheduler gwarantuje również, że dane pozostają w tym samym, ale szerszym regionie geograficznym, tylko w przypadku awarii na platformie Azure. W związku z tym nie trzeba duplikować zadań, jeśli chcesz uzyskać wysoką dostępność. Usługa Azure Scheduler automatycznie zapewnia wysoką dostępność dla zadań.

## <a name="reliability"></a>Niezawodność

Usługa Azure Scheduler gwarantuje własną wysoką dostępność, ale ma inne podejście do zadań tworzonych przez użytkownika. Załóżmy na przykład, że zadanie wywołuje punkt końcowy HTTP, który jest niedostępny. Usługa Azure Scheduler nadal próbuje uruchomić zadanie pomyślnie, oferując alternatywne sposoby obsługi niepowodzeń: 

* Skonfiguruj zasady ponawiania.
* Skonfiguruj alternatywne punkty końcowe.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Zasady ponawiania prób

Usługa Azure Scheduler umożliwia skonfigurowanie zasad ponawiania prób. Jeśli zadanie nie powiedzie się, domyślnie usługa Scheduler ponawia próbę wykonania zadania cztery razy w odstępach 30-sekundowych. Te zasady ponawiania można bardziej agresywnie, na przykład 10 razy w odstępach 30-sekundowych lub mniej agresywnych, takich jak dwa razy w dziennych odstępach czasu.

Załóżmy na przykład, że tworzysz cotygodniowe zadanie, które wywołuje punkt końcowy HTTP. Jeśli punkt końcowy HTTP stanie się niedostępny przez kilka godzin, gdy zadanie zostanie uruchomione, możesz nie chcieć czekać w innym tygodniu, co dzieje się, ponieważ domyślne zasady ponawiania nie będą działać w tym przypadku. W związku z tym możesz chcieć zmienić standardowe zasady ponawiania, aby ponowić próbę, na przykład co trzy godziny, a nie co 30 sekund. 

Aby dowiedzieć się, jak skonfigurować zasady ponawiania, zobacz [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternatywne punkty końcowe

Jeśli zadanie usługi Azure Scheduler wywoła punkt końcowy, który jest nieosiągalny, nawet po ponownym uruchomieniu zasad, usługa Scheduler powróci do alternatywnego punktu końcowego, który może obsłużyć takie błędy. Dlatego w przypadku skonfigurowania tego punktu końcowego usługa Scheduler wywołuje ten punkt końcowy, co sprawia, że własne zadania mają wysoką dostępność w przypadku wystąpienia awarii.

Na przykład na tym diagramie przedstawiono, jak harmonogram postępuje zgodnie z zasadami ponawiania podczas wywoływania usługi sieci Web w Nowym Jorku. Jeśli ponowna próba nie powiedzie się, usługa Scheduler sprawdza alternatywny punkt końcowy. Jeśli punkt końcowy istnieje, usługa Scheduler zacznie wysyłać żądania do alternatywnego punktu końcowego. Te same zasady ponawiania odnoszą się do oryginalnej akcji i alternatywnej akcji.

![Zachowanie usługi Scheduler z zasadami ponawiania i alternatywnym punktem końcowym](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Typ akcji dla akcji alternatywnej może się różnić od oryginalnej akcji. Na przykład mimo że oryginalna akcja wywołuje punkt końcowy HTTP, alternatywna akcja może rejestrować błędy przy użyciu kolejki magazynu, kolejki Service Bus lub akcji tematu Service Bus.

Aby dowiedzieć się, jak skonfigurować alternatywny punkt końcowy, zobacz [ErrorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia, terminologia oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Tworzenie złożonych i zaawansowanych harmonogramów cyklicznych](scheduler-advanced-complexity.md)
* [Limity, limity przydziału, wartości domyślne i kody błędów](scheduler-limits-defaults-errors.md)
