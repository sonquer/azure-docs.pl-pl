---
title: Opis formatu komunikatów usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — opisuje format i oczekiwaną zawartość komunikatów IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872482"
---
# <a name="create-and-read-iot-hub-messages"></a>Tworzenie i odczytywanie komunikatów usługi IoT Hub

Aby zapewnić bezproblemowe współdziałanie w ramach protokołów, IoT Hub definiuje typowy format komunikatów dla wszystkich protokołów dostępnych dla urządzeń. Ten format komunikatu służy do przesyłania komunikatów przesyłanych [z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) i z [chmury do urządzenia](iot-hub-devguide-messages-c2d.md) . 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementuje obsługę komunikatów z urządzenia do chmury przy użyciu wzorca przesyłania komunikatów przesyłania strumieniowego. Komunikaty z urządzenia do chmury IoT Hub są bardziej podobne do [Event Hubs](/azure/event-hubs/) *zdarzeń* niż [Service Bus](/azure/service-bus-messaging/) *komunikatów* w tym miejscu, że istnieje duża liczba zdarzeń przechodzących przez usługę, które mogą być odczytywane przez wielu czytelników.

Komunikat IoT Hub składa się z:

* Wstępnie zdefiniowany zestaw *Właściwości systemu* , jak pokazano poniżej.

* Zestaw *właściwości aplikacji*. Słownik właściwości ciągów, które aplikacja może definiować i uzyskać do nich dostęp, bez konieczności deserializacji treści komunikatu. IoT Hub nigdy nie modyfikuje tych właściwości.

* Nieprzezroczysta treść binarna.

Nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne ASCII, ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` a także w przypadku wysyłania komunikatów z urządzenia do chmury przy użyciu protokołu HTTPS lub wysyłania komunikatów z chmury do urządzenia.

Obsługa komunikatów przesyłanych z urządzeń do chmury przy użyciu IoT Hub ma następujące cechy:

* Komunikaty z urządzenia do chmury są trwałe i przechowywane w domyślnym punkcie końcowym **komunikatów/zdarzeń** usługi IoT Hub przez maksymalnie siedem dni.

* Komunikaty przesyłane z urządzenia do chmury mogą mieć co najwyżej 256 KB i mogą być pogrupowane w partiach, aby zoptymalizować wysyłanie. Wsady mogą mieć co najwyżej 256 KB.

* IoT Hub nie zezwala na dowolne partycjonowanie. Komunikaty z urządzenia do chmury są partycjonowane na podstawie ich źródłowego **deviceId**.

* Jak wyjaśniono w obszarze [Kontrola dostępu do IoT Hub](iot-hub-devguide-security.md), IoT Hub Włącza uwierzytelnianie na urządzenie i kontrolę dostępu.

* Można oznaczyć wiadomości zawierające informacje, które są umieszczane we właściwościach aplikacji. Aby uzyskać więcej informacji, zobacz [wzbogacanie komunikatów](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat kodowania i dekodowania komunikatów wysyłanych za pomocą różnych protokołów, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Właściwości systemowe komunikatów **D2C** IoT Hub

| Właściwość | Opis  |Jesteś w trakcie, aby użytkownik miał tabelę?|Słowo kluczowe dla </br>zapytanie routingu|
| --- | --- | --- | --- |
| Identyfikator komunikatu |Identyfikator użytkownika-settable dla wiadomości używany na potrzeby wzorców odpowiedzi na żądanie. Format: Ciąg uwzględniający wielkość liter (do 128 znaków) ASCII 7-bitowe znaki alfanumeryczne + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | Tak | messageId |
| iothub — enqueuedtime |Data i godzina odebrania komunikatu [z urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) przez IoT Hub. | Nie | enqueuedTime |
| Identyfikator użytkownika |Identyfikator używany do określania źródła komunikatów. Komunikaty generowane przez IoT Hub są ustawiane na `{iot hub name}`. | Tak | userId |
| iothub-Connection-ID urządzenia |Identyfikator ustawiony przez IoT Hub w komunikatach przesyłanych z urządzenia do chmury. Zawiera identyfikator **deviceId** urządzenia, które wysłało wiadomość. | Nie | connectionDeviceId |
| iothub-Connection-module-ID |Identyfikator ustawiony przez IoT Hub w komunikatach przesyłanych z urządzenia do chmury. Zawiera **moduleId** urządzenia, które wysłało wiadomość. | Nie | connectionModuleId |
| iothub-Connection-auth-Generation-ID |Identyfikator ustawiony przez IoT Hub w komunikatach przesyłanych z urządzenia do chmury. Zawiera **connectionDeviceGenerationId** (zgodnie z właściwościami [tożsamości urządzenia](iot-hub-devguide-identity-registry.md#device-identity-properties)) urządzenia, które wysłało komunikat. | Nie |connectionDeviceGenerationId |
| iothub-Connection-auth-Metoda |Metoda uwierzytelniania ustawiona przez IoT Hub w komunikatach z urządzenia do chmury. Ta właściwość zawiera informacje na temat metody uwierzytelniania używanej do uwierzytelniania urządzenia wysyłającego wiadomość.| Nie | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Właściwości systemowe komunikatów **C2D** IoT Hub

| Właściwość | Opis  |Jesteś w trakcie, aby użytkownik miał tabelę?|
| --- | --- | --- |
| Identyfikator komunikatu |Identyfikator użytkownika-settable dla wiadomości używany na potrzeby wzorców odpowiedzi na żądanie. Format: Ciąg uwzględniający wielkość liter (do 128 znaków) ASCII 7-bitowe znaki alfanumeryczne + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |Tak|
| Numer sekwencyjny |Liczba (unikatowa dla kolejki urządzenia) przypisana przez IoT Hub do poszczególnych komunikatów z chmury do urządzenia. |Nie|
| to |Lokalizacja docelowa określona w komunikatach [z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) . |Nie|
| absolute-expiry-time |Data i godzina wygaśnięcia komunikatu. |Nie|   |
| Identyfikator korelacji |Właściwość ciągu w komunikacie odpowiedzi, który zwykle zawiera wartość MessageId żądania w wzorcach żądania-odpowiedzi. |Tak|
| Identyfikator użytkownika |Identyfikator używany do określania źródła komunikatów. Komunikaty generowane przez IoT Hub są ustawiane na `{iot hub name}`. |Tak|
| iothub — ACK |Generator komunikatów opinii. Ta właściwość jest używana w komunikatach z chmury do urządzeń w celu żądania IoT Hub generowania komunikatów zwrotnych w wyniku użycia wiadomości przez urządzenie. Możliwe wartości: **Brak** (wartość domyślna): nie Wygenerowano żadnego komunikatuz opiniami pozytywnymi: otrzymasz wiadomość z informacją o opinii, jeśli wiadomość została ukończona, **wartość ujemna**: otrzymasz wiadomość z informacją o opinii, jeśli wiadomość wygasła (lub maksymalna liczba dostaw została osiągnięto) bez ukończenia przez urządzenie lub **pełne**: dodatnie i ujemne. |Tak|

## <a name="message-size"></a>Rozmiar komunikatu

IoT Hub mierzy rozmiar komunikatu w metodzie niezależny od, biorąc pod uwagę tylko rzeczywisty ładunek. Rozmiar w bajtach jest obliczany jako suma następujących wartości:

* Rozmiar treści w bajtach.
* Rozmiar w bajtach wszystkich wartości właściwości systemu komunikatów.
* Rozmiar w bajtach wszystkich nazw i wartości właściwości użytkownika.

Nazwy i wartości właściwości są ograniczone do znaków ASCII, więc długość ciągów jest równa rozmiarowi w bajtach.

## <a name="anti-spoofing-properties"></a>Właściwości chroniące przed fałszowaniem

Aby uniknąć fałszowania urządzenia w komunikatach przesyłanych z urządzeń do chmury, IoT Hub sygnatury wszystkich komunikatów o następujących właściwościach:

* **iothub-connection-device-id**
* **iothub-Connection-auth-Generation-ID**
* **iothub-Connection-auth-Metoda**

Pierwsze dwa zawierają **deviceId** i **generationId** urządzenia pochodzącego, zgodnie z [właściwościami tożsamości urządzenia](iot-hub-devguide-identity-registry.md#device-identity-properties).

Właściwość **iothub-Connection-auth-Method** zawiera serializowany obiekt JSON z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat limitów rozmiaru komunikatów w IoT Hub, zobacz [IoT Hub przydziały i ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md).

* Aby dowiedzieć się, jak tworzyć i odczytywać IoT Hub wiadomości w różnych językach programowania, zobacz [Przewodniki Szybki Start](quickstart-send-telemetry-node.md).
