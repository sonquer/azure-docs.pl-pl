---
title: Programy obsługi zdarzeń i miejsca docelowe — Azure Event Grid IoT Edge | Microsoft Docs
description: Programy obsługi zdarzeń i miejsca docelowe w Event Grid na krawędzi
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849749"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Programy obsługi zdarzeń i miejsca docelowe w Event Grid na krawędzi

Program obsługi zdarzeń to miejsce, w którym zdarzenie do dalszej akcji lub przetwarzania zdarzenia. Za pomocą Event Grid w module Edge program obsługi zdarzeń może znajdować się na tym samym urządzeniu brzegowym, innym urządzeniu lub w chmurze. Możesz użyć dowolnego elementu webhook do obsługi zdarzeń lub wysłać zdarzenia do jednego z natywnych programów obsługi, takich jak Azure Event Grid.

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania każdego z nich.

## <a name="webhook"></a>Elementu webhook

Aby opublikować w punkcie końcowym elementu webhook, ustaw `endpointType` na `WebHook` i podaj:

* endpointUrl: adres URL punktu końcowego elementu webhook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Aby opublikować w punkcie końcowym w chmurze Azure Event Grid, ustaw `endpointType` na `eventGrid` i podaj:

* endpointUrl: adres URL tematu Event Grid w chmurze
* sasKey: klucz SAS tematu Event Grid
* Nazwa tematu: należy oznaczyć wszystkie zdarzenia wychodzące do Event Grid. Nazwa tematu jest przydatna podczas ogłaszania w temacie domeny Event Grid.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge Hub

Aby opublikować w module centrum brzegowego, ustaw `endpointType` na `edgeHub` i podaj:

* outputName: dane wyjściowe, na których moduł Event Grid będzie kierować zdarzenia, które pasują do tej subskrypcji do edgeHub. Na przykład zdarzenia zgodne z poniższą subskrypcją będą zapisywane w/messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Centra zdarzeń

Aby opublikować w centrum zdarzeń, ustaw `endpointType` na `eventHub` i podaj:

* connectionString: parametry połączenia dla określonego centrum zdarzeń, które są przeznaczone do wygenerowania za pośrednictwem zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Parametry połączenia specyficzne dla jednostki można wygenerować, przechodząc do określonego centrum zdarzeń, które chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus

Aby opublikować w kolejce Service Bus, ustaw `endpointType` na `serviceBusQueue` i podaj:

* connectionString: parametry połączenia dla konkretnej kolejki Service Bus są generowane przez zasady dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do konkretnej kolejki Service Bus, w której chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Tematy usługi Service Bus

Aby opublikować w temacie Service Bus, ustaw `endpointType` na `serviceBusTopic` i podaj:

* connectionString: parametry połączenia dla określonego tematu Service Bus, które są przeznaczone do wygenerowania w ramach zasad dostępu współdzielonego.

    >[!NOTE]
    > Parametry połączenia muszą być specyficzne dla jednostki. Używanie parametrów połączenia z przestrzenią nazw nie będzie działało. Wygeneruj parametry połączenia specyficzne dla jednostki, przechodząc do określonego tematu Service Bus, w którym chcesz opublikować w witrynie Azure Portal, a następnie klikając pozycję **zasady dostępu współdzielonego** , aby wygenerować nowy ciąg connecection specyficzny dla jednostki.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Kolejki magazynu

Aby opublikować w kolejce magazynu, ustaw `endpointType` na `storageQueue` i podaj:

* QueueName: Nazwa kolejki magazynu, w której jest publikowany.
* connectionString: parametry połączenia dla konta magazynu, w którym znajduje się kolejka magazynu.

    >[!NOTE]
    > Nieliniowe Event Hubs, kolejki Service Bus i tematy Service Bus, parametry połączenia używane dla kolejek magazynu nie są specyficzne dla jednostki. Zamiast tego należy użyć parametrów połączenia dla konta magazynu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```