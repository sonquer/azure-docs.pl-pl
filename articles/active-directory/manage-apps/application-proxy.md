---
title: Dostęp zdalny do aplikacji lokalnych — serwer proxy aplikacji usługi Azure AD platformy Azure
description: Serwer proxy aplikacji Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może zapewnić dostęp zdalny i logowanie jednokrotne do aplikacji Pulpit zdalny, SharePoint, Teams, Tableau, Qlik i LOB.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274832"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Zdalny dostęp do aplikacji lokalnych przy użyciu serwera proxy aplikacji Azure Active Directory 

Serwer proxy aplikacji Azure Active Directory zapewnia bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web. Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji może zapewnić dostęp zdalny i logowanie jednokrotne do aplikacji Pulpit zdalny, SharePoint, Teams, Tableau, Qlik i LOB.

Serwer Proxy aplikacji usługi Azure AD jest:

- **Prosta do użycia**. Użytkownicy mogą uzyskiwać dostęp do aplikacji lokalnych w taki sam sposób, w jaki uzyskują dostęp do usługi O365 i innych aplikacji SaaS zintegrowanych z usługą Azure AD. Nie trzeba zmienić lub zaktualizować aplikacji do pracy z serwerem Proxy aplikacji. 

- **Zabezpiecz**. Aplikacje lokalne mogą korzystać z kontroli autoryzacji platformy Azure i analizy zabezpieczeń. Na przykład aplikacje lokalne mogą korzystać z dostępu warunkowego i weryfikacji dwuetapowej. Serwer proxy aplikacji nie wymaga otwierania połączeń przychodzących przez zaporę.
 
- **Ekonomiczne**. W przypadku rozwiązań lokalnych zwykle wymagane jest skonfigurowanie i konserwacja stref zdemilitaryzowana (stref DMZ), serwerów brzegowych lub innych złożonych infrastruktur. Serwer proxy aplikacji działa w chmurze, co ułatwia korzystanie z programu. Aby można było korzystać z serwera proxy aplikacji, nie trzeba zmieniać infrastruktury sieciowej ani instalować dodatkowych urządzeń w środowisku lokalnym.

## <a name="what-is-application-proxy"></a>Co to jest serwer proxy aplikacji?
Serwer proxy aplikacji to funkcja usługi Azure AD, która umożliwia użytkownikom dostęp do lokalnych aplikacji sieci Web z klienta zdalnego. Serwer proxy aplikacji obejmuje zarówno usługę serwera proxy aplikacji działającą w chmurze, jak i łącznik serwera proxy aplikacji, który działa na serwerze lokalnym. Azure AD, usługa serwera proxy aplikacji i łącznik serwera proxy aplikacji współpracują ze sobą, aby bezpiecznie przekazać token logowania użytkownika z usługi Azure AD do aplikacji sieci Web.

Serwer proxy aplikacji współpracuje z:

* Aplikacje sieci Web, które używają [zintegrowanego uwierzytelniania systemu Windows](application-proxy-configure-single-sign-on-with-kcd.md) na potrzeby uwierzytelniania  
* Aplikacje sieci Web korzystające z dostępu opartego na formularzach lub [nagłówkach](application-proxy-configure-single-sign-on-with-ping-access.md)  
* Interfejsy API, który chcesz udostępnić rozbudowane aplikacje na różnych urządzeniach w sieci Web  
* Aplikacje hostowane za [bramą pulpit zdalny](application-proxy-integrate-with-remote-desktop-services.md)  
* Rozbudowane aplikacje klienckie, które są zintegrowane z Active Directory Authentication Library (ADAL)

Serwer proxy aplikacji obsługuje logowanie jednokrotne. Aby uzyskać więcej informacji na temat obsługiwanych metod, zobacz [Wybieranie metody logowania](what-is-single-sign-on.md#choosing-a-single-sign-on-method)jednokrotnego.

Serwer proxy aplikacji jest zalecany do zapewniania użytkownikom zdalnym dostępu do zasobów wewnętrznych. Serwer proxy aplikacji zastępuje potrzebę sieci VPN lub zwrotnego serwera proxy. Nie jest on przeznaczony dla użytkowników wewnętrznych w sieci firmowej.  Ci użytkownicy niekoniecznie korzystający z serwera proxy aplikacji mogą wprowadzać nieoczekiwane i niepożądane problemy z wydajnością.

## <a name="how-application-proxy-works"></a>Jak działa serwer proxy aplikacji

Na poniższym diagramie pokazano, w jaki sposób usługa Azure AD i serwer proxy aplikacji współpracują ze sobą w celu zapewnienia logowania jednokrotnego do aplikacji lokalnych.

![Diagram serwera Proxy aplikacji usługi Azure AD](./media/application-proxy/azureappproxxy.png)

1. Po użytkownik uzyskał dostęp do aplikacji za pośrednictwem punktu końcowego, użytkownik jest kierowany do strony logowania usługi Azure AD. 
2. Po pomyślnym zalogowaniu usługa Azure AD wysyła token do urządzenia klienckiego użytkownika.
3. Klient wysyła token do usługi serwera proxy aplikacji, która pobiera główną nazwę użytkownika (UPN) i główną nazwę zabezpieczeń (SPN) z tokenu. Serwer proxy aplikacji wysyła żądanie do łącznika serwera proxy aplikacji.
4. Po skonfigurowaniu rejestracji jednokrotnej, łącznik wykonuje wszelkie dodatkowe uwierzytelnianie, wymagana w imieniu użytkownika.
5. Łącznik wysyła żądanie do aplikacji w środowisku lokalnym.  
6. Odpowiedź jest wysyłana przez łącznik i usługę serwera proxy aplikacji do użytkownika.

| Składnik | Opis |
| --------- | ----------- |
| Endpoint  | Punkt końcowy jest adresem URL lub [portalem użytkowników końcowych](end-user-experiences.md). Użytkownicy mogą korzystać z aplikacji znajduje się poza siecią, uzyskując dostęp do zewnętrznego adresu URL. Użytkownicy w Twojej sieci dostęp do aplikacji za pomocą adresu URL lub portalu użytkownika końcowego. Użytkownicy, przejdź do jednego z tych punktów końcowych, uwierzytelniania w usłudze Azure AD i następnie są kierowane za pośrednictwem łącznika do aplikacji w środowisku lokalnym.|
| Azure AD | Usługa Azure AD wykonuje uwierzytelnianie przy użyciu katalogu dzierżawy przechowywanego w chmurze. |
| Usługa serwera proxy aplikacji | Ta usługa serwera proxy aplikacji działa w chmurze w ramach usługi Azure AD. Przekazuje on token logowania od użytkownika do łącznika serwera proxy aplikacji. Serwer proxy aplikacji przekazuje wszystkie dostępne nagłówki w żądaniu i ustawia nagłówki zgodnie z protokołem na adres IP klienta. Jeśli żądanie przychodzące do serwera proxy ma już ten nagłówek, adres IP klienta zostanie dodany na końcu listy rozdzielanej przecinkami, która jest wartością nagłówka.|
| Łącznik serwera proxy aplikacji | Łącznik jest lekkim agentem działającym na serwerze z systemem Windows w sieci. Łącznik zarządza komunikacją między usługą serwera proxy aplikacji w chmurze a aplikacją lokalną. Łącznik używa tylko połączeń wychodzących, więc nie trzeba otwierać żadnych portów przychodzących ani umieszczać żadnych elementów w strefie DMZ. Łączniki są bezstanowe i pobierania informacji z chmury, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat łączników, takich jak równoważenie obciążenia i uwierzytelnianie, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory działa lokalnie, aby przeprowadzić uwierzytelnianie dla kont domeny. Po skonfigurowaniu logowania jednokrotnego łącznik komunikuje się z usługą AD, aby wykonać wymagane dodatkowe uwierzytelnianie.
| Aplikacja lokalna | Na koniec użytkownik może uzyskać dostęp do aplikacji lokalnej. 

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za pośrednictwem serwera proxy aplikacji](application-proxy-add-on-premises-application.md). 

Najnowsze wiadomości i aktualizacje można znaleźć na blogu dotyczącym [serwera proxy aplikacji](https://blogs.technet.com/b/applicationproxyblog/)


