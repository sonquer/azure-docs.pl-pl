---
title: Rejestrowanie nowego urządzenia Azure IoT Edge | Microsoft Docs
description: Zarejestrować nowe urządzenie usługi IoT Edge i pobieranie parametrów połączenia za pomocą rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 32121681b14989f23e29c3701826b4494988c263
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772435"
---
# <a name="register-an-azure-iot-edge-device"></a>Rejestrowanie urządzenia Azure IoT Edge

Aby móc korzystać z urządzeń IoT w Azure IoT Edge, musisz zarejestrować je w usłudze IoT Hub. Po zarejestrowaniu urządzenia można pobrać parametry połączenia w celu skonfigurowania urządzenia na potrzeby obciążeń IoT Edge.

Użytkownik ma możliwość rejestracji przy użyciu jednego z następujących narzędzi:

* [Zarejestrowanie urządzenia w Azure Portal,](#register-in-the-azure-portal) Jeśli wolisz graficzny interfejs użytkownika do tworzenia i wyświetlania zasobów platformy Azure oraz zarządzania nimi.
* [Zarejestrowanie urządzenia w usłudze Visual Studio Code](#register-with-visual-studio-code) , jeśli wolisz zarządzać zasobami usługi Azure IoT w tym samym miejscu, w którym tworzysz rozwiązania IoT.
* [Zarejestrowanie urządzenia w interfejsie wiersza polecenia platformy Azure](#register-with-the-azure-cli) , jeśli wolisz używać narzędzi wiersza poleceń do zarządzania zasobami platformy Azure, lub zamierzaj zautomatyzować zadania.

## <a name="register-in-the-azure-portal"></a>Zarejestruj się w Azure Portal

Wszystkie zadania rejestracji można wykonać w Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Wymagania wstępne dotyczące Azure Portal

Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Utwórz urządzenie IoT Edge w Azure Portal

W IoT Hub w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IOT, które nie są włączone.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
2. W lewym okienku wybierz **IoT Edge** z menu.
3. Wybierz pozycję **Dodaj urządzenie IoT Edge**.
4. Podaj identyfikator opisu urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i połączyć nowe urządzenie z centrum.
5. Wybierz pozycję **Zapisz**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Wyświetlanie IoT Edge urządzeń w Azure Portal

Wszystkie włączone krawędzi urządzenia, łączących się z Centrum IoT hub są wyświetlane na **usługi IoT Edge** strony.

![Wyświetl wszystkie urządzenia usługi IoT Edge w usłudze IoT hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Pobierz parametry połączenia w Azure Portal

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Na stronie **IoT Edge** w portalu kliknij identyfikator urządzenia na liście urządzeń IoT Edge.
2. Skopiuj wartość **podstawowych parametrów połączenia** lub **pomocniczych parametrów połączenia**.

## <a name="register-with-visual-studio-code"></a>Zarejestruj się w Visual Studio Code

Istnieje wiele sposobów, aby wykonać większość operacji w programie VS Code. W tym artykule jest używany Eksplorator, ale można również wykonać kroki przy użyciu palety poleceń.

### <a name="prerequisites-for-visual-studio-code"></a>Wymagania wstępne dotyczące Visual Studio Code

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się do dostępu do usługi IoT hub

Możesz użyć rozszerzeń usługi Azure IoT, aby Visual Studio Code do wykonywania operacji przy użyciu IoT Hub. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać IoT Hub.

1. W programie Visual Studio Code Otwórz **Explorer** widoku.
1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek.
1. Wybierz **wybierz Centrum IoT Hub**.
1. Jeśli nie zalogowano się na koncie platformy Azure, postępuj zgodnie z monitami, aby to zrobić.
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz Centrum IoT hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Tworzenie urządzenia IoT Edge z Visual Studio Code

1. W Eksploratorze programu VS Code rozwiń **Azure IoT Hub Devices** sekcji.
1. Kliknij pozycję **...**  w **Azure IoT Hub Devices** nagłówek sekcji. Jeśli nie widzisz wielokropka, kliknąć lub Najedź kursorem na nagłówek.
1. Wybierz **tworzenie urządzenia usługi IoT Edge**.
1. W polu tekstowym nadaj urządzeniu tego identyfikatora.

Dane wyjściowe na ekranie zobaczysz wynik użycia polecenia. Informacje o urządzeniu zostanie wydrukowany, która obejmuje **deviceId** podane i **connectionString** służące do łączenia z urządzenia fizycznego do usługi IoT hub.

Dane wyjściowe na ekranie zobaczysz wynik użycia polecenia. Informacje o urządzeniu zostanie wydrukowany, która obejmuje **deviceId** podane i **connectionString** służące do łączenia z urządzenia fizycznego do usługi IoT hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Wyświetlanie IoT Edge urządzeń z Visual Studio Code

Wszystkie urządzenia, które łączą się z Centrum IoT, są wymienione w sekcji **IoT Hub platformy Azure** w Eksploratorze Visual Studio Code. Urządzenia IoT Edge są odróżniane od urządzeń innych niż brzegowe z inną ikoną, a fakt, że moduły **$edgeAgent** i **$edgeHub** są wdrażane na poszczególnych urządzeniach IoT Edge.

![Wyświetl wszystkie urządzenia usługi IoT Edge w usłudze IoT hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobierz parametry połączenia z Visual Studio Code

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **IoT Hub platformy Azure** .
1. Wybierz **skopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka.

Możesz również wybrać **uzyskać informacje o urządzeniu** z menu kliknij prawym przyciskiem myszy, aby sprawdzić wszystkie informacje o urządzeniu, w tym ciągu połączenia w oknie danych wyjściowych.

## <a name="register-with-the-azure-cli"></a>Zarejestruj się w interfejsie wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Edge. Umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi device provisioning i połączonymi centrami po gotowych. Nowe rozszerzenie IoT uzupełnia interfejs wiersza polecenia platformy Azure przy użyciu funkcji, takich jak zarządzanie urządzeniami i pełne możliwości usługi IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* [Usługi IoT hub](../iot-hub/iot-hub-create-using-cli.md) w subskrypcji platformy Azure.
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w danym środowisku. Co najmniej z wiersza polecenia platformy Azure musi być w wersji 2.0.24 lub nowszej. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Tworzenie urządzenia IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Przykład:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Tego polecenia obejmuje trzy parametry:

* **Identyfikator urządzenia**: wprowadź opisową nazwę, która jest unikatowa w Centrum IoT.
* **Nazwa koncentratora**: Podaj nazwę Centrum IoT Hub.
* **włączone usługi Edge**: ten parametr deklaruje, że urządzenie jest przeznaczona do użytku z usługą IoT Edge.

   ![AZ iot hub — tożsamość urządzenia — Tworzenie danych wyjściowych](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie IoT Edge urządzeń za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) , aby wyświetlić wszystkie urządzenia w centrum IoT Hub. Przykład:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie, które są zarejestrowane jako urządzenia usługi IoT Edge będzie mieć ustawioną właściwość **capabilities.iotEdge** równa **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Pobieranie parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure

Gdy wszystko będzie gotowe skonfigurować urządzenie, należy parametry połączenia, która łączy urządzenie fizyczne za pomocą jej tożsamości w usłudze IoT hub. Użyj polecenia [AZ IoT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) , aby zwrócić parametry połączenia dla pojedynczego urządzenia:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

W wartości parametru `device-id` rozróżniana jest wielkość liter. Nie należy kopiować znaków cudzysłowu wokół parametry połączenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już zarejestrowaną tożsamość urządzenia w usłudze IoT Hub, możesz zainstalować środowisko uruchomieniowe IoT Edge na swoich urządzeniach. Zainstaluj środowisko uruchomieniowe zgodnie z systemem operacyjnym urządzenia:

* [Zainstaluj Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md)
* [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)
