---
title: Jak korzystać z usługi Azure Notification Hubs przy użyciu języka PHP
description: Dowiedz się, jak używać platformy Azure Notification Hubs z zaplecza PHP.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263850"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak używać Notification Hubs w języku PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Możesz uzyskać dostęp do wszystkich funkcji Notification Hubs z poziomu zaplecza Java/PHP/Ruby przy użyciu interfejsu REST centrum powiadomień, jak opisano w temacie MSDN [Notification Hubs interfejsy API REST](https://msdn.microsoft.com/library/dn223264.aspx).

W tym temacie pokazano, jak:

* Tworzenie klienta REST dla funkcji Notification Hubs w języku PHP;
* Postępuj zgodnie z [samouczkiem](notification-hubs-ios-apple-push-notification-apns-get-started.md) wprowadzenie do wybranej platformy mobilnej, implementując część zaplecza w języku PHP.

## <a name="client-interface"></a>Interfejs klienta

Główny interfejs klienta może zapewniać te same metody, które są dostępne w [zestawie SDK programu .net Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx), co pozwala na bezpośrednie przetłumaczenie wszystkich samouczków i przykładów dostępnych w tej witrynie oraz współtworzenie przez społeczność Internetu.

Możesz znaleźć cały kod dostępny w [Przykład otoki REST języka PHP].

Na przykład, aby utworzyć klienta:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Aby wysłać powiadomienie natywne systemu iOS:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Wdrażanie

Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z [Samouczek z wprowadzeniem] do ostatniej sekcji, w której musisz zaimplementować zaplecze.
Ponadto, jeśli chcesz użyć kodu z [Przykład otoki REST języka PHP] i przejść bezpośrednio do sekcji [Ukończ samouczek](#complete-tutorial) .

Wszystkie szczegóły dotyczące implementacji pełnej otoki REST można znaleźć w [witrynie MSDN](https://msdn.microsoft.com/library/dn530746.aspx). W tej sekcji opisano implementację języka PHP głównych kroków wymaganych do uzyskania dostępu do punktów końcowych REST Notification Hubs:

1. Analizowanie parametrów połączenia
2. Generowanie tokenu autoryzacji
3. Wykonaj wywołanie HTTP

### <a name="parse-the-connection-string"></a>Analizowanie parametrów połączenia

Oto główna Klasa implementująca klienta, którego Konstruktor analizuje parametry połączenia:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Tworzenie tokenu zabezpieczającego

Zapoznaj się z dokumentacją platformy Azure, aby uzyskać informacje na temat [tworzenia tokenu zabezpieczającego SAS](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Dodaj metodę `generateSasToken` do klasy `NotificationHub`, aby utworzyć token oparty na identyfikatorze URI bieżącego żądania, a poświadczenia wyodrębnione z parametrów połączenia.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Wyślij powiadomienie

Najpierw poinformuj nas o klasie reprezentującej powiadomienie.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Ta klasa jest kontenerem dla natywnej treści powiadomienia lub zestawem właściwości w przypadku powiadomienia dotyczącego szablonu oraz zestawu nagłówków, który zawiera format (natywną platformę lub szablon) i właściwości specyficzne dla platformy (na przykład właściwość wygaśnięcia firmy Apple i WNS nagłówki).

Zapoznaj się z [dokumentacją interfejsów API REST Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) i format określonych platform powiadomień dla wszystkich dostępnych opcji.

Za pomocą tej klasy możemy teraz napisać metody wysyłania powiadomień w ramach klasy `NotificationHub`:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Powyższe metody wysyłają żądanie HTTP POST do punktu końcowego `/messages` centrum powiadomień z prawidłową treścią i nagłówkami, aby wysłać powiadomienie.

## <a name="complete-tutorial"></a>Ukończ samouczek

Teraz możesz ukończyć samouczek wprowadzenie, wysyłając powiadomienie z zaplecza PHP.

Zainicjuj Notification Hubs klienta (Zastąp ciąg połączenia i nazwę centrum zgodnie z instrukcjami w [Samouczek z wprowadzeniem]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Następnie Dodaj kod wysyłania w zależności od docelowej platformy mobilnej.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Sklep Windows i Windows Phone 8,1 (bez Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 i 8,1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Uruchomienie kodu w języku PHP powinno teraz spowodować powiadomienie wyświetlane na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki

W tym temacie pokazano, jak utworzyć prosty klient protokołu Java REST dla Notification Hubs. W tym miejscu można wykonać następujące czynności:

* Pobierz pełny [przykład otoki REST języka PHP], który zawiera cały kod.
* Kontynuuj uczenie się Notification Hubs funkcji tagowania w [samouczek z najświeższymi wiadomościami]
* Dowiedz się więcej o wypychaniu powiadomień do poszczególnych użytkowników w temacie [Powiadamianie użytkowników — samouczek]

Aby uzyskać więcej informacji, zobacz również [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[Przykład otoki REST języka PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Samouczek z wprowadzeniem]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
