---
title: Azure Notification Hubs bezpieczne wypychanie dla systemu Windows
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane na platformie Azure. Przykłady kodu napisane w języku C# z użyciem interfejsu API programu .NET.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530735"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Bezpieczne wypychanie powiadomień z usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Przegląd

Obsługa powiadomień wypychanych w programie Microsoft Azure pozwala uzyskać dostęp do łatwej w skalowaniu infrastruktury wypychania z obsługą wielu platform, która znacznie upraszcza implementację powiadomień wypychanych zarówno dla aplikacji dla klientów, jak i dla przedsiębiorstw w przypadku urządzeń przenośnych. poszczególnych.

Ze względu na ograniczenia prawne lub zabezpieczenia czasami aplikacja może chcieć uwzględnić coś w powiadomieniu, którego nie można przesłać za pomocą standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska przez wysyłanie poufnych informacji za pomocą bezpiecznego, uwierzytelnionego połączenia między urządzeniem klienckim a zaplecze aplikacji.

Na wysokim poziomie przepływ jest następujący:

1. Zaplecze aplikacji:
   * Przechowuje bezpieczny ładunek w bazie danych zaplecza.
   * Wysyła identyfikator tego powiadomienia do urządzenia (żadne zabezpieczone informacje nie są wysyłane).
2. Aplikacja na urządzeniu podczas otrzymywania powiadomienia:
   * Urządzenie kontaktuje się z wewnętrzną prośbą o bezpieczny ładunek.
   * Aplikacja może wyświetlać ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) przyjęto założenie, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to całkowite bezproblemowe działanie, ponieważ urządzenie może pobrać bezpieczny ładunek powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po odebraniu powiadomienia powinna wyświetlić ogólne powiadomienie z monitem użytkownika o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomienia.

Ten samouczek bezpiecznego wypychania pokazuje, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek jest oparty na samouczku [Powiadamianie użytkowników](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , dlatego należy najpierw wykonać kroki opisane w tym samouczku.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzono i skonfigurowano centrum powiadomień zgodnie z opisem w [wprowadzenie z Notification Hubs (Sklep Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Należy również pamiętać, że Windows Phone 8,1 wymaga poświadczeń systemu Windows (nie Windows Phone), a zadania w tle nie działają na Windows Phone 8,0 lub Silverlight 8,1. W przypadku aplikacji ze sklepu Windows można otrzymywać powiadomienia za pośrednictwem zadania w tle tylko wtedy, gdy aplikacja jest włączona na ekranie blokady (kliknij pole wyboru w Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modyfikuj projekt Windows Phone

1. W projekcie **NotifyUserWindowsPhone** Dodaj następujący kod do App.XAML.cs, aby zarejestrować zadanie wypychania w tle. Dodaj poniższy wiersz kodu na końcu metody `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Nadal w App.xaml.cs, Dodaj następujący kod bezpośrednio po metodzie `OnLaunched()`:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. Dodaj następujące instrukcje `using` w górnej części pliku App.xaml.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. W menu **Plik** programu Visual Studio kliknij polecenie **Zapisz wszystko**.

## <a name="create-the-push-background-component"></a>Tworzenie składnika tła wypychania

Następnym krokiem jest utworzenie składnika w tle.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy węzeł najwyższego poziomu rozwiązania (w tym przypadku**rozwiązanie SecurePush** ), a następnie kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Nowy projekt**.
2. Rozwiń węzeł **aplikacje ze sklepu**, a następnie kliknij pozycję **Windows Phone aplikacje**, a następnie kliknij pozycję **składnik środowisko wykonawcze systemu Windows (Windows Phone)** . Nazwij projekt **PushBackgroundComponent**, a następnie kliknij przycisk **OK** , aby utworzyć projekt.

    ![][12]
3. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **PushBackgroundComponent (Windows Phone 8,1)** , a następnie kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Klasa**. Nadaj nowej klasie nazwę `PushBackgroundTask.cs`. Kliknij przycisk **Dodaj** , aby wygenerować klasę.
4. Zastąp całą zawartość definicji `PushBackgroundComponent` przestrzeni nazw następującym kodem, zastępując symbol zastępczy `{back-end endpoint}` z punktem końcowym zaplecza uzyskanym podczas wdrażania zaplecza:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **PushBackgroundComponent (Windows Phone 8,1)** , a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
6. Po lewej stronie kliknij opcję **Online**.
7. W polu **Wyszukaj** wpisz ciąg **Klient HTTP**.
8. Na liście wyników kliknij pozycję **biblioteki klienta http firmy Microsoft**, a następnie kliknij przycisk **Instaluj**. Ukończ instalację.
9. W polu **Wyszukaj** w menedżerze pakietów NuGet wpisz ciąg **Json.net**. Zainstaluj pakiet **JSON.NET** , a następnie zamknij okno Menedżera pakietów NuGet.
10. Dodaj następujące instrukcje `using` w górnej części pliku `PushBackgroundTask.cs`:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. W Eksplorator rozwiązań w projekcie **NotifyUserWindowsPhone (Windows Phone 8,1)** kliknij prawym przyciskiem myszy pozycję **odwołania**, a następnie kliknij pozycję **Dodaj odwołanie...** . W oknie dialogowym Menedżer odwołań zaznacz pole wyboru obok pozycji **PushBackgroundComponent**, a następnie kliknij przycisk **OK**.
12. W Eksplorator rozwiązań kliknij dwukrotnie pozycję **Package. appxmanifest** w projekcie **NotifyUserWindowsPhone (Windows Phone 8,1)** . W obszarze **powiadomienia**ustaw opcję **wyskakujący z możliwością** **tak**.

    ![][3]
13. Nadal w **pakiecie Package. appxmanifest**kliknij menu **deklaracji** w górnej części strony. Na liście **dostępnych deklaracji** kliknij pozycję **zadania w tle**, a następnie kliknij przycisk **Dodaj**.
14. W **Package. appxmanifest**, w obszarze **Właściwości**, zaznacz opcję **powiadomień wypychanych**.
15. W **pakiecie Package. appxmanifest**w obszarze **Ustawienia aplikacji**wpisz **PushBackgroundComponent. PushBackgroundTask** w polu **punkt wejścia** .

    ![][13]
16. W menu **Plik** kliknij polecenie **Zapisz wszystko**.

## <a name="run-the-application"></a>Uruchom aplikację

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie Visual Studio Uruchom aplikację internetowego interfejsu API **AppBackend** . Zostanie wyświetlona strona sieci Web ASP.NET.
2. W programie Visual Studio Uruchom aplikację Windows Phone **NotifyUserWindowsPhone (Windows Phone 8,1)** . Emulator Windows Phone uruchamia i ładuje aplikację automatycznie.
3. W interfejsie użytkownika aplikacji **NotifyUserWindowsPhone** wprowadź nazwę użytkownika i hasło. Mogą to być dowolne ciągi, ale muszą mieć tę samą wartość.
4. W interfejsie użytkownika aplikacji **NotifyUserWindowsPhone** kliknij pozycję **Zaloguj się i zarejestruj**. Następnie kliknij pozycję **Wyślij wypychanie**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
