---
title: Zarządzanie ruchem do aplikacji wielodostępnych przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera wskazówki dotyczące sposobu konfigurowania aplikacji sieci Web usługi Azure App Service jako członków w puli zaplecza na istniejącej lub nowej bramie aplikacji.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075159"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurowanie App Service przy użyciu Application Gateway

Ponieważ usługa App Service jest usługą z wieloma dzierżawami zamiast wdrożenia dedykowanego, używa nagłówka hosta w żądaniu przychodzącym, aby rozwiązać żądanie do poprawnego punktu końcowego usługi App Service. Zwykle nazwa DNS aplikacji, która z kolei jest nazwą DNS skojarzoną z frontonem usługi App Service, różni się od nazwy domeny usługi aplikacji zaplecza. W związku z tym nagłówek hosta w oryginalnym żądaniu odebranym przez bramę aplikacji nie jest taki sam jak nazwa hosta usługi wewnętrznej bazy danych. Z tego względu, jeśli nagłówek hosta w żądaniu z bramy Application Gateway do zaplecza nie zostanie zmieniony na nazwę hosta usługi wewnętrznej bazy danych, nadmiarowe punkty końcowe z wieloma dzierżawcami nie będą mogły rozpoznać żądania do prawidłowego punktu końcowego.

Application Gateway udostępnia przełącznik o nazwie `Pick host name from backend address`, który zastępuje nagłówek hosta w żądaniu nazwą hosta zaplecza, gdy żądanie jest kierowane z Application Gateway do zaplecza. Ta funkcja umożliwia obsługę zaplecza z wieloma dzierżawami, takich jak usługa Azure App Service i API Management. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Tworzenie puli zaplecza i dodawanie do niej App Service
> - Tworzenie ustawień HTTP i sondy niestandardowej z włączonymi przełącznikami "Wybierz nazwę hosta"

## <a name="prerequisites"></a>Wymagania wstępne

- Brama aplikacji: Jeśli nie masz istniejącej bramy aplikacji, zobacz jak [utworzyć bramę aplikacji](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) .
- App Service: Jeśli nie masz istniejącej usługi App Service, zobacz [dokumentację usługi App Service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Dodawanie usługi App Service jako puli zaplecza

1. W Azure Portal Otwórz widok konfiguracji bramy aplikacji.

2. W obszarze **Pule zaplecza**kliknij pozycję **Dodaj** , aby utworzyć nową pulę zaplecza.

3. Podaj odpowiednią nazwę puli zaplecza. 

4. W obszarze **obiekty docelowe**kliknij listę rozwijaną, a następnie wybierz **App Services** jako opcję.

5. Zostanie wyświetlona lista rozwijana bezpośrednio poniżej listy rozwijanej **targets** , która będzie zawierać listę App Services. Z tej listy rozwijanej wybierz App Service, które chcesz dodać jako element członkowski puli zaplecza, a następnie kliknij przycisk Dodaj.

   ![Zaplecze usługi App Service](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > Na liście rozwijanej będą wypełniane tylko te usługi aplikacji, które znajdują się w tej samej subskrypcji co Application Gateway. Jeśli chcesz użyć usługi App Service, która znajduje się w innej subskrypcji niż ta, w której znajduje się Application Gateway, a następnie wybierz pozycję **App Services** na liście rozwijanej **elementy docelowe** , zaznacz opcję **adres IP lub nazwa hosta** , a następnie wprowadź nazwę hosta (przykład. azurewebsites.net) usługi App Service.

## <a name="create-http-settings-for-app-service"></a>Tworzenie ustawień protokołu HTTP dla usługi App Service

1. W obszarze **Ustawienia protokołu HTTP**kliknij przycisk **Dodaj** , aby utworzyć nowe ustawienie protokołu HTTP.

2. Wprowadź nazwę ustawienia protokołu HTTP i możesz włączyć lub wyłączyć koligację opartą na plikach cookie zgodnie z wymaganiami.

3. Wybierz protokół jako HTTP lub HTTPS zgodnie z przypadkiem użycia. 

   > [!NOTE]
   > W przypadku wybrania protokołu HTTPS nie ma potrzeby przekazywania żadnych certyfikatów uwierzytelniania lub zaufanego certyfikatu głównego, aby dozwolonych zaplecza usługi App Service, ponieważ usługa App Service jest zaufaną usługą platformy Azure.

4. Zaznacz pole wyboru dla **App Service** . Należy pamiętać, że przełączniki `Create a probe with pick host name from backend address` i `Pick host name from backend address` będą automatycznie włączone.`Pick host name from backend address` spowoduje zastąpienie nagłówka hosta w żądaniu nazwą hosta zaplecza, gdy żądanie zostanie przekazane z Application Gateway do zaplecza.  

   `Create a probe with pick host name from backend address` automatycznie tworzy sondę kondycji i kojarzy ją z tym ustawieniem protokołu HTTP. Nie trzeba tworzyć żadnej innej sondy kondycji dla tego ustawienia protokołu HTTP. Możesz sprawdzić, czy nowa sonda o nazwie <HTTP Setting name><Unique GUID> została dodana na liście sond kondycji i czy ma już przełącznik `Pick host name from backend http settings enabled`.

   Jeśli masz już co najmniej jedno ustawienie HTTP, które jest używane dla usługi App Service, a jeśli te ustawienia protokołu HTTP używają tego samego protokołu, który jest używany w trakcie tworzenia, a następnie zamiast przełącznika `Create a probe with pick host name from backend address` zostanie wybrana lista rozwijana umożliwiająca wybranie jednej z sond niestandardowych. Wynika to z faktu, że istnieje już ustawienie protokołu HTTP z usługą App Service, dlatego również istnieje sonda kondycji, która ma `Pick host name from backend http settings enabled` przełącznika. Wybierz niestandardową sondę z listy rozwijanej.

5. Kliknij przycisk **OK** , aby utworzyć ustawienie http.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Utwórz regułę, aby powiązać odbiornik, pulę zaplecza i ustawienie HTTP

1. W obszarze **reguły**kliknij pozycję **podstawowa** , aby utworzyć nową regułę podstawową.

2. Podaj odpowiednią nazwę i wybierz odbiornik, który będzie akceptować przychodzące żądania dla usługi App Service.

3. Z listy rozwijanej **Pula zaplecza** Wybierz utworzoną powyżej pulę zaplecza.

4. Na liście rozwijanej **Ustawienia http** wybierz utworzone powyżej ustawienie protokołu HTTP.

5. Kliknij przycisk **OK** , aby zapisać tę regułę.

   ![Reguła](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Dodatkowa konfiguracja w przypadku przekierowania do ścieżki względnej usługi App Service

Gdy usługa App Service wysyła odpowiedź przekierowania do klienta w celu przekierowania do ścieżki względnej (na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2), używa tej samej nazwy hosta w nagłówku lokalizacji swojej odpowiedzi jako żądania otrzymanego od bramy aplikacji. Klient wyśle żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez bramę Application Gateway (contoso.com/path2). Pomijanie bramy aplikacji nie jest pożądane.

Jeśli w Twoim przypadku użycia, istnieją scenariusze, w których usługa App Service będzie musiała wysłać odpowiedź przekierowania do klienta, wykonaj [dodatkowe kroki w celu ponownego zapisania nagłówka lokalizacji](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Ogranicz dostęp

Aplikacje sieci Web wdrożone w tych przykładach używają publicznych adresów IP, do których można uzyskać dostęp bezpośrednio z Internetu. Ułatwia to rozwiązywanie problemów podczas uczenia się o nowej funkcji i podejmowanie nowych zadań. Ale jeśli zamierzasz wdrożyć funkcję w środowisku produkcyjnym, musisz dodać więcej ograniczeń.

Jednym ze sposobów ograniczenia dostępu do aplikacji sieci Web jest użycie [Azure App Service ograniczeń statycznych adresów IP](../app-service/app-service-ip-restrictions.md). Można na przykład ograniczyć aplikację sieci Web tak, aby tylko odbierał ruch z bramy aplikacji. Użyj funkcji ograniczenia adresów IP usługi App Service, aby wyświetlić listę adresów VIP bramy aplikacji jako jedyny adres z dostępem.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi App Service i innej obsługi wielu dzierżawców za pomocą usługi Application Gateway, zobacz [Obsługa wielu dzierżawców w usłudze Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
