---
title: Jak dodać obiekty blob do obiektów — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak dodawać obiekty blob do użytkowników, urządzeń i miejsc w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929622"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Dodaj obiekty blob do obiektów w usłudze Azure Digital bliźniaczych reprezentacji

Obiekty blob są niestrukturalnymi reprezentacjami wspólnych typów plików, takich jak obrazy i dzienniki. Obiekty blob śledzą rodzaj danych, które reprezentują przy użyciu typu MIME (na przykład: "Image/JPEG") i metadanych (nazwa, opis, typ itd.).

Usługa Azure Digital bliźniaczych reprezentacji obsługuje dołączanie obiektów BLOB do urządzeń, miejsc i użytkowników. Obiekty blob mogą reprezentować obraz profilu dla użytkownika, Zdjęcie urządzenia, wideo, mapę, kod zip oprogramowania układowego, dane JSON, dziennik itp.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Omówienie przekazywania obiektów BLOB

Możesz użyć wieloczęściowych żądań, aby przekazać obiekty blob do określonych punktów końcowych i ich odpowiednich funkcji.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz tworzenia zawartości **i** **usuwania zawartości**, żądania wieloczęściowych obiektów BLOB usługi Azure Digital bliźniaczych reprezentacji muszą określać poprawną treść JSON. Która treść JSON do przesłania zależy od rodzaju wykonywanej operacji żądania HTTP.

Cztery główne schematy JSON:

[![schematy JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Metadane obiektu BLOB JSON są zgodne z następującym modelem:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Atrybut | Typ | Opis |
| --- | --- | --- |
| **parentId** | Ciąg | Jednostka nadrzędna, z którą ma zostać skojarzony obiekt BLOB (miejsca, urządzenia lub Użytkownicy) |
| **Nazwa** |Ciąg | Przyjazna dla człowieka nazwa obiektu BLOB |
| **type** | Ciąg | Typ obiektu BLOB — nie można używać *typu* i elementu *typeId*  |
| **typeId** | Liczba całkowita | Identyfikator typu obiektu BLOB — nie można używać *typu* i elementu *typeId* |
| **Podtyp** | Ciąg | Podtyp obiektu BLOB — nie można użyć *podtypu* i elementu *subtypeid* |
| **subtypeId** | Liczba całkowita | Identyfikator podtypu dla obiektu BLOB — nie można użyć *podtypu* i elementu *subtypeid* |
| **zharmonizowan** | Ciąg | Dostosowany opis obiektu BLOB |
| **sharing** | Ciąg | Czy obiekt BLOB może być współużytkowany-enum [`None`, `Tree`, `Global`] |

Metadane obiektu BLOB są zawsze dostarczane jako pierwszy fragment z **typem zawartości** `application/json` lub jako plik `.json`. Dane pliku są dostarczane w drugim fragmencie i mogą być dowolnego obsługiwanego typu MIME.

Dokumentacja struktury Swagger opisuje te schematy modelu w pełnych szczegółach.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Więcej informacji na temat korzystania z dokumentacji referencyjnej można znaleźć w temacie [jak korzystać z struktury Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dane odpowiedzi obiektów BLOB

Pojedyncze zwrócone obiekty blob są zgodne z następującym schematem JSON:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Atrybut | Typ | Opis |
| --- | --- | --- |
| **id** | Ciąg | Unikatowy identyfikator obiektu BLOB |
| **Nazwa** |Ciąg | Przyjazna dla człowieka nazwa obiektu BLOB |
| **parentId** | Ciąg | Jednostka nadrzędna, z którą ma zostać skojarzony obiekt BLOB (miejsca, urządzenia lub Użytkownicy) |
| **type** | Ciąg | Typ obiektu BLOB — nie można używać *typu* i elementu *typeId*  |
| **typeId** | Liczba całkowita | Identyfikator typu obiektu BLOB — nie można używać *typu* i elementu *typeId* |
| **Podtyp** | Ciąg | Podtyp obiektu BLOB — nie można użyć *podtypu* i elementu *subtypeid* |
| **subtypeId** | Liczba całkowita | Identyfikator podtypu dla obiektu BLOB — nie można użyć *podtypu* i elementu *subtypeid* |
| **sharing** | Ciąg | Czy obiekt BLOB może być współużytkowany-enum [`None`, `Tree`, `Global`] |
| **zharmonizowan** | Ciąg | Dostosowany opis obiektu BLOB |
| **contentInfos** | Tablica | Określa informacje o metadanych bez struktury, w tym wersję |
| **fullName** | Ciąg | Pełna nazwa obiektu BLOB |
| **spacePaths** | Ciąg | Ścieżka miejsca |

Metadane obiektu BLOB są zawsze dostarczane jako pierwszy fragment z **typem zawartości** `application/json` lub jako plik `.json`. Dane pliku są dostarczane w drugim fragmencie i mogą być dowolnego obsługiwanego typu MIME.

### <a name="blob-multipart-request-examples"></a>Przykłady żądania wieloczęściowego obiektu BLOB

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Aby przekazać plik tekstowy jako obiekt BLOB i skojarzyć go z miejscem, wykonaj uwierzytelnione żądanie HTTP POST:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Następujące jednostki:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Wartość | Zamień na |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nazwa granicy wieloczęściowej zawartości |

Poniższy kod jest implementacją platformy .NET tego samego przekazywania obiektów BLOB przy użyciu klasy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Wreszcie, [zazwinięcie](https://curl.haxx.se/) użytkownicy mogą wykonywać wieloczęściowe żądania formularzy w taki sam sposób:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOKEN | Prawidłowy token uwierzytelniania OAuth 2,0 |
| YOUR_SPACE_ID | Identyfikator przestrzeni, z którą ma zostać skojarzony obiekt BLOB |
| PATH_TO_FILE | Ścieżka do pliku tekstowego |

[przykład za![ki](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Pomyślne ogłoszenie zwraca identyfikator nowego obiektu BLOB.

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API

W poniższych sekcjach opisano podstawowe punkty końcowe interfejsu API powiązane z obiektami BLOB i ich funkcje.

### <a name="devices"></a>Urządzenia

Obiekty blob można dołączać do urządzeń. Na poniższej ilustracji przedstawiono dokumentację referencyjną struktury Swagger dla interfejsów API zarządzania. Określa punkty końcowe interfejsu API związane z urządzeniami do użycia obiektów blob i wszystkie wymagane parametry ścieżki do przekazania do nich.

[![obiekty blob urządzenia](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Na przykład aby zaktualizować lub utworzyć obiekt BLOB i dołączyć obiekt BLOB do urządzenia, wykonaj uwierzytelnione żądanie HTTP PATCH:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobs-response-data).

### <a name="spaces"></a>Znaków

Możesz również dołączyć obiekty blob do obszarów. Na poniższej ilustracji przedstawiono punkty końcowe interfejsu API obszaru, które są odpowiedzialne za obsługę obiektów BLOB. Wyświetla również wszystkie parametry ścieżki do przekazania do tych punktów końcowych.

[![przestrzenie obiektów BLOB](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Aby na przykład zwrócić obiekt BLOB dołączony do spacji, należy wykonać uwierzytelnione żądanie HTTP GET:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobs-response-data).

Żądanie poprawki do tego samego punktu końcowego aktualizuje opisy metadanych i tworzy wersje obiektu BLOB. Żądanie HTTP jest nawiązywane za pośrednictwem metody PATCH, wraz z wszelkimi niezbędnymi danymi formularza meta i wieloczęściowych.

### <a name="users"></a>Użytkownicy

Obiekty blob można dołączać do modeli użytkowników (na przykład w celu skojarzenia obrazu profilu). Na poniższej ilustracji przedstawiono odpowiednie punkty końcowe interfejsu API użytkownika i wszystkie wymagane parametry ścieżki, takie jak `id`:

[![obiektów BLOB użytkownika](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Na przykład aby pobrać obiekt BLOB dołączony do użytkownika, należy wykonać uwierzytelnione żądanie HTTP GET przy użyciu wymaganych danych formularza, aby:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobs-response-data).

## <a name="common-errors"></a>Typowe błędy

* Typowy błąd nie obejmuje dostarczania informacji o prawidłowym nagłówku:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Aby rozwiązać ten problem, sprawdź, czy ogólne żądanie ma odpowiedni nagłówek **Content-Type** :

     * `multipart/mixed`
     * `multipart/form-data`

  Upewnij się również, że każdy *fragment wieloczęściowy* ma odpowiedni odpowiedni **Typ zawartości**.

* Drugi typowy błąd występuje, gdy wiele obiektów BLOB jest przypisanych do tego samego zasobu na [grafie analizy przestrzennej](concepts-objectmodel-spatialgraph.md):

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > Atrybut **Message** będzie różny w zależności od zasobu. 

  Tylko jeden obiekt BLOB (dowolnego rodzaju) może być dołączony do każdego zasobu w grafie przestrzennym. 

  Aby rozwiązać ten problem, zaktualizuj istniejący obiekt BLOB przy użyciu odpowiedniej operacji API HTTP PATCH. Spowoduje to zamianę istniejących danych obiektów BLOB na żądane dane.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat dokumentacji struktury Swagger dla usługi Azure Digital bliźniaczych reprezentacji, przeczytaj artykuł [Korzystanie z usługi Azure Digital bliźniaczych reprezentacji Swagger](how-to-use-swagger.md).

- Aby przekazać obiekty blob za poorednictwem programu Poster, przeczytaj artykuł [How to configure the Poster](./how-to-configure-postman.md).