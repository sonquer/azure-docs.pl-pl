---
title: Zdarzenie rozpoczęcia zmiany rozmiaru puli Azure Batch
description: Odwołanie do zdarzenia rozpoczęcia zmiany rozmiaru puli usługi Batch. Przykład pokazuje treść puli zmiany rozmiaru uruchomienia dla puli zmiana rozmiaru z 0 do 2 węzłów z ręczną zmianę rozmiaru.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023586"
---
# <a name="pool-resize-start-event"></a>Zdarzenie rozpoczęcia zmiany rozmiaru puli

 To zdarzenie jest emitowane po rozpoczęciu zmiany rozmiaru puli. Ponieważ zmiana rozmiaru puli jest zdarzeniem asynchronicznym, można oczekiwać, że zdarzenie ukończenia zmiany rozmiaru puli będzie emitowane po zakończeniu operacji zmiany rozmiaru.

 W poniższym przykładzie pokazano treść puli zmiana rozmiaru zdarzenia uruchomienia dla puli zmiana rozmiaru z 0 na 2 węzły z ręczną zmianę rozmiaru.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`nodeDeallocationOption`|Ciąg|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|`currentDedicatedNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetDedicatedNodes`|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych aktualnie przypisanych do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych, które są żądane dla puli.|
|`enableAutoScale`|Bool|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|`isAutoPool`|Bool|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
