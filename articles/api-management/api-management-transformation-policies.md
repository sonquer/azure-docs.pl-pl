---
title: Zasady transformacji API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad transformacji dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: c26cca40b0bf6d02bcec09945043f4ba854fa8e9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012230"
---
# <a name="api-management-transformation-policies"></a>Zasady transformacji API Management
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a>Zasady transformacji

-   [Convert JSON do formatu XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje treść żądania lub odpowiedzi z formatu JSON na XML.

-   [Konwertuj kod XML na format JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje treść żądania lub odpowiedzi z formatu XML na notację JSON.

-   [Znajdź i Zamień ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — umożliwia znalezienie żądania lub podciągu odpowiedzi i zastąpienie go innym podciągiem.

-   [Maskowanie adresów URL w](api-management-transformation-policies.md#MaskURLSContent) treści odpowiedzi, aby wskazywały na równoważne łącze za pośrednictwem bramy.

-   [Ustawianie usługi zaplecza](api-management-transformation-policies.md#SetBackendService) — zmienia usługę zaplecza dla żądania przychodzącego.

-   [Ustaw treść](api-management-transformation-policies.md#SetBody) — ustawia treść wiadomości dla żądań przychodzących i wychodzących.

-   [Ustawianie nagłówka HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącej odpowiedzi i/lub nagłówka żądania lub dodaje nową odpowiedź i/lub nagłówek żądania.

-   [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.

-   [Ponownie Napisz adres URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania z jego formularza publicznego na formularz oczekiwany przez usługę sieci Web.

-   [Przekształcanie kodu XML przy użyciu XSLT](api-management-transformation-policies.md#XSLTransform) — stosuje transformację XSL do pliku XML w treści żądania lub odpowiedzi.

##  <a name="ConvertJSONtoXML"></a>Konwertuj kod JSON na format XML
 Zasady `json-to-xml` konwertują treść żądania lub odpowiedzi z formatu JSON na XML.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|JSON-to-XML|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|stosowa|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -zawsze-zawsze stosuje konwersję.<br />-Content-Type-JSON — Konwertuj tylko w przypadku, gdy w nagłówku Content-Type jest obecność JSON.|Tak|Nie dotyczy|
|consider-accept-header|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -true — Zastosuj konwersję, jeśli żądanie JSON jest wymagane w nagłówku akceptowania żądania.<br />-false — zawsze stosuj konwersję.|Nie|true|
|Analiza — Data|Po ustawieniu wartości daty `false` są kopiowane po prostu podczas transformacji|Nie|true|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="ConvertXMLtoJSON"></a>Konwertuj kod XML na format JSON
 Zasady `xml-to-json` konwertują treść żądania lub odpowiedzi z formatu XML na notację JSON. Za pomocą tych zasad można przeprowadzić modernizację interfejsów API opartych na usługach sieci Web zaplecza tylko w języku XML.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|xml-to-json|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|Natur|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -przyjazny dla języka JavaScript — przekonwertowany kod JSON ma postać przyjazną dla deweloperów języka JavaScript.<br />-Direct — przekonwertowane dane JSON odzwierciedlają strukturę oryginalnego dokumentu XML.|Tak|Nie dotyczy|
|stosowa|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -zawsze Konwertuj zawsze.<br />-Content-Type-XML-Convert tylko wtedy, gdy w nagłówku Content-Type (odpowiedź) wskazuje obecność XML.|Tak|Nie dotyczy|
|consider-accept-header|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> -true — Zastosuj konwersję, jeśli zażądano kodu XML w nagłówku akceptowania żądania.<br />-false — zawsze stosuj konwersję.|Nie|true|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="Findandreplacestringinbody"></a>Znajdź i Zamień ciąg w treści
 Zasady `find-and-replace` odnajdują żądanie lub podciąg odpowiedzi i zamieniają je na inny podciąg.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Przykład

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Znajdź i Zamień|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|from|Ciąg do wyszukania.|Tak|Nie dotyczy|
|na|Ciąg zastępczy. Określ ciąg zastępczy o zerowej długości, aby usunąć ciąg wyszukiwania.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="MaskURLSContent"></a>Maskowanie adresów URL w zawartości
 Linki zasad `redirect-content-urls` ponownie zapisują (maski) w treści odpowiedzi, aby wskazywały odpowiedni link za pośrednictwem bramy. Użyj w sekcji wychodzącej, aby ponownie zapisać linki treści odpowiedzi, aby wskazywały na bramę. Użyj w sekcji ruchu przychodzącego, aby oddziałać odwrotnie.

> [!NOTE]
>  Te zasady nie zmieniają żadnych wartości nagłówka, takich jak nagłówki `Location`. Aby zmienić wartości nagłówka, użyj zasad [Set-header](api-management-transformation-policies.md#SetHTTPheader) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<redirect-content-urls />
```

### <a name="example"></a>Przykład

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|redirect-content-urls|Element główny.|Tak|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="SetBackendService"></a>Ustawianie usługi zaplecza
 Użyj zasad `set-backend-service`, aby przekierować żądanie przychodzące do innego zaplecza niż określony w ustawieniach interfejsu API dla tej operacji. Te zasady umożliwiają zmianę podstawowego adresu URL usługi wewnętrznej bazy danych żądania przychodzącego na określony w zasadach.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-backend-service base-url="base URL of the backend service" />
```

lub

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Jednostki zaplecza mogą być zarządzane za pośrednictwem [interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) zarządzania i [programu PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
W tym przykładzie zasady usługi zaplecza są kierowane na podstawie wartości wersji przekazaną w ciągu zapytania do innej usługi zaplecza niż określona w interfejsie API.

Początkowo podstawowy adres URL usługi wewnętrznej bazy danych pochodzi z ustawień interfejsu API. `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` adres URL żądania jest `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef`, gdzie `http://contoso.com/api/10.4/` jest adresem URL usługi wewnętrznej bazy danych określonym w ustawieniach interfejsu API.

Gdy [< wybierze\>](api-management-advanced-policies.md#choose) zasady zasad zostanie zastosowane, podstawowy adres URL usługi wewnętrznej bazy danych może zmienić się na `http://contoso.com/api/8.2` lub `http://contoso.com/api/9.1`, w zależności od wartości parametru zapytania o wersję. Na przykład, jeśli wartość jest `"2013-15"` końcowy adres URL żądania zostanie `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Jeśli pożądane jest dalsze przekształcenie żądania, można użyć innych [zasad przekształcania](api-management-transformation-policies.md#TransformationPolicies) . Na przykład, aby usunąć parametr zapytania wersji teraz, gdy żądanie jest kierowane do zaplecza specyficznego dla wersji, można użyć zasad [parametrów ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) , aby usunąć teraz nadmiarowy atrybut wersji.

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
W tym przykładzie zasady kierują żądanie do zaplecza usługi Service Fabric przy użyciu ciągu zapytania userId jako klucza partycji i przy użyciu podstawowej repliki partycji.

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Konfiguracja zaplecza — usługa|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|podstawowy adres URL|Nowy podstawowy adres URL usługi zaplecza.|Jeden z `base-url` lub `backend-id` musi być obecny.|Nie dotyczy|
|Identyfikator zaplecza|Identyfikator zaplecza do skierowania do. (Jednostki zaplecza są zarządzane za pośrednictwem [interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) i [programu PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)).|Jeden z `base-url` lub `backend-id` musi być obecny.|Nie dotyczy|
|SF-Partition-Key|Dotyczy tylko sytuacji, gdy zaplecze jest usługą Service Fabric i jest określona przy użyciu identyfikatora zaplecza. Służy do rozwiązywania określonej partycji z usługi rozpoznawania nazw.|Nie|Nie dotyczy|
|SF-Replica-Type|Dotyczy tylko sytuacji, gdy zaplecze jest usługą Service Fabric i jest określona przy użyciu identyfikatora zaplecza. Kontroluje, czy żądanie powinno przechodzić do podstawowej lub pomocniczej repliki partycji. |Nie|Nie dotyczy|
|SF-Rozwiązuj — warunek|Dotyczy tylko sytuacji, gdy zaplecze jest usługą Service Fabric. Warunek określający, czy wywołanie Service Fabric zaplecza musi być powtórzone przy użyciu nowego rozwiązania.|Nie|Nie dotyczy|
|sf-service-instance-name|Dotyczy tylko sytuacji, gdy zaplecze jest usługą Service Fabric. Zezwala na zmianę wystąpień usługi w czasie wykonywania. |Nie|Nie dotyczy|
|SF-Listener-Name|Dotyczy tylko sytuacji, gdy zaplecze jest usługą Service Fabric i jest określona przy użyciu identyfikatora zaplecza. Service Fabric Reliable Services umożliwia tworzenie wielu odbiorników w usłudze. Ten atrybut służy do wybierania określonego odbiornika, gdy usługa niezawodna zaplecza ma więcej niż jeden odbiornik. Jeśli ten atrybut nie jest określony, API Management podejmie próbę użycia odbiornika bez nazwy. Odbiornik bez nazwy jest typowy dla Reliable Services, które mają tylko jeden odbiornik. |Nie|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="SetBody"></a>Ustaw treść
 Użyj zasad `set-body`, aby ustawić treść komunikatu dla żądań przychodzących i wychodzących. Aby uzyskać dostęp do treści wiadomości, można użyć właściwości `context.Request.Body` lub `context.Response.Body`, w zależności od tego, czy zasady są w sekcji przychodzące lub wychodzące.

> [!IMPORTANT]
>  Należy pamiętać, że domyślnie podczas uzyskiwania dostępu do treści komunikatu przy użyciu `context.Request.Body` lub `context.Response.Body`, Oryginalna treść wiadomości zostaje utracona i musi być ustawiona przez zwrócenie treści z powrotem w wyrażeniu. Aby zachować zawartość treści, należy ustawić parametr `preserveContent`, aby `true` podczas uzyskiwania dostępu do komunikatu. Jeśli `preserveContent` jest ustawiona na `true` i w wyrażeniu zostanie zwrócona inna treść, zostanie użyta zwracana treść.
>
>  Należy pamiętać o następujących kwestiach dotyczących używania zasad `set-body`.
>
> - Jeśli używasz zasad `set-body` do zwrócenia nowej lub zaktualizowanej treści, nie musisz ustawiać `preserveContent` do `true`, ponieważ użytkownik jawnie dostarcza nową zawartość treści.
>   -   Zachowanie zawartości odpowiedzi w potoku przychodzącym nie ma sensu, ponieważ nie ma jeszcze odpowiedzi.
>   -   Zachowanie zawartości żądania w potoku wychodzącym nie ma sensu, ponieważ żądanie zostało już wysłane do zaplecza w tym momencie.
>   -   Jeśli te zasady są używane w przypadku braku treści wiadomości, na przykład w przypadku pobierania przychodzącego, zgłaszany jest wyjątek.

 Aby uzyskać więcej informacji, zobacz sekcje `context.Request.Body`, `context.Response.Body`i `IMessage` w tabeli [zmiennych kontekstowych](api-management-policy-expressions.md#ContextVariables) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Przykłady

#### <a name="literal-text-example"></a>Przykład tekstu literału

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Przykład uzyskiwania dostępu do treści jako ciągu. Należy pamiętać, że zachowujemy pierwotną treść żądania, aby można było uzyskać do niej dostęp później w potoku.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Przykład uzyskiwania dostępu do treści jako JObject. Zwróć uwagę, że ponieważ nie obsługujemy pierwotnej treści żądania, uzyskanie dostępu do niej w dalszej części potoku spowoduje wyjątek.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Filtrowanie odpowiedzi na podstawie produktu
 Ten przykład pokazuje, jak wykonywać filtrowanie zawartości przez usunięcie elementów danych z odpowiedzi otrzymanej z usługi wewnętrznej bazy wiedzy podczas korzystania z `Starter` produktu. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 34:30. Zacznij od 31:50, aby zapoznać się z omówieniem [interfejsu API prognozowania ciemnej przestrzeniki](https://developer.forecast.io/) używanej w tej wersji demonstracyjnej.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Używanie szablonów płynów z zestawem treści
Zasady `set-body` można skonfigurować tak, aby używały języka [Liquid](https://shopify.github.io/liquid/basics/introduction/) tworzenia szablonów do przekształcania treści żądania lub odpowiedzi. Może to być bardzo skuteczne, jeśli trzeba całkowicie zmienić format wiadomości.

> [!IMPORTANT]
> Implementacja płynu użyta w zasadach `set-body` jest konfigurowana wC# trybie "Mode". Jest to szczególnie ważne podczas wykonywania takich czynności jak filtrowanie. Przykładowo użycie filtru daty wymaga użycia wielkości liter i C# formatowania daty, np.:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Aby prawidłowo powiązać z treścią XML przy użyciu szablonu ciekłej, użyj zasad `set-header`, aby ustawić typ zawartości na wartość Application/XML, text/xml (lub dowolny typ kończący się znakami + XML). dla treści JSON, musi to być Application/JSON, text/JSON (lub dowolny typ kończący się znakami + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konwertowanie JSON na SOAP przy użyciu szablonu ciekłego
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Przekształć kod JSON przy użyciu szablonu ciekłego
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Ustaw treść|Element główny. Zawiera tekst treści lub wyrażenia zwracające treść.|Tak|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|szablon|Służy do zmiany trybu tworzenia szablonów, w którym będą uruchamiane zasady zestawu treści. Obecnie jedyną obsługiwaną wartością jest:<br /><br />-Liquid-zasady dotyczące zestawu treści będą używać aparatu ciekłej tworzenia szablonów |Nie||

Aby uzyskać dostęp do informacji na temat żądania i odpowiedzi, szablon płynu można powiązać z obiektem kontekstu o następujących właściwościach: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="SetHTTPheader"></a>Ustaw nagłówek HTTP
 Zasada `set-header` przypisuje wartość do istniejącej odpowiedzi i/lub nagłówka żądania lub dodaje nową odpowiedź i/lub nagłówek żądania.

 Wstawia listę nagłówków HTTP do wiadomości HTTP. Po umieszczeniu w potoku przychodzącym te zasady ustawiają nagłówki HTTP dla żądania przesyłanego do usługi docelowej. Po umieszczeniu w potoku wychodzącym te zasady ustawiają nagłówki HTTP dla odpowiedzi wysyłanej do klienta bramy.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Przykłady

#### <a name="example---adding-header-override-existing"></a>Przykład — dodawanie nagłówka, zastępowanie istniejącego

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Przykład — usuwanie nagłówka

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Przekazywanie informacji kontekstu do usługi wewnętrznej bazy danych
 Ten przykład pokazuje, jak zastosować zasady na poziomie interfejsu API, aby dostarczyć informacje kontekstu do usługi wewnętrznej bazy danych. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 10:30. Na 12:10 znajduje się pokaz wywoływania operacji w portalu dla deweloperów, w której można zobaczyć zasady w miejscu pracy.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Wiele wartości nagłówka są połączone z ciągiem CSV, na przykład: `headerName: value1,value2,value3`
>
> Wyjątki zawierają standardowe nagłówki, które są wartościami:
> - może zawierać przecinków (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - może zawierać datę (`Cookie`, `Set-Cookie`, `Warning`),
> - zawierają datę (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> W przypadku tych wyjątków wiele wartości nagłówka nie będzie łączonych w jeden ciąg i zostanie przesłane jako oddzielne nagłówki, na przykład: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|set-header|Element główny.|Tak|
|wartość|Określa wartość nagłówka, która ma zostać ustawiona. Dla wielu nagłówków o tej samej nazwie Dodaj dodatkowe `value` elementy.|Nie|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|Istnieje — akcja|Określa akcję, która ma zostać podjęta, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego nagłówka.<br />-Skip — nie zastępuje istniejącej wartości nagłówka.<br />-Append-dołącza wartość do istniejącej wartości nagłówka.<br />-DELETE — usuwa nagłówek z żądania.<br /><br /> Gdy ustawiona na `override` rejestrowanie wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wymienione wartości.|Nie|Mapowań|
|name|Określa nazwę nagłówka, który ma zostać ustawiony.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="SetQueryStringParameter"></a>Ustaw parametr ciągu zapytania
 Zasady `set-query-parameter` dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania. Może służyć do przekazywania parametrów zapytania oczekiwanych przez usługę zaplecza, które są opcjonalne lub nigdy nie występują w żądaniu.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Przekazywanie informacji kontekstu do usługi wewnętrznej bazy danych
 Ten przykład pokazuje, jak zastosować zasady na poziomie interfejsu API, aby dostarczyć informacje kontekstu do usługi wewnętrznej bazy danych. Aby zapoznać się z prezentacją konfigurowania i korzystania z tych zasad, zobacz temat [Cloud okładki epizod 177: więcej API Management funkcji z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przekazanie do 10:30. Na 12:10 znajduje się pokaz wywoływania operacji w portalu dla deweloperów, w której można zobaczyć zasady w miejscu pracy.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Set-Query-Parameter|Element główny.|Tak|
|wartość|Określa wartość parametru zapytania, która ma zostać ustawiona. Dla wielu parametrów zapytania o tej samej nazwie Dodaj dodatkowe `value` elementy.|Tak|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|Istnieje — akcja|Określa akcję, która ma zostać podjęta, gdy parametr zapytania jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego parametru.<br />-Skip — nie zastępuje istniejącej wartości parametru zapytania.<br />-Append-dołącza wartość do istniejącej wartości parametru zapytania.<br />-DELETE — Usuwa parametr zapytania z żądania.<br /><br /> Gdy ustawiona na `override` rejestracji wielu wpisów o tej samej nazwie powoduje, że parametr zapytania jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wymienione wartości.|Nie|Mapowań|
|name|Określa nazwę parametru zapytania, który ma zostać ustawiony.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="RewriteURL"></a>Ponownie Napisz adres URL
 Zasady `rewrite-uri` konwertują adres URL żądania z jego formularza publicznego na formularz oczekiwany przez usługę sieci Web, jak pokazano w poniższym przykładzie.

- Publiczny adres URL — `http://api.example.com/storenumber/ordernumber`

- Adres URL żądania — `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Tych zasad można używać w przypadku, gdy adres URL i/lub przyjazny dla przeglądarki powinien być przekształcony w format adresu URL oczekiwany przez usługę sieci Web. Te zasady muszą być stosowane tylko przy udostępnianiu alternatywnego formatu adresu URL, na przykład czystych adresów URL, adresów URL RESTful, przyjaznych dla użytkownika adresów URL lub przyjaznych dla funkcji optymalizacji adresów URL, które nie zawierają ciągu zapytania, a zamiast tego zawierają tylko ścieżkę do zasobu ( po schemacie i urzędzie). Jest to często wykonywane w celach estetycznych, użytecznych lub optymalizacji aparatu wyszukiwania (w przypadku funkcji optymalizacji).

> [!NOTE]
>  Parametry ciągu zapytania można dodawać tylko przy użyciu zasad. Nie można dodać dodatkowych parametrów ścieżki szablonu w adresie URL ponownego zapisywania.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|ponowne zapisywanie — identyfikator URI|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|szablon|Rzeczywisty adres URL usługi sieci Web z dowolnymi parametrami ciągu zapytania. W przypadku używania wyrażeń cała wartość musi być wyrażeniem.|Tak|Nie dotyczy|
|copy-unmatched-params|Określa, czy parametry zapytania w żądaniu przychodzącym nie występują w oryginalnym szablonie adresu URL są dodawane do adresu URL zdefiniowanego przez ponowne zapisanie szablonu|Nie|true|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="XSLTransform"></a>Przekształcanie kodu XML przy użyciu XSLT
 Zasady `Transform XML using an XSLT` stosują przekształcenia XSL do formatu XML w treści żądania lub odpowiedzi.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Przykład

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|przekształcenia XSL|Element główny.|Tak|
|konstruktora|Używane do definiowania zmiennych używanych w przekształceniu|Nie|
|xsl: stylesheet|Główny element arkusza stylów. Wszystkie elementy i atrybuty zdefiniowane w ramach standardowej [specyfikacji XSLT](https://www.w3.org/TR/xslt)|Tak|

### <a name="usage"></a>Sposób użycia
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące tematy:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
