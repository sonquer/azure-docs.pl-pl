---
title: Omówienie łączników serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2036bf9995725e4bbef44e4c039f8336eb81a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997041"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Omówienie łączników serwera Proxy aplikacji usługi Azure AD

Łączniki są na tym, co sprawia, że serwer Proxy aplikacji usługi Azure AD to możliwe. Są one proste, łatwe do wdrożenia i konserwacji oraz są bardzo wydajne. W tym artykule omówiono, jakie łączniki są, jak to działa i sugestie dotyczące sposobu optymalizacji danego wdrożenia.

## <a name="what-is-an-application-proxy-connector"></a>Co to jest łącznik serwera Proxy aplikacji?

Łączniki są uproszczone agentów, które znajdują się w środowisku lokalnym i ułatwienia połączenie wychodzące do usługi serwera Proxy aplikacji. Łączniki, musi być zainstalowany w systemie Windows Server, który ma dostęp do aplikacji zaplecza. Łączników można organizować w grupy łączników, z każdą grupą obsługi ruchu do określonych aplikacji.

## <a name="requirements-and-deployment"></a>Wymagania i wdrożenia

Aby pomyślnie wdrożyć serwer Proxy aplikacji, co najmniej jeden łącznik jest wymagany, ale zaleca się co najmniej dwóch pod kątem większej odporności. Zainstaluj łącznik na komputerze z systemem Windows Server 2012 R2 lub nowszym. Łącznik musi komunikować się z usługą serwera proxy aplikacji oraz z opublikowanymi aplikacjami lokalnymi.

### <a name="windows-server"></a>Server systemu Windows
Wymagany serwer z systemem Windows Server 2012 R2 lub nowszym na której można zainstalować łącznik serwera Proxy aplikacji. Serwer musi nawiązać połączenie z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które są publikowane.

Systemu windows server musi mieć protokół TLS 1.2, włączone, przed zainstalowaniem łącznika serwera Proxy aplikacji. Aby włączyć protokół TLS 1,2 na serwerze:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.

Aby uzyskać więcej informacji na temat wymagania sieciowe dotyczące serwera łącznika zobacz [Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Konserwacja

Łączniki i usługa zajmie się wszystkie zadania o wysokiej dostępności. Mogą one dodane lub usunięte dynamicznie. Każdorazowo, gdy nowe żądanie dociera jest kierowany do jednego z łączników, które jest obecnie dostępna. Łącznik jest tymczasowo niedostępny, nie reagować na ten ruch.

Łączniki są bezstanowe i nie mają konfiguracji danych na maszynie. Jedyne dane, które przechowują jest ustawienie łączenia usługi i jego certyfikat uwierzytelniania. Łączą się z usługą ściągania danych wymaganej konfiguracji i ich odświeżanie co kilka minut.

Łączniki wykonać sondowanie serwer, aby dowiedzieć się, czy dostępna jest nowsza wersja łącznika. Jeśli nie zostanie znalezione, łączniki, aktualizują się same.

Łączniki z komputera, na którym jest uruchomiona, można monitorować przy użyciu dziennika zdarzeń i liczniki wydajności. Lub można wyświetlić ich stan ze strony serwera Proxy aplikacji w witrynie Azure Portal:

![Przykład: Łączniki serwer proxy aplikacji usługi Azure AD platformy Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usunąć łączniki, które nie są używane. Łącznik jest uruchomiona, pozostaje aktywna jako nawiąże połączenie z usługą. Nieużywane łączniki są oznaczone jako _nieaktywne_ i zostaną usunięte po upływie 10 dni braku aktywności. Jeśli chcesz odinstalować łącznik, jednak odinstaluj usługę łącznika i usługę aktualizacji z serwera. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD zapewnia automatyczne otrzymywanie aktualizacji dla wszystkich łączników, które można wdrożyć. Tak długo, jak usługa aktualizator łącznika serwera Proxy aplikacji jest uruchomiona, łączników są aktualizowane automatycznie. Jeśli nie widzisz usługi aktualizator łącznika na serwerze, należy [ponownej instalacji usługi łącznika](application-proxy-add-on-premises-application.md) na pobranie aktualizacji.

Jeśli nie chcesz czekać na przełączenie automatycznej aktualizacji do łącznika, możesz przeprowadzić uaktualnienie ręczne. Przejdź do [strony pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, gdzie Twój łącznik jest zlokalizowany i wybierz pozycję **Pobierz**. Ten proces dotyczącego uaktualnienie lokalnego łącznika.

Dla dzierżawców dzięki wielu łącznikom aktualizacje automatyczne docelowe jeden łącznik w czasie w każdej grupie, aby uniknąć przestojów w danym środowisku.

Może wystąpić Przestój, po zaktualizowaniu łącznika, jeśli:
  
- Masz tylko jeden łącznik zalecamy zainstalowanie drugiego łącznika i [utworzenie grupy łączników](application-proxy-connector-groups.md). Pozwoli to uniknąć przestojów i zapewnić wyższą dostępność.  
- Łącznik został środku transakcji, podczas rozpoczęcia aktualizacji. Mimo że początkowej transakcja zostanie utracony, przeglądarka automatycznie spróbuj ponownie wykonać operację lub możesz odświeżyć stronę. Gdy żądanie jest wysyłane ponownie, ruch jest kierowany do tworzenia kopii zapasowej łącznika.

Aby wyświetlić informacje o poprzednio wydanych wersjach i ich zmianach, zobacz [serwer proxy aplikacji — historia wersji](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Tworzenie grupy łączników

Grupy łączników umożliwiają przypisywanie określonych łączników do obsługi określonych aplikacji. Można grupować wiele łączników, a następnie przypisz każdej aplikacji do grupy.

Grupy łączników ułatwiają zarządzanie dużych wdrożeń. Mogą również zwiększyć opóźnienie dla dzierżawy, które mają aplikacje hostowane w różnych regionach, ponieważ można utworzyć grupy oparte na lokalizacji łączników, aby obsługiwać tylko lokalne aplikacje.

Aby dowiedzieć się więcej na temat grupy łączników, zobacz [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planowanie pojemności

Ważne jest, aby upewnić się, że zaplanowano wystarczającą ilość miejsca między łącznikami do obsługi oczekiwanego natężenia ruchu. Zalecamy, aby każda grupa łączników miała co najmniej dwa łączniki zapewniające wysoką dostępność i skalowanie. W przypadku, gdy trzy łączniki są optymalne na wypadek, może być konieczne obsługę komputera w dowolnym momencie.

Ogólnie rzecz biorąc, im więcej użytkowników, tym większa jest potrzebna maszyna. Poniżej znajduje się tabela przedstawiająca konspekt woluminu i oczekiwane opóźnienie, które mogą obsłużyć różne maszyny. Należy pamiętać, że jest to wszystko w oparciu o oczekiwane transakcje na sekundę (TPS), a nie przez użytkownika, ponieważ wzorce użycia są różne i nie można ich użyć do przewidywania obciążenia. Istnieją również pewne różnice w zależności od rozmiaru odpowiedzi i rozmiarów odpowiedzi aplikacji zaplecza i krótszych czasów odpowiedzi spowoduje obniżenie maksymalnej TPS. Zalecamy także posiadanie dodatkowych maszyn, aby obciążenie rozproszone na maszynach zawsze zapewniać dużą bufor. Dodatkowa pojemność zapewni wysoką dostępność i odporność.

|Rdzenie|Pamięć RAM|Oczekiwany czas oczekiwania (MS) — poziomie P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* na tym komputerze użyto ustawienia niestandardowego w celu podniesienia liczby domyślnych limitów połączeń poza zalecanymi ustawieniami platformy .NET. Firma Microsoft zaleca uruchamianie testu przy użyciu ustawień domyślnych, skontaktuj się z pomocą techniczną, aby ten limit, zmieniona dla Twojej dzierżawy.

> [!NOTE]
> Nie jest dużo różnicy w maksymalna TPS 4, 8 i 16 rdzeni maszyny. Główna różnica między tymi jest oczekiwane opóźnienie.
>
> W tej tabeli przedstawiono również oczekiwaną wydajność łącznika w zależności od typu maszyny, na której jest zainstalowany. Jest to niezależne od limitów ograniczania przepustowości usługi serwera proxy aplikacji, zobacz [limity i ograniczenia usługi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Zabezpieczenia i sieci

Łączników można zainstalować w dowolnym miejscu w sieci, która pozwala im wysyłać żądania do usługi serwera Proxy aplikacji. Co to jest ważne jest, czy komputer z uruchomioną łącznik również ma dostęp do aplikacji. Wewnątrz sieci firmowej lub na maszynie wirtualnej, która działa w chmurze, można zainstalować łączniki. Łączniki mogą być uruchamiane w sieci obwodowej, znanej także jako strefa zdemilitaryzowana (DMZ), ale nie jest to konieczne, ponieważ cały ruch jest wychodzący, dzięki czemu sieć pozostaje zabezpieczona.

Łączniki tylko wysyłać żądania wychodzącego. Ruch wychodzący są wysyłane do usługi serwera Proxy aplikacji i do opublikowanych aplikacji. Nie trzeba otworzyć porty dla ruchu przychodzącego, ponieważ ruch odbywa się dwukierunkowo po ustanowieniu sesji. Nie trzeba również konfigurować dostępu przychodzącego za poorednictwem zapór.

Aby uzyskać więcej informacji o konfigurowaniu reguł zapory dla ruchu wychodzącego, zobacz [pracy przy użyciu istniejących serwerów proxy lokalnych](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skalowanie usługi Serwer Proxy aplikacji jest przejrzysta, jednak skala jest współczynnik dla łączników. Musisz mieć wystarczającej liczby łączników w celu obsługi szczytowego natężenia ruchu. Ponieważ łączniki są bezstanowe, nie ma to wpływu na liczbę użytkowników lub sesji. Zamiast tego mogą odpowiadać na liczbę żądań i ich rozmiar ładunku. Za pomocą ruch internetowy standard średnia maszyna może obsługiwać kilka tysięcy żądań na sekundę. Określonej pojemności, zależy od właściwości dokładnie maszyny.

Wydajność łącznika jest związana z procesora CPU i sieci. Wydajność procesora CPU jest potrzebny do protokołu SSL szyfrowania i odszyfrowywania, gdy sieć jest ważne, aby uzyskać szybkie łączności do aplikacji i usług online na platformie Azure.

Z kolei jest pamięci nie stanowi takiego problemu dla łączników. Usługi online zajmuje się znacznie przetwarzania danych, a także cały ruch nieuwierzytelnionych. Wszystko, co można zrobić w chmurze jest wykonywane w chmurze.

Jeśli z jakiegokolwiek powodu, że łącznik lub maszyny staną się niedostępne, ruch zostanie uruchomione, przechodząc do innego łącznika w grupie. Ta elastyczność jest również, dlaczego firma Microsoft zaleca posiadanie wielu łączników.

Innym czynnikiem, który wpływa na wydajność jest jakość siecią między łączniki, w tym:

- **Usługi online**: wolne lub dużym opóźnieniem połączenia z serwerem Proxy aplikacji usługi w usłudze Azure wpływają na wydajność łącznika. Aby uzyskać najlepszą wydajność należy połączyć Twojej organizacji na platformie Azure z usługą Express Route. W przeciwnym razie ma Twój zespół sieci, upewnij się, jak najbardziej wydajny obsługiwania połączenia z platformą Azure.
- **Aplikacji zaplecza**: W niektórych przypadkach, istnieją dodatkowe serwery proxy między łącznika i aplikacji zaplecza, które mogą spowalniać lub uniemożliwić połączenia. Aby rozwiązać ten scenariusz, otwórz przeglądarkę z serwerem łącznika i spróbuj uzyskać dostęp do aplikacji. Jeśli łączniki działające na platformie Azure, ale aplikacje są w środowisku lokalnym, proces może nie być oczekiwaniami użytkowników.
- **Kontrolery domeny**: Jeśli łączniki wykonują Logowanie jednokrotne (SSO) przy użyciu ograniczonego delegowania protokołu Kerberos, kontaktuje się z kontrolerami domeny przed wysłaniem żądania do zaplecza. Łączniki mają pamięci podręcznej bilety protokołu Kerberos, ale w środowisku zajęty szybkość reakcji kontrolerów domeny może wpłynąć na wydajność. Ten problem jest częściej łączników, które działają na platformie Azure, jednak komunikować się z kontrolerów domeny, które są w środowisku lokalnym.

Aby uzyskać więcej informacji o optymalizacji sieci, zobacz [zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy usługi Azure Active Directory Application](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Przyłączanie do domeny

Łączników można uruchomić na komputerze, który nie jest przyłączony do domeny. Jeśli chcesz logowania jednokrotnego (SSO) aplikacjom, które używają zintegrowanego Windows Authentication (Zintegrowane), należy jednak komputerze przyłączonym do domeny. W tym przypadku maszyny łącznika musi być przyłączony do domeny, które może wykonywać [Kerberos](https://web.mit.edu/kerberos) ograniczonego delegowania w imieniu użytkowników w opublikowanej aplikacji.

Łączników można również łączyć domen i lasów, które mają częściowej relacji zaufania lub kontrolerów domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Łącznik wdrożeń w środowiskach ze wzmocnionymi zabezpieczeniami

Zazwyczaj wdrożenia łącznika jest proste, a nie wymaga specjalnej konfiguracji. Istnieją jednak pewne unikatowe warunki, które należy uwzględnić:

- Organizacje, które ograniczają ruch wychodzący musi [otworzyć wymagane porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Maszyny zgodne ze standardem FIPS, może być konieczne zmienianie ich konfiguracji, aby zezwolić na procesy łącznika do generowania i przechowywania certyfikatu.
- Organizacje, które zablokowania ich środowisko oparte na procesy, które wysyłają żądania sieci, trzeba upewnić się, że obie te usługi łącznika są włączone, dostęp do wszystkich wymaganych portów i adresów IP.
- W niektórych przypadkach ruchu wychodzącego do przodu serwery proxy może przerwać uwierzytelnianie certyfikatu dwukierunkowe i spowodować, że komunikacja nie powiedzie się.

## <a name="connector-authentication"></a>Łącznik uwierzytelniania

Zapewnienie bezpiecznej usłudze, łączników do uwierzytelniania usługi, a usługa udostępnia do uwierzytelniania w kierunku łącznika. To uwierzytelnianie jest wykonywane przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenia. Dzięki temu nazwa użytkownika i hasło administratora nie są przechowywane na maszynie łącznika.

Certyfikaty używane są specyficzne dla usługi serwera Proxy aplikacji. Tworzone podczas wstępnej rejestracji i są automatycznie odnawiane przez łączniki co kilka miesięcy.

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, jego certyfikatów może być nieaktualna. W tym przypadku odinstalowanie i ponowne instalowanie łącznika do rejestracji wyzwalaczy. Można uruchomić następujące polecenia środowiska PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Kulisy

Łączniki zależą od systemu Windows serwer Proxy aplikacji sieci Web, więc większość z tych samych narzędzi zarządzania, w tym Windows, dzienniki zdarzeń

![Zarządzanie dziennikami zdarzeń za pomocą Podglądu zdarzeń](./media/application-proxy-connectors/event-view-window.png)

i Windows, liczniki wydajności.

![Dodawanie liczników do łącznika przy użyciu Monitora wydajności](./media/application-proxy-connectors/performance-monitor.png)

Łączniki mają zarówno administratora, jak i sesji dzienniki. Dzienniki administracyjne obejmują kluczowych zdarzeń i ich błędy. Dzienniki sesji obejmują wszystkie transakcje i ich szczegóły przetwarzania.

Aby wyświetlić dzienniki, przejdź do podglądu zdarzeń, otwórz **widoku** menu, a następnie włączyć **Pokaż analityczne i debugowania dzienniki**. Następnie włącz je rozpocząć zbieranie zdarzeń. Te dzienniki nie są wyświetlane na serwerze Proxy aplikacji sieci Web w systemie Windows Server 2012 R2, jak łączniki są oparte na nowszą wersję.

Można sprawdzić stanu usługi, w oknie usług. Łącznik składa się z dwóch usług systemu Windows: rzeczywisty łącznik i Aktualizator. Obie z nich należy uruchomić cały czas.

 ![Przykład: okno usług pokazujące lokalne usługi Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Następne kroki

- [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md)
- [Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)
- [Rozwiązywanie problemów z błędami serwera Proxy aplikacji i łączników](application-proxy-troubleshoot.md)
- [Jak dyskretnie zainstalować łącznik serwera Proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
