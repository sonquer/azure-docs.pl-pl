---
title: Punkty końcowe przesyłania strumieniowego (Źródło)
titleSuffix: Azure Media Services
description: Dowiedz się więcej na temat punktów końcowych przesyłania strumieniowego (pochodzenia), dynamicznego tworzenia pakietów i usługi przesyłania strumieniowego, które dostarczają zawartość bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: c1e9be605a6f01695f2472ae76a9e5a786388aa0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206110"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Punkty końcowe przesyłania strumieniowego (Źródło) w Azure Media Services

W Microsoft Azure Media Services [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienta przy użyciu jednego z popularnych protokołów multimediów przesyłania strumieniowego (HLS lub pauzy). Ponadto **punkt końcowy przesyłania strumieniowego** zapewnia dynamiczne (just-in-Time) szyfrowanie do wiodących w branży protokołów DRM.

Podczas tworzenia konta Media Services zostanie utworzony **domyślny** punkt końcowy przesyłania strumieniowego w stanie zatrzymanym. Nie można usunąć **domyślnego** punktu końcowego przesyłania strumieniowego. Więcej punktów końcowych przesyłania strumieniowego można utworzyć przy użyciu konta (zobacz [przydziały i ograniczenia](limits-quotas-constraints.md)).

> [!NOTE]
> Aby rozpocząć przesyłanie strumieniowe filmów wideo, należy uruchomić **punkt końcowy przesyłania strumieniowego** , z którego chcesz przesłać strumieniowo wideo.
>
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="naming-convention"></a>Konwencja nazewnictwa

Format nazwy hosta adresu URL przesyłania strumieniowego to: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, gdzie `servicename` = nazwa punktu końcowego przesyłania strumieniowego lub nazwa zdarzenia na żywo.

W przypadku korzystania z domyślnego punktu końcowego przesyłania strumieniowego `servicename` zostanie pominięty, więc adres URL to: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Ograniczenia

* Nazwa punktu końcowego przesyłania strumieniowego ma maksymalną wartość 24 znaków.
* Nazwa powinna być zgodna ze wzorcem [wyrażenia regularnego](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typy

Istnieją dwa typy **punktów końcowych przesyłania strumieniowego** : **standardowa** (wersja zapoznawcza) i **Premium**. Typ jest definiowany przez liczbę jednostek skalowania (`scaleUnits`) przydzielonego dla punktu końcowego przesyłania strumieniowego.

Tabela zawiera opis typów:

|Typ|Jednostki skalowania|Opis|
|--------|--------|--------|  
|**Standard**|0|Domyślny punkt końcowy przesyłania strumieniowego jest typem **standardowym** — można go zmienić na typ Premium przez dostosowanie `scaleUnits`.|
|**Premium**|>0|Wersja **Premium** Punkty końcowe przesyłania strumieniowego są odpowiednie dla zaawansowanych obciążeń i zapewniają dedykowaną i skalowalną przepustowość. Przechodzenie do typu **Premium** przez dostosowanie `scaleUnits` (jednostki przesyłania strumieniowego). `scaleUnits` zapewnić dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. W przypadku korzystania z typu **Premium** każda włączona jednostka zapewnia dodatkową przepustowość dla aplikacji. |

> [!NOTE]
> W przypadku klientów chcących dostarczyć zawartość do dużych odbiorców internetowych zalecamy włączenie usługi CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać informacje o umowie SLA, zobacz [Cennik i Umowa SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

Cecha|Standardowy|Premium
---|---|---
Przepływność |Do 600 MB/s i może zapewnić znacznie wyższą skuteczną przepływność w przypadku użycia sieci CDN.|200 MB/s na jednostkę przesyłania strumieniowego (SU). W przypadku korzystania z sieci CDN można zapewnić znacznie wyższą skuteczną przepływność.
CDN|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skala|Automatycznie Skaluj do dostosowanej przepływności.|Dodatkowe usługi SUs
Filtrowanie/G20 IP/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane w przypadku większości scenariuszy przesyłania strumieniowego.|Profesjonalne użycie.

<sup>1</sup> używany bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

## <a name="properties"></a>Właściwości

Ta sekcja zawiera szczegółowe informacje dotyczące niektórych właściwości punktu końcowego przesyłania strumieniowego. Przykłady tworzenia nowego punktu końcowego przesyłania strumieniowego i opisy wszystkich właściwości można znaleźć w temacie [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: służy do konfigurowania następujących ustawień zabezpieczeń dla tego punktu końcowego przesyłania strumieniowego: klucze uwierzytelniania nagłówka sygnatury Akamai i adresy IP, które mogą łączyć się z tym punktem końcowym. Tę właściwość można ustawić tylko wtedy, gdy `cdnEnabled` jest ustawiona na wartość false.

- `cdnEnabled`: wskazuje, czy integracja Azure CDN dla tego punktu końcowego przesyłania strumieniowego jest włączona (domyślnie wyłączona). Jeśli ustawisz wartość `cdnEnabled` na true, następujące konfiguracje zostaną wyłączone: `customHostNames` i `accessControl`.

    Nie wszystkie centra danych obsługują integrację Azure CDN. Aby sprawdzić, czy centrum danych ma dostęp do integracji Azure CDN, wykonaj następujące czynności:

  - Spróbuj ustawić dla `cdnEnabled` wartość true.
  - Sprawdź zwrócony wynik dla `HTTP Error Code 412` (PreconditionFailed) z komunikatem "przesyłanie strumieniowe CdnEnabled właściwości nie można ustawić na wartość true, ponieważ w bieżącym regionie nie jest dostępna funkcja sieci CDN".

    Jeśli zostanie wyświetlony ten błąd, centrum danych nie będzie go obsługiwać. Wypróbuj inne centrum danych.

- `cdnProfile`: gdy `cdnEnabled` ma wartość true, można również przekazać wartości `cdnProfile`. `cdnProfile` to nazwa profilu CDN, w którym zostanie utworzony punkt końcowy usługi CDN. Możesz podać istniejący cdnProfile lub użyć nowego. Jeśli wartość jest RÓWNa NULL, a `cdnEnabled` ma wartość true, zostanie użyta wartość domyślna "AzureMediaStreamingPlatformCdnProfile". Jeśli podany `cdnProfile` już istnieje, zostanie utworzony punkt końcowy. Jeśli profil nie istnieje, zostanie automatycznie utworzony nowy profil.
- `cdnProvider`: gdy Usługa CDN jest włączona, można również przekazać wartości `cdnProvider`. `cdnProvider` kontroluje, który dostawca będzie używany. Obecnie obsługiwane są trzy wartości: "StandardVerizon", "PremiumVerizon" i "StandardAkamai". Jeśli nie podano wartości i `cdnEnabled` ma wartość true, zostanie użyta wartość "StandardVerizon" (to jest domyślna).
- `crossSiteAccessPolicies`: służy do określania zasad dostępu między lokacjami dla różnych klientów. Aby uzyskać więcej informacji, zobacz [specyfikację plików zasad między domenami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) i [udostępnianie usługi w granicach domen](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Ustawienia dotyczą tylko Smooth Streaming.
- `customHostNames`: służy do konfigurowania punktu końcowego przesyłania strumieniowego w celu akceptowania ruchu kierowanego do niestandardowej nazwy hosta. Ta właściwość jest prawidłowa dla punktów końcowych przesyłania strumieniowego w warstwie Standardowa i Premium i można ją ustawić, gdy `cdnEnabled`: false.

    Własność nazwy domeny musi zostać potwierdzona przez Media Services. Media Services weryfikuje własność nazwy domeny, wymagając rekordu `CName` zawierającego identyfikator konta Media Services jako składnik do dodania do domeny w użyciu. Przykładowo dla elementu "sports.contoso.com", który ma być używany jako niestandardowa nazwa hosta dla punktu końcowego przesyłania strumieniowego, rekord `<accountId>.contoso.com` musi być skonfigurowany tak, aby wskazywał jedną z Media Services weryfikacji nazw hostów. Nazwa hosta weryfikacji składa się z verifydns.\<MediaServices — > strefy DNS.

    Poniżej znajdują się oczekiwane strefy DNS, które mają być używane w zweryfikowanym rekordzie dla różnych regionów świadczenia usługi Azure.
  
  - Ameryka Północna, Europa, Singapur, Hongkong SAR, Japonia:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Chinach

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Na przykład rekord `CName`, który mapuje "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.media.azure.net", potwierdza, że identyfikator Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad ma własność domeny contoso.com, co pozwala na użycie dowolnej nazwy w contoso.com jako niestandardowej nazwy hosta dla punktu końcowego przesyłania strumieniowego w ramach tego konta. Aby znaleźć wartość identyfikatora usługi multimediów, przejdź do [Azure Portal](https://portal.azure.com/) i wybierz swoje konto usługi multimediów. **Identyfikator konta** jest wyświetlany w prawym górnym rogu strony.

    Jeśli podjęto próbę ustawienia niestandardowej nazwy hosta bez prawidłowej weryfikacji rekordu `CName`, odpowiedź DNS zakończy się niepowodzeniem, a następnie będzie buforowana przez jakiś czas. Po wprowadzeniu prawidłowego rekordu może upłynąć trochę czasu, dopóki buforowana odpowiedź nie zostanie ponownie sprawdzona. W zależności od dostawcy DNS dla domeny niestandardowej trwa to kilka minut, aby ponownie sprawdzić poprawność rekordu.

    Oprócz `CName`, które mapują `<accountId>.<parent domain>` do `verifydns.<mediaservices-dns-zone>`, należy utworzyć kolejną `CName`, która mapuje niestandardową nazwę hosta (na przykład `sports.contoso.com`) na nazwę hosta punktu końcowego Media Services przesyłania strumieniowego (na przykład `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Punkty końcowe przesyłania strumieniowego znajdujące się w tym samym centrum danych nie mogą współużytkować tej samej nazwy hosta niestandardowego.

    Obecnie Media Services nie obsługuje protokołu SSL z domenami niestandardowymi.

- `maxCacheAge` — zastępuje domyślny nagłówek kontroli pamięci podręcznej protokołu HTTP max-age ustawiony przez punkt końcowy przesyłania strumieniowego dla fragmentów nośnika i manifestów na żądanie. Wartość jest ustawiona w sekundach.
- `resourceState` -

    - Zatrzymano: początkowy stan punktu końcowego przesyłania strumieniowego po utworzeniu
    - Uruchamianie: przechodzi do stanu uruchomienia
    - Uruchamianie: umożliwia przesyłanie strumieniowe zawartości do klientów
    - Skalowanie: trwa zwiększanie lub zmniejszanie jednostek skalowania
    - Zatrzymywanie: przechodzi do stanu zatrzymania
    - Usuwanie: jest usuwane

- `scaleUnits`: zapewniają dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. Jeśli musisz przejść do typu **Premium** , Dostosuj `scaleUnits`.

## <a name="working-with-cdn"></a>Praca z usługą CDN

W większości przypadków usługa CDN powinna być włączona. Jeśli jednak przewidujesz maksymalną współbieżność mniejszą niż 500 osób przeglądających, zaleca się wyłączenie usługi CDN, ponieważ usługa CDN jest optymalna dla współbieżności.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Punkt końcowy przesyłania strumieniowego `hostname` i adres URL przesyłania strumieniowego pozostają takie same niezależnie od tego, czy jest włączona sieć CDN.
* Jeśli potrzebujesz możliwości testowania zawartości z użyciem usługi CDN lub bez niej, Utwórz inny punkt końcowy przesyłania strumieniowego, który nie jest włączony w sieci CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Szczegółowe wyjaśnienie działania buforowania

Podczas dodawania sieci CDN nie ma określonej wartości przepustowości, ponieważ przepustowość wymagana przez punkt końcowy przesyłania strumieniowego z obsługą sieci CDN jest różna. Partia zależy od typu zawartości, jego popularności, szybkości transmisji bitów i protokołów. Usługa CDN umożliwia buforowanie tylko tego, co jest wymagane. Oznacza to, że popularna zawartość będzie obsługiwana bezpośrednio z sieci CDN — tak długo, jak fragment wideo jest buforowany. Zawartość na żywo jest prawdopodobnie w pamięci podręcznej, ponieważ zazwyczaj masz wiele osób oglądających dokładnie te same rzeczy. Zawartość na żądanie może być trickier bitowym, ponieważ istnieje pewna zawartość, która jest najbardziej popularna, a niektóre z nich nie są. Jeśli masz miliony zasobów wideo, które nie są popularne (tylko jeden lub dwa osoby przeglądające w tygodniu), ale masz tysiące osób oglądających wszystkie różne wideo, Sieć CDN jest znacznie mniej skuteczna. W przypadku tych chybień w pamięci podręcznej można zwiększyć obciążenie punktu końcowego przesyłania strumieniowego.

Należy również wziąć pod uwagę sposób działania adaptacyjnego przesyłania strumieniowego. Każdy pojedynczy fragment wideo jest buforowany jako jego obiekt własny. Na przykład załóżmy, że po raz pierwszy oglądasz film wideo. Jeśli przeglądarka pominie około zaledwie kilku sekund, a tylko fragmenty wideo skojarzone z tym, co ktoś ogląda w pamięci podręcznej w sieci CDN. Dzięki adaptacyjnemu przesyłaniu strumieniowym zazwyczaj masz od 5 do 7 różnych szybkości transmisji wideo. Jeśli jedna osoba ogląda jedną szybkość transmisji bitów, a inna osoba ogląda inną szybkość transmisji bitów, to każda z nich jest buforowana osobno w sieci CDN. Nawet jeśli dwie osoby oglądają tę samą szybkość transmisji bitów, mogą one być przesyłane strumieniowo za pośrednictwem różnych protokołów. Każdy protokół (HLS, MPEG-KRESKa, Smooth Streaming) jest buforowany osobno. Dlatego każda szybkość transmisji bitów i protokół są buforowane oddzielnie, a tylko te fragmenty wideo, które zostały żądane, są buforowane.

### <a name="enable-azure-cdn-integration"></a>Włącz integrację Azure CDN

> [!IMPORTANT]
> Nie można włączyć usługi CDN dla kont platformy Azure z wersji próbnej lub ucznia.
>
> Integracja z usługą CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem regionów federalnych i w Chinach.

Po udostępnieniu punktu końcowego przesyłania strumieniowego z włączoną usługą CDN jest zdefiniowany czas oczekiwania na Media Services przed aktualizacją usługi DNS w celu zamapowania punktu końcowego przesyłania strumieniowego na punkt końcowy usługi CDN.

Jeśli zechcesz później wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi znajdować się w stanie **zatrzymanym** . Włączenie integracji Azure CDN może potrwać do dwóch godzin, a zmiany zostaną uaktywnione we wszystkich punktach pop usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i strumień bez przerw w punkcie końcowym przesyłania strumieniowego, a po zakończeniu integracji zostanie dostarczony strumień z sieci CDN. W trakcie okresu aprowizacji punkt końcowy przesyłania strumieniowego będzie w stanie **uruchomienia** i może wystąpić spadek wydajności.

Po utworzeniu standardowego punktu końcowego przesyłania strumieniowego jest on konfigurowany domyślnie przy użyciu standardowego Verizon. Korzystając z interfejsów API REST, można skonfigurować Verizon Premium lub standardowego dostawcy Akamai.

Integracja Azure Media Services z Azure CDN jest zaimplementowana w **Azure CDN z Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **Azure CDN warstw cenowych i dostawców**. 

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Ustal, czy wprowadzono zmianę w systemie DNS

Można określić, czy zmiana DNS została wprowadzona w punkcie końcowym przesyłania strumieniowego (ruch jest kierowany do Azure CDN) przy użyciu https://www.digwebinterface.com. Jeśli wyniki zawierają nazwy domen azureedge.net w wynikach, ruch jest teraz wskazywany do sieci CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

[Omówienie usługi CDN](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>Następne kroki

W przykładzie [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) przedstawiono sposób uruchamiania domyślnego punktu końcowego przesyłania strumieniowego przy użyciu platformy .NET.
