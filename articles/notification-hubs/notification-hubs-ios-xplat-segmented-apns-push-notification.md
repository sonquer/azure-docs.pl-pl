---
title: Powiadomienia wypychane do określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: W tym samouczku dowiesz się, jak wysyłać powiadomienia wypychane do określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 618be4bc2d7669879daa927d5c4392b1097d29af
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774885"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Samouczek: powiadomienia wypychane do określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Przegląd

W tym samouczku pokazano, jak za pomocą usługi Azure Notification Hubs rozgłaszać powiadomienia o najświeższych wiadomościach do aplikacji systemu iOS. Po zakończeniu możesz zarejestrować się w celu uzyskania ważnych kategorii wiadomości, które Cię interesują, i odbierać tylko powiadomienia wypychane dla tych kategorii. Ten scenariusz jest typowym wzorcem dla wielu aplikacji wymagających wysyłania powiadomień do grup użytkowników, które wcześniej zadeklarowały zainteresowanie nimi, na przykład czytników danych RSS, aplikacji dla fanów muzyki itp.

Scenariusze rozgłaszania są włączone w przypadku co najmniej jednego *tagu* podczas tworzenia rejestracji w centrum powiadomień. Gdy powiadomienia są wysyłane do znacznika, urządzenia, które zostały zarejestrowane dla tagu, otrzymają powiadomienie. Tagi to po prostu ciągi, dlatego nie muszą być aprowizowane z wyprzedzeniem. Aby uzyskać więcej informacji na temat tagów, zobacz [Notification Hubs Routing and Tag Expressions (Wyrażenia routingu i tagów w usłudze Notification Hubs)](notification-hubs-tags-segment-push-message.md).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Dodaj wybór kategorii do aplikacji
> * Wysyłanie powiadomień z tagami
> * Wysyłanie powiadomień z urządzenia
> * Uruchamianie aplikacji i generowanie powiadomień

## <a name="prerequisites"></a>Wymagania wstępne

Ten temat jest oparty na aplikacji utworzonej w [samouczku: powiadomienia wypychane do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs][get-started]. Przed rozpoczęciem tego samouczka musisz mieć już ukończony [Samouczek: powiadomienia wypychane do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącego scenorysu, który umożliwia użytkownikowi wybranie kategorii do zarejestrowania. Kategorie wybrane przez użytkownika są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. W **MainStoryboard_iPhone. scenorys** Dodaj następujące składniki z biblioteki obiektów:

   * Etykieta z tekstem "z najświeższymi wiadomościami",
   * Etykiety z dokumentami kategorii "World", "Polityka", "Business", "Technology", "nauka", "Sport",
   * Sześć przełączników, jednej na kategorię, każdy **stan** przełącznika jest domyślnie **wyłączony** .
   * Jeden przycisk o nazwie "Subskrybuj"

     Scenorys powinien wyglądać następująco:

     ![Konstruktor interfejsu Xcode][3]

2. W edytorze asystenta Utwórz opcje dla wszystkich przełączników i Wywołaj je "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Utwórz akcję dla przycisku o nazwie `subscribe`; `ViewController.h` powinien zawierać następujący kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Utwórz nową **klasę dotykową kakao** o nazwie `Notifications`. Skopiuj następujący kod w sekcji interfejsu pliku powiadomienia. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Dodaj następującą dyrektywę importu do powiadomień. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Skopiuj następujący kod w sekcji implementacji pliku powiadomienia. m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Ta klasa używa magazynu lokalnego do przechowywania i pobierania kategorii wiadomości odbieranych przez to urządzenie. Zawiera również metodę rejestrowania dla tych kategorii przy użyciu rejestracji [szablonu](notification-hubs-templates-cross-platform-push-messages.md) .

7. W pliku `AppDelegate.h` Dodaj instrukcję import dla `Notifications.h` i Dodaj właściwość dla wystąpienia klasy `Notifications`:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. W metodzie `didFinishLaunchingWithOptions` w `AppDelegate.m`Dodaj kod, aby zainicjować wystąpienie powiadomień na początku metody.  
    `HUBNAME` i `HUBLISTENACCESS` (zdefiniowane w `hubinfo.h`) powinny mieć już symbole zastępcze `<hub name>` i `<connection string with listen access>` zamienione na nazwę centrum powiadomień oraz parametry połączenia dla *DefaultListenSharedAccessSignature* , które zostały uzyskane wcześniej

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj bezpieczne, należy przy użyciu aplikacji klienckiej dystrybuować wyłącznie klucz dostępu do nasłuchiwania. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

9. W metodzie `didRegisterForRemoteNotificationsWithDeviceToken` w `AppDelegate.m`Zastąp kod w metodzie poniższym kodem, aby przekazać token urządzenia do klasy `notifications`. Klasa `notifications` wykonuje rejestrację w celu otrzymywania powiadomień z kategoriami. Jeśli użytkownik zmieni wybrane kategorie, wywołaj metodę `subscribeWithCategories` w odpowiedzi na przycisk **Subskrybuj** , aby je zaktualizować.

    > [!NOTE]
    > Ponieważ token urządzenia przypisany przez Apple Push Notification Service (APNS) można zmienić w dowolnym momencie, należy regularnie rejestrować powiadomienia, aby uniknąć błędów powiadomień. Poniższy przykład przeprowadza rejestrację w celu otrzymywania powiadomień za każdym razem, gdy aplikacja jest uruchamiana. W przypadku często uruchamianych aplikacji — więcej niż raz dziennie — prawdopodobnie możesz pominąć rejestrację, aby zachować przepustowość, jeśli od poprzedniej rejestracji upłynął czas krótszy niż jeden dzień.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    W tym momencie nie powinien istnieć żaden inny kod w metodzie `didRegisterForRemoteNotificationsWithDeviceToken`.

10. Poniższe metody powinny już znajdować się w `AppDelegate.m` z samouczka Rozpoczynanie [pracy z Notification Hubs][get-started] . Jeśli nie, Dodaj je.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Ta metoda obsługuje powiadomienia otrzymywane, gdy aplikacja jest uruchomiona, wyświetlając prostą **UIAlert**.

11. W `ViewController.m`Dodaj instrukcję `import` dla `AppDelegate.h` i skopiuj następujący kod do metody `subscribe` wygenerowanej przez XCode. Ten kod aktualizuje rejestrację powiadomień w celu użycia nowych tagów kategorii wybranych przez użytkownika w interfejsie użytkownika.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Ta metoda tworzy `NSMutableArray` kategorii i używa klasy `Notifications` do przechowywania listy w magazynie lokalnym i rejestrowania odpowiednich tagów w centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.

12. W `ViewController.m`Dodaj następujący kod w metodzie `viewDidLoad`, aby ustawić interfejs użytkownika w oparciu o poprzednio zapisane kategorie.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Aplikacja może teraz przechowywać zestaw kategorii w lokalnym magazynie urządzenia użytym do zarejestrowania się w centrum powiadomień przy każdym uruchomieniu aplikacji. Użytkownik może zmienić wybór kategorii w czasie wykonywania i kliknąć metodę `subscribe`, aby zaktualizować rejestrację urządzenia. Następnie zaktualizujesz aplikację, aby wysyłać najświeższe powiadomienia o wiadomościach bezpośrednio w samej aplikacji.

## <a name="optional-send-tagged-notifications"></a>obowiązkowe Wyślij otagowane powiadomienia

Jeśli nie masz dostępu do programu Visual Studio, możesz przejść do następnej sekcji i wysyłać powiadomienia z samej aplikacji. Możesz również wysłać powiadomienie o odpowiednim szablonie z [Azure Portal] przy użyciu karty debugowanie dla centrum powiadomień.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>obowiązkowe Wysyłanie powiadomień z urządzenia

Zwykle powiadomienia są wysyłane przez usługę zaplecza, ale możesz wysyłać do nich powiadomienia o najświeższych wiadomościach bezpośrednio z aplikacji. W tym celu należy zaktualizować metodę `SendNotificationRESTAPI`, która została zdefiniowana w samouczku Rozpoczynanie [pracy z Notification Hubs][get-started] .

1. W `ViewController.m`zaktualizuj metodę `SendNotificationRESTAPI` w następujący sposób, aby akceptowała parametr dla tagu Category i wysyłał odpowiednie powiadomienie dotyczące [szablonu](notification-hubs-templates-cross-platform-push-messages.md) .

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. W `ViewController.m`zaktualizuj akcję `Send Notification`, jak pokazano w poniższym kodzie. Dzięki temu powiadomienia są wysyłane pojedynczo i wysyłane do wielu platform.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Skompiluj ponownie projekt i upewnij się, że nie ma żadnych błędów kompilacji.

## <a name="run-the-app-and-generate-notifications"></a>Uruchamianie aplikacji i generowanie powiadomień

1. Naciśnij przycisk Run (Uruchom), aby skompilować projekt i uruchomić aplikację. Wybierz niektóre z najważniejszych opcji Aktualności, aby subskrybować, a następnie naciśnij przycisk **Subskrybuj** . Powinno zostać wyświetlone okno dialogowe informujące o subskrybowaniu powiadomień.

    ![Przykładowe powiadomienie w systemie iOS][1]

    Po wybraniu opcji **Subskrybuj**Aplikacja konwertuje wybrane kategorie na Tagi i zażąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień.

2. Wprowadź komunikat, który ma być wysyłany jako najświeższe wiadomości, a następnie naciśnij przycisk **Wyślij powiadomienie** . Alternatywnie można uruchomić aplikację konsolową .NET w celu wygenerowania powiadomień.

    ![Zmień preferencje powiadomień w systemie iOS][2]

3. Każde urządzenie subskrybuje najświeższe wiadomości odbiera wysłane powiadomienia o wiadomościach.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano powiadomienia dotyczące emisji do określonych urządzeń z systemem iOS, które zostały zarejestrowane dla kategorii. Aby dowiedzieć się, jak wypchnąć zlokalizowane powiadomienia, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień zlokalizowanych](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
