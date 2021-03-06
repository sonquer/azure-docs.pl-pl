---
title: Komunikaty z chmury do urządzenia za pomocą usługi Azure IoT Hub (węzeł) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla środowiska Node. js. Zmodyfikuj aplikację symulowanego urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110802"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (Node. js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat [wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md). Pokazano, jak:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.
* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.
* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w [przewodniku dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie aplikacje konsolowe środowiska Node. js:

* **SimulatedDevice**, zmodyfikowana wersja aplikacji utworzona w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), która łączy się z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym C, Java, Python i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Wymagania wstępne

* Node. js w wersji 10.0. x lub nowszej. [Przygotuj środowisko programistyczne](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) , w którym opisano sposób instalowania środowiska Node. js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieraj komunikaty w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz aplikację symulowanego urządzenia utworzoną w artykule wysyłanie danych [telemetrycznych z urządzenia do usługi IoT Hub](quickstart-send-telemetry-node.md) w celu odbierania komunikatów z chmury do urządzenia z Centrum IoT Hub.

1. Za pomocą edytora tekstów Otwórz plik **SimulatedDevice. js** . Ten plik znajduje się w folderze **IoT-hub\Quickstarts\simulated-Device** , który znajduje się w folderze głównym w przykładowym kodzie środowiska Node. js, który został pobrany z urządzenia do przewodnika Szybki Start dotyczącego usługi [IoT Hub](quickstart-send-telemetry-node.md) .

2. Zarejestruj procedurę obsługi z klientem urządzenia, aby odbierać komunikaty wysyłane z IoT Hub. Dodaj wywołanie do `client.on` tuż po wierszu, który tworzy klienta urządzenia, tak jak w poniższym fragmencie kodu:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    W tym przykładzie urządzenie wywoła **kompletną** funkcję w celu powiadomienia IoT Hub o przetworzeniu komunikatu. Wywołanie do **ukończenia** nie jest wymagane, jeśli używany jest transport MQTT i można go pominąć. Jest to wymagane w przypadku protokołów HTTPS i AMQP.
  
   > [!NOTE]
   > Jeśli używasz protokołu HTTPS zamiast MQTT lub AMQP jako transportu, wystąpienie **DeviceClient** sprawdza komunikaty IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji o różnicach między obsługą MQTT, AMQP i HTTPS oraz ograniczenia IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową środowiska Node. js, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia. Potrzebujesz identyfikatora urządzenia dodanego w polu [Wyślij telemetrię z urządzenia do](quickstart-send-telemetry-node.md) przewodnika Szybki Start dotyczącego usługi IoT Hub. Potrzebne są również parametry połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

1. Utwórz pusty folder o nazwie **sendcloudtodevicemessage**. W folderze **sendcloudtodevicemessage** Utwórz plik Package. JSON przy użyciu następującego polecenia z poziomu wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```shell
    npm init
    ```

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** Uruchom następujące polecenie, aby zainstalować pakiet **Azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz plik **SendCloudToDeviceMessage. js** w folderze **SendCloudToDeviceMessage** .

4. Dodaj następujące instrukcje `require` na początku pliku **SendCloudToDeviceMessage. js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Dodaj następujący kod do pliku **SendCloudToDeviceMessage. js** . Zastąp wartości symboli zastępczych "{ciąg połączenia usługi IoT Hub}" i "{Device ID}" parametrami połączenia usługi IoT Hub i zanotowanym wcześniej IDENTYFIKATORem urządzenia:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, aby drukować wyniki operacji do konsoli:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Dodaj następującą funkcję, aby drukować wiadomości z opiniami o dostarczaniu do konsoli programu:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Dodaj następujący kod, aby wysłać wiadomość na urządzenie i obsłużyć komunikat o opinii, gdy urządzenie potwierdzi komunikat z chmury do urządzenia:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Zapisz i zamknij plik **SendCloudToDeviceMessage. js** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze **symulowane urządzenia** Uruchom następujące polecenie, aby wysłać dane telemetryczne do IoT Hub i nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    node SimulatedDevice.js
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-node-node-c2d/receivec2d.png)

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** Uruchom następujące polecenie, aby wysłać komunikat z chmury do urządzenia i poczekać na potwierdzenie opinii:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Uruchom aplikację, aby wysłać polecenie z chmury do urządzenia](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Dla uproszczenia w tym samouczku nie są implementowane żadne zasady ponawiania. W polu kod produkcyjny należy zaimplementować zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz [Akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
