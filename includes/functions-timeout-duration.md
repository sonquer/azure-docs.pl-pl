---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198330"
---
## <a name="timeout"></a>Czas trwania aplikacji funkcji 

Czas trwania aplikacji funkcji jest definiowany przez właściwość `functionTimeout` w pliku projektu [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . W poniższej tabeli przedstawiono wartości domyślne i maksymalne w minutach dla obu planów i różnych wersji środowiska uruchomieniowego:

| Planowanie | Wersja środowiska uruchomieniowego | Domyślne | Maksimum |
|------|---------|---------|---------|
| Zużycie | 1.x | 5 | 10 |
| Zużycie | 2.x | 5 | 10 |
| Zużycie | wersji | 5 | 10 |
| Premium | 1.x | 30 | Nieograniczona liczba |
| Premium | 2.x | 30 | Nieograniczona liczba |
| Premium | wersji | 30 | Nieograniczona liczba |
| App Service | 1.x | Nieograniczona liczba | Nieograniczona liczba |
| App Service | 2.x | 30 | Nieograniczona liczba |
| App Service | wersji | 30 | Nieograniczona liczba |

> [!NOTE] 
> Niezależnie od ustawienia limitu czasu aplikacji funkcji, 230 sekund to maksymalny czas, jaki może wykonać funkcja wyzwalana przez protokół HTTP w celu odpowiedzi na żądanie. Jest to spowodowane [domyślnym limitem czasu bezczynności wynoszącym Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Aby wydłużyć czas przetwarzania, rozważ użycie [wzorca Durable Functions Async](../articles/azure-functions/durable/durable-functions-overview.md#async-http) lub [odroczenie rzeczywistej pracy i zwrócenie natychmiastowej odpowiedzi](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
