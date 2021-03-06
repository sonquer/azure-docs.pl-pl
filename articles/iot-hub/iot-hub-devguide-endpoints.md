---
title: Informacje o punktach końcowych usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — informacje referencyjne dotyczące IoT Hub punktów końcowych związanych z urządzeniami i usługami.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: da6d17e42407048b7ecbcacade67ef48046d7fe1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457127"
---
# <a name="reference---iot-hub-endpoints"></a>Punkty końcowe IoT Hub odwołania

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nazwy IoT Hub

Nazwę hosta Centrum IoT Hub, który hostuje punkty końcowe w portalu, można znaleźć na stronie **omówienia** centrum. Domyślnie nazwa DNS Centrum IoT wygląda następująco: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanych punktów końcowych IoT Hub

Azure IoT Hub to usługa z wieloma dzierżawami, która udostępnia swoje funkcje różnym aktorom. Na poniższym diagramie przedstawiono różne punkty końcowe, które IoT Hub uwidaczniają.

![Punkty końcowe usługi IoT Hub](./media/iot-hub-devguide-endpoints/endpoints.png)

Na poniższej liście opisano punkty końcowe:

* **Dostawca zasobów**. Dostawca zasobów IoT Hub uwidacznia Interfejs [Azure Resource Manager](../azure-resource-manager/management/overview.md) . Ten interfejs umożliwia właścicielom subskrypcji platformy Azure tworzenie i usuwanie centrów IoT oraz aktualizowanie właściwości Centrum IoT. IoT Hub właściwości regulują [zasady zabezpieczeń na poziomie centrum](iot-hub-devguide-security.md#access-control-and-permissions), w przeciwieństwie do kontroli dostępu na poziomie urządzenia i opcji funkcjonalnych dla obsługi komunikatów z chmury do urządzeń i urządzeń z chmurą. Dostawca zasobów IoT Hub umożliwia również [Eksportowanie tożsamości urządzeń](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Zarządzanie tożsamościami urządzeń**. Każde Centrum IoT Hub udostępnia zestaw punktów końcowych protokołu HTTPS do zarządzania tożsamościami urządzeń (tworzenia, pobierania, aktualizowania i usuwania). [Tożsamości urządzeń](iot-hub-devguide-identity-registry.md) są używane do uwierzytelniania urządzeń i kontroli dostępu.

* **Zarządzanie bliźniaczymi urządzeniami**. Każde Centrum IoT Hub udostępnia zestaw punktu końcowego REST protokołu HTTPS, który umożliwia wysyłanie zapytań i aktualizowanie [urządzeń bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md) (aktualizowanie tagów i właściwości).

* **Zarządzanie zadaniami**. Każde Centrum IoT Hub udostępnia zestaw punktów końcowych protokołu HTTPS opartych na usłudze w celu wykonywania zapytań dotyczących [zadań](iot-hub-devguide-jobs.md)i zarządzania nimi.

* **Punkty końcowe urządzeń**. Dla każdego urządzenia w rejestrze tożsamości usługa IoT Hub uwidacznia zestaw punktów końcowych:

  * *Wysyłanie komunikatów z urządzenia do chmury*. Urządzenie używa tego punktu końcowego do [wysyłania komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md).

  * *Odbieraj komunikaty z chmury do urządzenia*. Urządzenie używa tego punktu końcowego do odbierania kierowanych [komunikatów z chmury do urządzenia](iot-hub-devguide-messages-c2d.md).

  * *Inicjuj operacje przekazywania plików*. Urządzenie używa tego punktu końcowego do odbierania identyfikatora URI SAS usługi Azure Storage z IoT Hub, aby [przekazać plik](iot-hub-devguide-file-upload.md).

  * *Pobierz i zaktualizuj właściwości sznurka urządzenia*. Urządzenie używa tego punktu końcowego do uzyskiwania dostępu do właściwości [sznurka urządzenia](iot-hub-devguide-device-twins.md).

  * *Odbieraj żądania metody bezpośredniej*. Urządzenie używa tego punktu końcowego do nasłuchiwania żądań [bezpośredniej metody](iot-hub-devguide-direct-methods.md).

    Punkty końcowe są ujawniane przy użyciu protokołów [MQTT v 3.1.1](https://mqtt.org/), https 1,1 i [AMQP 1,0](https://www.amqp.org/) . AMQP jest również dostępna za pośrednictwem obiektów [WebSockets](https://tools.ietf.org/html/rfc6455) na porcie 443.

* **Punkty końcowe usługi**. Każde Centrum IoT Hub udostępnia zestaw punktów końcowych dla zaplecza rozwiązania do komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są udostępniane tylko przy użyciu protokołu [AMQP](https://www.amqp.org/) . Punkt końcowy wywołania metody jest udostępniany za pośrednictwem protokołu HTTPS.
  
  * *Odbieraj komunikaty z urządzenia do chmury*. Ten punkt końcowy jest zgodny z [usługą Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Usługa zaplecza może używać jej do odczytywania komunikatów z [urządzenia do chmury](iot-hub-devguide-messages-d2c.md) wysyłanych przez urządzenia. Oprócz tego wbudowanego punktu końcowego można utworzyć niestandardowe punkty końcowe w centrum IoT.
  
  * *Wysyłanie komunikatów z chmury do urządzeń i otrzymywanie potwierdzeń dostarczenia*. Te punkty końcowe umożliwiają zapleczu rozwiązania do wysyłania niezawodnych [komunikatów z chmury do urządzenia](iot-hub-devguide-messages-c2d.md)oraz do odbierania odpowiednich potwierdzeń dostarczania lub ważności.
  
  * *Odbieranie powiadomień o plikach*. Ten punkt końcowy obsługi komunikatów pozwala otrzymywać powiadomienia o tym, kiedy urządzenia pomyślnie przekażą plik. 
  
  * *Wywołanie metody bezpośredniej*. Ten punkt końcowy umożliwia usłudze zaplecza wywoływanie [metody bezpośredniej](iot-hub-devguide-direct-methods.md) na urządzeniu.
  
  * *Odbieraj zdarzenia monitorowania operacji*. Ten punkt końcowy pozwala otrzymywać zdarzenia monitorowania operacji, jeśli Centrum IoT zostało skonfigurowane do ich emisji. Aby uzyskać więcej informacji, zobacz [IoT Hub monitorowania operacji](iot-hub-operations-monitoring.md).

W artykule dotyczącej [zestawów SDK usługi Azure IoT](iot-hub-devguide-sdks.md) opisano różne sposoby uzyskiwania dostępu do tych punktów końcowych.

Wszystkie punkty końcowe IoT Hub korzystają z protokołu [TLS](https://tools.ietf.org/html/rfc5246) i żaden punkt końcowy nie jest kiedykolwiek ujawniany w niezaszyfrowanych/niezabezpieczonych kanałach.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Istniejące usługi platformy Azure w ramach subskrypcji można połączyć z Centrum IoT Hub, aby działały jako punkty końcowe dla routingu komunikatów. Punkty końcowe działają jako punkty końcowe usługi i są używane jako ujścia dla tras komunikatów. Urządzenia nie mogą zapisywać bezpośrednio w dodatkowych punktach końcowych. Dowiedz się więcej o [routingu komunikatów](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub obecnie obsługuje następujące usługi platformy Azure jako dodatkowe punkty końcowe:

* Kontenery usługi Azure Storage
* Centra zdarzeń
* Kolejki usługi Service Bus
* Tematy usługi Service Bus

Aby uzyskać limity liczby punktów końcowych, które można dodać, zobacz [przydziały i ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md).

Aby uzyskać stan kondycji punktów końcowych, można użyć interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Zalecamy używanie [metryk IoT Hub](iot-hub-metrics.md) związanych z opóźnieniem komunikatów routingu w celu identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub zła, ponieważ oczekuje się, że opóźnienie ma być wyższe, gdy punkt końcowy jest w jednym z tych stanów.

|Stan kondycji|Opis|
|---|---|
|healthy|Punkt końcowy akceptuje komunikaty zgodnie z oczekiwaniami.|
|Złej kondycji|Punkt końcowy nie akceptuje komunikatów zgodnie z oczekiwaniami, a IoT Hub ponawia próbę wysłania danych do tego punktu końcowego. Stan punktu końcowego w złej kondycji zostanie zaktualizowany w dobrej kondycji, gdy IoT Hub ustanowił ostatecznie spójny stan kondycji.|
|nieznany|IoT Hub nie nawiązać połączenia z punktem końcowym. Z tego punktu końcowego nie dostarczono żadnych komunikatów ani nie zostały one odrzucone.|
|martwy|Punkt końcowy nie akceptuje komunikatów, po IoT Hub ponowieniu próby wysłania komunikatów przez okres próbny.|

## <a name="field-gateways"></a>Bramy pól

W rozwiązaniu IoT *brama pola* znajduje się między urządzeniami i IoT Hub punktami końcowymi. Zwykle znajduje się ona blisko Twoich urządzeń. Urządzenia komunikują się bezpośrednio z bramą usługi Field przy użyciu protokołu obsługiwanego przez urządzenia. Brama pola nawiązuje połączenie z punktem końcowym IoT Hub przy użyciu protokołu obsługiwanego przez IoT Hub. Brama pola może być dedykowanym urządzeniem sprzętowym lub komputerem z niską możliwością uruchamiania oprogramowania bramy niestandardowej.

Aby zaimplementować bramę pola, można użyć [Azure IoT Edge](/azure/iot-edge/) . IoT Edge oferuje funkcje, takie jak możliwość multipleksowania komunikacji z wielu urządzeń na ten sam IoT Hub połączenie.

## <a name="next-steps"></a>Następne kroki

Inne tematy referencyjne w tym IoT Hub przewodniku dla deweloperów obejmują:

* [IoT Hub język zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md)
* [Informacje o adresie IP Centrum IoT Hub](iot-hub-understand-ip-address.md)