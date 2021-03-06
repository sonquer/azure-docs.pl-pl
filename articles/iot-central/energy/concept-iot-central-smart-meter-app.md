---
title: Koncepcje dotyczące architektury w usłudze Azure IoT Central — energia | Microsoft Docs
description: W tym artykule przedstawiono najważniejsze pojęcia związane z architekturą szablonu aplikacji energetycznej usługi Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024317"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central — architektura aplikacji licznika inteligentnego



Ten artykuł zawiera omówienie architektury szablonu aplikacji do monitorowania mierników inteligentnych. Na poniższym diagramie przedstawiono powszechnie używaną architekturę dla inteligentnego miernika aplikacji na platformie Azure przy użyciu platformy IoT Central.

> [!div class="mx-imgBorder"]
> ![architektury mierników inteligentnych](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Niniejsza architektura zawiera następujące składniki. Niektóre rozwiązania mogą nie wymagać każdego składnika wymienionego w tym miejscu.

## <a name="smart-meters-and-connectivity"></a>Inteligentne liczniki i łączność 

Inteligentny licznik jest jednym z najważniejszych urządzeń we wszystkich zasobach energii. Rejestruje i przekazuje dane dotyczące zużycia energii do narzędzi do monitorowania i innych przypadków użycia, takich jak rozliczenia i reagowanie na żądania. Na podstawie typu licznika może połączyć się z IoT Central przy użyciu bram lub innych urządzeń lub systemów pośrednich, takich jak urządzenia brzegowe i systemy główne. Kompiluj IoT Central mostek urządzeń, aby połączyć urządzenia, które nie mogą być połączone bezpośrednio. IoT Central mostka urządzenia to rozwiązanie Open Source, w [tym miejscu](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)można znaleźć pełne szczegóły. 


## <a name="iot-central-platform"></a>IoT Central platformę

Azure IoT Central to platforma, która upraszcza tworzenie rozwiązania IoT oraz zmniejsza obciążenie i koszty związane z zarządzaniem IoT, operacjami i programowaniem. Dzięki IoT Central można łatwo łączyć i monitorować zasoby Internet rzeczy (IoT) oraz zarządzać nimi na dużą skalę. Po połączeniu inteligentnych mierników do IoT Central szablon aplikacji będzie używać wbudowanych funkcji, takich jak modele urządzeń, polecenia i pulpity nawigacyjne. Szablon aplikacji korzysta również z IoT Central magazynu dla scenariuszy ścieżki ciepłej, takich jak monitorowanie danych, analiza, reguły i wizualizacja w czasie rzeczywistym. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Opcje rozszerzalności do skompilowania przy użyciu IoT Central
Platforma IoT Central udostępnia dwie opcje rozszerzalności: ciągły eksport danych (CDE) i interfejsy API. Klienci i partnerzy mogą wybrać jedną z tych opcji, aby dostosować swoje rozwiązania do konkretnych potrzeb. Na przykład jeden z naszych partnerów skonfigurował CDE z Azure Data Lake Storage (ADLS). Korzystają one z ADLS do długoterminowego przechowywania danych i innych scenariuszy magazynu zimnej ścieżki, takich jak przetwarzanie wsadowe, Inspekcja i raportowanie. 

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy już znasz architekturę, [Utwórz za darmo aplikację z miernikiem inteligentnym](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)
