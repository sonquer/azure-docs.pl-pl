---
title: Tłumaczenie linków i adresów URL aplikacja usługi Azure AD proxy | Microsoft Docs
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533700"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Przekieruj linki stałe dla aplikacji opublikowanych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD

Usługa Azure serwer proxy aplikacji usługi Azure AD udostępnia aplikacje lokalne użytkownikom zdalnie lub na ich własnych urządzeniach. Niektóre aplikacje zostały jednak opracowane przy użyciu lokalnych linków osadzonych w kodzie HTML. Te linki nie działają prawidłowo, gdy aplikacja jest używana zdalnie. Gdy masz kilka aplikacji lokalnych, użytkownicy oczekują linków do działania, gdy nie znajdują się one w biurze. 

Najlepszym sposobem, aby upewnić się, że linki działają tak samo zarówno wewnątrz sieci firmowej, jak i poza nią, ma na celu skonfigurowanie zewnętrznych adresów URL aplikacji tak, aby były takie same jak wewnętrzne adresy URL. Użyj [domen niestandardowych](application-proxy-configure-custom-domain.md) , aby skonfigurować zewnętrzne adresy URL w celu posiadania firmowej nazwy domeny zamiast domyślnej domeny serwera proxy aplikacji.


Jeśli w dzierżawie nie można używać domen niestandardowych, istnieje kilka innych opcji zapewniających tę funkcję. Wszystkie z nich są również zgodne z domenami niestandardowymi i innymi, w razie potrzeby można skonfigurować niestandardowe domeny i inne rozwiązania.

> [!NOTE]
> Tłumaczenie linków nie jest obsługiwane w przypadku zakodowanych wewnętrznych adresów URL generowanych za poorednictwem języka JavaScript.

**Opcja 1: Użyj Managed Browser lub Microsoft Edge** — to rozwiązanie ma zastosowanie tylko wtedy, gdy planujesz zalecać lub wymagać, aby użytkownicy mieli dostęp do aplikacji za pomocą Intune Managed Browser lub przeglądarki Microsoft Edge. Będzie obsługiwać wszystkie opublikowane adresy URL. 

**Opcja 2: Korzystanie z rozszerzenia** moje aplikacje — to rozwiązanie wymaga od użytkowników zainstalowania rozszerzenia przeglądarki po stronie klienta, ale będzie obsługiwać wszystkie opublikowane adresy URL i współpracuje z najpopularniejszymi przeglądarkami. 

**Opcja 3: Użyj ustawienia** tłumaczenia linku — to ustawienie po stronie administratora, które jest niewidoczne dla użytkowników. Będzie jednak obsługiwać tylko adresy URL w formacie HTML i CSS.   

Te trzy funkcje przechowują swoje linki niezależnie od tego, gdzie znajdują się użytkownicy. Jeśli masz aplikacje, które wskazują bezpośrednio na wewnętrzne punkty końcowe lub porty, możesz mapować te wewnętrzne adresy URL na opublikowane adresy URL serwera proxy aplikacji zewnętrznych. 

 
> [!NOTE]
> Ostatnia opcja dotyczy tylko dzierżawców, które z jakiegoś powodu nie mogą używać domen niestandardowych, aby mieć te same wewnętrzne i zewnętrzne adresy URL dla swoich aplikacji. Przed włączeniem tej funkcji Sprawdź, czy [w usłudze Azure serwer proxy aplikacji usługi Azure AD można korzystać z domen niestandardowych](application-proxy-configure-custom-domain.md) . 
> 
> Lub, jeśli aplikacja, którą chcesz skonfigurować przy użyciu translacji łączy, to SharePoint, zobacz [Konfigurowanie mapowań dostępu alternatywnego dla programu sharepoint 2013](https://technet.microsoft.com/library/cc263208.aspx) , aby uzyskać inne podejście do mapowania linków. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Option 1: Intune Managed Browser i integracja z programem Microsoft Edge 

Możesz użyć Intune Managed Browser lub Microsoft Edge, aby zapewnić lepszą ochronę aplikacji i zawartości. Aby można było korzystać z tego rozwiązania, należy wymagać/zalecać użytkownikom dostęp do aplikacji za pomocą Intune Managed Browser. Wszystkie wewnętrzne adresy URL opublikowane przy użyciu serwera proxy aplikacji zostaną rozpoznane przez Managed Browser i przekierowane do odpowiedniego zewnętrznego adresu URL. Gwarantuje to, że wszystkie zakodowane wewnętrzne adresy URL działają i jeśli użytkownik przejdzie do przeglądarki i bezpośrednio wpisze wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby dowiedzieć się więcej, w tym o sposobie konfigurowania tej opcji, zapoznaj się z dokumentacją [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) .  

### <a name="option-2-myapps-browser-extension"></a>Opcja 2: Rozszerzenie przeglądarki Moje aplikacje 

Dzięki rozszerzeniu przeglądarki Moje aplikacje wszystkie wewnętrzne adresy URL opublikowane przy użyciu serwera proxy aplikacji są rozpoznawane przez rozszerzenie i przekierowywane do odpowiedniego zewnętrznego adresu URL. Gwarantuje to, że wszystkie zakodowane wewnętrzne adresy URL działają i jeśli użytkownik przechodzi do paska adresu przeglądarki i bezpośrednio wpisze wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby użyć tej funkcji, użytkownik musi pobrać rozszerzenie i być zalogowany. Dla administratorów lub użytkowników nie jest wymagana żadna inna konfiguracja. 

Aby dowiedzieć się więcej, w tym o sposobie konfigurowania tej opcji, zobacz dokumentację [rozszerzenia przeglądarki Moje aplikacje](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Opcja 3: Połącz ustawienie tłumaczenia 

Po włączeniu translacji łączy usługa serwera proxy aplikacji przeszukuje kod HTML i CSS w poszukiwaniu opublikowanych linków wewnętrznych i tłumaczy je tak, aby użytkownicy mogli korzystać z nieprzerwanego środowiska. Korzystanie z rozszerzenia przeglądarki Moje aplikacje jest preferowane dla ustawienia tłumaczenia linku, ponieważ zapewnia użytkownikom bardziej wydajne środowisko.

> [!NOTE]
> Jeśli używasz opcji 2 lub 3, tylko jeden z nich powinien być włączony w danym momencie.

## <a name="how-link-translation-works"></a>Jak działa tłumaczenie linków

Po uwierzytelnieniu, gdy serwer proxy przekazuje dane aplikacji do użytkownika, serwer proxy aplikacji skanuje aplikację pod kątem linków stałe i zastępuje je odpowiednimi opublikowanymi zewnętrznymi adresami URL.

Serwer proxy aplikacji zakłada, że aplikacje są kodowane w formacie UTF-8. Jeśli tak się nie dzieje, określ typ kodowania w nagłówku odpowiedzi HTTP, np `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Na jakie linki są one modyfikowane?

Funkcja tłumaczenia łączy wyszukuje tylko linki, które znajdują się w tagach kodu w treści aplikacji. Serwer proxy aplikacji ma osobną funkcję do tłumaczenia plików cookie lub adresów URL w nagłówkach. 

Istnieją dwa popularne typy linków wewnętrznych w aplikacjach lokalnych:

- **Względne linki wewnętrzne** wskazujące na zasób udostępniony w strukturze plików lokalnych, takich `/claims/claims.html`jak. Te linki automatycznie pracują w aplikacjach publikowanych za pomocą serwera proxy aplikacji i nadal pracują z tłumaczeniem linków lub bez niego. 
- **Stałe wewnętrzne linki** do innych aplikacji lokalnych, takich jak `http://expenses` lub opublikowanych plików, `http://expenses/logo.jpg`takich jak. Funkcja tłumaczenia łączy działa na łączach wewnętrznych stałe i zmienia je w taki sposób, aby wskazywały zewnętrzne adresy URL, do których użytkownicy zdalni muszą przejść.

Kompletna lista tagów kodu HTML, które serwer proxy aplikacji obsługuje tłumaczenie linków dla:
* a
* dźwięku
* opiera
* przycisk
* div
* Osadź
* seryjn
* klatce
* MTP
* html
* wbudowane
* img
* wejście
* link
* elementem
* odpowiada
* object
* script
* source
* sprawdzić
* wideo

Ponadto w obszarze CSS atrybut adresu URL jest również tłumaczony.

### <a name="how-do-apps-link-to-each-other"></a>Jak aplikacje łączą się ze sobą?

Tłumaczenie linków jest włączone dla każdej aplikacji, dzięki czemu masz kontrolę nad czynnościami użytkownika na poziomie aplikacji. Włącz tłumaczenie łączy dla aplikacji, jeśli chcesz, aby linki *z* tej aplikacji były tłumaczone, a nie linki *do* tej aplikacji. 

Załóżmy na przykład, że masz trzy aplikacje publikowane przy użyciu serwera proxy aplikacji, które łączą się ze sobą: Korzyści, wydatki i podróże. Istnieje czwarta aplikacja, która nie jest publikowana za pomocą serwera proxy aplikacji.

Po włączeniu tłumaczenia linków dla aplikacji korzyści linki do wydatków i podróży są przekierowywane do zewnętrznych adresów URL dla tych aplikacji, ale link do opinii nie zostanie przekierowany z powodu braku zewnętrznego adresu URL. Linki z wydatków i podróż z powrotem do korzyści nie działają, ponieważ nie włączono tłumaczenia linków dla tych dwóch aplikacji.

![Linki od korzyści do innych aplikacji po włączeniu translacji łączy](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Które linki nie są tłumaczone?

Aby zwiększyć wydajność i bezpieczeństwo, niektóre linki nie są tłumaczone:

- Linki poza tagami kodu. 
- Linki, które nie są w formacie HTML ani CSS. 
- Linki w formacie z kodowaniem URL.
- Linki wewnętrzne otwarte z innych programów. Linki wysyłane za pośrednictwem poczty e-mail lub wiadomości błyskawicznych lub zawarte w innych dokumentach nie będą tłumaczone. Użytkownicy muszą wiedzieć, aby przejść do zewnętrznego adresu URL.

Jeśli potrzebujesz obsługi jednego z tych dwóch scenariuszy, Użyj tych samych wewnętrznych i zewnętrznych adresów URL zamiast tłumaczenia linków.  

## <a name="enable-link-translation"></a>Włącz tłumaczenie linków

Wprowadzenie do tłumaczenia linków jest tak proste, jak kliknięcie przycisku:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do pozycji **Azure Active Directory** > **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje** > Wybierz aplikację, którą chcesz zarządzać > **serwerem proxy aplikacji**.
3. Włącz opcję **tłumaczenie adresów URL w treści aplikacji** na **wartość tak**.

   ![Wybierz opcję tak, aby przetłumaczyć adresy URL w treści aplikacji](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

Teraz, gdy użytkownicy uzyskują dostęp do tej aplikacji, serwer proxy automatycznie przeskanuje pod kątem wewnętrznych adresów URL, które zostały opublikowane za pomocą serwera proxy aplikacji w dzierżawie.

## <a name="send-feedback"></a>Wyślij opinię

Chcemy, aby Twoja funkcja działała we wszystkich aplikacjach. Przeszukiwane są ponad 30 tagów w formacie HTML i CSS. Jeśli masz przykład wygenerowanych linków, które nie są tłumaczone, Wyślij fragment kodu do [opinii serwera proxy aplikacji](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Następne kroki
[Używanie domen niestandardowych w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) w celu posiadania tego samego wewnętrznego i zewnętrznego adresu URL

[Konfigurowanie mapowań dostępu alternatywnego dla programu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
