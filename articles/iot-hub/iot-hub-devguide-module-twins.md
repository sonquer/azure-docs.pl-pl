---
title: Opis usługi Azure IoT Hub module bliźniaczych reprezentacji | Microsoft Docs
description: Przewodnik dla deweloperów — Używanie modułu bliźniaczych reprezentacji do synchronizowania danych stanu i konfiguracji między IoT Hub i urządzeniami
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 064bfd7a51f3ccb0252f37fbaa11ebc122a4b97f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807429"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Zrozumienie i Używanie modułu bliźniaczych reprezentacji w IoT Hub

W tym artykule przyjęto założenie, że zapoznaj [się z artykułem omówienie i użycie bliźniaczych reprezentacji urządzeń w IoT Hub](iot-hub-devguide-device-twins.md) . W IoT Hub w obszarze każdej tożsamości urządzenia można utworzyć maksymalnie 20 tożsamości modułów. Każda tożsamość modułu niejawnie generuje sznurek modułu. Podobnie jak w przypadku urządzeń bliźniaczych reprezentacji, moduł bliźniaczych reprezentacji to dokumenty JSON, które przechowują informacje o stanie modułu, w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub obsługuje sznurki modułu dla każdego modułu, z którym nawiążesz połączenie IoT Hub. 

Po stronie urządzenia zestawy SDK urządzeń IoT Hub umożliwiają tworzenie modułów, w których każdy z nich otwiera niezależne połączenie z IoT Hub. Ta funkcja umożliwia korzystanie z oddzielnych obszarów nazw dla różnych składników na urządzeniu. Na przykład masz maszynę do sprzedaży z trzema różnymi czujnikami. Każdy czujnik jest kontrolowany przez różne działy w firmie. Moduł można utworzyć dla każdego czujnika. W ten sposób każdy dział może jedynie wysyłać zadania lub bezpośrednie metody do czujnika, który kontroluje, unikając konfliktów i błędów użytkowników.

 Sznury i moduły modułu zapewniają takie same możliwości, jak tożsamość urządzenia i sznurki urządzeń, ale z większą szczegółowością. Ten bardziej szczegółowy stopień szczegółowości umożliwia urządzeniom opartym na systemie operacyjnym lub urządzeniom oprogramowania układowego zarządzanie wieloma składnikami, a także izolowanie konfiguracji i warunków dla każdego z tych składników. Tożsamość modułu i bliźniaczych reprezentacji modułów umożliwiają zarządzanie rozdzieleniem problemów podczas pracy z urządzeniami IoT, które mają modularne składniki oprogramowania. Zachęcamy do obsługi wszystkich funkcji przędzy dla urządzeń na poziomie niedostępności modułów przez dwuosiowy moduł. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura sznurka modułu: *Tagi*, *żądane* i *raportowane właściwości*.
* Operacje, które moduły i zaplecza mogą wykonywać na module bliźniaczych reprezentacji.

Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) , aby uzyskać wskazówki dotyczące korzystania z raportowanych właściwości, komunikatów przesyłanych z urządzeń do chmury lub przekazywania plików.

Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między chmurą i urządzeniami](iot-hub-devguide-c2d-guidance.md) , aby uzyskać wskazówki dotyczące używania żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia.

## <a name="module-twins"></a>Bliźniaczych reprezentacjach modułów

Moduł bliźniaczych reprezentacji magazynu informacje dotyczące modułu, które:

* Moduły na urządzeniu i IoT Hub mogą służyć do synchronizowania warunków i konfiguracji modułu.

* Zaplecze rozwiązania może użyć do wykonywania zapytań i docelowych operacji długotrwałych.

Cykl życia wieloosiowego modułu jest połączony z odpowiadającą [tożsamością modułu](iot-hub-devguide-identity-registry.md). Moduły bliźniaczych reprezentacji są niejawnie tworzone i usuwane, gdy tożsamość modułu jest tworzona lub usuwana w IoT Hub.

Sznurki modułu jest dokumentem JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, z której zaplecze rozwiązania może odczytywać i zapisywać dane. Tagi nie są widoczne dla modułów na urządzeniu. Tagi są ustawione na potrzeby wykonywania zapytań.

* **Żądane właściwości**. Używane wraz z raportowanymi właściwościami do synchronizacji konfiguracji lub warunków modułu. Zaplecze rozwiązania może ustawić żądane właściwości, a aplikacja modułu może je odczytać. Aplikacja modułu może również odbierać powiadomienia o zmianach w odpowiednich właściwościach.

* **Raportowane właściwości**. Używane wraz z pożądanymi właściwościami do synchronizowania konfiguracji lub warunków modułu. Aplikacja modułu może ustawiać raportowane właściwości, a zaplecze rozwiązania może odczytywać i wysyłać do nich zapytania.

* **Właściwości tożsamości modułu**. Katalog główny dokumentu JSON modułu z sznurem zawiera właściwości tylko do odczytu z odpowiedniej tożsamości modułu przechowywanej w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md).

![Reprezentacja architektury urządzeń z architekturą](./media/iot-hub-devguide-device-twins/module-twin.jpg)

W poniższym przykładzie przedstawiono dokument JSON:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

W obiekcie głównym są właściwości Identity modułu i obiekty kontenera dla `tags` i obie `reported` i `desired` właściwości. Kontener `properties` zawiera tylko elementy tylko do odczytu (`$metadata`, `$etag`i `$version`) opisane w [metadanych wieloosiowych modułu](iot-hub-devguide-module-twins.md#module-twin-metadata) i [optymistycznych sekcjach współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Przykład raportowanej właściwości

W poprzednim przykładzie sznurek modułu zawiera właściwość `batteryLevel`, która jest raportowana przez aplikację modułu. Ta właściwość umożliwia wykonywanie zapytań i operowanie na modułach na podstawie ostatniego zgłoszonego poziomu baterii. Inne przykłady obejmują możliwości modułu raportowania aplikacji modułu lub opcje łączności.

> [!NOTE]
> Raportowane właściwości upraszczają scenariusze, w których zaplecze rozwiązania jest zainteresowane ostatnią znaną wartością właściwości. Użyj [komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) , jeśli zaplecze rozwiązania musi przetworzyć dane telemetryczne modułu w postaci sekwencji zdarzeń z sygnaturami czasowymi, takich jak szeregi czasowe.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie pożądana i zgłoszone właściwości w module `telemetryConfig` są używane przez zaplecze rozwiązania i aplikację modułu w celu zsynchronizowania konfiguracji telemetrii dla tego modułu. Na przykład:

1. Zaplecze rozwiązania ustawia odpowiednią właściwość o żądaną wartość konfiguracji. Poniżej znajduje się część dokumentu z żądanym zestawem właściwości:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Aplikacja modułu zostanie powiadomiona o zmianie natychmiast po powiązaniu połączenia lub przy pierwszym ponownym połączeniu. Aplikacja modułu raportuje zaktualizowaną konfigurację (lub warunek błędu przy użyciu właściwości `status`). Oto część raportowanych właściwości:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Zaplecze rozwiązania może śledzić wyniki operacji konfiguracji w wielu modułach, wykonując [zapytania dotyczące](iot-hub-devguide-query-language.md) modułu bliźniaczych reprezentacji.

> [!NOTE]
> Poprzednie fragmenty kodu są przykładami, zoptymalizowane pod kątem czytelności, w jednym ze sposobów kodowania konfiguracji modułu i jego stanu. IoT Hub nie nakłada określonego schematu dla odpowiedniej sieci i zgłosił właściwości w module bliźniaczych reprezentacji.
> 
> 

## <a name="back-end-operations"></a>Operacje zaplecza
Zaplecze rozwiązania działa na sznurze module przy użyciu następujących operacji niepodzielnych udostępnianych za pośrednictwem protokołu HTTPS:

* **Pobierz sznurki modułu według identyfikatora**. Ta operacja zwraca dokument z sznurem modułu, w tym Tagi i odpowiednie i zgłoszone właściwości systemu.

* **Częściowo Aktualizuj sznurek modułu**. Ta operacja umożliwia zaplecze rozwiązania częściowo zaktualizować Tagi lub żądane właściwości w postaci sznurka modułu. Aktualizacja częściowa jest wyrażona jako dokument JSON, który dodaje lub aktualizuje każdą właściwość. Właściwości ustawione na `null` są usuwane. Poniższy przykład tworzy nową pożądaną właściwość o wartości `{"newProperty": "newValue"}`, zastępuje istniejącą wartość `existingProperty` z `"otherNewValue"`i usuwa `otherOldProperty`. Nie wprowadzono żadnych innych zmian do istniejących żądanych właściwości lub tagów:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Zastąp żądane właściwości**. Ta operacja umożliwia zapleczu rozwiązania całkowicie zastępowanie wszystkich istniejących żądanych właściwości i zastąpienie nowego dokumentu JSON dla `properties/desired`.

* **Zamień Tagi**. Ta operacja umożliwia zaplecze rozwiązania całkowicie zastępowanie wszystkich istniejących tagów i zastąpienie nowego dokumentu JSON dla `tags`.

* **Otrzymywanie powiadomień bliźniaczych**. Ta operacja umożliwia zaplecze rozwiązania powiadamianie o modyfikacji dwuosiowej. W tym celu Twoje rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *twinChangeEvents*. Domyślnie żadne powiadomienia o przędze nie są wysyłane, czyli nie ma takich tras. Jeśli współczynnik zmiany jest zbyt wysoki lub z innych przyczyn, takich jak błędy wewnętrzne, IoT Hub może wysłać tylko jedno powiadomienie zawierające wszystkie zmiany. W związku z tym, jeśli aplikacja wymaga niezawodnej inspekcji i rejestrowania wszystkich stanów pośrednich, należy użyć komunikatów z urządzenia do chmury. Wiadomość z powiadomieniem o przędzy obejmuje właściwości i treść.

  - Właściwości

    | Nazwa | Wartość |
    | --- | --- |
    Typ $content | application/json |
    $iothub-enqueuedtime |  Godzina wysłania powiadomienia |
    $iothub-message-source | twinChangeEvents |
    $content — kodowanie | UTF-8 |
    deviceId | Identyfikator urządzenia |
    moduleId | Identyfikator modułu |
    hubName | Nazwa IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) sygnatura czasowa operacji |
    iothub-message-schema | twinChangeNotification |
    opType | "replaceTwin" lub "updateTwin" |

    Właściwości systemu komunikatów są poprzedzone symbolem `$`.

  - Treść
        
    Ta sekcja zawiera wszystkie zmiany w formacie JSON. Używa tego samego formatu co poprawka, z różnicą, że może zawierać wszystkie sekcje sznurów: Tagi, właściwości. raportowane, właściwości. wymagane i że zawiera elementy "$metadata". Na przykład:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Wszystkie poprzednie operacje obsługują [optymistyczną współbieżność](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają uprawnienia **serviceconnect** , zgodnie z definicją w artykule [Kontrola dostępu do IoT Hub](iot-hub-devguide-security.md) artykułu.

Oprócz tych operacji zaplecze rozwiązania może wysyłać zapytania do modułu bliźniaczych reprezentacji przy użyciu [języka zapytań IoT Hub](iot-hub-devguide-query-language.md), takich jak SQL.

## <a name="module-operations"></a>Operacje modułu

Aplikacja modułu działa na sznurze module przy użyciu następujących operacji niepodzielnych:

* **Pobierz sznurki modułu**. Ta operacja zwraca dokument sznurka modułu (w tym Tagi i żądane i zgłoszone właściwości systemu) dla aktualnie połączonego modułu.

* **Częściowo Zaktualizowano raportowane właściwości**. Ta operacja włącza częściową aktualizację zgłoszonych właściwości aktualnie połączonego modułu. Ta operacja używa tego samego formatu aktualizacji JSON, którego zaplecze rozwiązanie używa do częściowej aktualizacji żądanych właściwości.

* **Obserwuj żądane właściwości**. Aktualnie połączony moduł może otrzymywać powiadomienia o aktualizacjach odpowiednich właściwości, gdy wystąpią. Moduł otrzymuje tę samą formę aktualizacji (częściowej lub pełnej) wykonywanej przez zaplecze rozwiązania.

Wszystkie poprzednie operacje wymagają uprawnienia **ModuleConnect** , zgodnie z definicją w artykule [kontrola dostępu do IoT Hub](iot-hub-devguide-security.md) artykułu.

[Zestawy SDK urządzeń Azure IoT](iot-hub-devguide-sdks.md) ułatwiają używanie powyższych operacji z wielu języków i platform.

## <a name="tags-and-properties-format"></a>Formatowanie tagów i właściwości

Tagi, żądane właściwości i raportowane właściwości są obiektami JSON z następującymi ograniczeniami:

* Wszystkie klucze w obiektach JSON uwzględniają wielkość liter 64 bajtów UTF-8 UNICODE. Dozwolone znaki wykluczają znaki kontrolne UNICODE (segmenty C0 i C1) oraz `.`, SP i `$`.

* Wszystkie wartości w obiektach JSON mogą mieć następujące typy JSON: Boolean, Number, String, Object. Tablice są niedozwolone. Maksymalna wartość dla liczb całkowitych to 4503599627370495, a minimalna wartość dla liczb całkowitych to-4503599627370496.

* Wszystkie obiekty JSON w tagach, żądanych i raportowanych właściwościach mogą mieć maksymalną głębokość wynoszącą 5. Na przykład następujący obiekt jest prawidłowy:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Wszystkie wartości ciągu mogą mieć długość maksymalnie 512 bajtów.

## <a name="module-twin-size"></a>Rozmiar sznurka modułu

IoT Hub wymusza limit rozmiaru 8 KB dla wartości `tags`, a rozmiar 32 KB zostanie ograniczony dla wartości `properties/desired` i `properties/reported`. Te sumy zawierają wyłącznie elementy tylko do odczytu.

Rozmiar jest obliczany przez liczenie wszystkich znaków, z wyłączeniem znaków kontrolnych UNICODE (segmenty C0 i C1) i spacji, które znajdują się poza stałymi ciągami.

IoT Hub odrzuca z powodu błędu wszystkie operacje, które spowodują zwiększenie rozmiaru tych dokumentów powyżej limitu.

## <a name="module-twin-metadata"></a>Metadane sznurka modułu

IoT Hub utrzymuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w pożądanej przędze modułu i raportowane właściwości. Sygnatury czasowe są w formacie UTC i zakodowane w `YYYY-MM-DDTHH:MM:SS.mmmZ`formatu [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .
Na przykład:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Te informacje są przechowywane na każdym poziomie (nie tylko przed opuszczeniem struktury JSON), aby zachować aktualizacje, które usuwają klucze obiektów.

## <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Tagi, odpowiednie i raportowane właściwości All obsługują optymistyczną współbieżność.
Tagi mają element ETag, jak na [RFC7232](https://tools.ietf.org/html/rfc7232), który reprezentuje reprezentację JSON znacznika. Aby zapewnić spójność, można użyć elementów ETag w operacjach aktualizacji warunkowej z zaplecza rozwiązania.

Pożądana sznurek modułu i zgłoszone właściwości nie mają elementów ETag, ale mają wartość `$version`, która ma gwarantowane przyrost. Podobnie jak w przypadku elementu ETag, wersja może być używana przez stronę aktualizacji w celu wymuszenia spójności aktualizacji. Na przykład aplikacja modułu dla raportowanej właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy Agent obserwowania (na przykład aplikacja modułu obserwowanie żądanych właściwości) musi uzgodnić Races między wynikiem operacji pobierania a powiadomieniem o aktualizacji. Sekcja [przepływu ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji. 

## <a name="next-steps"></a>Następne kroki

Aby wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujące samouczki IoT Hub:

* [Wprowadzenie do IoT Hub tożsamość modułu i sznurki modułu przy użyciu zaplecza platformy .NET i urządzenia .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
