---
title: Używanie funkcji automatycznego skalowania do wysyłania powiadomień o alertach dotyczących wiadomości e-mail i elementów webhook
description: Dowiedz się, jak używać akcji skalowania automatycznego do wywoływania internetowych adresów URL lub wysyłania powiadomień e-mail w Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120693"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Użyj akcji skalowania automatycznego do wysyłania powiadomień o alertach poczty e-mail i elementów webhook w Azure Monitor
W tym artykule pokazano, jak skonfigurować wyzwalacze, aby umożliwić wywoływanie określonych adresów URL sieci Web lub wysyłanie wiadomości e-mail na podstawie akcji skalowania automatycznego na platformie Azure.  

## <a name="webhooks"></a>Elementy webhook
Elementy webhook umożliwiają kierowanie powiadomień o alertach platformy Azure do innych systemów na potrzeby powiadomień po zakończeniu przetwarzania lub niestandardowych. Na przykład, kierowanie alertu do usług, które mogą obsługiwać przychodzące żądanie sieci Web w celu wysyłania wiadomości SMS, rejestrowania usterek, powiadamiania zespołu przy użyciu usług rozmowy lub Messaging itp. Identyfikator URI elementu webhook musi być prawidłowym punktem końcowym HTTP lub HTTPS.

## <a name="email"></a>Email
Wiadomości e-mail można wysyłać na dowolny prawidłowy adres e-mail. Administratorzy i współadministratorzy subskrypcji, w której uruchomiona jest reguła, również będą powiadamiani.

## <a name="cloud-services-and-app-services"></a>Cloud Services i App Services
Możesz zdecydować się na podstawie Azure Portal dla Cloud Services i farm serwerów (App Services).

* Wybierz pozycję **skalowanie według** metryki.

![Skalowanie według](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
W przypadku nowszych Virtual Machines utworzonych za pomocą Menedżer zasobów (zestawy skalowania maszyn wirtualnych) można je skonfigurować za pomocą interfejsu API REST, szablonów Menedżer zasobów, programu PowerShell i interfejsu wiersza polecenia. Interfejs portalu nie jest jeszcze dostępny.
W przypadku korzystania z interfejsu API REST lub szablonu Menedżer zasobów należy uwzględnić w [autoscalesettings](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) element Notifications z następującymi opcjami.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Pole | Wypełnione? | Opis |
| --- | --- | --- |
| operation |tak |wartość musi być równa "Skala" |
| sendToSubscriptionAdministrator |tak |wartość musi być równa "true" lub "false" |
| sendToSubscriptionCoAdministrators |tak |wartość musi być równa "true" lub "false" |
| customEmails |tak |wartość może być równa null [] lub tablicy ciągów wiadomości e-mail |
| webhooks |tak |wartość może być równa null lub być prawidłowym identyfikatorem URI |
| serviceUri |tak |prawidłowy identyfikator URI protokołu https |
| properties |tak |wartość musi być pusta {} lub może zawierać pary klucz-wartość |

## <a name="authentication-in-webhooks"></a>Uwierzytelnianie w elementach webhook
Element webhook może uwierzytelniać się przy użyciu uwierzytelniania opartego na tokenach, w którym można zapisać identyfikator URI elementu webhook z IDENTYFIKATORem tokenu jako parametr zapytania. Na przykład https:\//mysamplealert/webcallback? obiektu tokenidd = sometokenid & someparameter = wartość someValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schemat ładunku elementu webhook powiadomień automatycznego skalowania
Po wygenerowaniu powiadomienia skalowania automatycznego następujące metadane są uwzględniane w ładunku elementu webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Pole | Wypełnione? | Opis |
| --- | --- | --- |
| status |tak |Stan wskazujący, że Wygenerowano akcję automatycznego skalowania |
| operation |tak |W przypadku zwiększenia liczby wystąpień będzie to "skalowalne w poziomie", a w przypadku spadku wystąpień będzie to "skalowane". |
| context |tak |Kontekst akcji skalowania automatycznego |
| sygnatura czasowa |tak |Sygnatura czasowa, gdy akcja skalowania automatycznego została wyzwolona |
| id |Yes |Identyfikator Menedżer zasobów ustawienia skalowania automatycznego |
| name |Yes |Nazwa ustawienia automatycznego skalowania |
| details informacje |Yes |Wyjaśnienie akcji podjętej przez usługę automatycznego skalowania i zmianę liczby wystąpień |
| subscriptionId |Yes |Identyfikator subskrypcji zasobu docelowego, który jest skalowany |
| resourceGroupName |Yes |Nazwa grupy zasobów dla zasobu docelowego, który jest skalowany |
| resourceName |Yes |Nazwa zasobu docelowego, który jest skalowany |
| resourceType |Yes |Trzy obsługiwane wartości: "Microsoft. classiccompute/DomainNames/Slots/role" — role usługi w chmurze "Microsoft. COMPUTE/virtualmachinescalesets"-Virtual Machine Scale Sets i "Microsoft. Web/dopuszczalna" — aplikacja sieci Web |
| resourceId |Yes |Identyfikator Menedżer zasobów zasobu docelowego, który jest skalowany |
| portalLink |Yes |Azure Portal Połącz ze stroną podsumowania zasobu docelowego |
| oldCapacity |Yes |Bieżąca (stara) liczba wystąpień, gdy automatyczne skalowanie zajęło akcję skalowania |
| newCapacity |Yes |Nowe wystąpienie, które automatycznie skaluje zasób do |
| properties |Nie |Opcjonalny. Zestaw < klucza, wartości > par (na przykład < słownika, ciąg >). Pole właściwości jest opcjonalne. W niestandardowym interfejsie użytkownika lub przepływie pracy opartym na aplikacji logiki można wprowadzać klucze i wartości, które mogą być przesyłane przy użyciu ładunku. Alternatywny sposób przekazywania właściwości niestandardowych z powrotem do wychodzącego wywołania elementu webhook polega na użyciu samego identyfikatora URI elementu webhook (jako parametrów zapytania). |

