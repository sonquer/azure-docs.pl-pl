---
title: Utwórz Azure Media Services dane wejściowe zadania z pliku lokalnego | Microsoft Docs
description: W tym artykule pokazano, jak utworzyć Azure Media Services dane wejściowe zadania z pliku lokalnego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: c5acda0ccec409ec06d0f3f2226b9819e3f130c7
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538417"
---
# <a name="create-a-job-input-from-a-local-file"></a>Tworzenie danych wejściowych zadania z pliku lokalnego

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Wejściowy film wideo może być przechowywany jako zasób usługi Media. w takim przypadku tworzony jest wejściowy zasób oparty na pliku (przechowywany lokalnie lub w usłudze Azure Blob Storage). W tym temacie pokazano, jak utworzyć dane wejściowe zadania z pliku lokalnego. Aby zapoznać się z pełnymi przykładami, zobacz ten [przykład serwisu GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Przykład platformy .NET

Poniższy kod pokazuje, jak utworzyć zasób wejściowy i użyć go jako danych wejściowych dla zadania. Funkcja CreateInputAsset wykonuje następujące akcje:

* Tworzy element zawartości
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do [kontenera zasobu w magazynie](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Poniższy fragment kodu tworzy wyjściowy element zawartości, jeśli jeszcze nie istnieje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Poniższy fragment kodu przesyła zadanie kodowania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Utwórz dane wejściowe zadania na podstawie adresu URL https](job-input-from-http-how-to.md).
