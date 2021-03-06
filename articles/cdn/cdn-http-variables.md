---
title: Zmienne HTTP dla aparatu reguł Azure CDN | Microsoft Docs
description: Zmienne HTTP umożliwiają pobieranie metadanych żądań i odpowiedzi HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593814"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Zmienne HTTP dla aparatu reguł Azure CDN
Zmienne HTTP zapewniają metodę, za pomocą której można pobrać metadane żądań i odpowiedzi HTTP. Można następnie użyć tych metadanych do dynamicznego zmiany żądania lub odpowiedzi. Użycie zmiennych HTTP jest ograniczone do następujących funkcji aparatu zasad:

- [Pamięć podręczna — ponowne zapisywanie klucza](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modyfikowanie nagłówka żądania klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modyfikowanie nagłówka odpowiedzi klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Przekierowanie adresu URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Ponowne zapisywanie adresów URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definicje
W poniższej tabeli opisano obsługiwane zmienne HTTP. Wartość pusta jest zwracana, gdy metadane geograficzne (na przykład kod pocztowy) są niedostępne dla określonego żądania.


| Name (Nazwa) | Zmienna | Opis | Wartość przykładowa |
| ---- | -------- | ----------- | ------------ |
| ASN (zleceniodawca) | %{geo_asnum} | Wskazuje numer osoby żądającej. <br /><br />**Przestarzałe:** % {virt_dst_asnum}. <br />Ta zmienna została zaniechana na rzecz elementu% {geo_asnum}. Mimo że reguła, która używa tej przestarzałej zmiennej, będzie nadal działała, należy ją zaktualizować tak, aby korzystała z nowej zmiennej. | AS15133 |
| Miasto (zleceniodawca) | %{geo_city} | Wskazuje miasto osoby żądającej. | Los Angeles |
| Kontynent (zleceniodawca) | %{geo_continent} | Wskazuje kontynent osoby żądającej przez jego skrót. <br />Prawidłowe wartości to: <br />AF Afryka<br />AS: Azja<br />EUROPEJSKA Europa<br />POTRĄCON Ameryka Północna<br />OC Oceanii<br />RAMACH Ameryka Południowa<br /><br />**Przestarzałe:** % {virt_dst_continent}. <br />Ta zmienna została zaniechana na rzecz elementu% {geo_continent}. <br />Mimo że reguła, która używa tej przestarzałej zmiennej, będzie nadal działała, należy ją zaktualizować tak, aby korzystała z nowej zmiennej.| ND |
| Wartość cookie | %{cookie_Cookie} | Zwraca wartość odpowiadającą kluczowi cookie identyfikowanemu przez termin cookie. | Przykładowe użycie: <br />%{cookie__utma}<br /><br />Przykładowa wartość:<br />111662281.2.10.1222100123 |
| Kraj (zleceniodawca) | %{geo_country} | Wskazuje kraj osoby żądającej za pomocą swojego kodu kraju. <br />**Przestarzałe:** % {virt_dst_country}. <br /><br />Ta zmienna została zaniechana na rzecz elementu% {geo_country}. Mimo że reguła, która używa tej przestarzałej zmiennej, będzie nadal działała, należy ją zaktualizować tak, aby korzystała z nowej zmiennej. | USA |
| Wyznaczono obszar rynkowy (zleceniodawca) | %{geo_dma_code} |Wskazuje rynek multimedialny osoby żądającej według kodu regionu. <br /><br />To pole ma zastosowanie tylko do żądań, które pochodzą z Stany Zjednoczone.| 745 |
| Metoda żądania HTTP | %{request_method} | Wskazuje metodę żądania HTTP. | GET |
| Kod stanu HTTP | % {status} | Wskazuje kod stanu HTTP odpowiedzi. | 200 |
| Adres IP (Obiekt żądający) | %{virt_dst_addr} | Wskazuje adres IP osoby żądającej. | 192.168.1.1 |
| Latitude (zleceniodawca) | %{geo_latitude} | Wskazuje szerokość firmy żądającej. | 34.0995 |
| Długość geograficzna (Obiekt żądający) | %{geo_longitude} | Wskazuje długość geograficzną osoby żądającej. | -118,4143 |
| Obszar statystyczny metropolitalnych (zleceniodawca) | %{geo_metro_code} | Wskazuje obszar metropolitalnych osoby żądającej. <br /><br />To pole ma zastosowanie tylko do żądań, które pochodzą z Stany Zjednoczone.<br />| 745 |
| Port (Obiekt żądający) | %{virt_dst_port} | Wskazuje port tymczasowych osoby żądającej. | 55885 |
| Kod pocztowy (Obiekt żądający) | %{geo_postal_code} | Wskazuje kod pocztowy osoby żądającej. | 90210 |
| Znaleziono ciąg zapytania | %{is_args} | Wartość tej zmiennej różni się w zależności od tego, czy żądanie zawiera ciąg zapytania.<br /><br />-Znaleziono ciąg zapytania:?<br />-Brak ciągu zapytania: NULL | ? |
| Znaleziono parametr ciągu zapytania | %{is_amp} | Wartość tej zmiennej różni się w zależności od tego, czy żądanie zawiera co najmniej jeden parametr ciągu zapytania.<br /><br />-Znaleziono parametr: &<br />-Brak parametrów: NULL | & |
| Wartość parametru ciągu zapytania | % {arg_&lt;parametr&gt;} | Zwraca wartość odpowiadającą parametrowi ciągu zapytania identyfikowanego przez &lt;warunek parametru.&gt; | Przykładowe użycie: <br />%{arg_language}<br /><br />Przykładowy parametr ciągu zapytania: <br />? Language = pl<br /><br />Przykładowa wartość: pl |
| Wartość ciągu zapytania | %{query_string} | Wskazuje całą wartość ciągu zapytania zdefiniowaną w adresie URL żądania. |Klucz1 = Val1 & klucz2 = val2 & key3 = Val3 |
| Domena odwołująca | %{referring_domain} | Wskazuje domenę zdefiniowaną w nagłówku żądania odwołującego się. | <www.google.com> |
| Region (zleceniodawca) | %{geo_region} | Wskazuje region osoby żądającej (na przykład Województwo) za pomocą skrótu alfanumerycznego. | Urząd certyfikacji |
| Wartość nagłówka żądania | %{http_RequestHeader} | Zwraca wartość odpowiadającą nagłówkowi żądania identyfikowanemu przez termin RequestHeader. <br /><br />Jeśli nazwa nagłówka żądania zawiera kreskę (na przykład User-Agent), Zastąp ją znakiem podkreślenia (na przykład User_Agent).| Przykładowe użycie:% {http_Connection}<br /><br />Przykładowa wartość: Utrzymywanie aktywności | 
| Żądaj hosta | % {Host} | Wskazuje hosta zdefiniowanego w adresie URL żądania. | < www. webdomain. com > |
| Protokół żądania | %{request_protocol} | Wskazuje protokół żądania. | HTTP/1.1 |
| Schemat żądania | %{scheme} | Wskazuje schemat żądania. |http |
| Identyfikator URI żądania (względne) | %{request_uri} | Wskazuje ścieżkę względną, łącznie z ciągiem zapytania zdefiniowanym w identyfikatorze URI żądania. | /Marketing/foo.js? zalogowano = prawda |
| Identyfikator URI żądania (względny bez ciągu zapytania) | % {URI} | Wskazuje ścieżkę względną do wymaganej zawartości. <br /><br/>Informacje o kluczu:<br />-Ta ścieżka względna wyklucza ciąg zapytania.<br />-Ta ścieżka względna odzwierciedla ponowne zapisywanie adresów URL. Adres URL zostanie zapisany w następujących warunkach:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Funkcja ponownego zapisywania adresów URL: Ta funkcja ponownie zapisuje ścieżkę względną zdefiniowaną w identyfikatorze URI żądania.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge adres URL CNAME: Ten typ żądania jest ponownie zapisywana na odpowiednim adresie URL usługi CDN. |/800001/corigin/rewrittendir/foo.js |
| Identyfikator URI żądania | % {Request} | Zawiera opis żądania. <br />Obowiązuje &lt;Protokół http&gt; ścieżki &lt; względnejmetody&lt;http&gt;&gt; | Pobierz/Marketing/foo.js? zarejestrowano = true HTTP/1.1 |
| Wartość nagłówka odpowiedzi | % {resp_&lt;ResponseHeader&gt;} | Zwraca wartość odpowiadającą nagłówkowi odpowiedzi identyfikowanemu przez &lt;termin ResponseHeader.&gt; <br /><br />Jeśli nazwa nagłówka odpowiedzi zawiera Łącznik (na przykład User-Agent), zastąp go znakiem podkreślenia (na przykład User_Agent). | Przykładowe użycie:% {resp_Content_Length}<br /><br />Przykładowa wartość: 100 |

## <a name="usage"></a>Użycie
W poniższej tabeli opisano poprawną składnię określania zmiennej HTTP.


| Składnia | Przykład | Opis |
| ------ | -------- | ---------- |
| % {&lt;HTTPVariable&gt;} | % {Host} | Użyj tej składni, aby uzyskać całą wartość odpowiadającą określonemu &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {Host,} | Użyj tej składni, aby ustawić wielkość liter dla całej wartości odpowiadającej określonemu &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | % {Host/= ^ www\.([^\.] +)\.([^\.:] +)/CDN. $2. $3:80} | Użyj wyrażenia regularnego dla &lt;HTTPVariableDelimiterExpression&gt; , aby zamienić, usunąć lub manipulować wartością zmiennej http. |

Nazwy zmiennych HTTP obsługują tylko litery i znaki podkreślenia. Konwertuj nieobsługiwane znaki na podkreślenia.

## <a name="delimiter-reference"></a>Odwołanie ogranicznika
Ogranicznik można określić po zmiennej HTTP w celu osiągnięcia dowolnego z następujących efektów:

- Przekształć wartość skojarzoną ze zmienną.

     Przykład: Przekonwertuj całą wartość na małe litery.

- Usuń wartość skojarzoną ze zmienną.

- Manipulowanie wartością skojarzoną ze zmienną.

     Przykład: Użyj wyrażeń regularnych, aby zmienić wartość skojarzoną z zmienną HTTP.

Ograniczniki są opisane w poniższej tabeli.

| Ogranicznik | Opis |
| --------- | ----------- |
| := | Wskazuje, że wartość domyślna zostanie przypisana do zmiennej, gdy jest: <br />-Brak <br />-Ustaw na wartość NULL. |
| :+ | Wskazuje, że wartość domyślna zostanie przypisana do zmiennej, gdy wartość zostanie przypisana do niej. |
| : | Wskazuje, że podciąg wartości przypisanej do zmiennej zostanie rozwinięty. |
| # | Wskazuje, że wzorzec określony po tym ograniczniku powinien zostać usunięty, gdy zostanie znaleziony na początku wartości skojarzonej ze zmienną. |
| % | Wskazuje, że wzorzec określony po tym ograniczniku powinien zostać usunięty, gdy zostanie znaleziony na końcu wartości skojarzonej ze zmienną. <br />Ta definicja ma zastosowanie tylko wtedy, gdy symbol% jest używany jako ogranicznik. |
| / | Ogranicza zmienną HTTP lub wzorzec. |
| // | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| /= | Znajdź, skopiuj i ponownie napisz wszystkie wystąpienia określonego wzorca. |
| , | Konwertuj wartość skojarzoną z zmienną HTTP na małe litery. |
| ^ | Konwertuj wartość skojarzoną z zmienną HTTP na wielkie litery. |
| ,, | Konwertuj wszystkie wystąpienia określonego znaku w wartości skojarzonej z zmienną HTTP na małe litery. |
| ^^ | Konwertuj wszystkie wystąpienia określonego znaku w wartości skojarzonej z zmienną HTTP na wielkie litery. |

## <a name="exceptions"></a>Wyjątki
W poniższej tabeli opisano sytuacje, w których określony tekst nie jest traktowany jako zmienna HTTP.

| Warunek | Opis | Przykład |
| --------- | ----------- | --------|
| Symbol ucieczki% | Symbol procentu można zmienić za pomocą ukośnika odwrotnego. <br />Wartość przykładowa po prawej stronie będzie traktowana jako wartość literału, a nie jako zmienna HTTP.| \%{host} |
| Nieznane zmienne | Pusty ciąg jest zawsze zwracany dla nieznanych zmiennych. | %{unknown_variable} |
| Nieprawidłowe znaki lub Składnia | Zmienne, które zawierają nieprawidłowe znaki lub składnię, są traktowane jako wartości literału. <br /><br />Przykład #1: Określona wartość zawiera nieprawidłowy znak (na przykład-). <br /><br />Przykład #2: Określona wartość zawiera podwójny zestaw nawiasów klamrowych. <br /><br />Przykład #3: Określona wartość nie zawiera zamykającego nawiasu klamrowego.<br /> | Przykład #1:% {resp_user-Agent} <br /><br />Przykład #2:% {{Host}} <br /><br />Przykład #3:% {Host |
| Brak nazwy zmiennej | Wartość NULL jest zawsze zwracana, jeśli zmienna nie została określona. | %{} |
| Tylko | Znaki kończące zmienną są traktowane jako wartości literału. <br />Przykładowa wartość po prawej stronie zawiera końcowy nawias klamrowy, który będzie traktowany jako wartość literału. | % {Host}} |

## <a name="setting-default-header-values"></a>Ustawianie domyślnych wartości nagłówka
Wartość domyślna może być przypisana do nagłówka, gdy spełnia jeden z następujących warunków:
- Brak/Cofnij ustawienie
- Ustaw na wartość NULL.

W poniższej tabeli opisano sposób definiowania wartości domyślnej.

| Warunek | Składnia | Przykład | Opis |
| --------- | ------ | --------| ----------- |
| Ustaw nagłówek na wartość domyślną, gdy spełni jeden z następujących warunków: <br /><br />— Brak nagłówka <br /><br />-Header wartość jest ustawiona na wartość NULL.| % {Variable: = value} | %{http_referrer:=unspecified} | Nagłówek odwołującego zostanie ustawiony tylko jako nieokreślony , gdy nie ma lub ma wartość null. Żadna akcja nie zostanie wykonana, jeśli została ustawiona. |
| Jeśli brakuje nagłówka, ustaw wartość domyślną. | % {Variable = wartość} | %{http_referrer=unspecified} | Jeśli brakuje nagłówka odwołującego, będzie on ustawiany tylko jako nieokreślony. Żadna akcja nie zostanie wykonana, jeśli została ustawiona. |
| Ustaw nagłówek na wartość domyślną, jeśli nie spełnia on żadnego z następujących warunków: <br /><br />-Brak<br /><br /> -Ustaw na wartość NULL. | % {Zmienna: + wartość} | % {http_referrer: + nieokreślone} | Nagłówek odwołującego zostanie ustawiony tylko jako nieokreślony , gdy wartość zostanie przypisana do niej. Żadna akcja nie zostanie wykonana, jeśli brakuje jej lub ustawiono wartość NULL. |

## <a name="manipulating-variables"></a>Manipulowanie zmiennymi
Zmienne można manipulować w następujący sposób:

- Powiększanie podciągów
- Usuwanie wzorców

### <a name="substring-expansion"></a>Rozszerzanie podciągu
Domyślnie zmienna zostanie rozwinięta do pełnej wartości. Użyj następującej składni, aby rozwinąć podciąg wartości zmiennej.

`%<Variable>:<Offset>:<Length>}`

Informacje o kluczu:

- Wartość przypisana do okresu przesunięcia określa początkowy znak podciągu:

     - Dodatni Początkowy znak podciągu jest obliczany na podstawie pierwszego znaku w ciągu.
     - Zer Początkowy znak podciągu jest pierwszym znakiem w ciągu.
     - Poziomem Początkowy znak podciągu jest obliczany na podstawie ostatniego znaku w ciągu.

- Długość podciągu jest określana na podstawie *długości* okresu:

     - Pominięty Pominięcie terminu umożliwia uwzględnienie wszystkich znaków między znakiem początkowym a końcem ciągu.
     - Dodatni Określa długość podciągu od znaku początkowego po prawej stronie.
     - Poziomem Określa długość podciągu od znaku początkowego po lewej stronie.

#### <a name="example"></a>Przykład:

W poniższym przykładzie zależą od następującego przykładowego adresu URL żądania:

https:\//CDN.mydomain.com/folder/Marketing/myconsultant/Proposal.html

Następujący ciąg ilustruje różne metody manipulowania zmiennymi:

https:\//www%{HTTP_HOST: 3}/Mobile/%{REQUEST_URI: 7:10}/%{REQUEST_URI:-5:-8}. htm

Na podstawie przykładowego adresu URL żądania w powyższej zmiennej manipulowanie zostanie utworzona następująca wartość:

https:\//www.mydomain.com/Mobile/Marketing/Proposal.htm


### <a name="pattern-removal"></a>Usuwanie wzorca
Tekst, który pasuje do określonego wzorca, można usunąć z początku lub do końca wartości zmiennej.

| Składnia | Action |
| ------ | ------ |
| %{Variable#Pattern} | Usuń tekst, gdy określony wzorzec zostanie znaleziony na początku wartości zmiennej. |
| Wzorzec% {Variable%} | Usuń tekst, gdy określony wzorzec zostanie znaleziony na końcu wartości zmiennej. |

#### <a name="example"></a>Przykład:

W tym przykładowym scenariuszu zmienna *REQUEST_URI* jest ustawiona na:

`/800001/myorigin/marketing/product.html?language=en-US`

W poniższej tabeli przedstawiono sposób działania tej składni.

| Przykładowa składnia | Wyniki | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/Marketing/Product.html? language = EN-US | Ponieważ zmienna zaczyna się od wzorca, została zastąpiona. |
| % {REQUEST_URI% HTML} htm | /800001/myorigin/marketing/product.html?language=en-US | Ponieważ zmienna nie kończy się wzorcem, nie wprowadzono zmian.|

### <a name="find-and-replace"></a>Znajdź i Zamień
Składnia Znajdź i Zamień została opisana w poniższej tabeli.

| Składnia | Action |
| ------ | ------ |
| % {Variable/Znajdź/Zamień} | Znajdź i Zamień pierwsze wystąpienie określonego wzorca. |
| % {Zmienna//Znajdź/Zamień} | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| % {Zmienna ^} |Konwertuj całą wartość na wielkie litery. |
| %{Variable^Find} | Konwertuj pierwsze wystąpienie określonego wzorca na wielkie litery. |
| % {Variable,} | Przekonwertuj całą wartość na małe litery. |
| % {Zmienna, Znajdź} | Konwertuj pierwsze wystąpienie określonego wzorca na małe litery. |

### <a name="find-and-rewrite"></a>Znajdź i Zapisz ponownie
Aby uzyskać informacje na temat wyszukiwania i zamieniania, użyj tekstu, który pasuje do określonego wzorca podczas jego zapisywania. Składnia Znajdź i napisz ponownie została opisana w poniższej tabeli.

| Składnia | Action |
| ------ | ------ |
| % {Variable/= Znajdź/Przepisz} | Znajdź, skopiuj i ponownie napisz wszystkie wystąpienia określonego wzorca. |
| % {Variable/^ Znajdź/ponownie Napisz} | Znajdź, skopiuj i ponownie Napisz określony wzorzec, gdy występuje na początku zmiennej. |
| % {Variable/$Find/Rewrite} | Znajdź, skopiuj i ponownie Napisz określony wzorzec, gdy występuje na końcu zmiennej. |
| % {Variable/Find} | Znajdź i Usuń wszystkie wystąpienia określonego wzorca. |

Informacje o kluczu:

- Rozwiń tekst, który pasuje do określonego wzorca, określając znak dolara, po którym następuje cała liczba całkowita (na przykład $1).

- Można określić wiele wzorców. Kolejność, w której jest określony wzorzec określa liczbę całkowitą, która zostanie do niego przypisana. W poniższym przykładzie pierwszy wzorzec pasuje do "www.," Drugi wzorzec jest zgodny z domeną drugiego poziomu, a trzeci wzorzec jest zgodny z domeną najwyższego poziomu:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Wartość zapisywana może składać się z dowolnej kombinacji tekstu i tych symboli zastępczych.

    W poprzednim przykładzie nazwa hosta jest ponownie `cdn.$2.$3:80` zapisywana (na przykład CDN.mydomain.com:80).

- Symbol zastępczy wzorca (na przykład $1) można modyfikować poprzez następujące flagi:
     - 'T Wielkie litery w rozwiniętej wartości.

         Przykładowa składnia:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - & Mała wartość rozwinięta.

         Przykładowa składnia:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operator musi być określony przed wzorcem. Określony operator określa zachowanie podczas przechwytywania wzorca:

     - `=`: Wskazuje, że wszystkie wystąpienia określonego wzorca muszą być przechwytywane i ponownie zapisywane.
     - `^`: Wskazuje, że zostanie przechwycony tylko tekst zaczynający się od określonego wzorca.
     - `$`: Wskazuje, że zostanie przechwycony tylko tekst kończący się określonym wzorcem.
 
- W przypadku pominięcia wartości */Rewrite* tekst, który pasuje do wzorca, zostanie usunięty.


