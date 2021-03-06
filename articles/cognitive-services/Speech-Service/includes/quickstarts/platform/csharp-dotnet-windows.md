---
title: 'Szybki Start: zestaw Speech SDK dla .NET Framework (Windows) Konfiguracja platformy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik umożliwia skonfigurowanie platformy .NET Framework dla C# systemu Windows za pomocą zestawu Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: db00398efa638ac945bc188ad0c44f904059e689
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468069"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla .NET Framework (Windows).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Tworzenie projektu programu Visual Studio i Instalowanie zestawu Speech SDK

Musisz zainstalować [pakiet NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) , aby można było odwołać się do niego w kodzie. Aby to zrobić, konieczne może być utworzenie projektu **HelloWorld** . Jeśli masz już projekt z udostępnionym obciążeniem **programu .NET Desktop** , możesz użyć tego projektu i przejść do korzystania z [Menedżera pakietów NuGet w celu zainstalowania zestawu Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Utwórz projekt HelloWorld

1. Open Visual Studio 2019.

1. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**. 

1. W oknie **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Framework)** , a następnie wybierz przycisk **dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź wartość *HelloWorld* w polu **Nazwa projektu**, wybierz lub Utwórz ścieżkę katalogu w **lokalizacji**, a następnie wybierz pozycję **Utwórz**.

1. Na pasku menu programu Visual Studio wybierz kolejno pozycje **narzędzia** > **Pobierz narzędzia i funkcje**, które otwiera Instalator programu Visual Studio i wyświetla okno dialogowe **Modyfikowanie** .

1. Sprawdź, czy jest dostępne obciążenie **Programowanie aplikacji klasycznych dla platformy .NET** . Jeśli obciążenie nie zostało zainstalowane, zaznacz pole wyboru obok niego, a następnie wybierz pozycję **Modyfikuj** , aby rozpocząć instalację. Pobranie i zainstalowanie tego składnika może potrwać kilka minut.

   Jeśli pole wyboru obok pozycji **Programowanie aplikacji klasycznych platformy .NET** jest już zaznaczone, wybierz pozycję **Zamknij** , aby zamknąć okno dialogowe.

   ![Włącz pakiet roboczy Programowanie aplikacji klasycznych dla platformy .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zamknij Instalator programu Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Instalowanie zestawu Speech SDK przy użyciu Menedżera pakietów NuGet

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **HelloWorld** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet** , aby wyświetlić Menedżera pakietów NuGet.

   ![Menedżer pakietów NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. W prawym górnym rogu Znajdź pole listy rozwijanej **Źródło pakietu** i upewnij się, że **`nuget.org`** jest zaznaczone.

1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.

1. W polu wyszukiwania wpisz *Microsoft. CognitiveServices. Speech* i wybierz **Enter**.

1. Z wyników wyszukiwania wybierz pakiet **Microsoft. CognitiveServices. Speech** , a następnie wybierz pozycję **Zainstaluj** , aby zainstalować najnowszą stabilną wersję.

   ![Zainstaluj pakiet NuGet Microsoft. CognitiveServices. Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.

   Po zainstalowaniu pakietu w oknie **konsola Menedżera pakietów** zostanie wyświetlone potwierdzenie.

Teraz możesz przejść do [kolejnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
