---
title: Funkcje w wersji zapoznawczej usługa Azure Stream Analytics
description: W tym artykule wymieniono funkcje usługi Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 2/1/2020
ms.openlocfilehash: aaff56ba1de69485d1c3b93bc7ed95ce1a3cbd88
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983553"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej usługa Azure Stream Analytics

Ten artykuł zawiera podsumowanie wszystkich funkcji, obecnie w wersji zapoznawczej usługi Azure Stream Analytics. Korzystanie z funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są w publicznej wersji zapoznawczej. Można korzystać z tych funkcji już dziś, ale nie są używane w środowisku produkcyjnym.

### <a name="online-scaling"></a>Skalowanie online

Skalowanie online nie wymaga zatrzymania zadania, jeśli trzeba zmienić alokację SU. Można zwiększyć lub zmniejszyć pojemność SU uruchomionego zadania bez konieczności jego zatrzymania. Ta kompilacja dotyczy obietnicy klientów długotrwałych potoków o krytycznym znaczeniu, które Stream Analytics obecnie oferowane. Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure Stream Analytics przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#niestandardowe deserializatory
Deweloperzy mogą wykorzystać możliwości Azure Stream Analytics, aby przetwarzać dane w protobuf, XML lub w dowolnym formacie niestandardowym. Można zaimplementować [niestandardowe cofnięcia serializatorów](custom-deserializer-examples.md) w C#, które mogą być następnie używane do deserializacji zdarzeń odebranych przez Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozszerzalność C# przy użyciu kodu niestandardowego

Deweloperzy tworzący moduły Stream Analytics w chmurze lub na IoT Edge mogą zapisywać i ponownie używać C# funkcji niestandardowych i wywoływać je bezpośrednio w zapytaniu za pomocą [funkcji zdefiniowanych przez użytkownika](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Uwierzytelnianie tożsamości zarządzanej za pomocą Power BI

Azure Stream Analytics oferuje pełną obsługę uwierzytelniania opartego na tożsamościach przy użyciu Power BI na potrzeby dynamicznego środowiska pulpitu nawigacyjnego.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Azure Stream Analytics "modele uczenia maszynowego mają obsługę wykrywania i wyrzucania *DIP* oprócz dwukierunkowego, powolnego pozytywnego *i wolniejszego* wykrywania negatywnych trendów. Aby uzyskać więcej informacji, zobacz [wykrywanie anomalii w Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Kroki zapytania debugowania w programie Visual Studio

Możesz łatwo wyświetlić podgląd pośredniego zestawu wierszy na diagramie danych podczas przeprowadzania testów lokalnych w Azure Stream Analytics Narzędzia dla programu Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testowanie lokalne z danymi dynamicznymi w Visual Studio Code

Możesz przetestować zapytania dotyczące danych na żywo na komputerze lokalnym przed przesłaniem zadania do platformy Azure. Każda iteracja testowa trwa średnio od dwóch do trzech sekund, co skutkuje bardzo wydajnym procesem tworzenia oprogramowania.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code Azure Stream Analytics

Azure Stream Analytics zadania mogą być tworzone w Visual Studio Code. Zapoznaj się z naszym [samouczkiem wprowadzenie do vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integracja z usługą Azure Machine Learning

Możesz skalować zadania usługi Stream Analytics, za pomocą usługi Machine Learning () ml. Aby dowiedzieć się więcej na temat wykorzystania funkcji uczenia Maszynowego w ramach zadania usługi Stream Analytics, odwiedź stronę [skalować zadania usługi Stream Analytics przy użyciu funkcji usługi Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Zapoznaj się z rzeczywistych scenariuszy za pomocą [przeprowadzania analizy tonacji za pomocą usługi Azure Stream Analytics i Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Na żywo danych testowania w programie Visual Studio

Visual Studio tools dla usługi Azure Stream Analytics pozwala zwiększyć lokalnego testowania funkcji, dająca możliwość testowania zapytań dla strumieni zdarzeń na żywo ze źródeł w chmurze takich jak Centrum zdarzeń lub IoT hub. Dowiedz się, jak [danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio Test](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funkcje zdefiniowane przez użytkownika platformy .NET na brzegowych urządzeniach IoT

Za pomocą platformy .NET standard funkcje zdefiniowane przez użytkownika może uruchamiać kod .NET Standard, jako część potoku transmisji strumieniowych. Można utworzyć proste klas języka C# lub zaimportować pełny projektu i bibliotek. Pełne tworzenia i obsługi debugowania jest obsługiwana w programie Visual Studio. Aby uzyskać więcej informacji, odwiedź stronę [opracowywanie .NET Standard funkcje zdefiniowane przez użytkownika dla zadań usługi Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Inne wersje zapoznawcze

Poniższe funkcje są również dostępne w wersji zapoznawczej na żądanie.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Ocena wysokiej wydajności w czasie rzeczywistym z niestandardowymi modelami ML zarządzanymi przez Azure Machine Learning

Azure Stream Analytics obsługuje wysoką wydajność i ocenianie w czasie rzeczywistym, wykorzystując niestandardowe, wstępnie nauczone modele Machine Learning zarządzane przez Azure Machine Learning i hostowane w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI) przy użyciu przepływu pracy to nie wymaga pisania kodu. [Utwórz konto](https://aka.ms/asapreview1) w wersji zapoznawczej

### <a name="support-for-azure-stack"></a>Obsługa Azure Stack
Ta funkcja jest włączona w środowisku uruchomieniowym Azure IoT Edge, wykorzystuje niestandardowe funkcje Azure Stack, takie jak Natywna obsługa lokalnych wejść i wyjść w Azure Stack (na przykład Event Hubs, IoT Hub, Blob Storage). Ta nowa Integracja pozwala tworzyć architektury hybrydowe, które mogą analizować dane blisko miejsca, w którym są generowane, obniżać czas oczekiwania i maksymalizując szczegółowe informacje.
Ta funkcja umożliwia tworzenie architektur hybrydowych, które mogą analizować dane blisko miejsca, w którym są generowane, obniżające czas oczekiwania i maksymalizując szczegółowe informacje. Musisz [zarejestrować się](https://aka.ms/asapreview1) w tej wersji zapoznawczej.
