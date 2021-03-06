---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933471"
---
Wybierz pozycję **kompilacja**. W otwartym oknie dialogowym Wybierz folder, do którego chcesz wyeksportować projekt Xcode.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

> [!NOTE]
> Jeśli zostanie wyświetlone okno z pytaniem, czy chcesz zamienić lub dołączyć, zalecamy wybranie opcji **Dołącz** , ponieważ jest ona szybsza. Należy wybrać opcję **Zamień** tylko w przypadku zmiany elementów zawartości w scenie. (Na przykład w przypadku dodawania, usuwania lub zmieniania relacji nadrzędny/podrzędny lub dodawania, usuwania lub zmieniania właściwości). Jeśli wprowadzasz tylko zmiany w kodzie źródłowym, **dołączenie** powinno być wystarczające.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Konwertuj projekt Xcode na xcworkspace zawierający odwołania do zakotwiczeń przestrzennych platformy Azure

W wyeksportowanym folderze projektu Xcode Uruchom to polecenie w terminalu, aby zainstalować wymagane CocoaPods dla projektu:

```bash
pod install --repo-update
```

Teraz możesz otworzyć `Unity-iPhone.xcworkspace`, aby otworzyć projekt w Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Zapoznaj się z [krokami](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) rozwiązywania problemów, jeśli masz problemy z CocoaPod po uaktualnieniu do macOS Catalina (10,15).

Wybierz węzeł główny **Unity-iPhone** , aby wyświetlić ustawienia projektu, a następnie wybierz kartę **Ogólne** .

W obszarze **podpisywanie**upewnij się, że opcja **automatycznie Zarządzaj podpisywaniem** jest włączona. Jeśli tak nie jest, włącz ją, a następnie wybierz opcję **Włącz automatyczne** w oknie dialogowym, które pojawia się, aby zresetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)** .

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Jeśli zobaczysz błąd `library not found for -lPods-Unity-iPhone`, prawdopodobnie otwarto plik `.xcodeproj` zamiast pliku `.xcworkspace`.
