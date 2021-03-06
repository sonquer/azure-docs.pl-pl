---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu języka Python | Microsoft Docs
description: Jak przekazywać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla języka Python. Przekazane pliki są przechowywane w kontenerze obiektów BLOB usługi Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110747"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym artykule pokazano, jak używać [funkcji przekazywania plików IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania plików do [usługi Azure Blob Storage](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpieczne udostępnianie kontenera magazynu na potrzeby przekazywania pliku.

* Użyj klienta języka Python, aby przekazać plik za pomocą Centrum IoT.

[Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) przedstawia podstawowe funkcje obsługi komunikatów z urządzenia do chmury IoT Hub. Jednak w niektórych scenariuszach nie można łatwo zmapować danych wysyłanych przez urządzenia do bezwzględnie niewielkich komunikatów z urządzenia do chmury, które IoT Hub akceptowane. W przypadku konieczności przewożenia plików z urządzenia można nadal korzystać z zabezpieczeń i niezawodności IoT Hub.

> [!NOTE]
> IoT Hub Python SDK obecnie obsługuje tylko przekazywanie plików opartych na znakach, takich jak pliki **txt** .

Na końcu tego samouczka uruchomisz aplikację konsolową języka Python:

* **FileUpload.py**, który przekazuje plik do magazynu przy użyciu zestawu SDK urządzenia w języku Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Ten przewodnik używa przestarzałego zestawu SDK języka Python V1, ponieważ funkcja przekazywania plików nie została jeszcze zaimplementowana w nowym zestawie SDK V2.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia w celu przekazania pliku do centrum IoT Hub.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-iothub-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Za pomocą edytora tekstów Utwórz plik testowy, który zostanie przekazany do usługi BLOB Storage.

    > [!NOTE]
    > IoT Hub Python SDK obecnie obsługuje tylko przekazywanie plików opartych na znakach, takich jak pliki **txt** .

3. Za pomocą edytora tekstów Utwórz plik **FileUpload.py** w folderze roboczym.

4. Dodaj następujące instrukcje i zmienne `import` na początku pliku **FileUpload.py** . 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. W pliku Zastąp `[Device Connection String]` parametrami połączenia urządzenia IoT Hub. Zastąp `[Full path to file]` ścieżką do utworzonego pliku testowego lub dowolnym plikiem na urządzeniu, które chcesz przekazać. Zastąp `[File name for storage]` nazwą, którą chcesz nadać plikowi po przekazaniu do magazynu obiektów BLOB. 

6. Utwórz wywołanie zwrotne dla funkcji **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Dodaj następujący kod, aby połączyć klienta i przekazać plik. Uwzględnij również procedurę `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Zapisz i zamknij plik **UploadFile.py** .

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz możesz przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia w folderze roboczym Uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

2. Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **FileUpload** :

    ![Wyjście z aplikacji symulowanej — urządzenie](./media/iot-hub-python-python-file-upload/1.png)

3. Możesz użyć portalu, aby wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

    ![Przekazany plik](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz w dalszym ciągu eksplorować funkcje i scenariusze dotyczące programu IoT Hub z następującymi artykułami:

* [Programistyczne tworzenie Centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)
