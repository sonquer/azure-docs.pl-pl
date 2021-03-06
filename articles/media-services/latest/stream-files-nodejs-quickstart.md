---
title: Przesyłanie strumieniowe plików wideo za pomocą Azure Media Services-Node. js | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto Azure Media Services, zakodować plik i przesłać go strumieniowo do Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639390"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — Node. js

W tym samouczku pokazano, jak łatwo zakodować i rozpocząć przesyłanie strumieniowe wideo na wielu różnych przeglądarkach i urządzeniach przy użyciu Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.

W przykładzie w tym artykule zawarto informacje o zawartości, która jest dostępna za pośrednictwem adresu URL HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Na końcu samouczka będziesz mieć możliwość przesyłania strumieniowego wideo.  

![Odtwarzanie wideo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego Node. js do maszyny przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Przykład znajduje się w folderze [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Otwórz program [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) w pobranym projekcie. Zastąp wartości poświadczeniami uzyskanymi w celu [uzyskania dostępu do interfejsów API.](access-api-cli-how-to.md) `endpoint config`

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzenie **zasobu** danych wyjściowych, który zostanie użyty jako dane wyjściowe **zadania** kodowania.
3. Tworzenie danych wejściowych **zadania** opartych na adresie URL protokołu HTTPS.
4. Przesłanie **zadania** kodowania przy użyciu danych przychodzących i wychodzących, które zostały utworzone wcześniej.
5. Sprawdzenie stanu zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Aplikacja pobiera zakodowane pliki. Utwórz folder, w którym chcesz umieścić pliki wyjściowe i zaktualizować wartość zmiennej **outputFolder** w pliku [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) .
1. Otwórz **wiersz polecenia**, przejdź do katalogu przykładowego i wykonaj następujące polecenia.

    ```
    npm install 
    node index.js
    ```

Po zakończeniu działania należy zobaczyć podobne dane wyjściowe:

![Uruchom polecenie](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym Media Services i kont magazynu utworzonych dla tego samouczka, Usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zobacz także

[Kody błędów zadań](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Koncepcje Media Services](concepts-overview.md)
