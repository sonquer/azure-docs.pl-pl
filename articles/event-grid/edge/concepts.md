---
title: Pojęcia — IoT Edge Azure Event Grid | Microsoft Docs
description: Pojęcia dotyczące Event Grid w IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992550"
---
# <a name="event-grid-concepts"></a>Event Grid — pojęcia

W tym artykule opisano główne koncepcje w Azure Event Grid.

## <a name="events"></a>Wydarzenia

Wydarzenie jest najmniejszą ilością informacji, które w pełni opisują coś, co się stało w systemie. Każde zdarzenie ma typowe informacje, takie jak: Źródło zdarzenia, czas trwania zdarzenia i unikatowy identyfikator. Każde zdarzenie ma także określone informacje, które mają zastosowanie tylko do określonego typu zdarzenia. Obsługa zdarzeń o rozmiarze do 1 MB jest obecnie dostępna w wersji zapoznawczej.

Aby uzyskać właściwości, które są uwzględnione w zdarzeniu, zobacz [Azure Event Grid schemacie zdarzeń](event-schemas.md).

## <a name="publishers"></a>Wydawcy

Wydawca to użytkownik lub organizacja, która podejmuje decyzję o wysłaniu zdarzeń do Event Grid. Możesz publikować zdarzenia z własnej aplikacji.

## <a name="event-sources"></a>Źródła zdarzeń

Źródło zdarzenia to miejsce, w którym występuje zdarzenie. Każde źródło zdarzenia jest powiązane z co najmniej jednym typem zdarzenia. Na przykład usługa Azure Storage jest źródłem zdarzeń dla zdarzeń utworzonych przez obiekty blob. Aplikacja jest źródłem zdarzeń dla zdefiniowanych niestandardowych zdarzeń. Źródła zdarzeń są odpowiedzialne za wysyłanie zdarzeń do Event Grid.

## <a name="topics"></a>Tematy

Temat GridY zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatka zdarzeń i decyduje o tym, czy źródło zdarzenia wymaga jednego tematu, czy też więcej niż jednego tematu. Temat służy do zbierania powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy zasubskrybować.

Podczas projektowania aplikacji można określić, jak wiele tematów należy utworzyć. W przypadku dużych rozwiązań Utwórz niestandardowy temat dla każdej kategorii powiązanych zdarzeń. Rozważmy na przykład aplikację, która wysyła zdarzenia związane z modyfikowaniem kont użytkowników i przetwarzania zamówień. Jest mało prawdopodobne, że procedura obsługi zdarzeń potrzebuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe i pozwól obsłudze zdarzeń subskrybować te, które je interesują. W przypadku małych rozwiązań można chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą odfiltrować żądane typy zdarzeń.

Zobacz [dokumentację interfejsu API REST](api.md) na temat zarządzania tematami w programie Event Grid.

## <a name="event-subscriptions"></a>Subskrypcje zdarzeń

Subskrypcja informuje Event Grid, które zdarzenia zostały odebrane w temacie. Podczas tworzenia subskrypcji jest udostępniany punkt końcowy do obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. 

Zobacz [dokumentację interfejsu API REST](api.md) na temat zarządzania subskrypcjami w programie Event Grid.

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

W perspektywie Event Grid program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi wykonuje dalsze czynności, aby przetworzyć zdarzenie. Event Grid obsługuje kilka typów obsługi. Do obsługi programu można użyć obsługiwanej usługi platformy Azure lub własnego elementu webhook. W zależności od typu procedury obsługi Event Grid są zgodne z różnymi mechanizmami w celu zagwarantowania dostarczania zdarzenia. Jeśli program obsługi zdarzeń docelowych jest punktem zaczepienia sieci Web HTTP, zdarzenie jest ponawiane, gdy program obsługi zwróci kod stanu `200 – OK`. W przypadku centrum brzegowego, jeśli zdarzenie jest dostarczane bez żadnego wyjątku, zostanie uznane za pomyślne.

## <a name="security"></a>Zabezpieczenia

Event Grid zapewnia zabezpieczenia subskrybowania tematów i publikowania tematów. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).

## <a name="event-delivery"></a>Dostarczanie zdarzeń

Jeśli Event Grid nie może potwierdzić, że zdarzenie zostało odebrane przez punkt końcowy subskrybenta, ponownie dostarcza zdarzenie. Aby uzyskać więcej informacji, zobacz [Event Grid dostarczania komunikatów i ponów próbę](delivery-retry.md).

## <a name="batching"></a>Tworzenie partii

W przypadku korzystania z tematu niestandardowego zdarzenia muszą być zawsze publikowane w tablicy. W przypadku scenariuszy o niskiej przepływności tablica będzie zawierać tylko jedną wartość. W przypadku dużych przypadków użycia zaleca się wykonanie wielu zadań wsadowych w celu osiągnięcia wyższej wydajności. Liczba partii może wynosić do 1 MB. Każde zdarzenie nie powinno być większe niż 1 MB (wersja zapoznawcza).