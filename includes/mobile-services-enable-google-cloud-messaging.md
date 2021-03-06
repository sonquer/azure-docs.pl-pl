---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a7d0a899b4a2ef86153e82a4e3f04e7ec430a1bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183823"
---
1. Przejdź do konsoli [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager) i zaloguj się przy użyciu poświadczeń konta Google. 
2. Wybierz opcję **Create Project** (Utwórz projekt) na pasku narzędzi. 
   
    ![Tworzenie nowego projektu](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Aby uzyskać **nazwę projektu**, wprowadź nazwę swojego projektu i kliknij przycisk **Utwórz**.
4. Naciśnij przycisk **alerty** na pasku narzędzi i wybierz swój projekt z listy. Zostanie wyświetlony pulpit nawigacyjny projektu. Możesz także przejść bezpośrednio do pulpitu nawigacyjnego przy użyciu adresu URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Wybieranie projektu w alertach](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Zanotuj **Numer projektu** na kafelku pulpitu nawigacyjnego **Informacje o projekcie**. 

    ![Identyfikator projektu](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Na kafelku pulpitu nawigacyjnego **Interfejsy API** wybierz pozycję **Przejdź do omówienia interfejsów API**. 

    ![Link omówienia interfejsu API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Na stronie **Interfejs API** wybierz pozycję **WŁĄCZ INTERFEJSY API I USŁUGI**. 

    ![Przycisk włączania interfejsów API i usług](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Wyszukaj i wybierz usługę **Google Cloud Messaging**. 

    ![Wyszukiwanie i wybieranie usługi Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Aby włączyć usługę Google Cloud Messaging dla projektu, wybierz pozycję **WŁĄCZ**.

    ![Włączanie usługi Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Na pasku narzędzi wybierz pozycję **Utwórz poświadczenia**. 

    ![Przycisk Tworzenie poświadczeń](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Na stronie **Dodawanie poświadczeń do projektu** wybierz link **Klucz interfejsu API**. 

    ![Przycisk Tworzenie poświadczeń](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Na stronie **Klucz interfejsu API** wybierz pozycję **Utwórz/Zapisz**. W poniższym przykładzie jest wybrana opcja **Adresy IP**, a **0.0.0.0/0** jest wprowadzone dla dozwolonych adresów IP. Należy odpowiednio ograniczyć klucz interfejsu API. 

    ![Klucz interfejsu API — przycisk Utwórz](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Skopiuj **Klucz interfejsu API** do Schowka, a następnie zapisz go gdzieś. 

    ![Kopiowanie klucza interfejsu API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Ta wartość klucza interfejsu API zostanie użyta do włączenia na platformie Azure funkcji uwierzytelniania za pomocą usługi GCM i wysyłania powiadomień wypychanych w imieniu aplikacji. Aby przejść z powrotem do pulpitu nawigacyjnego projekt, należy użyć adresu URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

