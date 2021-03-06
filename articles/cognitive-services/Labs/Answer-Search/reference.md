---
title: Dokumentacja projektu wyszukiwania odpowiedzi
titlesuffix: Azure Cognitive Services
description: Odwołanie do punktu końcowego wyszukiwania odpowiedzi projektu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705328"
---
# <a name="project-answer-search-v7-reference"></a>Odwołanie do wersji 7 wyszukiwania odpowiedzi projektu

Odpowiedź Bing SearchAPI przyjmuje parametr zapytania i `searchResponse` zwraca z `answerType`: `facts` lub `entities`. 

Aplikacje korzystające z interfejsu API wyszukiwania odpowiedzi wysyłają żądania do punktu końcowego z adresem URL, aby wyświetlić podgląd w parametrze zapytania.  Żądanie musi zawierać parametr i `q=searchTerm` wartość *OCP-APIM-Subscription-Key* .   

Odpowiedź JSON można analizować pod kątem faktów i jednostek, które zawierają szczegółowe informacje o obiekcie wyszukiwania.

## <a name="endpoint"></a>Endpoint
Aby zażądać wyników wyszukiwania odpowiedzi, Wyślij żądanie do poniższego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

Pobieranie punktu końcowego: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

Żądanie musi korzystać z protokołu HTTPS i zawierać następujący parametr zapytania:
-  `q=<URL>`-Zapytanie, które identyfikuje obiekt wyszukiwania

Aby zapoznać się z przykładami, które pokazują, jak wykonywać żądania, zobacz [ C# Przewodnik](c-sharp-quickstart.md) szybki start lub [Java — szybki start](java-quickstart.md). 

W poniższych sekcjach znajdują się szczegółowe informacje techniczne na temat obiektów odpowiedzi, parametrów zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania. 
  
Aby uzyskać informacje o nagłówkach, które powinny obejmować żądania, zobacz [nagłówki](#headers).  
  
Aby uzyskać informacje na temat parametrów zapytania, które powinny zawierać żądania, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje na temat obiektów JSON, które zawiera odpowiedź, zobacz [obiekty odpowiedzi](#response-objects).

Maksymalna długość adresu URL zapytania to 2 048 znaków. Aby zapewnić, że długość adresu URL nie przekracza limitu, Maksymalna długość parametrów zapytania powinna być krótsza niż 1 500 znaków. Jeśli adres URL przekracza 2 048 znaków, serwer zwraca 404 nie znaleziono.  

Aby uzyskać informacje na temat dozwolonych użycia i wyświetlania wyników, zobacz [użycie i wyświetlanie wymagań](use-display-requirements.md). 

> [!NOTE]
> Niektóre nagłówki żądań mające znaczenie dla innych interfejsów API wyszukiwania nie wpływają na Podgląd adresu URL
> - Pragma — obiekt wywołujący nie ma kontroli nad tym, czy adres URL podglądu używa pamięci podręcznej
> - Cache-Control — obiekt wywołujący nie ma kontroli nad tym, czy adres URL podglądu używa pamięci podręcznej
> - User-Agent
> 
> Ponadto niektóre parametry nie są obecnie zrozumiałe dla interfejsu API w wersji zapoznawczej, ale mogą być używane w przyszłości na potrzeby ulepszonego globalizacji. 
 
## <a name="headers"></a>Nagłówki  
Poniżej znajdują się nagłówki, których może dotyczyć żądanie i odpowiedź.  
  
|nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Opcjonalny nagłówek żądania.<br /><br /> Domyślnym typem nośnika jest Application/JSON. Aby określić, że odpowiedź ma używać [JSON-LD](https://json-ld.org/), ustaw nagłówek Accept na Application/LD + JSON.|  
|<a name="acceptlanguage" />Accept-Language|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest zorganizowana w malejącym porządku preferencji. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i parametr zapytania [setLang](#setlang) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> W przypadku ustawienia tego nagłówka należy również określić parametr zapytania DW. Aby ustalić rynek, dla którego mają zostać zwrócone wyniki, usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście i łączy go z wartością parametru `cc`. Jeśli lista nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie, lub używa rynku zagregowanego bądź domyślnego. Aby sprawdzić, jakiego rynku użyła usługa Bing, zobacz nagłówek BingAPIs-Market.<br /><br /> Używaj tego nagłówka i parametru zapytania `cc` tylko wtedy, gdy określasz wiele języków. W przeciwnym razie użyj parametrów zapytania [mkt](#mkt) i [setLang](#setlang).<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|  
|<a name="market" />BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Opcjonalny nagłówek żądania<br /><br /> Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).
|<a name="useragent" />User-Agent|Opcjonalny nagłówek żądania.<br /><br /> Agent użytkownika, od którego pochodzi żądanie. Usługa Bing korzysta z agenta użytkownika, aby zapewnić zoptymalizowane środowisko obsługi dla użytkowników urządzeń przenośnych. Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać.<br /><br /> Nagłówek user-agent powinien być takim samym ciągiem, jaki wysyła każda powszechnie używana przeglądarka. Aby uzyskać informacje na temat agentów użytkownika, zobacz [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów nagłówka user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodne; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; jak Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 jak Mac OS X) AppleWebKit/536.26 (KHTML; jak Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) jak Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 jak Mac OS X) AppleWebKit/537.51.1 (KHTML, jak Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br /> **WAŻNE** Chociaż opcjonalne, należy rozważyć ten nagłówek. Utrwalenie identyfikatora klienta dla wielu żądań dla połączenia tego samego użytkownika końcowego i urządzenia umożliwia (1) zapewnienie użytkownikowi interfejsu API spójnego środowiska obsługi oraz (2) uzyskanie wyższej częstotliwości kliknięć dzięki lepszej jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGA** Należy upewnić się, że ten identyfikator klienta nie jest połączony z żadnym z uwierzytelnianymi informacjami o koncie użytkownika.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi Bing mogą być nieuwzględnione w tym nagłówku lub nie. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** Jeśli dołączysz symbol X-MSEdge-ClientID, nie musisz zawierać plików cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **UWAGA:** Chociaż opcjonalne, zaleca się zawsze określenia tego nagłówka i nagłówka X-Search-Location.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|  
|<a name="location" />X-Search-Location|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują geograficzną lokalizację klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i aby zwracać odpowiednią zawartość lokalną. Parę klucz/wartość określ w formacie \<klucz\>:\<wartość\>. Poniżej przedstawiono klucze, które służą do określania lokalizacji użytkownika.<br /><br /><ul><li>&mdash;Szerokość geograficzna lokalizacji klienta (w stopniach). Szerokość geograficzna musi być większa niż lub równa -90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują południową szerokość geograficzną, a wartości dodatnie wskazują północną szerokość geograficzną.<br /><br /></li><li>&mdash;długość długości geograficznej lokalizacji klienta (w stopniach). Długość geograficzna musi być większa niż lub równa -180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują zachodnią długość geograficzną, a wartości dodatnie wskazują wschodnią długość geograficzną.<br /><br /></li><li>odnowić&mdash; promień w licznikach, który określa poziom dokładności współrzędnych. Przekaż wartość zwróconą przez usługę lokalizacji urządzenia. Typowe wartości mogą wynosić 22 m dla sieci GPS/Wi-Fi, 380 m dla triangulacji wieży sieci komórkowej i 18 000 m dla wstecznego wyszukiwania adresu IP.<br /><br /></li><li>&mdash; czas znacznika czasu UTC systemu UNIX w przypadku, gdy klient znajdował się w lokalizacji. (Sygnatura czasowa systemu UNIX to liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>head&mdash;Opcjonalne. Względny kierunek przemieszczania się klienta. Określ kierunek ruchu jako liczbę stopni z zakresu od 0 do 360, zliczanych zgodnie z ruchem wskazówek zegara względem prawdziwej północy. Określ ten klucz tylko wtedy, gdy wartość klucza `sp` jest różna od zera.<br /><br /></li><li>&mdash; przyrządy (prędkość) w mikrosekundach na sekundę, które urządzenie klienckie jest podróżujące.<br /><br /></li><li>Alt&mdash; wysokość urządzenia klienckiego w metrach.<br /><br /></li><li>are&mdash;Opcjonalne. Promień, w metrach, który określa pionową dokładność współrzędnych. Wartość domyślna usługi RADIUS to 50 kilometrów. Określ ten klucz tylko wtedy, gdy wartość klucza `alt` także została określona.<br /><br /></li></ul> **UWAGA:** Chociaż te klucze są opcjonalne, im więcej informacji zawiera użytkownik, tym dokładniejsze są wyniki lokalizacji.<br /><br /> **UWAGA:** Zaleca się, aby zawsze określać lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie fizycznej lokalizacji użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy uwzględnić ten nagłówek i nagłówek X-MSEdge-ClientIP, a co najmniej należy uwzględnić ten nagłówek.|

> [!NOTE] 
> Pamiętaj, że Warunki użytkowania wymagają zgodności ze wszystkimi obowiązującymi przepisami prawa, w tym dotyczącymi korzystania z tych nagłówków. W niektórych systemach prawnych, na przykład w Europie, wymagane jest uzyskania zgody użytkownika przed umieszczeniem narzędzi śledzących na urządzeniu użytkownika.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może zawierać następujące parametry zapytania. Sprawdź wymaganą kolumnę dla wymaganych parametrów. Należy zakodować parametry zapytania w adresie URL.  
  
  
|Name (Nazwa)|Wartość|Type|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynkowych, zobacz Kody rynku.<br /><br /> **UWAGA:** Interfejs API podglądu adresów URL obecnie obsługuje tylko rynek en-us i język.<br /><br />|String|Tak|  
|<a name="query" />pytania|Adres URL do podglądu|String|Tak|  
|<a name="responseformat" />responseFormat|Typ nośnika, który ma być używany na potrzeby odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje na temat obiektów JSON zawartych w odpowiedzi, zobacz temat [obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, które zawierają wyniki wyszukiwania. Aby uzyskać informacje na temat JSON-LD, zobacz [JSON-LD](https://json-ld.org/).|String|Nie|  
|<a name="safesearch" />safeSearch|Filtr używany do odfiltrowania zawartości dla dorosłych. Poniżej przedstawiono możliwe wartości filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracanie stron sieci Web z tekstem dla dorosłych, obrazami lub klipami wideo.<br /><br/></li><li>Umiarkowanie&mdash;zwracaj strony sieci Web z tekstem dla dorosłych, ale nie z obrazami lub wideo dla dorosłych.<br /><br/></li><li>Ścisłe&mdash;nie zwracają stron internetowych z tekstem dla dorosłych, obrazami lub klipami wideo.</li></ul><br /> Wartość domyślna to Moderate.<br /><br /> **UWAGA:** Jeśli żądanie pochodzi ze rynku, że zasady dla dorosłych usługi Bing wymagają `safeSearch` ustawienia Strict, Bing `safeSearch` ignoruje wartość i używa rygorystyczne.<br/><br/>**UWAGA:** Jeśli używasz `site:` operatora zapytania, istnieje możliwość, że odpowiedź może zawierać treści dla dorosłych niezależnie od tego, `safeSearch` co parametr zapytania jest ustawiony na. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język ciągów interfejsu użytkownika. Określ język przy użyciu 2-literowego kodu języka w standardzie ISO 639-1. Na przykład kod języka dla języka angielskiego to EN. Wartość domyślna to EN (język angielski).<br /><br /> Mimo, że jest to opcjonalne, należy zawsze określić język. Na ogół dla parametru `setLang` ustawia się język określony przez parametr `mkt`, chyba że użytkownik chce, aby ciągi interfejsu użytkownika były wyświetlane w innym języku.<br /><br /> Ten parametr i nagłówek [Accept-Language](#acceptlanguage) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Ponadto wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|Ciąg|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Schemat odpowiedzi to [Strona sieci Web] lub zwrócono, jak w interfejsie API wyszukiwanie w sieci Web. Jeśli żądanie nie powiedzie się, obiekt najwyższego poziomu jest obiektem [zwrócono](#errorresponse) .


|Object|Opis|  
|------------|-----------------|  
|Stron|Obiekt JSON najwyższego poziomu, który zawiera atrybuty wersji zapoznawczej.|  
|Wieloczynnikowe|Obiekt JSON najwyższego poziomu, który zawiera fakty.| 
|[Jednostki|Obiekt JSON najwyższego poziomu, który zawiera szczegóły jednostki.| 

  
### <a name="error"></a>Błąd  
Określa błąd, który wystąpił.  
  
|Element|Opis|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />kodu|Kod błędu, który identyfikuje kategorię błędu. Listę możliwych kodów można znaleźć w temacie [kody błędów](#error-codes).|String|  
|<a name="error-message" />Komunikat|Opis błędu.|String|  
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o błędzie.|Ciąg|  
|<a name="error-parameter" />konstruktora|Parametr zapytania w żądaniu, który spowodował błąd.|String|  
|<a name="error-subcode" />podkod|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może to być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value" />wartościami|Wartość parametru zapytania, która jest nieprawidłowa.|String|  
  

### <a name="errorresponse"></a>Zwrócono  
Obiekt najwyższego poziomu, który odpowiedź zawiera, gdy żądanie nie powiedzie się.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu.|Ciąg|  
|<a name="errors" />Błędy|Lista błędów opisujących przyczyny niepowodzenia żądania.|[Error](#error)|  

  
  
### <a name="license"></a>Licencja  
Definiuje licencję, pod którą można używać tekstu lub fotografii.  
  
|Name (Nazwa)|Value|Type|  
|----------|-----------|----------|  
|name|Nazwa licencji.|String|  
|url|Adres URL witryny sieci Web, w której użytkownik może uzyskać więcej informacji na temat licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|String|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definiuje regułę umowną dotyczącą przypisywania licencji.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiona na LicenseAttribution.|String|  
|licencja|Licencja, pod którą można użyć zawartości.|[Licencjonowan](#license)|  
|licenseNotice|Licencja, która ma być wyświetlana obok pola Target. Na przykład "tekst w obszarze licencji CC-BY-SA".<br /><br /> Użyj nazwy i adresu URL licencji w polu, `license` aby utworzyć hiperłącze do witryny sieci Web, która opisuje szczegóły licencji. Następnie zastąp nazwę licencji w `licenseNotice` ciągu (na przykład CC-by-sa) utworzonym hiperłączem.|String|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły musi być umieszczona w pobliżu pola, do którego odnosi się reguła. W przypadku **wartości true**zawartość musi być umieszczona w pobliżu sąsiedztwa. Jeśli **wartość jest równa false**, lub to pole nie istnieje, zawartość może być umieszczona według uznania obiektu wywołującego.|Boolean|  
|targetPropertyName|Nazwa pola, do którego odnosi się reguła.|Ciąg|  
  

### <a name="link"></a>Łącze  
Definiuje składniki hiperłącza.  
  
|Name (Nazwa)|Value|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu.|String|  
|text|Wyświetlany tekst.|Ciąg|  
|url|ADRES URL. Użyj adresu URL i tekstu wyświetlanego, aby utworzyć hiperłącze.|String|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definiuje regułę umowną dla przypisywania łączy.  
  
|Name (Nazwa)|Value|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiona na LinkAttribution.|String|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły musi być umieszczona w pobliżu pola, do którego odnosi się reguła. W przypadku **wartości true**zawartość musi być umieszczona w pobliżu sąsiedztwa. Jeśli **wartość jest równa false**, lub to pole nie istnieje, zawartość może być umieszczona według uznania obiektu wywołującego.|Boolean|  
|targetPropertyName|Nazwa pola, do którego odnosi się reguła.<br /><br /> Jeśli obiekt docelowy nie zostanie określony, przypisanie ma zastosowanie do jednostki jako całości i powinno być wyświetlane bezpośrednio po prezentacji jednostki. Jeśli istnieje wiele reguł dotyczących autorstwa tekstu i łącza, które nie określają celu, należy je połączyć i wyświetlić przy użyciu etykiety "dane z:". Na przykład "dane pochodzące od dostawcy < Name1\> &#124; < dostawcy NAME2\>".|Ciąg|  
|text|Tekst przypisywany.|String|  
|url|Adres URL witryny sieci Web dostawcy. Użyj `text` i URL, aby utworzyć hiperłącze.|String|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definiuje regułę umowną dla przypisywania multimediów.  
  
|Name (Nazwa)|Value|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiona na MediaAttribution.|String|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły musi być umieszczona w pobliżu pola, do którego odnosi się reguła. W przypadku **wartości true**zawartość musi być umieszczona w pobliżu sąsiedztwa. Jeśli **wartość jest równa false**, lub to pole nie istnieje, zawartość może być umieszczona według uznania obiektu wywołującego.|Boolean|  
|targetPropertyName|Nazwa pola, do którego odnosi się reguła.|String|  
|url|Adres URL używany do tworzenia hiperłącza zawartości multimedialnej. Na przykład jeśli obiektem docelowym jest obraz, użyj adresu URL, aby można było kliknąć obraz.|Ciąg|  
  
  
  
### <a name="organization"></a>Organizacja  
Definiuje wydawcę.  
  
Należy pamiętać, że Wydawca może podać jego nazwę lub witrynę sieci Web.  
  
|Name (Nazwa)|Value|Type|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|String|  
|url|Adres URL witryny sieci Web wydawcy.<br /><br /> Zwróć uwagę, że Wydawca może nie dostarczyć witryny sieci Web.|String|  
  
  

### <a name="webpage"></a>Stron  
Definiuje informacje o stronie sieci Web w wersji zapoznawczej.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|String|
|url|Adres URL, który był w rzeczywistości przeszukiwany (żądanie mogło być wykonane przekierowania)|Ciąg|  
|description|Krótki opis strony i zawartości|String|  
|isFamilyFriendly|Najdokładniejsze dla elementów w indeksie sieci Web; Pobieranie w czasie rzeczywistym umożliwia wykrycie wyłącznie na podstawie adresu URL, a nie zawartości strony|boolean|
|primaryImageOfPage/contentUrl|Adres URL reprezentatywnego obrazu do uwzględnienia w wersji zapoznawczej|String| 
  
  
### <a name="querycontext"></a>Kontekst zapytania  
Definiuje kontekst zapytania, który jest używany przez usługę Bing dla żądania.  
  
|Element|Opis|Type|  
|-------------|-----------------|----------|  
|adultIntent|Wartość logiczna wskazująca, czy określone zapytanie ma zamiar dla dorosłych. Wartość jest **równa true** , jeśli zapytanie ma dla dorosłych cel; w przeciwnym razie **false**.|Boolean|  
|alterationOverrideQuery|Ciąg zapytania, który ma zostać użyty w celu wymuszenia użycia oryginalnego ciągu przez usługę Bing. Na przykład jeśli ciąg zapytania to *Saling downwind*, przesłonięcie ciągu zapytania będzie *+ Saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, którego wynikiem jest *% 2Bsaling + downwind*.<br /><br /> To pole jest dostępne tylko wtedy, gdy pierwotny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|alteredQuery|Ciąg zapytania używany przez usługę Bing do wykonania zapytania. Bing używa zmienionego ciągu zapytania, jeśli oryginalny ciąg zapytania zawiera błędy pisowni. Na przykład jeśli ciąg zapytania to `saling downwind`, zmieniony ciąg zapytania `sailing downwind`będzie.<br /><br /> To pole jest dostępne tylko wtedy, gdy pierwotny ciąg zapytania zawiera błąd pisowni.|String|  
|askUserForLocation|Wartość logiczna wskazująca, czy Bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli określono lokalizację użytkownika przy użyciu nagłówków [x-MSEdge-ClientIP](#clientip) i [X-Search-Location](#location) , można zignorować to pole.<br /><br /> W przypadku zapytań dotyczących lokalizacji, takich jak "Pogoda dzisiaj" lub "Restauracje w pobliżu", które potrzebują lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione na **wartość true**.<br /><br /> W przypadku zapytań dotyczących lokalizacji, które zawierają lokalizację (na przykład "Pogoda pogodowa"), to pole jest ustawione na **wartość false**. To pole jest również ustawiane na **wartość false** dla zapytań, które nie obsługują lokalizacji, takich jak "Najlepsza sprzedaż".|Boolean|  
|originalQuery|Ciąg zapytania określony w żądaniu.|String|  

### <a name="identifiable"></a>Identyfikowan

|Name (Nazwa)|Value|Type|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|String|
 
### <a name="rankinggroup"></a>RankingGroup
Definiuje grupę wyników wyszukiwania, taką jak linii głównej.

|Name (Nazwa)|Value|Type|  
|-------------|-----------------|----------|
|items|Lista wyników wyszukiwania, które mają być wyświetlane w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania do wyświetlenia.

|Name (Nazwa)|Wartość|Type|  
|-------------|-----------------|----------|
|resultIndex|Indeks (liczony od zera) elementu w odpowiedzi, który ma być wyświetlany. Jeśli element nie zawiera tego pola, Wyświetl wszystkie elementy w odpowiedzi. Na przykład Wyświetl wszystkie artykuły z wiadomościami w odpowiedzi na wiadomości.|Liczba całkowita|
|odpowiedź|Odpowiedź zawierająca element do wyświetlenia. Na przykład wiadomości.<br /><br />Użyj typu, aby znaleźć odpowiedź w obiekcie SearchResponse. Typ jest nazwą pola SearchResponse.<br /><br /> Należy jednak użyć typu odpowiedzi tylko wtedy, gdy ten obiekt zawiera pole Value; w przeciwnym razie zignoruj ją.|String|
|textualIndex|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Liczba całkowita bez znaku|
|value|Identyfikator identyfikujący odpowiedź do wyświetlenia lub element odpowiedzi do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedź, Wyświetl wszystkie elementy odpowiedzi.|Identyfikowan|

### <a name="rankingresponse"></a>RankingResponse  
Definiuje, gdzie powinna zostać umieszczona zawartość strony wyników wyszukiwania i w jakiej kolejności.  
  
|Name (Nazwa)|Wartość|  
|----------|-----------|  
|<a name="ranking-mainline" />linii głównej|Wyniki wyszukiwania do wyświetlenia w linii głównej.|  
|<a name="ranking-pole" />słup|Wyniki wyszukiwania, które powinny mieć zapewnione najbardziej widoczne traktowanie (na przykład widoczne nad linii głównej i paskiem bocznym).|  
|<a name="ranking-sidebar" />—|Wyniki wyszukiwania do wyświetlenia na pasku bocznym.| 


### <a name="searchresponse"></a>SearchResponse  
Definiuje obiekt najwyższego poziomu, który odpowiedź zawiera, gdy żądanie zakończy się pomyślnie.  
  
Należy pamiętać, że jeśli usługa podejrzewa atak typu "odmowa usługi", żądanie zakończy się pomyślnie (kod stanu HTTP to 200 OK); Jednakże treść odpowiedzi będzie pusta.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiona na SearchResponse.|String|  
|Stron|Obiekt JSON, który definiuje Podgląd|ciąg|  
  
  
### <a name="textattribution"></a>Alokacja  
Definiuje regułę umowną dla przypisywania zwykłego tekstu.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiana na element przypisywania.|Ciąg|  
|text|Tekst przypisywany.<br /><br /> Przypisanie tekstu ma zastosowanie do jednostki jako całości i powinno być wyświetlane bezpośrednio po prezentacji jednostki. Jeśli istnieje wiele reguł autorstwa tekstu lub łącza, które nie określają celu, należy je połączyć i wyświetlić przy użyciu etykiety "dane z:".|String| 


## <a name="error-codes"></a>Kody błędów

Oto możliwe kody stanu HTTP zwracane przez żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy.|  
|401|Brak klucza subskrypcji lub jest on nieprawidłowy.|  
|403|Użytkownik jest uwierzytelniany (na przykład użył prawidłowego klucza subskrypcji), ale nie ma uprawnień do żądanego zasobu.<br /><br /> Jeśli obiekt wywołujący przekroczył limit przydziału zapytań na miesiąc, może również zwrócić ten stan.|  
|410|Żądanie używało protokołu HTTP zamiast protokołu HTTPS. Jedynym obsługiwanym protokołem jest protokół HTTPS.|  
|429|Obiekt wywołujący przekroczył liczbę zapytań na sekundę.|  
|500|Nieoczekiwany błąd serwera.|

Jeśli żądanie nie powiedzie się, odpowiedź zawiera obiekt [zwrócono](#errorresponse) , który zawiera listę obiektów [błędów](#error) , które opisują przyczynę błędu. Jeśli błąd jest związany z parametrem, `parameter` pole Identyfikuje parametr, który jest problemem. A jeśli błąd jest związany z wartością parametru, `value` pole określa nieprawidłową wartość.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Poniżej przedstawiono możliwy kod błędu i wartości kodu błędu podrzędnego.

|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>NotImplemented|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Bing zwraca HttpNotAllowed, a kod stanu HTTP to 410.
|RateLimitExceeded|Brak kodów podrzędnych|Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>W przypadku przekroczenia zapytań, Bing zwraca kod stanu HTTP 429 i w przypadku przekroczenia QPM, Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Usługa Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Taka sytuacja może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)

