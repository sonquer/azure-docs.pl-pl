---
title: Rejestrowanie urządzeń X. 509 w usłudze Azure Device Provisioning przy użyciu środowiska Node. js
description: W tym przewodniku Szybki start używane są rejestracje grupowe. W tym przewodniku szybki start nastąpi zarejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu zestawu SDK usługi Node. js
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 4bb3af4ddad7e40cbf7edd58cf5899ced2757512
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548802"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Przewodnik Szybki start: rejestrowanie urządzeń X.509 w usłudze Device Provisioning Service przy użyciu środowiska Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ten przewodnik szybki Start przedstawia sposób użycia środowiska Node.js w celu programowego utworzenia [grupy rejestracji](concepts-service.md#enrollment-group), która będzie używać certyfikatów X.509 pośredniego lub głównego urzędu certyfikacji. Grupa rejestracji jest tworzona przy użyciu [zestawu SDK usługi IoT dla środowiska Node.js](https://github.com/Azure/azure-iot-sdk-node) oraz przykładowej aplikacji Node.js. Grupa rejestracji steruje dostępem do usługi aprowizacji dla urządzeń, które mają wspólny certyfikat podpisywania w swoim łańcuchu certyfikatów. Aby dowiedzieć się więcej, zobacz [Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Aby uzyskać więcej informacji na temat używania infrastruktury kluczy publicznych opartej na certyfikatach X.509 z usługą Azure IoT Hub i Device Provisioning, zobacz [Omówienie zabezpieczeń certyfikatu X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Ten przewodnik Szybki start zakłada, że utworzono już wystąpienie usług IoT Hub i Device Provisioning Service. Jeżeli nie utworzono jeszcze tych zasobów, ukończ przewodnik Szybki start [Konfigurowanie usługi IoT Hub Device Provisioning Service przy użyciu witryny Azure Portal](./quick-setup-auto-provision.md) przed dalszą lekturą tego artykułu.

Mimo że kroki opisane w tym artykule działają zarówno na maszynach z systemem Windows, jak i Linux, ten artykuł został opracowany dla maszyny deweloperskiej z systemem Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).
- Zainstaluj oprogramowanie [Git](https://git-scm.com/download/).


## <a name="prepare-test-certificates"></a>Przygotowywanie certyfikatów testowych

Na potrzeby tego przewodnika Szybki start musisz mieć plik pem lub cer, który zawiera publiczną część certyfikatu X.509 pośredniego lub głównego urzędu certyfikacji. Ten certyfikat musi zostać przekazany do usługi aprowizacji i zweryfikowany przez usługę. 

[Zestaw SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) zawiera narzędzia testowe, które mogą ułatwić tworzenie łańcucha certyfikatów X.509, przekazywanie certyfikatu głównego lub pośredniego z tego łańcucha oraz wykonywanie operacji dowodu posiadania w usłudze w celu weryfikacji certyfikatu. Certyfikaty utworzone za pomocą narzędzi zestawu SDK są przeznaczone tylko do użycia na potrzeby **testowania podczas programowania**. Te certyfikaty **nie mogą być stosowane w produkcji**. Zawierają one zapisane na stałe hasła („1234”), które wygasają po 30 dniach. Informacje na temat uzyskiwania certyfikatów odpowiednich do użycia w produkcji znajdują się w artykule [Jak uzyskać certyfikat X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) w dokumentacji usługi Azure IoT Hub.

Aby użyć tych narzędzi testowych do wygenerowania certyfikatów, wykonaj następujące kroki:
 
1. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK języka C usługi Azure IoT.

2. Otwórz wiersz polecenia lub powłokę Git Bash i przejdź do folderu roboczego na swojej maszynie. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości parametru `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

   Narzędzia testowe znajdują się w folderze *azure-iot-sdk-c/tools/CACertificates* sklonowanego repozytorium.

3. Postępuj zgodnie z instrukcjami opisanymi w artykule [Zarządzanie certyfikatami testowymi urzędu certyfikacji na potrzeby przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 



## <a name="create-the-enrollment-group-sample"></a>Tworzenie przykładowej grupy rejestracji 

 
1. Z poziomu okna polecenia w folderze roboczym uruchom następujące polecenie:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Za pomocą edytora tekstu utwórz plik **create_enrollment_group.js** w folderze roboczym. Dodaj następujący kod do pliku i zapisz go:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ```

## <a name="run-the-enrollment-group-sample"></a>Uruchamianie przykładowej grupy rejestracji
 
1. Do uruchomienia przykładu będą potrzebne parametry połączenia usługi aprowizacji. 
    1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę Device Provisioning. 
    2. Kliknij pozycję **zasady dostępu współdzielonego**, a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć jego właściwości. W oknie **Zasady dostępu** skopiuj i zanotuj parametry połączenia klucza podstawowego. 

       ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Jak określono w [przygotowaniu certyfikatów testowych](quick-enroll-device-x509-node.md#prepare-test-certificates), potrzebny będzie także plik pem zawierający certyfikat X.509 pośredniego lub głównego urzędu certyfikacji, który został wcześniej przesłany do usługi aprowizacji i przez nią zweryfikowany. Aby sprawdzić, czy certyfikat został przekazany i zweryfikowany, na stronie podsumowania usługi Device Provisioning w obszarze Azure Portal wybierz pozycję **Certyfikaty**. Znajdź certyfikat, którego chcesz używać na potrzeby rejestracji grupowej, i upewnij się, że jego stan ma wartość *zweryfikowany*.

    ![Zweryfikowany certyfikat w portalu](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Aby utworzyć grupę rejestracji dla certyfikatu, uruchom następujące polecenie (z uwzględnieniem cudzysłowów wokół argumentów polecenia):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Po pomyślnym utworzeniu nowej grupy rejestracji w oknie polecenia zostaną wyświetlone jej właściwości.

    ![Właściwości rejestracji w danych wyjściowych polecenia](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Upewnij się, że grupa rejestracji została utworzona. W witrynie Azure Portal w bloku podsumowania usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Grupy rejestracji** i upewnij się, że obecny jest nowy wpis rejestracji (*pierwszy*).

    ![Właściwości rejestracji w portalu](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz Eksplorowanie przykładów usługi Node. js, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start.
 
1. Zamknij okno danych wyjściowych przykładu środowiska Node.js na swojej maszynie.
2. Przejdź do usługi Device Provisioning w Azure Portal wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **grupy rejestracji** . Zaznacz pole wyboru obok *nazwy grupy* dla urządzeń X. 509 zarejestrowanych w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.    
3. W obszarze Azure Portal wybierz pozycję **Certyfikaty**, wybierz certyfikat, który został przekazany do tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okna **Szczegóły certyfikatu** .  
 
## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono rejestrację grupy dla certyfikatu pośredniego lub głównego urzędu certyfikacji X. 509 przy użyciu IoT Hub Device Provisioning Service platformy Azure. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 

Zobacz również [przykład aprowizacji urządzenia Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
 
> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
