---
title: Zaplecze z wieloma dzierżawcami
titleSuffix: Azure Application Gateway
description: Ta strona zawiera omówienie obsługi wielodostępnych zapleczy w usłudze Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075189"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway obsługa zaplecza wielodostępnego, takiego jak usługa App Service

W przypadku projektów architektonicznych z wieloma dzierżawcami na serwerach sieci Web, w tym samym wystąpieniu serwera sieci Web jest uruchomionych wiele witryn internetowych. Nazwy hostów są używane do rozróżniania między różnymi obsługiwanymi aplikacjami. Domyślnie usługa Application Gateway nie zmienia przychodzącego nagłówka hosta HTTP z klienta i wysyła niezmieniony nagłówek do zaplecza. Jest to dobre rozwiązanie w przypadku elementów członkowskich puli zaplecza, takich jak karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP i nazwa FQDN, ponieważ nie polegają one na określonym nagłówku hosta lub rozszerzeniu SNI w celu rozpoznania właściwego punktu końcowego. Istnieje jednak wiele usług, takich jak aplikacje sieci Web usługi Azure App Service i usługa Azure API Management, które mają wiele dzierżaw i polegają na konkretnym nagłówku hosta lub rozszerzeniu SNI w celu rozpoznania właściwego punktu końcowego. Zwykle nazwa DNS aplikacji, która z kolei jest nazwą DNS skojarzoną z bramą aplikacji, różni się od nazwy domeny usługi zaplecza. W związku z tym nagłówek hosta w oryginalnym żądaniu odebranym przez bramę aplikacji nie jest taki sam jak nazwa hosta usługi wewnętrznej bazy danych. Z tego względu, jeśli nagłówek hosta w żądaniu z bramy Application Gateway do zaplecza nie zostanie zmieniony na nazwę hosta usługi wewnętrznej bazy danych, nadmiarowe punkty końcowe z wieloma dzierżawcami nie będą mogły rozpoznać żądania do prawidłowego punktu końcowego. 

Usługa Application Gateway umożliwia użytkownikom zastępowanie nagłówka hosta HTTP żądania na podstawie nazwy hosta zaplecza. Ta funkcja umożliwia obsługę zapleczy wielu dzierżaw, na przykład aplikacji internetowych usługi Azure App Service i interfejsu API zarządzania. Ta możliwość jest dostępna dla standardowych jednostek SKU i jednostek SKU zapory aplikacji internetowych w wersjach 1 oraz 2. 

![zastąpienie hosta](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Nie dotyczy to środowiska Azure App Service Environment (ASE), ponieważ środowisko ASE jest dedykowanym zasobem, w przeciwieństwie do usługi Azure App Service, która jest zasobem z wieloma dzierżawcami.

## <a name="override-host-header-in-the-request"></a>Zastąp nagłówek hosta w żądaniu

Możliwość określania przesłonięcia hosta jest definiowana w [ustawieniach protokołu HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) i może być stosowana do dowolnej puli zaplecza podczas tworzenia reguły. Obsługiwane są następujące dwa sposoby przesłaniania nagłówka hosta i rozszerzenia SNI dla zaplecza z wieloma dzierżawami:

- Możliwość ustawienia dla nazwy hosta stałej wartości jawnie wprowadzonej w ustawieniach protokołu HTTP. Ta funkcja zapewnia, że nagłówek hosta zostanie przesłonięty na tę wartość dla całego ruchu do puli zaplecza, w której stosowane są określone ustawienia protokołu HTTP. W przypadku korzystania z kompleksowej usługi SSL ta zastąpiona nazwa hosta jest używana w rozszerzeniu SNI. Ta funkcja umożliwia obsługę scenariuszy, w których Farma puli zaplecza oczekuje nagłówka hosta, który różni się od przychodzącego nagłówka hosta klienta.

- Możliwość uzyskania nazwy hosta z adresu IP lub nazwy FQDN członków puli zaplecza. Ustawienia protokołu HTTP udostępniają również opcję dynamicznego wybierania nazwy hosta z nazwy FQDN składowej puli zaplecza, jeśli została skonfigurowana z użyciem opcji, aby utworzyć pochodną nazwę hosta z pojedynczego elementu członkowskiego puli zaplecza. W przypadku korzystania z kompleksowej usługi SSL ta nazwa hosta jest określana na podstawie nazwy FQDN i używana w rozszerzeniu SNI. Ta funkcja umożliwia obsługę scenariuszy, w których Pula zaplecza może mieć co najmniej dwie wielodostępne usługi PaaS, takie jak aplikacje sieci Web platformy Azure, a nagłówek hosta żądania do każdego elementu członkowskiego zawiera nazwę hosta pochodzącą od jego nazwy FQDN. W przypadku wdrażania tego scenariusza używamy przełącznika w ustawieniach protokołu HTTP o nazwie [Nazwa hosta z adresu zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) , co spowoduje dynamiczne zastępowanie nagłówka hosta w oryginalnym żądaniu do tego, który jest wymieniony w puli zaplecza.  Na przykład jeśli nazwa FQDN puli zaplecza zawiera "contoso11.azurewebsites.net" i "contoso22.azurewebsites.net", pierwotny nagłówek hosta żądania, który jest contoso.com, zostanie przesłonięty do contoso11.azurewebsites.net lub contoso22.azurewebsites.net gdy żądanie jest wysyłane do odpowiedniego serwera wewnętrznej bazy danych. 

  ![Scenariusz aplikacji internetowej](./media/application-gateway-web-app-overview/scenario.png)

Korzystając z tej funkcji, klienci mogą określić opcje w ustawieniach HTTP i niestandardowe sondy na potrzeby prawidłowej konfiguracji. To ustawienie jest następnie powiązane z odbiornikiem i pulą zaplecza przy użyciu reguły.

## <a name="special-considerations"></a>Specjalne uwagi

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Kończenie protokołu SSL i kompleksowa obsługa protokołu SSL z usługami wielodostępnymi

W przypadku usług wielodostępnych jest obsługiwane zarówno zakończenie protokołu SSL, jak i kompleksowe szyfrowanie SSL. W przypadku zakończenia protokołu SSL w bramie aplikacji certyfikat SSL jest nadal wymagany do dodania do odbiornika usługi Application Gateway. Jednak w przypadku kompleksowego protokołu SSL zaufane usługi platformy Azure, takie jak Azure App Service Web Apps, nie wymagają listy dozwolonych zaplecza w bramie aplikacji. W związku z tym nie trzeba dodawać żadnych certyfikatów uwierzytelniania. 

![Kompleksowa próba SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Należy zauważyć, że na powyższym obrazie nie jest wymagane dodanie certyfikatów uwierzytelniania, gdy usługa App Service została wybrana jako zaplecze.

### <a name="health-probe"></a>Sonda kondycji

Zastępowanie nagłówka hosta w **ustawieniach http** ma wpływ tylko na żądanie i jego Routing. nie ma to wpływu na zachowanie sondy kondycji. Aby kompleksowa funkcja działała, ustawienia sondowania i ustawienia HTTP muszą zostać zmodyfikowane w celu odzwierciedlenia prawidłowej konfiguracji. Oprócz zapewnienia możliwości określania nagłówka hosta w konfiguracji sondowania, niestandardowe sondy obsługują również możliwość uzyskania nagłówka hosta z aktualnie skonfigurowanych ustawień protokołu HTTP. Tę konfigurację można określić za pomocą parametru `PickHostNameFromBackendHttpSettings` w konfiguracji sondowania.

### <a name="redirection-to-app-services-url-scenario"></a>Scenariusz przekierowywania do scenariusza adresu URL App Service

Mogą istnieć sytuacje, w których nazwa hosta w odpowiedzi z usługi App Service może kierować przeglądarkę użytkownika końcowego do nazwy hosta *. azurewebsites.net zamiast domeny skojarzonej z Application Gateway. Ten problem może wystąpić, gdy:

- Skonfigurowano przekierowanie na App Service. Przekierowanie może być proste, dodając końcowy ukośnik do żądania.
- Masz uwierzytelnianie usługi Azure AD, które powoduje przekierowanie.

Aby rozwiązać te problemy, zobacz [Rozwiązywanie problemów z adresem URL usługi App Service](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować bramę aplikacji za pomocą aplikacji wielodostępnej, takiej jak usługa Azure App Service Web App jako członek puli zaplecza, odwiedzając [konfigurowanie App Service Web Apps z Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
