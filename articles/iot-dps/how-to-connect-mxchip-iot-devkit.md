---
title: Korzystanie z usługi Azure IoT Hub Device Provisioning Service autoaprowizacji w celu zarejestrowania zestawu deweloperskiego IoT DevKit z IoT Hub | Microsoft Docs
description: Jak korzystać z samoobsługowego udostępniania usługi Azure IoT Hub Device Provisioning Service (DPS), aby zarejestrować zestawu deweloperskiego IoT DevKit z IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975656"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Zarejestrowanie zestawu deweloperskiego IoT DevKit przy użyciu usługi Azure IoT Hub Device Provisioning Service IoT Hub

W tym artykule opisano sposób korzystania z usługi Azure IoT Hub Device Provisioning Service [autoaprowizacji](concepts-auto-provisioning.md)w celu zarejestrowania DevKit IoT zestawu deweloperskiego w usłudze Azure IoT Hub. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj globalny punkt końcowy usługi Device Provisioning na urządzeniu.
* Aby wygenerować certyfikat X. 509, należy użyć unikatowego klucza tajnego urządzenia.
* Zarejestruj pojedyncze urządzenie.
* Sprawdź, czy urządzenie zostało zarejestrowane.

[Zestawu deweloperskiego IoT DevKit](https://aka.ms/iot-devkit) to cała tablica niezgodna z programem Arduino z rozbudowanymi urządzeniami peryferyjnymi i czujnikami. Możesz to zrobić za pomocą [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure iot Tools](https://aka.ms/azure-iot-tools) w Visual Studio Code. DevKit jest dostępny w katalogu rosnące [projekty](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) , aby kierować rozwiązania do tworzenia prototypów Internet rzeczy (IoT), które wykorzystują usługi platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, najpierw wykonaj następujące zadania:

* Skonfiguruj sieć Wi-Fi DevKit i przygotuj środowisko programistyczne, postępując zgodnie z instrukcjami w sekcji "Przygotowanie środowiska programistycznego" w artykule [Connect IoT DEVKIT AZ3166 do platformy Azure IoT Hub w chmurze](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Uaktualnij do najnowszego oprogramowania układowego (1.3.0 lub nowszego) za pomocą samouczka [aktualizacji oprogramowania układowego DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Utwórz i Połącz IoT Hub z wystąpieniem usługi Device Provisioning Service, wykonując kroki opisane w temacie [konfigurowanie IoT Hub Device Provisioning Service za pomocą Azure Portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, że Twój DevKit IoT **nie jest podłączony** do komputera. Najpierw Rozpocznij VS Code, a następnie połącz DevKit z komputerem.

1. Kliknij `F1`, aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Otwórz przykłady...** . Następnie wybierz pozycję **IoT DevKit** jako tablicę.

1. Na stronie Przykłady IoT Workbench Znajdź pozycję **Rejestracja urządzenia w usłudze DPS** , a następnie kliknij pozycję **Otwórz przykład**. Następnie wybiera domyślną ścieżkę do pobrania przykładowego kodu.
    ![otworzyć](media/how-to-connect-mxchip-iot-devkit/open-sample.png) przykład

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Zapisz unikatowy klucz tajny urządzenia w magazynie zabezpieczeń urządzenia

Funkcja autoaprowizacji można skonfigurować na urządzeniu na podstawie [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism)urządzenia. Zestawu deweloperskiego IoT DevKit używa [aparatu kompozycji tożsamości urządzenia](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). Do wygenerowania unikatowego [certyfikatu X. 509](concepts-security.md#x509-certificates)urządzenia zapisywany jest **unikatowy klucz tajny urządzenia** (STSAFE Security[STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) na DevKit. Certyfikat jest używany później do procesu rejestracji w usłudze Device Provisioning i podczas rejestracji w czasie wykonywania.

Typowym wyciągiem jest 64-znakowego ciągu, jak pokazano w poniższym przykładzie:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Aby zapisać w DevKit:

1. W VS Code kliknij pasek stanu, aby wybrać port COM dla DevKit.
  ![wybierz port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. W DevKit, naciśnij **przycisk a**, wypchnij i zwolnij przycisk **Reset** , a następnie zwolnij **przycisk a**. DevKit przechodzi do trybu konfiguracji.

1. Kliknij `F1`, aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Konfigurowanie ustawień urządzenia... > Unikatowy ciąg urządzenia**.
  ![skonfigurować](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Zanotuj wygenerowany ciąg. Będzie on potrzebny do wygenerowania certyfikatu X. 509. Następnie naciśnij `Enter`.
  ![Copy](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Potwierdź, że w witrynie STSAFE pomyślnie skonfigurowano alert.
  ![skonfigurować sukces](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternatywnie można skonfigurować żądanie konfiguracji za pomocą portu szeregowego, korzystając z narzędzi takich jak. Aby to zrobić, postępuj zgodnie z [trybem konfiguracji](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) .

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizowanie globalnego punktu końcowego urządzenia i zakresu identyfikatorów

W polu kod urządzenia należy określić [punkt końcowy aprowizacji urządzeń](/azure/iot-dps/concepts-service#device-provisioning-endpoint) i zakres identyfikatorów, aby zapewnić izolację dzierżawy.

1. W Azure Portal Wybierz okienko **Przegląd** usługi Device Provisioning i zanotuj wartości **globalny punkt końcowy urządzenia** oraz **zakres identyfikatorów** .
  ![globalny punkt końcowy usługi Device Provisioning i zakres identyfikatorów](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otwórz **DevKitDPS. oknach**. Znajdź i Zamień `[Global Device Endpoint]` i `[ID Scope]` z wartościami, które właśnie zanotowano.
  ![punktu końcowego usługi Device Provisioning](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Wypełnij zmienną `registrationId` w kodzie. Dozwolone są tylko kombinacje alfanumeryczne, małe i łącznikowe z maksymalnie 128 znaków. Zanotowano również wartość.
  Identyfikator rejestracji ![](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kliknij `F1`, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Rozpocznie Kompilowanie i przekazywanie kodu do DevKit.
  ![](media/how-to-connect-mxchip-iot-devkit/device-upload.png) przekazywania urządzenia

## <a name="generate-x509-certificate"></a>Generuj certyfikat X. 509

[Mechanizm zaświadczania](/azure/iot-dps/concepts-device#attestation-mechanism) używany przez ten przykład to certyfikat X. 509. Musisz użyć narzędzia do jego wygenerowania.

1. W VS Code kliknij pozycję `F1`, wpisz i wybierz pozycję **Otwórz nowy terminal** , aby otworzyć okno terminalu.

1. Uruchom `dps_cert_gen.exe` w folderze `tool`.

1. Określ skompilowaną lokalizację pliku binarnego jako `..\.build\DevKitDPS`. Następnie **Wklej, jak** i **Identyfikator rejestracji** . 
  ![Generuj](media/how-to-connect-mxchip-iot-devkit/gen-x509.png) X. 509

1. Certyfikat X. 509 `.pem` generowany w tym samym folderze.
  ![pliku X. 509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Tworzenie wpisu rejestracji urządzenia

1. W Azure Portal Otwórz usługę aprowizacji urządzeń, przejdź do sekcji Zarządzanie rejestracjami, a następnie kliknij pozycję **Dodaj rejestrację indywidualną**.
  ![dodać](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png) rejestracji indywidualnej

1. Kliknij pozycję Ikona pliku obok pozycji **certyfikat podstawowy. pem lub plik CER** , aby załadować wygenerowany plik `.pem`.
  ![Przekaż PEM](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Sprawdź, czy DevKit jest zarejestrowany w usłudze Azure IoT Hub

Naciśnij przycisk **Reset** na DevKit. Powinna zostać wyświetlona usługa **DPS połączona!** na ekranie DevKit. Po ponownym uruchomieniu urządzenia są wykonywane następujące akcje:

1. Urządzenie wysyła żądanie rejestracji do usługi Device Provisioning.
1. Usługa Device Provisioning wysyła wyzwanie rejestracji, na które odpowiada urządzenie.
1. Po pomyślnej rejestracji usługa Device Provisioning wyśle identyfikator URI IoT Hub, identyfikator urządzenia i zaszyfrowany klucz z powrotem do urządzenia.
1. Aplikacja kliencka IoT Hub na urządzeniu nawiązuje połączenie z centrum.
1. Po pomyślnym nawiązaniu połączenia z centrum zobaczysz, że urządzenie zostanie wyświetlone w Device Explorer IoT Hub.
  zarejestrowano urządzenie ![](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z tematem [często zadawanych pytań](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)dotyczących usługi IoT DevKit lub uzyskaj dostęp do następujących kanałów, aby uzyskać pomoc techniczną:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się bezpiecznie zarejestrować urządzenie w usłudze Device Provisioning przy użyciu aparatu kompozycji tożsamości urządzenia, aby umożliwić automatyczne rejestrowanie urządzenia w usłudze Azure IoT Hub. 

Podsumowując, wiesz, jak:

> [!div class="checklist"]
> * Skonfiguruj globalny punkt końcowy usługi Device Provisioning na urządzeniu.
> * Użyj unikatowego klucza tajnego urządzenia do wygenerowania certyfikatu X. 509.
> * Zarejestruj pojedyncze urządzenie.
> * Sprawdź, czy urządzenie zostało zarejestrowane.

Dowiedz się [, jak utworzyć i udostępnić symulowane urządzenie](./quick-create-simulated-device.md).

