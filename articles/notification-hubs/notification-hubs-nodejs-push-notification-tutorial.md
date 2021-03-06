---
title: Wysyłanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs i środowiska Node. js
description: Dowiedz się, jak wysyłać powiadomienia wypychane z aplikacji node. js za pomocą Notification Hubs.
keywords: Powiadomienie wypychane, powiadomienia wypychane, wypychanie Node. js push, wypychanie systemu iOS
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213194"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Wysyłanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs i środowiska Node. js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Przegląd

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut, korzystając z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

W tym przewodniku pokazano, jak wysyłać powiadomienia wypychane za pomocą Notification Hubs platformy Azure bezpośrednio z aplikacji [Node. js](https://nodejs.org) .

Omówione scenariusze obejmują wysyłanie powiadomień wypychanych do aplikacji na następujących platformach:

- Android
- iOS
- Platforma uniwersalna systemu Windows
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Usługa Azure Notification Hubs zapewnia łatwą w użyciu, wieloplatformową infrastrukturę do wysyłania powiadomień wypychanych do urządzeń przenośnych. Aby uzyskać szczegółowe informacje na temat infrastruktury usług, zobacz stronę [Notification Hubs platformy Azure](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node. js

Pierwszym krokiem w tym samouczku jest utworzenie nowej pustej aplikacji node. js. Aby uzyskać instrukcje dotyczące tworzenia aplikacji w języku Node. js, zobacz [Tworzenie i wdrażanie aplikacji node. js w usłudze Azure Web site][nodejswebsite], w [chmurze Node. js][Node.js Cloud Service] przy użyciu programu Windows PowerShell lub [witryny sieci Web z programem WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Skonfiguruj aplikację do używania Notification Hubs

Aby korzystać z usługi Azure Notification Hubs, należy pobrać i użyć [pakietu platformy Azure](https://www.npmjs.com/package/azure)w języku Node. js, który zawiera wbudowany zestaw bibliotek pomocnika, które komunikują się z usługami REST powiadomień wypychanych.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Korzystanie z programu Node Package Manager (NPM) w celu uzyskania pakietu

1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (Windows), **Terminal** (Mac) lub **bash** (Linux), i przejdź do folderu, w którym została utworzona pusta aplikacja.
2. Wykonaj `npm install azure-sb` w oknie poleceń.
3. Możesz ręcznie uruchomić `ls` `dir`polecenielub , aby sprawdzić, czy folderzostałutworzony.`node_modules`
4. W tym folderze Znajdź pakiet **platformy Azure** , który zawiera biblioteki potrzebne do uzyskania dostępu do centrum powiadomień.

> [!NOTE]
> Więcej informacji na temat instalowania NPM można znaleźć na oficjalnym [blogu npm](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importowanie modułu
Korzystając z edytora tekstów, Dodaj następujący tekst na początku `server.js` pliku aplikacji:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Konfigurowanie połączenia centrum powiadomień platformy Azure

`NotificationHubService` Obiekt umożliwia korzystanie z centrów powiadomień. Poniższy kod tworzy `NotificationHubService` obiekt dla centrum powiadomień o nazwie `hubname`. Dodaj ją w górnej części `server.js` pliku, po instrukcji importowania modułu platformy Azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Uzyskaj wartość połączenia `connectionstring` z [Azure Portal] , wykonując następujące czynności:

1. W okienku nawigacji po lewej stronie kliknij przycisk **Przeglądaj**.
2. Wybierz pozycję **Notification Hubs**, a następnie Znajdź centrum, którego chcesz użyć dla przykładu. Aby uzyskać pomoc dotyczącą tworzenia nowego centrum powiadomień, można zapoznać się z [samouczkiem dotyczącym sklepu Windows wprowadzenie](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
3. Wybierz **ustawienia**.
4. Kliknij pozycję **zasady dostępu**. Zobaczysz parametry połączenia dostępu współdzielonego i pełnego.

![Azure Portal — Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Możesz również pobrać parametry połączenia za pomocą polecenia cmdlet **Get-AzureSbNamespace** , które jest dostępne przez [Azure PowerShell](/powershell/azureps-cmdlets-docs) lub **przestrzeń nazw Azure SB show** Command z [interfejsem wiersza polecenia platformy Azure (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Architektura ogólna

`NotificationHubService` Obiekt uwidacznia następujące wystąpienia obiektów do wysyłania powiadomień wypychanych do określonych urządzeń i aplikacji:

- **Android** — Użyj `GcmService` obiektu, który jest dostępny w`notificationHubService.gcm`
- **iOS** — Użyj `ApnsService` obiektu, który jest dostępny w`notificationHubService.apns`
- **Windows Phone** — Użyj `MpnsService` obiektu, który jest dostępny w`notificationHubService.mpns`
- **Platforma uniwersalna systemu Windows** — Użyj `WnsService` obiektu, który jest dostępny w`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Instrukcje: Wysyłanie powiadomień push do aplikacji systemu Android

`GcmService` Obiekt`send` udostępnia metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu Android. `send` Metoda akceptuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli żaden tag nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — ładunek JSON lub nieprzetworzony ciąg komunikatu.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na temat formatu ładunku, zapoznaj się z [dokumentacją ładunku](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Poniższy kod używa `GcmService` wystąpienia uwidocznionego przez program, `NotificationHubService` aby wysyłać powiadomienia wypychane do wszystkich zarejestrowanych klientów.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji systemu iOS

Tak samo jak w przypadku aplikacji systemu Android opisanych `ApnsService` powyżej, obiekt `send` udostępnia metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji systemu iOS. `send` Metoda akceptuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli żaden tag nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — kod JSON lub ciąg komunikatu.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać więcej informacji na temat formatu ładunku, zapoznaj się z sekcją **ładunek powiadomień** w dokumencie [Podręcznik programowania powiadomień lokalnych i wypychanych](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Poniższy kod używa `ApnsService` wystąpienia uwidocznionego `NotificationHubService` przez program w celu wysyłania komunikatu o alercie do wszystkich klientów:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji Windows Phone

`MpnsService` Obiekt`send` udostępnia metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji Windows Phone. `send` Metoda akceptuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli żaden tag nie jest podany, powiadomienie jest wysyłane do wszystkich klientów.
- **Ładunek** — ładunek XML komunikatu.
- Docelowadlapowiadomieńwyskakujących.`toast`  -  `token`dla powiadomień kafelków.
- **NotificationClass** — priorytet powiadomienia. Zapoznaj się z sekcją **elementy nagłówka HTTP** w [powiadomieniach wypychanych z dokumentu serwera](https://msdn.microsoft.com/library/hh221551.aspx) , aby uzyskać prawidłowe wartości.
- **Opcje** — opcjonalne nagłówki żądań.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowych `TargetName` `NotificationClass` opcji i nagłówków, zapoznaj się z tematem [powiadomienia wypychane](https://msdn.microsoft.com/library/hh221551.aspx) na stronie serwera.

Następujący przykładowy kod używa `MpnsService` wystąpienia uwidocznionego `NotificationHubService` przez program w celu wysłania powiadomienia o wyskakującym wypchnięciu:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Instrukcje: Wysyłanie powiadomień wypychanych do aplikacji platforma uniwersalna systemu Windows (platformy UWP)

`WnsService` Obiekt`send` udostępnia metodę, która może służyć do wysyłania powiadomień wypychanych do aplikacji platforma uniwersalna systemu Windows.  `send` Metoda akceptuje następujące parametry:

- **Tagi** — identyfikator tagu. Jeśli żaden tag nie zostanie podany, powiadomienie zostanie wysłane do wszystkich zarejestrowanych klientów.
- **Ładunek** — ładunek wiadomości XML.
- **Typ** — Typ powiadomienia.
- **Opcje** — opcjonalne nagłówki żądań.
- **Wywołanie zwrotne** — funkcja wywołania zwrotnego.

Aby uzyskać listę prawidłowych typów i nagłówków żądań, zobacz sekcję [żądanie obsługi powiadomień wypychanych i nagłówki odpowiedzi](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Poniższy kod używa `WnsService` wystąpienia uwidocznionego przez program, `NotificationHubService` aby wysłać powiadomienie wyskakujące powiadomień wypychanych do aplikacji platformy UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Następne kroki

Przykładowe fragmenty kodu umożliwiają łatwe tworzenie infrastruktury usług w celu dostarczania powiadomień wypychanych do różnorodnych urządzeń. Teraz, gdy znasz już podstawy używania Notification Hubs z programem Node. js, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o tym, jak można dokładniej rozszerzyć te możliwości.

- Zapoznaj się z dokumentacją MSDN dotyczącą [usługi Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Aby uzyskać więcej przykładów i szczegółów implementacji, odwiedź witrynę [Zestaw Azure SDK dla platformy Node] w serwisie GitHub.

[Zestaw Azure SDK dla platformy Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
