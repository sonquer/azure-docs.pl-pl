---
title: Schemat zdarzeń dziennika aktywności platformy Azure
description: Opisuje schemat zdarzeń dla każdej kategorii w dzienniku aktywności platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 272b71fe5fddea9299e5d660484fcbb3eb367d58
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749471"
---
# <a name="azure-activity-log-event-schema"></a>Schemat zdarzeń dziennika aktywności platformy Azure
[Dziennik aktywności platformy Azure](platform-logs-overview.md) zawiera szczegółowe informacje o wszystkich zdarzeniach na poziomie subskrypcji, które wystąpiły na platformie Azure. W tym artykule opisano schemat zdarzeń dla każdej kategorii. 

W poniższych przykładach przedstawiono schemat podczas uzyskiwania dostępu do dziennika aktywności z poziomu portalu, programu PowerShell, interfejsu wiersza polecenia i API REST. Schemat jest różny podczas [przesyłania strumieniowego dziennika aktywności do magazynu lub Event Hubs](resource-logs-stream-event-hubs.md). Mapowanie właściwości do [schematu dzienników zasobów](diagnostic-logs-schema.md) znajduje się na końcu artykułu.

## <a name="administrative"></a>Administracyjne
Ta kategoria zawiera rekord wszystkich operacji tworzenia, aktualizowania, usuwania i akcji wykonywanych za pomocą Menedżer zasobów. Przykłady typów zdarzeń widocznych w tej kategorii obejmują "Tworzenie maszyny wirtualnej" i "Usuwanie sieciowej grupy zabezpieczeń" Każda Akcja podejmowana przez użytkownika lub aplikację przy użyciu Menedżer zasobów jest modelowana jako operacja dla określonego typu zasobu. Jeśli typem operacji jest zapis, usuwanie lub Akcja, rekordy zarówno rozpoczęcia, jak i sukcesu lub niepowodzenia tej operacji są rejestrowane w kategorii administracyjnej. Kategoria administracyjna zawiera również wszelkie zmiany w ramach kontroli dostępu opartej na rolach w ramach subskrypcji.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| authorization |Obiekt BLOB właściwości RBAC zdarzenia. Zazwyczaj obejmuje właściwości "Action", "role" i "Scope". |
| Obiekt wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenie nazwy UPN lub oświadczenie SPN na podstawie dostępności. |
| dyplomatyczn |Jedna z następujących wartości: "admin", "Operation" |
| claims |Token JWT używany przez Active Directory do uwierzytelniania użytkownika lub aplikacji w celu wykonania tej operacji w Menedżer zasobów. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które współużytkują identyfikator korelacji, należy do tej samej akcji Uber. |
| description |Tekst statyczny opisu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| eventName | Przyjazna nazwa zdarzenia administracyjnego. |
| category | Zawsze "administracyjne" |
| httpRequest |Obiekt BLOB opisujący żądanie HTTP. Zwykle obejmuje "identyfikatorem żądania klienta", "clientIpAddress" i "Method" (metoda HTTP. Na przykład Umieść. |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "Error", "Warning" i "informacyjny" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu, którego dotyczy problem. |
| resourceProviderName |Nazwa dostawcy zasobów dla zasobu, którego dotyczy problem |
| resourceType | Typ zasobu, którego dotyczy zdarzenie administracyjne. |
| resourceId |Identyfikator zasobu zasobu, którego dotyczy problem. |
| operationId |Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne, rozwiązane. |
| subStatus |Zazwyczaj kod stanu HTTP odpowiadającego wywołania REST, ale może również zawierać inne ciągi opisujące podstan, takie jak te wspólne wartości: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowany (kod stanu http: 202), brak zawartości (stan HTTP Kod: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404), konflikt (kod stanu http: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504). |
| eventTimestamp |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="service-health"></a>Kondycja usługi
Ta kategoria zawiera rekord wszystkich zdarzeń związanych z kondycją usług, które wystąpiły na platformie Azure. Przykładem typu zdarzenia, które widzisz w tej kategorii jest "usługa SQL Azure w regionie Wschodnie stany USA ma czas przestoju". Zdarzenia dotyczące kondycji usługi znajdują się w pięciu odmianach: wymagana akcja, odzyskanie, incydent, konserwacja, informacje lub zabezpieczenia, i pojawia się tylko wtedy, gdy w subskrypcji znajduje się zasób, na który wpłynie zdarzenie.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Zapoznaj się z artykułem dotyczącym [powiadomień o kondycji usługi](./../../azure-monitor/platform/service-notifications.md) , aby uzyskać informacje na temat wartości we właściwościach.

## <a name="resource-health"></a>Resource Health
Ta kategoria zawiera rekord wszystkich zdarzeń związanych z kondycją zasobów, które wystąpiły w Twoich zasobach platformy Azure. Przykładem typu zdarzenia, które zobaczysz w tej kategorii jest "stan kondycji maszyny wirtualnej zmienił się na niedostępny". Zdarzenia dotyczące kondycji zasobów mogą reprezentować jeden z czterech stanów kondycji: dostępne, niedostępne, obniżone i nieznane. Ponadto zdarzenia kondycji zasobów można klasyfikować jako zainicjowane przez platformę lub zainicjowane przez użytkownika.

### <a name="sample-event"></a>Zdarzenie próbkowania

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| dyplomatyczn | Zawsze "administrator, operacja" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Opis tekstu statycznego zdarzenia alertu. |
| eventDataId |Unikatowy identyfikator zdarzenia alertu. |
| category | Zawsze "ResourceHealth" |
| eventTimestamp |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "błąd", "ostrzeżenie", "informacyjny" i "verbose" |
| operationId |Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName |Nazwa operacji. |
| resourceGroupName |Nazwa grupy zasobów zawierającej zasób. |
| resourceProviderName |Zawsze "Microsoft. Resourcehealth/healthevent/Action". |
| resourceType | Typ zasobu, którego dotyczy zdarzenie Resource Health. |
| resourceId | Nazwa identyfikatora zasobu, którego dotyczy problem. |
| status |Ciąg opisujący stan zdarzenia kondycji. Mogą to być: aktywne, rozwiązane, w trakcie, zaktualizowane. |
| subStatus | Zazwyczaj wartość null dla alertów. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zdarzenia.|
| Properties. title | Przyjazny dla użytkownika ciąg opisujący stan kondycji zasobu. |
| Właściwości. szczegóły | Przyjazny dla użytkownika ciąg opisujący dalsze szczegóły dotyczące zdarzenia. |
| Właściwości. currentHealthStatus | Bieżący stan kondycji zasobu. Jedna z następujących wartości: "dostępne", "niedostępne", "obniżone" i "nieznany". |
| properties.previousHealthStatus | Poprzedni stan kondycji zasobu. Jedna z następujących wartości: "dostępne", "niedostępne", "obniżone" i "nieznany". |
| Properties. Type | Opis typu zdarzenia dotyczącego kondycji zasobu. |
| Properties. nmożliwa Przyczyna | Opis przyczyny zdarzenia dotyczącego kondycji zasobu. "UserInitiated" i "PlatformInitiated". |


## <a name="alert"></a>Alert
Ta kategoria zawiera rekord wszystkich aktywacji alertów platformy Azure. Przykładem typu zdarzenia, które zobaczysz w tej kategorii jest "procesor CPU% na myVM został przekroczony 80 dla ostatnich 5 minut". Różne systemy platformy Azure mają koncepcję alertów — można zdefiniować regułę określonego sortowania i otrzymywać powiadomienie, gdy warunki są zgodne z tą regułą. Za każdym razem, gdy obsługiwany typ alertu platformy Azure "aktywuje" lub warunki są spełnione, aby wygenerować powiadomienie, rekord aktywacji jest również wypychany do tej kategorii dziennika aktywności.

### <a name="sample-event"></a>Zdarzenie próbkowania

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| Obiekt wywołujący | Zawsze Microsoft. Insights/alertRules |
| dyplomatyczn | Zawsze "administrator, operacja" |
| claims | Obiekt BLOB JSON z nazwą SPN (główna nazwa usługi) lub typem zasobu aparatu alertów. |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Opis tekstu statycznego zdarzenia alertu. |
| eventDataId |Unikatowy identyfikator zdarzenia alertu. |
| category | Zawsze "Alert" |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "Error", "Warning" i "informacyjny" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu, którego dotyczy problem, jeśli jest to alert dotyczący metryki. W przypadku innych typów alertów jest to nazwa grupy zasobów, która zawiera alert. |
| resourceProviderName |Nazwa dostawcy zasobów, którego dotyczy problem, jeśli jest to alert dotyczący metryki. W przypadku innych typów alertów jest to nazwa dostawcy zasobów dla samego alertu. |
| resourceId | Nazwa identyfikatora zasobu, którego dotyczy problem, jeśli jest to alert dotyczący metryki. W przypadku innych typów alertów jest to identyfikator zasobu samego zasobu alertu. |
| operationId |Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zdarzenia. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla alertów. |
| eventTimestamp |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

### <a name="properties-field-per-alert-type"></a>Pole właściwości dla typu alertu
Pole właściwości będzie zawierać różne wartości w zależności od źródła zdarzenia alertu. Dwoma dostawcami zdarzeń typowych alertów są alerty dziennika aktywności i alerty metryk.

#### <a name="properties-for-activity-log-alerts"></a>Właściwości alertów dziennika aktywności
| Nazwa elementu | Opis |
| --- | --- |
| properties.subscriptionId | Identyfikator subskrypcji ze zdarzenia dziennika aktywności, który spowodował aktywowanie tej reguły alertu dziennika aktywności. |
| properties.eventDataId | Identyfikator danych zdarzenia ze zdarzenia dziennika aktywności, który spowodował aktywowanie tej reguły alertu dziennika aktywności. |
| properties.resourceGroup | Grupa zasobów ze zdarzenia dziennika aktywności, która spowodowała aktywowanie tej reguły alertu dziennika aktywności. |
| properties.resourceId | Identyfikator zasobu ze zdarzenia dziennika aktywności, który spowodował aktywowanie tej reguły alertu dziennika aktywności. |
| properties.eventTimestamp | Sygnatura czasowa zdarzenia zdarzenia dziennika aktywności, która spowodowała aktywowanie tej reguły alertu dziennika aktywności. |
| properties.operationName | Nazwa operacji ze zdarzenia dziennika aktywności, która spowodowała aktywowanie tej reguły alertu dziennika aktywności. |
| properties.status | Stan zdarzenia dziennika aktywności, który spowodował aktywowanie tej reguły alertu dziennika aktywności.|

#### <a name="properties-for-metric-alerts"></a>Właściwości alertów dotyczących metryk
| Nazwa elementu | Opis |
| --- | --- |
| properties.RuleUri | Identyfikator zasobu reguły alertu metryki. |
| properties.RuleName | Nazwa reguły alertu metryki. |
| aœciwoœci. RuleDescription | Opis reguły alertu dotyczącego metryki (zgodnie z definicją w regule alertu). |
| aœciwoœci. Próg | Wartość progowa używana podczas obliczania reguły alertu metryki. |
| properties.WindowSizeInMinutes | Rozmiar okna używany podczas obliczania reguły alertu metryki. |
| properties.Aggregation | Typ agregacji zdefiniowany w regule alertu metryki. |
| aœciwoœci. Zakład | Operator warunkowy używany podczas obliczania reguły alertu metryki. |
| properties.MetricName | Nazwa metryki metryki używanej w ocenie reguły alertu metryki. |
| properties.MetricUnit | Jednostka metryki dla metryki używanej w ocenie reguły alertu metryki. |

## <a name="autoscale"></a>Automatyczne skalowanie
Ta kategoria zawiera rekord wszystkich zdarzeń związanych z działaniem aparatu skalowania automatycznego na podstawie wszelkich ustawień automatycznego skalowania zdefiniowanych w ramach subskrypcji. Przykładem typu zdarzenia, które zobaczysz w tej kategorii jest "Akcja skalowania automatycznego nie powiodła się". Korzystając z funkcji automatycznego skalowania, można automatycznie skalować w poziomie i skalować liczbę wystąpień w obsługiwanym typie zasobów na podstawie czasu i/lub obciążenia (dane pomiarowe) przy użyciu ustawienia skalowania automatycznego. Gdy warunki są spełnione w celu skalowania w górę lub w dół, zdarzenia Start i zakończony powodzeniem lub zakończone niepowodzeniem zostaną zarejestrowane w tej kategorii.

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| Obiekt wywołujący | Always Microsoft.Insights/autoscaleSettings |
| dyplomatyczn | Zawsze "administrator, operacja" |
| claims | Obiekt BLOB JSON z nazwą SPN (główna nazwa usługi) lub typem zasobu aparatu skalowania automatycznego. |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Tekst statyczny opisu zdarzenia skalowania automatycznego. |
| eventDataId |Unikatowy identyfikator zdarzenia skalowania automatycznego. |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "Error", "Warning" i "informacyjny" |
| resourceGroupName |Nazwa grupy zasobów dla ustawienia skalowania automatycznego. |
| resourceProviderName |Nazwa dostawcy zasobów dla ustawienia skalowania automatycznego. |
| resourceId |Identyfikator zasobu ustawienia skalowania automatycznego. |
| operationId |Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zdarzenia. |
| aœciwoœci. Zharmonizowan | Szczegółowy opis działania aparatu skalowania automatycznego. |
| properties.ResourceName | Identyfikator zasobu zasobu, którego dotyczy problem (zasób, na którym jest wykonywana akcja skalowania) |
| properties.OldInstancesCount | Liczba wystąpień przed zastosowaniem akcji skalowania automatycznego. |
| properties.NewInstancesCount | Liczba wystąpień wykonanych po akcji skalowania automatycznego. |
| properties.LastScaleActionTime | Sygnatura czasowa, gdy wystąpiła akcja skalowania automatycznego. |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla skalowania automatycznego. |
| eventTimestamp |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="security"></a>Zabezpieczenia
Ta kategoria zawiera rekordy wszystkich alertów wygenerowanych przez Azure Security Center. Przykładem typu zdarzenia, które zobaczysz w tej kategorii jest "podejrzany plik rozszerzenia o podwójnym rozszerzeniu".

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| dyplomatyczn | Zawsze "operacja" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Statyczny opis zdarzenia zabezpieczeń. |
| eventDataId |Unikatowy identyfikator zdarzenia zabezpieczeń. |
| eventName |Przyjazna nazwa zdarzenia zabezpieczeń. |
| category | Zawsze "zabezpieczenia" |
| ID |Unikatowy identyfikator zasobu zdarzenia zabezpieczeń. |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "błąd", "ostrzeżenie" lub "informacyjny" |
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Nazwa dostawcy zasobów dla Azure Security Center. Zawsze "Microsoft. Security". |
| resourceType |Typ zasobu, który wygenerował zdarzenie zabezpieczeń, na przykład "Microsoft. Security/Locations/Alerts" |
| resourceId |Identyfikator zasobu alertu zabezpieczeń. |
| operationId |Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zdarzenia. Te właściwości będą się różnić w zależności od typu alertu zabezpieczeń. [Ta strona](../../security-center/security-center-alerts-overview.md) zawiera opis typów alertów, które pochodzą z Security Center. |
| aœciwoœci. Obrażeń |Poziom ważności. Możliwe wartości to "High", "medium" lub "Low". |
| status |Ciąg opisujący stan operacji. Niektóre typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne, rozwiązane. |
| subStatus | Zazwyczaj wartość null dla zdarzeń zabezpieczeń. |
| eventTimestamp |Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |

## <a name="recommendation"></a>Zalecenie
Ta kategoria zawiera rejestr wszelkich nowych zaleceń, które są generowane dla usług. Przykładem zalecenia będzie "użycie zestawów dostępności w celu zwiększenia odporności na uszkodzenia". Istnieją cztery typy zdarzeń rekomendacji, które mogą być generowane: wysoka dostępność, wydajność, zabezpieczenia i optymalizacja kosztów. 

### <a name="sample-event"></a>Zdarzenie próbkowania
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Opisy właściwości
| Nazwa elementu | Opis |
| --- | --- |
| dyplomatyczn | Zawsze "operacja" |
| correlationId | Identyfikator GUID w formacie ciągu. |
| description |Tekst statyczny opisu zdarzenia rekomendacji |
| eventDataId | Unikatowy identyfikator zdarzenia rekomendacji. |
| category | Zawsze "rekomendacja" |
| ID |Unikatowy identyfikator zasobu zdarzenia rekomendacji. |
| level |Poziom zdarzenia. Jedna z następujących wartości: "krytyczna", "błąd", "ostrzeżenie" lub "informacyjny" |
| operationName |Nazwa operacji.  Zawsze "Microsoft. Advisor/generateRecommendations/Action"|
| resourceGroupName |Nazwa grupy zasobów dla zasobu. |
| resourceProviderName |Nazwa dostawcy zasobów dla zasobu, do którego odnosi się to zalecenie, np. "MICROSOFT. COMPUTE" |
| resourceType |Nazwa typu zasobu, do którego odnosi się to zalecenie, na przykład "MICROSOFT. COMPUTE/virtualmachines" |
| resourceId |Identyfikator zasobu zasobu, do którego odnosi się zalecenie |
| status | Zawsze "aktywne" |
| submissionTimestamp |Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| properties |Zestaw par `<Key, Value>` (czyli słownik) opisujących szczegóły zalecenia.|
| Właściwości. recommendationSchemaVersion| Wersja schematu właściwości rekomendacji opublikowanych w wpisie dziennika aktywności |
| Właściwości. recommendationCategory | Kategoria Zalecenia. Możliwe wartości to "wysoka dostępność", "wydajność", "zabezpieczenia" i "koszt" |
| properties.recommendationImpact| Wpływ zalecenia. Możliwe wartości to "High", "medium", "Low" |
| Właściwości. recommendationRisk| Ryzyko zalecenia. Możliwe wartości to "Error", "Warning", "none" |

## <a name="policy"></a>Zasady

Ta kategoria zawiera rekordy wszystkich operacji działania akcji wykonywanych przez [Azure Policy](../../governance/policy/overview.md). Przykłady typów zdarzeń, które zobaczysz w tej kategorii, obejmują _inspekcję_ i _odmowę_. Wszystkie akcje podejmowane przez zasady są modelowane jako operacje na zasobach.

### <a name="sample-policy-event"></a>Przykładowe zdarzenie zasad

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Opisy właściwości zdarzeń zasad

| Nazwa elementu | Opis |
| --- | --- |
| authorization | Tablica właściwości RBAC zdarzenia. W przypadku nowych zasobów jest to akcja i zakres żądania, które wyzwoliło ocenę. Dla istniejących zasobów akcja ma wartość "Microsoft. resources/checkPolicyCompliance/Read". |
| Obiekt wywołujący | W przypadku nowych zasobów tożsamość, która zainicjowała wdrożenie. W przypadku istniejących zasobów identyfikator GUID usługi RP usługi Microsoft Azure Insights. |
| dyplomatyczn | Zdarzenia zasad używają tylko kanału operacji. |
| claims | Token JWT używany przez Active Directory do uwierzytelniania użytkownika lub aplikacji w celu wykonania tej operacji w Menedżer zasobów. |
| correlationId | Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które współużytkują identyfikator korelacji, należy do tej samej akcji Uber. |
| description | To pole jest puste dla zdarzeń zasad. |
| eventDataId | Unikatowy identyfikator zdarzenia. |
| eventName | "BeginRequest" lub "EndRequest". "BeginRequest" jest używany w przypadku opóźnionych ocen auditIfNotExists i deployIfNotExists oraz kiedy efekt deployIfNotExists uruchamia wdrożenie szablonu. Wszystkie inne operacje zwracają wartość "EndRequest". |
| category | Deklaruje zdarzenie dziennika aktywności jako należące do "zasad". |
| eventTimestamp | Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu. |
| ID | Unikatowy identyfikator zdarzenia dla określonego zasobu. |
| level | Poziom zdarzenia. Inspekcja używa "Warning" i Odmów używa "Error". Błąd auditIfNotExists lub deployIfNotExists może generować "Warning" lub "Error" w zależności od ważności. Wszystkie inne zdarzenia zasad używają "informacyjnych". |
| operationId | Identyfikator GUID współużytkowany przez zdarzenia, które odpowiadają pojedynczej operacji. |
| operationName | Nazwa operacji i bezpośrednio skorelowanej z efektem zasad. |
| resourceGroupName | Nazwa grupy zasobów dla szacowanego zasobu. |
| resourceProviderName | Nazwa dostawcy zasobów dla szacowanego zasobu. |
| resourceType | W przypadku nowych zasobów jest oceniany typ. W przypadku istniejących zasobów zwraca wartość "Microsoft. resources/checkPolicyCompliance". |
| resourceId | Identyfikator zasobu szacowanego zasobu. |
| status | Ciąg opisujący stan wyniku oceny zasad. Większość ocen zasad zwraca wartość "powodzenie", ale efekt odrzucenia zwróci wartość "Niepowodzenie". Błędy w auditIfNotExists lub deployIfNotExists również zwracają wartość "Niepowodzenie". |
| subStatus | Pole jest puste dla zdarzeń zasad. |
| submissionTimestamp | Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań. |
| subscriptionId | Identyfikator subskrypcji platformy Azure. |
| properties.isComplianceCheck | Zwraca wartość "false", gdy zostanie wdrożony nowy zasób lub Zaktualizowano Menedżer zasobów właściwości istniejącego zasobu. Wszystkie inne [wyzwalacze oceny](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) spowodują wynik "true". |
| Właściwości. resourceLocation | Region świadczenia usługi Azure ocenianego zasobu. |
| Properties. przodks | Rozdzielana przecinkami lista nadrzędnych grup zarządzania uporządkowanych od bezpośrednich elementów nadrzędnych do najdalej najwyższego poziomu. |
| Właściwości. zasady | Zawiera szczegółowe informacje na temat definicji zasad, przypisania, efektu i parametrów, których wynikiem jest ocena zasad. |
| relatedEvents | To pole jest puste dla zdarzeń zasad. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schemat z centrów zdarzeń i kont magazynu
Podczas przesyłania strumieniowego dziennika aktywności platformy Azure do konta magazynu lub centrum zdarzeń dane są zgodne ze [schematem dziennika zasobów](diagnostic-logs-schema.md). Poniższa tabela zawiera mapowanie właściwości z schematu powyżej do schematu dzienników zasobów.

> [!IMPORTANT]
> Format danych dziennika aktywności zapisany na koncie magazynu został zmieniony na wiersze JSON na lis. 1, 2018. Aby uzyskać szczegółowe informacje na temat tego formatu [, zobacz Przygotowywanie do zmiany formatu do Azure monitor dzienników zasobów zarchiwizowanych na koncie magazynu](diagnostic-logs-append-blobs.md) .


| Właściwości schematu dzienników zasobów | Właściwość schematu interfejsu API REST dziennika aktywności | Uwagi |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | wszystkie identyfikatory subskrypcji, ResourceType, resourceGroupName są wywnioskowane z identyfikatora zasobu. |
| operationName | operationName.value |  |
| category | Część nazwy operacji | Zagadnień typu operacji — "Write"/"Delete"/"Action" |
| resultType | status. Value | |
| resultSignature | Substatus. wartość | |
| resultDescription | description |  |
| durationMs | ND | Zawsze 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| tożsamość | Właściwości oświadczeń i autoryzacji |  |
| Poziom | Poziom |  |
| location | ND | Lokalizacja, w której zdarzenie zostało przetworzone. *Nie jest to lokalizacja zasobu, ale zamiast tego, gdzie zdarzenie zostało przetworzone. Ta właściwość zostanie usunięta w przyszłej aktualizacji.* |
| Właściwości | Właściwości. eventProperties |  |
| Właściwości. eventCategory | category | Jeśli właściwość. eventCategory nie istnieje, kategoria ma wartość "Administrative" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| Właściwości. eventProperties | properties |  |

Poniżej znajduje się przykład zdarzenia korzystającego z tego schematu.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o dzienniku aktywności](platform-logs-overview.md)
* [Tworzenie ustawień diagnostycznych w celu wysyłania dziennika aktywności do Log Analytics obszaru roboczego, usługi Azure Storage lub centrów zdarzeń](diagnostic-settings.md)

