---
title: Pozyskiwanie historycznych danych telemetrycznych
description: W tym artykule opisano sposób pozyskiwania historycznych danych telemetrycznych.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0d220d1d88d9d761d9f0eba6187abefb372681be
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131902"
---
# <a name="ingest-historical-telemetry-data"></a>Pozyskiwanie historycznych danych telemetrycznych

W tym artykule opisano sposób pozyskiwania historycznych danych czujników do usługi Azure FarmBeats.

Pozyskiwanie danych historycznych z zasobów Internet rzeczy (IoT), takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzysz metadane dla urządzeń i czujników, a następnie pozyskasz dane historyczne do FarmBeats w postaci kanonicznej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed przejściem do tego artykułu upewnij się, że zainstalowano FarmBeats i zebrano dane historyczne z urządzeń IoT.
Należy również włączyć dostęp partnera, jak wspomniano w poniższych krokach.

## <a name="enable-partner-access"></a>Włącz dostęp partnera

Musisz włączyć integrację partnera z wystąpieniem usługi Azure FarmBeats. W tym kroku jest tworzony klient, który ma dostęp do wystąpienia usługi Azure FarmBeats jako partner urządzenia i zawiera następujące wartości, które są wymagane w kolejnych krokach:

- Punkt końcowy interfejsu API: jest to adres URL Datahub, na przykład https://\<Datahub >. azurewebsites. NET.
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta
- Parametry połączenia EventHub

Wykonaj następujące kroki.

>[!NOTE]
> Musisz być administratorem, aby wykonać następujące czynności.

1. Pobierz [plik zip](https://aka.ms/farmbeatspartnerscriptv2)i wyodrębnij go na dysk lokalny. Plik zip będzie zawierać jeden plik.
2. Zaloguj się do https://portal.azure.com/ i przejdź do pozycji Azure Active Directory-> rejestracji aplikacji

3. Kliknij rejestrację aplikacji, która została utworzona w ramach wdrożenia FarmBeats. Ma taką samą nazwę jak FarmBeats Datahub.

4. Kliknij pozycję "Uwidacznianie interfejsu API" — > kliknij pozycję "Dodaj aplikację kliencką" i wprowadź **04b07795-8ddb-461a-bbee-02f9e1bf7b46** i zaznacz pole wyboru Autoryzuj zakres. Zapewni to dostęp do interfejsu wiersza polecenia platformy Azure (Cloud Shell), aby wykonać poniższe kroki.

5. Otwórz usługę Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu Azure Portal.

    ![Azure Portal pasek narzędzi](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Upewnij się, że środowisko jest ustawione na **PowerShell**. Domyślnie jest ono ustawione na bash.

    ![Ustawienie paska narzędzi programu PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Przekaż plik z kroku 1 w wystąpieniu Cloud Shell.

    ![Przekaż przycisk paska narzędzi](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Przejdź do katalogu, w którym plik został przekazany. Domyślnie pliki są przekazywane do katalogu macierzystego w ramach nazwy użytkownika.

9. Uruchom następujący skrypt. Skrypt pyta o identyfikator dzierżawy, który można uzyskać ze strony Przegląd Azure Active Directory->.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Postępuj zgodnie z instrukcjami na ekranie, aby przechwycić wartości dla **punktów końcowych interfejsu API**, **identyfikatora dzierżawy**, **identyfikatora klienta**, **klucza tajnego klienta**i **parametrów połączenia centrum EventHub**.
## <a name="create-device-or-sensor-metadata"></a>Tworzenie metadanych urządzenia lub czujnika

 Teraz, gdy masz wymagane poświadczenia, możesz zdefiniować urządzenie i czujniki. W tym celu Utwórz metadane, wywołując interfejsy API FarmBeats. Należy pamiętać, że należy wywołać interfejsy API jako aplikację kliencką utworzoną w powyższej sekcji

 FarmBeats Datahub zawiera następujące interfejsy API, które umożliwiają tworzenie metadanych urządzenia lub czujnika i zarządzanie nimi. Należy pamiętać, że jako partner masz dostęp tylko do odczytu, tworzenia i aktualizowania metadanych; **Usunięcie nie jest dozwolone przez partnera.**

- /**DeviceModel**: DeviceModel odpowiada metadanych urządzenia, takich jak producent i typ urządzenia, który jest bramą lub węzłem.
- **urządzenie**/: urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie.
- /**SensorModel**: SensorModel odpowiada metadanych czujnika, takich jak producent, typ czujnika, który jest analogowy lub cyfrowy i pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
- **czujnik**/: czujnik odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.  


|        DeviceModel   |  Sugestie   |
| ------- | -------             |
|     Typ (węzeł, brama)        |          Typ węzła urządzenia lub bramy      |
|          Producent            |         Nazwa producenta    |
|  ProductCode                    |  Kod produktu urządzenia lub nazwa modelu lub numer. Na przykład EnviroMonitor # 6800.  |
|            Porty          |     Nazwa i typ portu, które są cyfrowe lub analogowe.
|     Name (Nazwa)                 |  Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu.
      Opis     | Podaj znaczący opis modelu.
|    Właściwości          |    Dodatkowe właściwości producenta.   |
|    **Pliku**             |                      |
|   DeviceModelId     |     Identyfikator skojarzonego modelu urządzenia.  |
|  HardwareId          | Unikatowy identyfikator urządzenia, na przykład adres MAC.
|  ReportingInterval        |   Interwał raportowania (w sekundach).
|  Lokalizacja            |  Urządzenia Latitude (-90 do + 90), długości geograficznej (-180 do 180) i podniesienia uprawnień (w metrach).   
|ParentDeviceId       |    Identyfikator urządzenia nadrzędnego, z którym jest połączone to urządzenie. Na przykład węzeł połączony z bramą. Węzeł ma parentDeviceId jako bramę.  |
|    Name (Nazwa)            | Nazwa identyfikująca zasób. Partnerzy urządzeń muszą wysłać nazwę zgodną z nazwą urządzenia po stronie partnera. Jeśli nazwa urządzenia partnerskiego jest zdefiniowana przez użytkownika, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do FarmBeats.|
|     Opis       |      Podaj znaczący opis. |
|     Właściwości    |  Dodatkowe właściwości producenta.
|     **SensorModel**        |          |
|       Typ (analogowy, cyfrowy)          |      Typ czujnika, niezależnie od tego, czy jest on analogowy, czy cyfrowy.       |
|          Producent            |       Producent czujnika.     |
|     ProductCode| Kod produktu lub nazwa modelu lub numer. Na przykład RS-CO2-N01. |
|       Nazwa > SensorMeasures       | Nazwa miary czujnika. Obsługiwane są tylko małe litery. W przypadku pomiarów z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być zgodna z danymi telemetrycznymi.  |
|          SensorMeasures > DataType       |Typ danych telemetrii. Obecnie jest obsługiwana Podwójna precyzja.|
|    Typ > SensorMeasures    |Typ pomiaru danych telemetrii czujnika. Typy zdefiniowane przez system to AmbientTemperature, CO2, Głębokość, ElectricalConductivity, LeafWetness, długość, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType.|
|        Jednostka > SensorMeasures              | Jednostka danych telemetrii czujnika. Jednostki zdefiniowane przez system to nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetr, centymetr, metr, cal, stopy, kilometry, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, aby dodać więcej, zapoznaj się z interfejsem API programu/ExtendedType.|
|    SensorMeasures > agregacji    |  Wartości mogą być brak, Average, maksimum, minimum lub StandardDeviation.  |
|          Name (Nazwa)            | Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu.  |
|    Opis        | Podaj znaczący opis modelu.  |
|   Właściwości       |  Dodatkowe właściwości producenta.  |
|    **Czujnik**      |          |
| HardwareId          |   Unikatowy identyfikator czujnika określonego przez producenta. |
|  SensorModelId     |    Identyfikator skojarzonego modelu czujnika.   |
| Lokalizacja          |  Czujnik Latitude (-90 do + 90), Długość geograficzna (-180 do 180) i podniesienie (w metrach).|
|   Nazwa > portu        |  Nazwa i typ portu, z którym jest połączony czujnik na urządzeniu. Musi to być taka sama nazwa, jak zdefiniowana w modelu urządzenia. |
|    Identyfikator  |    Identyfikator urządzenia, z którym jest połączony czujnik.     |
| Name (Nazwa)            |   Nazwa identyfikująca zasób. Na przykład nazwa czujnika lub nazwa produktu i numer modelu lub kod produktu.|
|    Opis      | Podaj znaczący opis. |
|    Właściwości        |Dodatkowe właściwości producenta. |

Aby uzyskać więcej informacji na temat obiektów, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Żądanie interfejsu API do tworzenia metadanych

Aby wykonać żądanie interfejsu API, należy połączyć metodę HTTP (POST), adres URL usługi interfejsu API i identyfikator URI zasobu do wysłania zapytania, przesłać dane do żądania, utworzyć lub usunąć żądanie. Następnie dodasz co najmniej jeden nagłówek żądania HTTP. Adres URL usługi interfejsu API to punkt końcowy interfejsu API, czyli adres URL Datahub (https://\<yourdatahub >. azurewebsites. NET).  

### <a name="authentication"></a>Uwierzytelnianie

FarmBeats Datahub używa uwierzytelniania okaziciela, który wymaga następujących poświadczeń, które zostały wygenerowane w poprzedniej sekcji:

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy

Przy użyciu tych poświadczeń wywołujący może zażądać tokenu dostępu. Token musi być wysyłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, w następujący sposób:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Następujący przykładowy kod w języku Python daje token dostępu, który może być używany do kolejnych wywołań interfejsu API do FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**Nagłówki żądań HTTP**

Poniżej znajdują się najczęstsze nagłówki żądań, które muszą zostać określone podczas wywołania interfejsu API FarmBeats Datahub:

- **Content-Type**: Application/JSON
- **Autoryzacja**: < tokenu dostępu >
- **Akceptuj**: Application/JSON

### <a name="input-payload-to-create-metadata"></a>Ładunek wejściowy do tworzenia metadanych

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Urządzenie

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Czujnik

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Poniższe przykładowe żądanie tworzy urządzenie. To żądanie ma wejściowy kod JSON jako ładunek z treścią żądania.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Należy zachować identyfikatory, aby wysłać odpowiednie komunikaty telemetryczne.

### <a name="send-telemetry"></a>Wysyłanie danych telemetrycznych

Teraz, po utworzeniu urządzeń i czujników w FarmBeats, można wysłać skojarzone komunikaty telemetryczne.

### <a name="create-a-telemetry-client"></a>Tworzenie klienta telemetrii

Musisz wysłać dane telemetryczne do usługi Azure Event Hubs w celu przetworzenia. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji. Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat wysyłania telemetrii, zobacz [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Wyślij komunikat telemetrii jako klienta

Po ustanowieniu połączenia jako klienta Event Hubs można wysyłać komunikaty do centrum zdarzeń jako plik JSON.

Oto przykładowy kod w języku Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Przekonwertuj historyczny format danych z czujnika na format kanoniczny, który jest rozpoznawany przez platformę Azure FarmBeats. Format komunikatu kanonicznego jest następujący:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Po dodaniu odpowiednich urządzeń i czujników należy uzyskać identyfikator urządzenia i identyfikator czujnika w komunikacie telemetrii zgodnie z opisem w poprzedniej sekcji.

Oto przykład komunikatu telemetrii:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po pozyskaniu danych historycznych/przesyłanych strumieniowo z czujników

**Objaw**: wdrożono urządzenia lub czujniki, a w usłudze EventHub zostały utworzone urządzenia/czujniki dotyczące FarmBeats i pozyskiwanej telemetrii, ale nie można uzyskać ani wyświetlić danych telemetrycznych w FarmBeats.

**Działanie naprawcze**:

1. Upewnij się, że pomyślnie ukończono rejestrację partnera — możesz to sprawdzić, przechodząc do datahub Swagger, przejdź do interfejsu API/partner, a następnie wybierz pozycję Pobierz i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj [kroki opisane tutaj](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) , aby dodać partnera.
2. Upewnij się, że zostały utworzone metadane (DeviceModel, Device, SensorModel, sensor) przy użyciu poświadczeń klienta partnera.
3. Upewnij się, że użyto poprawnego formatu komunikatów telemetrycznych (jak określono poniżej):

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o szczegółach integracji opartych na interfejsie API REST, zobacz [interfejs API REST](rest-api-in-azure-farmbeats.md).
