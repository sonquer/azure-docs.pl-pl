---
title: Wywoływanie elementu webhook przy użyciu klasycznego alertu metryki w Azure Monitor
description: Dowiedz się, jak przekierować alerty metryki platformy Azure do innych systemów nienależących do platformy Azure.
author: harelbr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: fd4bf2d404a7152da04e72d323f463c18167f5bf
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705517"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Wywoływanie elementu webhook przy użyciu klasycznego alertu metryki w Azure Monitor
Elementy webhook umożliwiają kierowanie powiadomień o alertach platformy Azure do innych systemów w przypadku akcji wykonywanych po przetworzeniu lub po ich wykonaniu. Możesz użyć elementu webhook dla alertu, aby skierować go do usług, które wysyłają wiadomości SMS, aby rejestrować usterki w celu powiadomienia zespołu za pośrednictwem usług rozmowy lub Messaging lub dla różnych innych akcji. 

W tym artykule opisano sposób ustawiania elementu webhook dla alertu dotyczącego metryki platformy Azure. Przedstawiono w nim również, w jaki sposób ma być używany ładunek HTTP POST na element webhook. Aby uzyskać informacje na temat konfiguracji i schematu alertu dziennika aktywności platformy Azure (alert dotyczący zdarzeń), zobacz [wywoływanie elementu webhook w ramach alertu dotyczącego dziennika aktywności platformy Azure](alerts-log-webhook.md).

Alerty platformy Azure używają POST protokołu HTTP do wysyłania zawartości alertu w formacie JSON do identyfikatora URI elementu webhook, który jest udostępniany podczas tworzenia alertu. Schemat został zdefiniowany w dalszej części tego artykułu. Identyfikator URI musi być prawidłowym punktem końcowym HTTP lub HTTPS. Usługa Azure zapisuje wpis na żądanie po aktywowaniu alertu.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurowanie elementów webhook za pośrednictwem Azure Portal
Aby dodać lub zaktualizować identyfikator URI elementu webhook, w [Azure Portal](https://portal.azure.com/)przejdź do obszaru **Tworzenie/aktualizowanie alertów**.

![Dodawanie okienka reguły alertu](./media/alerts-webhooks/Alertwebhook.png)

Można również skonfigurować alert do publikowania w identyfikatorze URI elementu webhook przy użyciu [poleceń cmdlet Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [MIĘDZYPLATFORMOWEGO interfejsu wiersza polecenia](../../azure-monitor/platform/cli-samples.md#work-with-alerts)lub [Azure monitor interfejsów API REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Uwierzytelnianie elementu webhook
Element webhook może być uwierzytelniany przy użyciu autoryzacji opartej na tokenach. Identyfikator URI elementu webhook jest zapisywany z IDENTYFIKATORem tokenu. Na przykład: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schemat ładunku
Operacja POST zawiera następujący ładunek i schemat JSON dla wszystkich alertów opartych na metrykach:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Pole | Obowiązkowy | Stały zestaw wartości | Uwagi |
|:--- |:--- |:--- |:--- |
| status |Tak |Aktywowane, rozwiązane |Stan alertu na podstawie ustawionych warunków. |
| context |Tak | |Kontekst alertu. |
| sygnatura czasowa |Tak | |Godzina, o której alert został wyzwolony. |
| id |Tak | |Każda reguła alertu ma unikatowy identyfikator. |
| name |Tak | |Nazwa alertu. |
| description |Tak | |Opis alertu. |
| conditionType |Tak |Metryka, zdarzenie |Obsługiwane są dwa typy alertów: Metryka i zdarzenie. Alerty metryk są oparte na warunku metryki. Alerty zdarzeń są oparte na zdarzeniu w dzienniku aktywności. Użyj tej wartości, aby sprawdzić, czy alert jest oparty na metryce czy zdarzeniu. |
| condition |Tak | |Określone pola do sprawdzenia na podstawie wartości **conditiontype** . |
| MetricName |W przypadku alertów dotyczących metryk | |Nazwa metryki, która definiuje elementy monitorowane przez regułę. |
| metricUnit |W przypadku alertów dotyczących metryk |Bajty, BytesPerSecond, Count, CountPerSecond, procent, s |Jednostka dozwolona w metryce. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |W przypadku alertów dotyczących metryk | |Rzeczywista wartość metryki, która spowodowała alert. |
| threshold |W przypadku alertów dotyczących metryk | |Wartość progowa, przy której alert jest aktywowany. |
| windowSize |W przypadku alertów dotyczących metryk | |Okres czasu, który jest używany do monitorowania aktywności alertu na podstawie progu. Wartość musi mieścić się w przedziale od 5 minut do 1 dnia. Wartość musi być w formacie czasu trwania ISO 8601. |
| timeAggregation |W przypadku alertów dotyczących metryk |Średnia, Ostatnia, maksimum, minimum, brak, suma |Sposób, w jaki zbierane dane powinny być połączone z upływem czasu. Wartość domyślna to Average. Zobacz [dozwolone wartości](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |W przypadku alertów dotyczących metryk | |Operator używany do porównywania bieżących danych metryki z progiem zestawu. |
| subscriptionId |Tak | |Identyfikator subskrypcji platformy Azure. |
| resourceGroupName |Tak | |Nazwa grupy zasobów dla zasobu, którego to dotyczy. |
| resourceName |Tak | |Nazwa zasobu, którego dotyczy ten zasób. |
| resourceType |Tak | |Typ zasobu, którego dotyczy ten zasób. |
| resourceId |Tak | |Identyfikator zasobu, którego dotyczy ten zasób. |
| resourceRegion |Tak | |Region lub lokalizacja zasobu, którego to dotyczy. |
| portalLink |Tak | |Bezpośredni link do strony podsumowania zasobów portalu. |
| properties |N |Opcjonalne |Zestaw par klucz/wartość, które zawierają szczegółowe informacje o zdarzeniu. Na przykład `Dictionary<String, String>`. Pole właściwości jest opcjonalne. W niestandardowym interfejsie użytkownika lub przepływie pracy opartym na aplikacji logiki użytkownicy mogą wprowadzać pary klucz/wartość, które mogą być przekazane za pośrednictwem ładunku. Alternatywny sposób przekazywania właściwości niestandardowych z powrotem do elementu webhook odbywa się za pośrednictwem samego identyfikatora URI elementu webhook (jako parametrów zapytania). |

> [!NOTE]
> Pole **Właściwości** można ustawić tylko przy użyciu [Azure monitor interfejsów API REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o alertach i elementach webhook systemu Azure w filmie wideo [Integruj alerty platformy Azure z usługą usługi PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Dowiedz się, jak [wykonywać skrypty Azure Automation (elementy Runbook) w alertach platformy Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Dowiedz się, jak za [pomocą aplikacji logiki wysyłać wiadomości SMS za pośrednictwem usługi Twilio z poziomu alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Dowiedz się, jak za [pomocą aplikacji logiki wysyłać komunikaty o zapasach z alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Dowiedz się, jak za [pomocą aplikacji logiki wysyłać komunikaty do kolejki platformy Azure z poziomu alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

