---
title: Azure Traffic Manager — metody routingu ruchu
description: Te artykuły ułatwiają zrozumienie różnych metod routingu ruchu używanych przez Traffic Manager
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938447"
---
# <a name="traffic-manager-routing-methods"></a>Metody routingu w usłudze Traffic Manager

Usługa Azure Traffic Manager obsługuje sześć metod routingu ruchu sieciowego w celu określenia sposobu kierowania ruchu w sieci do różnych punktów końcowych usługi. Dla dowolnego profilu Traffic Manager stosuje metodę routingu ruchu skojarzoną z nią do każdego odebranego zapytania DNS. Metoda routingu ruchu określa, który punkt końcowy jest zwracany w odpowiedzi DNS.

W Traffic Manager są dostępne następujące metody routingu ruchu:

* **[Priorytet](#priority-traffic-routing-method):** wybierz pozycję **priorytet** , jeśli chcesz użyć podstawowego punktu końcowego usługi dla całego ruchu i podać kopie zapasowe w przypadku, gdy podstawowy lub punkt końcowy kopii zapasowej są niedostępne.
* **[Ważone](#weighted):** wybierz opcję **ważone** , gdy chcesz rozpowszechnić ruch między zestawem punktów końcowych, nawet w zależności od wagi zdefiniowanej przez użytkownika.
* **[Wydajność](#performance):** wybierz **wydajność** , gdy masz punkty końcowe w różnych lokalizacjach geograficznych, i chcesz, aby użytkownicy końcowi korzystali z punktu końcowego "najbliższy" w warunkach najmniejszego opóźnienia sieci.
* **[Geograficzna](#geographic):** wybierz opcję **geograficzne** , aby użytkownicy przekierowywani do określonych punktów końcowych (Azure, External lub Nested) na podstawie lokalizacji geograficznej, z której pochodzą zapytanie DNS. Pozwala to Traffic Manager klientom na umożliwienie scenariuszy, w których wiadomo region geograficzny użytkownika i kierowanie ich na podstawie tego, co jest ważne. Przykładami mogą być wymagania dotyczące suwerenności danych, lokalizacji zawartości & środowiska użytkownika i mierzenia ruchu z różnych regionów.
* **Z [wieloma wartościami](#multivalue):** **Wybierz opcję** wielu dla profilów Traffic Manager, które mogą mieć tylko adresy IPv4/IPv6 jako punkty końcowe. Po odebraniu zapytania dla tego profilu są zwracane wszystkie zdrowe punkty końcowe.
* **[Podsieć](#subnet):** wybierz metodę routingu ruch **podsieci** w celu mapowania zestawów zakresów adresów IP użytkowników końcowych na konkretny punkt końcowy w ramach profilu Traffic Manager. Po odebraniu żądania punkt końcowy zostanie przypisany do źródłowego adresu IP tego żądania. 


Wszystkie profile Traffic Manager obejmują monitorowanie kondycji punktów końcowych i automatyczne przełączanie do trybu failover punktu końcowego. Aby uzyskać więcej informacji, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md). Pojedynczy profil Traffic Manager może używać tylko jednej metody routingu ruchu. W dowolnym momencie możesz wybrać inną metodę routingu ruchu dla profilu. Zmiany są stosowane w ciągu jednej minuty i nie są naliczane żadne przestoje. Metody routingu ruchu można łączyć za pomocą zagnieżdżonych profilów Traffic Manager. Zagnieżdżanie umożliwia zaawansowane i elastyczne konfiguracje routingu ruchu, które spełniają potrzeby większych, złożonych aplikacji. Aby uzyskać więcej informacji, zobacz [profile Traffic Manager zagnieżdżonych](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Priorytetowy ruch-Metoda routingu

Często organizacja chce zapewnić niezawodność swoich usług, wdrażając co najmniej jedną usługę tworzenia kopii zapasowych na wypadek awarii usługi podstawowej. Metoda routingu ruchu "Priority" pozwala klientom platformy Azure łatwo zaimplementować ten wzorzec trybu failover.

![Usługa Azure Traffic Manager "Priority" — Metoda routingu](media/traffic-manager-routing-methods/priority.png)

Profil Traffic Manager zawiera listę punktów końcowych usługi z priorytetem. Domyślnie Traffic Manager wysyła cały ruch do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy jest niedostępny, Traffic Manager kieruje ruch do drugiego punktu końcowego. Jeśli zarówno podstawowy, jak i pomocniczy punkt końcowy są niedostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność punktu końcowego jest oparta na skonfigurowanym stanie (włączonym lub wyłączonym) i trwającym monitorowaniu punktów końcowych.

### <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Za pomocą Azure Resource Manager można jawnie skonfigurować priorytet punktu końcowego przy użyciu właściwości "Priority" dla każdego punktu końcowego. Ta właściwość jest wartością z przedziału od 1 do 1000. Niższe wartości reprezentują wyższy priorytet. Punkty końcowe nie mogą udostępniać wartości priorytetu. Ustawienie właściwości jest opcjonalne. W przypadku pominięcia jest używany domyślny priorytet oparty na kolejności punktów końcowych.

## <a name = "weighted"></a>Ważony ruch sieciowy — Metoda routingu
"Ważona" Metoda routingu ruchu umożliwia równomierne dystrybuowanie ruchu lub użycie wstępnie zdefiniowanej wagi.

![Platforma Azure Traffic Manager "ważona" — Metoda routingu ruchu](media/traffic-manager-routing-methods/weighted.png)

W przypadku metody routingu ruchu ważonego należy przypisać wagę do każdego punktu końcowego w konfiguracji profilu Traffic Manager. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr jest opcjonalny. W przypadku pominięcia menedżerowie ruchu używają domyślnej wagi "1". Im wyższa waga, tym wyższy priorytet.

Dla każdego odebranego zapytania DNS Traffic Manager losowo wybiera dostępny punkt końcowy. Prawdopodobieństwo wyboru punktu końcowego zależy od wag przypisanych do wszystkich dostępnych punktów końcowych. Użycie tej samej wagi we wszystkich punktach końcowych skutkuje równym rozkładem ruchu. Użycie wyższych lub niższych wag w określonych punktach końcowych powoduje, że te punkty końcowe są zwracane częściej lub rzadziej w odpowiedziach DNS.

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* Stopniowe Uaktualnianie aplikacji: przydzielanie procentu ruchu do trasy do nowego punktu końcowego i stopniowe zwiększanie ruchu w czasie do 100%.
* Migrowanie aplikacji na platformę Azure: Utwórz profil z platformą Azure i zewnętrznymi punktami końcowymi. Dostosuj wagę punktów końcowych, aby preferować nowe punkty końcowe.
* Magazynowanie w chmurze w celu uzyskania dodatkowej pojemności: szybkie rozszerzanie lokalnego wdrożenia do chmury przez umieszczenie go za profilem Traffic Manager. Jeśli potrzebujesz dodatkowej pojemności w chmurze, możesz dodać lub włączyć więcej punktów końcowych i określić, jaka część ruchu przechodzi do każdego punktu końcowego.

Oprócz korzystania z Azure Portal można skonfigurować wagi przy użyciu Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST.

Ważne jest, aby zrozumieć, że odpowiedzi DNS są buforowane przez klientów i cykliczne serwery DNS używane przez klientów do rozpoznawania nazw DNS. Buforowanie może mieć wpływ na ważone rozkłady ruchu. Gdy liczba klientów i cykliczne serwery DNS są duże, dystrybucja ruchu działa zgodnie z oczekiwaniami. Jeśli jednak liczba klientów lub rekursywny serwer DNS jest mała, buforowanie może znacząco pochylić rozkład ruchu.

Typowe przypadki użycia obejmują:

* Środowiska deweloperskie i testowe
* Komunikacja między aplikacjami
* Aplikacje przeznaczone do użycia w wąskim znaczeniu użytkownika, które współużytkują wspólną infrastrukturę DNS rekursywnie (na przykład pracownicy firmy łączący się za pomocą serwera proxy)

Te efekty buforowania DNS są wspólne dla wszystkich systemów routingu ruchu opartych na systemie DNS, a nie tylko na platformie Azure Traffic Manager. W niektórych przypadkach jawne czyszczenie pamięci podręcznej DNS może stanowić obejście problemu. W innych przypadkach alternatywna metoda routingu ruchu może być bardziej odpowiednia.

## <a name = "performance"></a>Ruch wydajności — Metoda routingu

Wdrożenie punktów końcowych w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć czas odpowiedzi wielu aplikacji przez kierowanie ruchu do lokalizacji najbliżej siebie. Ta funkcja zapewnia metodę routingu ruchu "wydajność".

![Usługa Azure Traffic Manager "wydajność" — Metoda routingu](media/traffic-manager-routing-methods/performance.png)

Punkt końcowy "najbliższy" nie musi być najbliższy, mierzony przez odległość geograficzną. Zamiast tego Metoda routingu ruchu "Performance" określa najbliższy punkt końcowy przez pomiar opóźnienia sieci. Traffic Manager utrzymuje tabelę opóźnienia internetowego do śledzenia czasu rundy między zakresami adresów IP a każdym centrum danych platformy Azure.

Traffic Manager wyszukuje źródłowy adres IP przychodzącego żądania DNS w tabeli opóźnienia Internetu. Traffic Manager następnie wybiera dostępny punkt końcowy w centrum danych platformy Azure o najniższym opóźnieniu dla tego zakresu adresów IP i zwraca ten punkt końcowy w odpowiedzi DNS.

Zgodnie z opisem w temacie [jak działa Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zamiast tego zapytania DNS pochodzą z cyklicznej usługi DNS, z której korzystają klienci. W związku z tym adres IP używany do określenia punktu końcowego "najbliższy" nie jest adresem IP klienta, ale jest adresem IP cyklicznej usługi DNS. W tym przypadku ten adres IP jest dobrym serwerem proxy dla klienta.


Traffic Manager regularnie aktualizuje tabelę opóźnienia Internetu w celu uwzględnienia zmian w globalnej sieci Internet i nowych regionach świadczenia usługi Azure. Jednak wydajność aplikacji różni się w zależności od zmian w czasie rzeczywistym w ramach obciążenia przez Internet. Ruch związany z wydajnością — Routing nie monitoruje obciążenia w danym punkcie końcowym usługi. Jeśli jednak punkt końcowy będzie niedostępny, Traffic Manager nie będzie uwzględniać go w odpowiedziach zapytań DNS.


Informacje, które należy zwrócić uwagę:

* Jeśli Twój profil zawiera wiele punktów końcowych w tym samym regionie świadczenia usługi Azure, Traffic Manager dystrybuuje ruch równomiernie przez dostępne punkty końcowe w tym regionie. Jeśli wolisz inny rozkład ruchu w obrębie regionu, możesz użyć [zagnieżdżonych profilów Traffic Manager](traffic-manager-nested-profiles.md).
* Jeśli wszystkie włączone punkty końcowe w najbliższym regionie platformy Azure mają obniżoną wydajność, Traffic Manager przenosi ruch do punktów końcowych w następnym najbliższym regionie świadczenia usługi Azure. Jeśli chcesz zdefiniować preferowaną sekwencję trybu failover, użyj [zagnieżdżonych profilów Traffic Manager](traffic-manager-nested-profiles.md).
* W przypadku używania metody routingu ruchu o wydajności z zewnętrznymi punktami końcowymi lub zagnieżdżonych punktów końcowych należy określić lokalizację tych punktów końcowych. Wybierz region platformy Azure znajdujący się najbliżej wdrożenia. Te lokalizacje są wartościami obsługiwanymi przez tabelę opóźnienia Internetu.
* Algorytm wybierający punkt końcowy jest deterministyczny. Powtarzające się zapytania DNS z tego samego klienta są kierowane do tego samego punktu końcowego. Zazwyczaj klienci korzystają z różnych rekursywnych serwerów DNS podczas podróży. Klient może być kierowany do innego punktu końcowego. W przypadku routingu mogą być również narażone aktualizacje tabeli opóźnienia Internetu. W związku z tym Metoda routingu ruchu o wydajności nie gwarantuje, że klient jest zawsze kierowany do tego samego punktu końcowego.
* Po zmianie tabeli opóźnienia internetowego można zauważyć, że niektórzy klienci są kierowani do innego punktu końcowego. Ta zmiana routingu jest bardziej dokładna w oparciu o bieżące dane opóźnienia. Te aktualizacje są niezbędne do zachowania dokładności routingu ruchu sieciowego w miarę ciągłego rozwoju Internetu.

## <a name = "geographic"></a>Ruch geograficzny — Metoda routingu

Profile Traffic Manager można skonfigurować tak, aby korzystały z metody routingu geograficznego, dzięki czemu użytkownicy są kierowani do określonych punktów końcowych (platformy Azure, zewnętrznych lub zagnieżdżonych) w zależności od lokalizacji geograficznej, z której pochodzi kwerenda DNS. Pozwala to Traffic Manager klientom na umożliwienie scenariuszy, w których wiadomo region geograficzny użytkownika i kierowanie ich na podstawie tego, co jest ważne. Przykładami mogą być wymagania dotyczące suwerenności danych, lokalizacji zawartości & środowiska użytkownika i mierzenia ruchu z różnych regionów.
Jeśli profil jest skonfigurowany pod kątem routingu geograficznego, każdy punkt końcowy skojarzony z tym profilem musi mieć przypisane do niego zestaw regionów geograficznych. Region geograficzny może mieć następujące poziomy szczegółowości 
- World — dowolny region
- Grupowanie regionalne — na przykład Afryka, Bliski Wschód, Australia/Pacyfik itp. 
- Kraj/Region — na przykład Irlandia, Peru, SRA Hongkong itp. 
- Województwo — na przykład USA-Kalifornia, Australia-Queensland, Kanada-Alberta itp. (Uwaga: ten poziom szczegółowości jest obsługiwany tylko w przypadku Stanów/prowincji w Australii, Kanadzie i USA).

Po przypisaniu regionu lub zestawu regionów do punktu końcowego wszystkie żądania z tych regionów są kierowane tylko do tego punktu końcowego. Traffic Manager używa źródłowego adresu IP zapytania DNS w celu ustalenia regionu, z którego pochodzi użytkownik — zwykle jest to adres IP lokalnego programu rozpoznawania nazw DNS wykonującego zapytanie w imieniu użytkownika.  

![Azure Traffic Manager "geograficzny" — Metoda routingu](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager odczytuje źródłowy adres IP zapytania DNS i decyduje, z którego regionu geograficznego pochodzi. Następnie sprawdza, czy istnieje punkt końcowy, do którego jest mapowany ten region geograficzny. Ta funkcja wyszukiwania zaczyna się na najniższym poziomie szczegółowości (stan/region, w którym jest obsługiwany, inne na poziomie kraju/regionu) i przechodzi do najwyższego poziomu, który jest **światem**. Pierwsze dopasowanie znalezione przy użyciu tego przechodzenia jest wyznaczane jako punkt końcowy do zwrócenia w odpowiedzi na zapytanie. W przypadku dopasowywania z typem zagnieżdżonym punkt końcowy w tym profilu podrzędnym jest zwracany na podstawie jego metody routingu. Następujące punkty mają zastosowanie do tego zachowania:

- Region geograficzny można zamapować tylko na jeden punkt końcowy w profilu Traffic Manager, gdy typem routingu jest Routing geograficzny. Dzięki temu Routing użytkowników jest deterministyczny, a klienci mogą włączać scenariusze, które wymagają niejednoznacznych granic geograficznych.
- Jeśli region użytkownika znajduje się pod dwoma różnymi mapowaniami geograficznymi punktów końcowych, Traffic Manager wybiera punkt końcowy o najniższym poziomie szczegółowości i nie uwzględnia żądań routingu z tego regionu do innego punktu końcowego. Rozważmy na przykład profil typu routingu geograficznego z dwoma punktami końcowymi — Endpoint1 i Endpoint2. Endpoint1 jest skonfigurowany do odbierania ruchu z Irlandii, a Endpoint2 jest skonfigurowany do odbierania ruchu z Europy. Jeśli żądanie pochodzi z Irlandii, zawsze jest kierowane do Endpoint1.
- Ponieważ region można zamapować tylko do jednego punktu końcowego, Traffic Manager zwraca go niezależnie od tego, czy punkt końcowy jest w dobrej kondycji.

    >[!IMPORTANT]
    >Zdecydowanie zaleca się, aby klienci korzystający z metody routingu geograficznego skojarzyć ją z punktami końcowymi typu zagnieżdżonego, które mają profile podrzędne zawierające co najmniej dwa punkty końcowe w ramach każdego z nich.
- Jeśli zostanie znaleziony odpowiednik punktu końcowego i ten punkt końcowy jest w stanie **zatrzymania** , Traffic Manager zwraca odpowiedź NoData. W takim przypadku żadne dalsze wyszukiwania nie są większe w hierarchii regionów geograficznych. To zachowanie jest również stosowane dla zagnieżdżonych typów punktów końcowych, gdy profil podrzędny jest w stanie **zatrzymania** lub **wyłączenia** .
- Jeśli punkt końcowy wyświetla stan **wyłączony** , nie zostanie uwzględniony w procesie dopasowywania regionu. To zachowanie jest również stosowane dla zagnieżdżonych typów punktów końcowych, gdy punkt końcowy jest w stanie **wyłączenia** .
- Jeśli zapytanie pochodzi z regionu geograficznego, który nie ma mapowania w tym profilu, Traffic Manager zwraca odpowiedź NoData. W związku z tym zdecydowanie zaleca się, aby klienci używali routingu geograficznego z jednym punktem końcowym, idealnie do typu zagnieżdżonego z co najmniej dwoma punktami końcowymi w profilu podrzędnym, **z przypisanym** do niego regionem. Zapewnia to również, że obsługiwane są wszystkie adresy IP, które nie są mapowane do regionu.

Zgodnie z opisem w temacie [jak działa Traffic Manager](traffic-manager-how-it-works.md), Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zamiast tego zapytania DNS pochodzą z cyklicznej usługi DNS, z której korzystają klienci. W związku z tym adres IP używany do określenia regionu nie jest adresem IP klienta, ale jest adresem IP cyklicznej usługi DNS. W tym przypadku ten adres IP jest dobrym serwerem proxy dla klienta.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których funkcja routingu geograficznego jest przydatna?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Jak mogę zdecydować, czy należy użyć metody routingu wydajności czy metody routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Jakie regiony są obsługiwane przez Traffic Manager na potrzeby routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Jak Usługa Traffic Manager określa, skąd użytkownik wykonuje zapytania?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Czy jest gwarantowane, że Traffic Manager może prawidłowo określić dokładną lokalizację geograficzną użytkownika w każdym przypadku?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Czy punkt końcowy musi być fizycznie zlokalizowany w tym samym regionie, w którym jest skonfigurowany do routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Czy można przypisywać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Dlaczego otrzymuję błąd podczas próby zmiany metody routingu istniejącego profilu na geograficzny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Dlaczego zdecydowanie zaleca się, aby klienci utworzyli profile zagnieżdżone zamiast punktów końcowych w ramach profilu z włączonym routingiem geograficznym?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API, która obsługuje ten typ routingu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>Wielowartościowy ruch-Metoda routingu
Metoda **routingu ruchu wielowartościowego** umożliwia uzyskanie wielu prawidłowych punktów końcowych w pojedynczej odpowiedzi na zapytanie DNS. Dzięki temu obiekt wywołujący może wykonać ponowną próbę po stronie klienta z innymi punktami końcowymi w przypadku, gdy zwrócony punkt końcowy nie odpowiada. Ten wzorzec może zwiększyć dostępność usługi i ograniczyć opóźnienia związane z nowym zapytaniem DNS w celu uzyskania prawidłowego punktu końcowego. Metoda routingu z wieloma wartościami działa tylko wtedy, gdy wszystkie punkty końcowe typu "External" i są określone jako adresy IPv4 lub IPv6. Po odebraniu zapytania dla tego profilu są zwracane wszystkie prawidłowe punkty końcowe i podlegają konfigurowalnej maksymalnej liczbie zwracanych wartości.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których Routing z wieloma wartościami jest przydatny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Ile punktów końcowych jest zwracanych w przypadku użycia routingu wielowartościowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Czy w przypadku użycia routingu wielowartościowego otrzymasz ten sam zestaw punktów końcowych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>Ruch podsieci — Metoda routingu
Metoda routingu ruchu **podsieci** umożliwia mapowanie zestawu zakresów adresów IP użytkowników końcowych na określone punkty końcowe w profilu. Po tym, jeśli Traffic Manager odbiera zapytanie DNS dla tego profilu, sprawdzi źródłowy adres IP tego żądania (w większości przypadków będzie to wychodzący adres IP programu rozpoznawania nazw DNS używany przez obiekt wywołujący), ustal, który punkt końcowy jest zamapowany i zwróci t punkt końcowy w odpowiedzi na zapytanie. 

Adres IP, który ma być mapowany do punktu końcowego, można określić jako zakresy CIDR (np. 1.2.3.0/24) lub jako zakres adresów (np. 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z punktem końcowym muszą być unikatowe w ramach tego profilu i nie mogą nakładać się na siebie z zestawem adresów IP innego punktu końcowego w tym samym profilu.
W przypadku zdefiniowania punktu końcowego bez zakresu adresów, który działa jako rezerwowe i pobiera ruch z pozostałych podsieci. Jeśli żaden rezerwowy punkt końcowy nie jest uwzględniony, Traffic Manager wysyła odpowiedź NoData dla wszelkich niezdefiniowanych zakresów. Dlatego zdecydowanie zaleca się zdefiniowanie rezerwowego punktu końcowego lub upewnij się, że wszystkie możliwe zakresy adresów IP są określone w punktach końcowych.

Routing podsieci może służyć do dostarczania innego środowiska dla użytkowników nawiązujących połączenie z określoną przestrzenią adresów IP. Na przykład przy użyciu routingu podsieci klient może kierować wszystkie żądania z ich biura firmowego do innego punktu końcowego, gdzie mogą testować tylko wewnętrzną wersję aplikacji. Inny scenariusz polega na tym, że chcesz udostępnić innym użytkownikom połączenie z konkretnym usługodawcą internetowym (na przykład zablokować użytkowników z danego USŁUGODAWCy internetowego).

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których Routing podsieci jest przydatny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Jak Traffic Manager znać adres IP użytkownika końcowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Jak można określić adresy IP w przypadku używania routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Jak określić rezerwowy punkt końcowy w przypadku używania routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Co się stanie, jeśli punkt końcowy jest wyłączony w profilu typu routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak opracowywać aplikacje o wysokiej dostępności przy użyciu funkcji [monitorowania punktów końcowych Traffic Manager](traffic-manager-monitoring.md)




