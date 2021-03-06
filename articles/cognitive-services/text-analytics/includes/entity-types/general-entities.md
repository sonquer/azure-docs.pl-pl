---
title: Ogólne nazwane jednostki
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211438"
---
## <a name="general-entity-types"></a>Ogólne typy jednostek:

### <a name="person"></a>Person (Osoba)

Rozpoznawaj nazwiska osób w tekście.

Języki:
* Publiczna wersja zapoznawcza: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` i `Turkish`

| Nazwa podtypu | Opis                                                      | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Rozpoznawane nazwy osób, na przykład `Bill Gates`, `Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Persontype
Typ zadania lub rola zatrzymywana przez osobę.

Języki:
* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                                                                | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Typy zadań na przykład `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Lokalizacja

Tereny, struktury, funkcje geograficzne i geopolityczne.

Języki:

* Publiczna wersja zapoznawcza: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` i `Turkish`

| Nazwa podtypu              | Opis                                                                              | Dostępne począwszy od wersji modelu |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | lokalizacje, na przykład `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Jednostka geopolityczna (GPE) — tylko w języku angielskim| Miasta, kraje, Stany, na przykład `Seattle`, `Pennsylvania`, `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organizacja  

Uznawane organizacje, korporacje, agencje i inne grupy osób. Na przykład: firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne. Narodowe i religijne nie są uwzględnione w tym typie jednostki. 

Języki: 

* Publiczna wersja zapoznawcza: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` i `Turkish`

| Nazwa podtypu | Opis                                                                                             | Dostępne począwszy od wersji modelu |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizacje, na przykład `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Zdarzenie  

Zdarzenia historyczne, społeczne i naturalne.  

Języki: 

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                                            | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Zdarzenia, takie jak `wedding`, `hurricane`, `car accident``solar eclipse`, `American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product (Produkt)  

Obiekty fizyczne różnych kategorii.  

Języki: 

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                                                        | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Na przykład `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag`, `Xbox` | `2020-02-01`                           |
| Obliczanie    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Czy  

Jednostka opisująca możliwości lub wiedzę fachową.  

Języki: 

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                                                 | Dostępne począwszy od wersji modelu |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/D          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Numer telefonu

Numery telefonów (tylko numery telefonów USA). 

Języki:

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                    | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Numery telefonów US, na przykład `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Adres e-mail. 

Języki:

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                      | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Adres e-mail, na przykład `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>{1&gt;URL&lt;1}

Internetowe adresy URL.

Języki:

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                                          | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | Adresy URL do witryn sieci Web, na przykład `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Adres IP

Adres protokołu internetowego

Języki:

* Publiczna wersja zapoznawcza: `English`

| Nazwa podtypu | Opis                              | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Adres sieciowy na przykład `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Jednostki daty i godziny. 

* Dostępne począwszy od wersji modelu `2019-10-01`

Języki:

* Publiczna wersja zapoznawcza: `Chinese-Simplified`, `English`, `French`, `German` i `Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| N/D         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Czas trwania | `2.5 minutes`, `one and a half hours`         |
| Set | `every Saturday`         |

###  <a name="quantity"></a>Liczba

Liczby i ilości liczbowe. 

* Dostępne począwszy od wersji modelu `2019-10-01`

Języki:

* Publiczna wersja zapoznawcza: `Chinese-Simplified`, `English`, `French`, `German` i `Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| Liczba         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Liczbą     | `2nd`, `second`              |
| Wiek         | `90 day old`, `30 years old` |
| Waluta    | `$10.99`, `€30.00`           |
| Elementów   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
