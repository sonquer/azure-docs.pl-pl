---
title: Użyj interfejsu wiersza polecenia, aby skalować jednostki zarezerwowane multimediów — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skalowanie przetwarzania multimediów z użyciem usługi Azure Media Services za pomocą interfejsu wiersza polecenia.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102944"
---
# <a name="scaling-media-processing"></a>Skalowanie przetwarzania multimediów

Usługa Azure Media Services umożliwia skalowanie przetwarzania multimediów w ramach Twojego konta, umożliwiając zarządzanie jednostek zarezerwowanych multimediów (lokalizacje MRU). MRUs określić szybkość przetwarzania zadań przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. 

Oprócz określania typu jednostki zarezerwowanej możesz określić, aby udostępnić konto za pomocą jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie wykonywanych współbieżnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania czekają w kolejce i zostaną pobrane do przetwarzania sekwencyjnego po zakończeniu uruchomionego zadania. Jeśli dla konta nie zainicjowano obsługi żadnych jednostek zarezerwowanych, zadania będą wybierane sekwencyjnie. W takim przypadku czas oczekiwania między kolejnymi zadaniami zostanie zależał od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybieranie między różnymi typami jednostek zarezerwowanych

Poniższa tabela ułatwia podejmowanie decyzji podczas wybierania różnych szybkości kodowania. Zawiera również kilka testów porównawczych [wideo, które można pobrać,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) aby wykonać własne testy:

|Typ RU|Scenariusz|Przykładowe wyniki dla [wideo z 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki o wartościach SD lub niższych, a nie czasowe, niskie koszty.|Kodowanie do pliku MP4 o pojedynczej szybkości transmisji bitów przy użyciu polecenia "wielokrotna H264 Single szybkość transmisji bitów 16x9" zajmuje około 7 minut.|
| **S2**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Normalne użycie zarówno dla kodowania SD, jak i HD.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 720" zajmuje około 6 minut.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 o wielu szybkościach transmisji bitów" zajmuje około 12 minut.|
| **S3**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Pełne wideo o rozdzielczości HD i 4K. Czuły czasowo, szybsze kodowanie szybkością oferowaną.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 1080p" zajmuje około 3 minuty.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 Multiple szybkość transmisji bitów 1080p" zajmuje około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia

* W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez Media Services v3 lub Video Indexer, jest zdecydowanie zalecane.
* W przypadku używania puli udostępnionej, czyli bez żadnych jednostek zarezerwowanych, zadania kodowania mają taką samą wydajność jak w przypadku S1 jednostek ru. Nie istnieje jednak Górna granica czasu, w którym zadania mogą być spędzane w stanie umieszczonym w kolejce, a w dowolnym momencie tylko jedno zadanie zostanie uruchomione.

W pozostałej części artykułu pokazano, jak używać [interfejsu wiersza polecenia Media Services v3](https://aka.ms/ams-v3-cli-ref) do skalowania MRUs.

> [!NOTE]
> Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).
>
> Obecnie nie można używać Azure Portal do zarządzania innymi zasobami v3. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Zarezerwowane multimediów skalowania jednostek przy użyciu interfejsu wiersza polecenia

Uruchom polecenie `mru`.

Następujące [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) zestawy jednostek zarezerwowanych multimediów na "amsaccount" konta przy użyciu polecenia **liczba** i **typu** parametrów.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="next-step"></a>Następny krok

[Analizowanie plików wideo](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zobacz także

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
