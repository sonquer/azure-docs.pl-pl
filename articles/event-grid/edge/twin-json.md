---
title: Dwuosiowy moduł Azure Event Grid IoT Edge | Microsoft Docs
description: Konfiguracja za pośrednictwem sznurka modułu.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992147"
---
# <a name="module-twin-json-schema"></a>Schemat JSON

Event Grid na IoT Edge integruje się z ekosystemem IoT Edge i obsługuje tworzenie tematów i subskrypcji za pośrednictwem sznurka modułowego. Raport przedstawia także bieżący stan wszystkich tematów i subskrypcji zdarzeń do raportowanych właściwości na sznurze modułu.

> [!WARNING]
> Ze względu na ograniczenia w ekosystemie IoT Edge wszystkie elementy tablicy w poniższym przykładzie JSON zostały zakodowane jako ciągi JSON. Zobacz `EventSubscription.Filter.EventTypes` i `EventSubscription.Filter.AdvancedFilters` klucze w poniższym przykładzie.

## <a name="desired-properties-json"></a>Dane JSON żądanych właściwości

* Wartość każdej pary klucz-wartość w sekcji Tematy ma dokładnie ten sam schemat JSON, który jest używany przez `Topic.Properties` w interfejsie API podczas tworzenia tematów.
* Wartość każdej pary klucz-wartość w sekcji **EventSubscriptions** ma dokładnie ten sam schemat JSON, który jest używany dla `EventSubscription.Properties` w interfejsie API podczas tworzenia tematów.
* Aby usunąć temat, ustaw jego wartość na `null` w odpowiednich właściwościach.
* Usuwanie subskrypcji zdarzeń przy użyciu żądanych właściwości nie jest obsługiwane.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Zgłoszono kod JSON właściwości

Sekcja raportowane właściwości sznurka modułu zawiera następujące informacje:

* Zestaw tematów i subskrypcji istniejących w sklepie modułu
* Wszystkie błędy napotkane podczas tworzenia żądanych tematów/subskrypcji zdarzeń
* Wszelkie błędy rozruchowe (takie jak analiza JSON żądanych właściwości nie powiodło się)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
