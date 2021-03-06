---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1c3f2009dc71df1a5496d585bdcba986a79ac0d0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768467"
---
## <a name="prepare-your-web-app"></a>Przygotowywanie aplikacji internetowej

Aby powiązać niestandardowy certyfikat protokołu SSL (certyfikat innej firmy lub certyfikat usługi App Service) z Twoją aplikacją internetową, Twój [Plan usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) musi znajdować się w warstwie **Podstawowa**, **Standardowa**, **Premium** lub **Izolowana**. W tym kroku musisz się upewnić, że Twoja aplikacja internetowa jest w obsługiwanej warstwie cenowej.

### <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Otwórz [Portalu Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Przejdź do swojej aplikacji internetowej

Wyszukaj i wybierz **App Services**.

![Wybierz App Services](./media/app-service-ssl-prepare-app/app-services.png)

Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-ssl-prepare-app/select-app.png)

Na stronie Zarządzanie aplikacji sieci Web.  

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie Twojej aplikacji internetowej przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)** .

![Menu skalowania w górę](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Upewnij się, że Twoja aplikacja internetowa nie znajduje się w warstwie **F1** ani **D1**. Bieżąca warstwa Twojej aplikacji internetowej jest wyróżniona ciemnoniebieskim polem.

![Sprawdzanie warstwy cenowej](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Niestandardowy protokół SSL nie jest obsługiwany w warstwie **F1** ani **D1**. Jeśli musisz skalować w górę, wykonaj kroki opisane w następnej sekcji. W przeciwnym razie zamknij stronę **Skalowanie w górę** i przejdź do sekcji [Skalowanie w górę planu usługi App Service](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij przycisk **Zastosuj**.

![Wybieranie warstwy cenowej](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Powiadomienie o skalowaniu w górę](./media/app-service-ssl-prepare-app/scale-notification.png)

