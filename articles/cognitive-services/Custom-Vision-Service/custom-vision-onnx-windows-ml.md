---
title: 'Samouczek: używanie modelu ONNX z funkcją Windows ML — Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć aplikację Windows UWP używającą modelu ONNX wyeksportowanego z Azure Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 3b56cd12aec21cffd98a0143f5cf3f083ec55e94
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960557"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Samouczek: używanie modelu ONNX z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza)

Dowiedz się, jak używać modelu ONNX wyeksportowanego z usługi Custom Vision z funkcją Windows ML (wersja zapoznawcza).

W tym artykule opisano sposób używania pliku ONNX wyeksportowanego z Custom Vision Service z funkcją Windows ML. Dostępna jest przykładowa aplikacja Windows UWP. Do przykładowej aplikacji dołączony jest nauczony model rozpoznawania. Podano również instrukcję używania własnego modelu z tą przykładową aplikacją.

> [!div class="checklist"]
> * Informacje o przykładowej aplikacji
> * Pobieranie przykładowego kodu
> * Uruchomienie przykładu
> * Używanie własnego modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Windows 10 w wersji 1809 lub nowszej

* Windows SDK dla kompilacji 17763 lub nowszej

* Visual Studio 2017 w wersji 15.7 lub nowszej z włączonym pakietem __Opracowywanie zawartości dla platformy uniwersalnej systemu Windows__.

* Włączony tryb dewelopera. Więcej informacji można znaleźć w artykule [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Konfigurowanie urządzenia pod kątem tworzenia aplikacji).

## <a name="about-the-example-app"></a>Informacje o przykładowej aplikacji

Aplikacja jest aplikacją ogólną Windows UWP. Dzięki temu można wybrać z komputera obraz, który zostanie następnie wysyłany do modelu. Tagi i wyniki zwrócone przez model są wyświetlane obok obrazu.

## <a name="get-the-example-code"></a>Pobieranie przykładowego kodu

Przykładowa aplikacja jest dostępna na stronie [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample).

## <a name="run-the-example"></a>Uruchomienie przykładu

1. Naciśnij klawisz `F5`, aby uruchomić aplikację z poziomu programu Visual Studio. Może zostać wyświetlony monit o włączenie trybu dewelopera. Więcej informacji można znaleźć w artykule [Enable your device for development](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) (Konfigurowanie urządzenia pod kątem tworzenia aplikacji).

1. Po uruchomieniu aplikacji wybierz obraz do ocenienia przy użyciu przycisku.

## <a name="use-your-own-model"></a>Używanie własnego modelu

Aby używać własnego modelu, wykonaj następujące czynności:

1. [Utwórz i naucz](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) klasyfikator przy użyciu usługi Custom Vision Service. Aby wyeksportować modelu, wybierz domenę __kompaktową__, taką jak **General (kompaktowa)** . Aby wyeksportować istniejący klasyfikator, przekonwertuj domenę na domenę kompaktową przez wybranie ikony koła zębatego w prawym górnym rogu. Na stronie __Ustawienia__ wybierz model kompaktowy, zapisz i naucz projekt.  

1. [Wyeksportuj model](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) , przechodząc do karty wydajność. Wybierz iterację przeszkolonej przy użyciu domeny kompaktowej. zostanie wyświetlony przycisk "Eksportuj". Wybierz opcję *Eksportuj*, *ONNX*, a następnie *Eksportuj*. Gdy plik będzie gotowy, wybierz przycisk *Pobierz*.

1. Upuść plik ONNX w folderze __Assets__ projektu. 

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder Assets, a następnie wybierz pozycję __Dodaj istniejący element__. Wybierz plik ONNX.

1. W Eksploratorze rozwiązań wybierz plik ONNX z folderu Assets. Zmień następujące właściwości pliku:

    * __Akcja kompilacji__  ->  __Zawartość__
    * __Kopiuj do katalogu wyjściowego__  ->  __Kopiuj, jeśli nowszy__

1. Dla zmiennej `_onnxFileNames` ustaw nazwę pliku ONNX. Zmień też element `ClassLabel` na liczbę etykiet w modelu.

1. Skompiluj i uruchom.

1. Kliknij przycisk, aby wybrać obraz do oceny.

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej sposobów eksportowania i używania modeli Custom Vision, przeczytaj następujące artykuły:

* [Eksportowanie modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Używanie wyeksportowanego modelu TensorFlow w aplikacji systemu Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Używanie wyeksportowanego modelu CoreML w aplikacji w języku Swift dla systemu iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Używanie wyeksportowanego modelu CoreML w aplikacji dla systemu iOS na platformie Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Więcej informacji o używaniu modeli ONNX z funkcją Windows ML znajdziesz w artykule [Integrate a model into your app with Windows ML](/windows/ai/windows-ml/integrate-model) (Włączanie modelu do aplikacji przy użyciu funkcji Windows ML).
