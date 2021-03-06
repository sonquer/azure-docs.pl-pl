---
title: Dodawanie uwierzytelniania w systemie Android
description: Dowiedz się, jak za pomocą usługi Azure App Service uwierzytelniać użytkowników aplikacji systemu Android za pomocą dostawców tożsamości, takich jak Google, Facebook, Twitter i Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461644"
---
# <a name="add-authentication-to-your-android-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku dodasz uwierzytelnianie do projektu szybkiego startu todolist w systemie Android przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [wprowadzenie do Mobile Apps] , który należy wykonać w pierwszej kolejności.

## <a name="register"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _w systemie adresu_ URL. Można jednak użyć dowolnego wybranego schematu adresów URL. Powinna być unikatowa dla aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal]wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź `appname://easyauth.callback`.  Nazwa _użytkownika w tym_ ciągu to schemat adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Save** (Zapisz).

## <a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W Android Studio Otwórz projekt, który został ukończony przy użyciu samouczka Rozpoczynanie [Wprowadzenie do Mobile Apps]. W menu **Uruchom** kliknij pozycję **Uruchom aplikację**, a następnie sprawdź, czy po uruchomieniu aplikacji został zgłoszony nieobsługiwany wyjątek z kodem stanu 401 (nieautoryzowany).

     Ten wyjątek występuje, ponieważ aplikacja próbuje uzyskać dostęp do zaplecza jako nieuwierzytelniony użytkownik, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizujesz aplikację w celu uwierzytelniania użytkowników przed zażądaniem zasobów z Mobile Apps zaplecza.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Buforuj tokeny uwierzytelniania na kliencie
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Następne kroki
Po ukończeniu tego samouczka dotyczącego uwierzytelniania podstawowego Rozważ przejście do jednego z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji systemu Android](app-service-mobile-android-get-started-push.md).
  Dowiedz się, jak skonfigurować zaplecze Mobile Apps, aby użyć centrów powiadomień platformy Azure do wysyłania powiadomień wypychanych.
* [Włącz synchronizację w trybie offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę offline do aplikacji przy użyciu zaplecza Mobile Apps. Dzięki synchronizacji w trybie offline użytkownicy mogą korzystać z aplikacji mobilnej&mdash;przeglądania, dodawania i modyfikowania danych&mdash;nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Wprowadzenie do Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
