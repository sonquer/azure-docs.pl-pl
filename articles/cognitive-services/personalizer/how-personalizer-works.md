---
title: Jak działa program Personalizuj-Personalizacja
titleSuffix: Azure Cognitive Services
description: Personalizacja korzysta z uczenia maszynowego, aby poznać działanie, które ma być używane w kontekście. Każda pętla szkoleniowa ma model, który jest szkolony wyłącznie na danych wysłanych do niego za pośrednictwem wywołań rangi i nagrody. Każda pętla szkoleniowa jest całkowicie niezależna od siebie.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 902bf84ebf090cf9f0f886ad1e774ff7bdfeca93
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490745"
---
# <a name="how-personalizer-works"></a>Jak działa usługa Personalizacja

Personalizacja korzysta z uczenia maszynowego, aby poznać działanie, które ma być używane w kontekście. Każda pętla szkoleniowa ma model, który jest szkolony wyłącznie na danych wysłanych do niego za pośrednictwem wywołań **rangi** i **nagrody** . Każda pętla szkoleniowa jest całkowicie niezależna od siebie. Utwórz pętlę szkoleniową dla każdej części lub zachowania aplikacji, którą chcesz spersonalizować.

Dla każdej pętli należy **wywołać interfejs API rangi** w oparciu o bieżący kontekst z:

* Lista możliwych akcji: elementy zawartości, z których ma zostać wybrana Górna akcja.
* Lista [funkcji kontekstu](concepts-features.md): kontekstowe istotne dane, takie jak użytkownik, zawartość i kontekst.

Interfejs API **rangi** decyduje się użyć:

* _Wykorzystanie_: bieżący model, aby określić najlepszą akcję na podstawie przeszłych danych.
* _Eksploruj_: Wybierz inną akcję, a nie górną akcję.

Interfejs API **nagradzania** :

* Zbiera dane do uczenia modelu przez zarejestrowanie funkcji i nagrody każdego wywołania rangi.
* Używa tych danych do aktualizowania modelu w oparciu o konfigurację określoną w _zasadach nauki_.

## <a name="architecture"></a>Architektura

Na poniższej ilustracji przedstawiono przepływ architektury wywołujący wywołania rangi i nagrody:

![tekst alternatywny](./media/how-personalizer-works/personalization-how-it-works.png "Jak działa Personalizacja")

1. Personalizacja używa wewnętrznego modelu AI do określenia rangi akcji.
1. Usługa decyduje, czy wykorzystać bieżący model, czy eksplorować nowe opcje modelu.  
1. Wynik klasyfikacji jest wysyłany do centrum EventHub.
1. Gdy Personalizowanie otrzymuje wynagrodzenie, wynagrodzenie jest wysyłane do centrum EventHub. 
1. Ranga i Nagroda są skorelowane.
1. Model AI jest aktualizowany w oparciu o wyniki korelacji.
1. Aparat wnioskowania jest aktualizowany nowym modelem. 

## <a name="research-behind-personalizer"></a>Zbadaj za personalizacją

Personalizacja jest oparta na wykorzystaniu nauki i badań w dziedzinie [nauki wzmacniania](concepts-reinforcement-learning.md) , w tym na dokumentach, w badaniach naukowych i w ciągłych obszarach eksploracji firmy Microsoft.

## <a name="terminology"></a>Terminologia

* **Pętla szkoleniowa**: możesz utworzyć pętlę szkoleniową dla każdej części aplikacji, która może korzystać z personalizacji. Jeśli masz więcej niż jedno środowisko do personalizacji, Utwórz pętlę dla każdego z nich. 

* **Akcje**: akcje to elementy zawartości, takie jak produkty lub promocje, do wyboru. Personalizacja wybiera górną akcję pokazywaną użytkownikom, znaną jako _Akcja nagrody_, za pośrednictwem interfejsu API rangi. Każda akcja może mieć funkcje przesłane przy użyciu żądania rangi.

* **Kontekst**: aby zapewnić dokładniejszą rangę, podaj informacje o Twoim kontekście, na przykład:
    * Użytkownik.
    * Urządzenie, na którym się znajdują. 
    * Bieżący czas.
    * Inne dane dotyczące bieżącej sytuacji.
    * Dane historyczne dotyczące użytkownika lub kontekstu.

    Dana aplikacja może mieć różne informacje o kontekście. 

* **[Funkcje](concepts-features.md)** : jednostka informacji o elemencie zawartości lub kontekście użytkownika.

* **Wynagrodzenie**: miara, w jaki Użytkownik odpowiedział na działanie interfejsu API rangi, jako wynik z przedziału od 0 do 1. Wartość od 0 do 1 jest ustawiana przez logikę biznesową, w zależności od tego, jak wybór pozwala osiągnąć cele biznesowe personalizacji. 

* **Eksploracja**: usługa personalizacji szuka, gdy, zamiast zwracać najlepszą akcję, wybiera inną akcję dla użytkownika. Usługa personalizacji pozwala uniknąć dryfowania, stagnation i można dostosowywać do bieżących zachowań użytkowników przez Eksplorowanie. 

* **Czas trwania eksperymentu**: ilość czasu, przez jaką usługa personalizacji czeka na wynagrodzenie, rozpoczynając od momentu wywołania rangi dla tego zdarzenia.

* **Zdarzenia nieaktywne**: zdarzenie nieaktywne to wystąpienie o nazwie rangi, ale nie masz pewności, że użytkownik zobaczy wynik z powodu decyzji aplikacji klienta. Nieaktywne zdarzenia umożliwiają tworzenie i przechowywanie wyników personalizacji, a następnie podjęcie decyzji o odrzuceniu ich później bez wpływu na model uczenia maszynowego.

* **Model**: model personalizowania przechwytuje wszystkie dane, które są uzyskiwane na temat zachowania użytkowników, uzyskiwanie danych szkoleniowych z kombinacji argumentów wysyłanych do rangi i płatnych wywołań oraz z zachowaniem szkolenia określonym przez zasady uczenia się. 

* **Zasady uczenia**: jak program łączący tworzy model dla każdego zdarzenia, zostanie określony przez niektóre meta-parametry, które wpływają na działanie algorytmów uczenia maszynowego. Nowe pętle personalizacji zaczynają się od domyślnych zasad uczenia, co może spowodować umiarkowaną wydajność. Podczas przeprowadzania [obliczeń](concepts-offline-evaluation.md)program personalizujer może utworzyć nowe zasady uczenia, które zostały zoptymalizowane pod kątem użycia pętli. Personalizacja będzie działać znacznie lepiej dzięki zasadom zoptymalizowanym dla każdej konkretnej pętli, generowanej podczas obliczania.

## <a name="example-use-cases-for-personalizer"></a>Przykładowe przypadki użycia dla personalizacji

* Wyjaśnienie zamiaru & uściślania: Pomóż użytkownikom w lepszym działaniu, gdy ich intencje nie są jasne, zapewniając opcję spersonalizowaną dla każdego użytkownika.
* Domyślne sugestie dotyczące menu & Opcje: bot Sugeruj najbardziej prawdopodobną pozycję w spersonalizowanym trybie jako pierwszy krok, zamiast przedstawiać menu lub listę alternatyw.
* Cechy bot & ton: dla botów, które mogą różnić się od tonu, szczegółowości i stylu pisania, rozważ zróżnicowanie tych cech w spersonalizowany sposób.
* Treść alertu & powiadomień: określ tekst, który ma być używany na potrzeby alertów, aby zapewnić użytkownikom więcej.
* Czas alertu & powiadomień: masz spersonalizowaną naukę podczas wysyłania powiadomień do użytkowników w celu ich przeprowadzenia.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak używać personalizacji w aplikacji sieci Web

Dodawanie pętli do aplikacji sieci Web obejmuje:

* Określ, które środowisko ma być personalizowane, jakie akcje i funkcje, jakie funkcje kontekstu mają być używane, i jakie znaczenie ma.
* Dodaj odwołanie do zestawu SDK personalizacji w swojej aplikacji.
* Wywołaj interfejs API rangi, gdy wszystko będzie gotowe do personalizacji.
* Zapisz eventId. Za pomocą interfejsu API nagradzania wysyłasz premię później.
1. Wywołaj aktywację dla zdarzenia po upewnieniu się, że użytkownik zobaczy spersonalizowaną stronę.
1. Zaczekaj, aż użytkownik wybierze sklasyfikowaną zawartość. 
1. Wywołaj interfejs API nagradzania, aby określić, jak dobrze dane wyjściowe interfejsu API rangi zostały wykonane.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak używać funkcji personalizowania z usługą Chat bot

W tym przykładzie zobaczysz, jak za pomocą personalizacji wprowadzić domyślną sugestię zamiast wysyłania użytkownika w dół szereg menu lub opcji za każdym razem.

* Pobierz [kod](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) dla tego przykładu.
* Skonfiguruj rozwiązanie bot. Upewnij się, że aplikacja LUIS została opublikowana. 
* Zarządzaj wywołaniami interfejsu API rangi i nagradzania dla bot.
    * Dodaj kod, aby zarządzać LUIS zamiarem. Jeśli wartość **none** nie jest zwracana jako zamierzenie górne lub wartość oceny najwyższego poziomu jest niższa od progu logiki biznesowej, Wyślij listę intencji do personalizacji, aby zaklasyfikować intencje.
    * Pokaż listę intencji użytkownikom jako wybrane linki z pierwszym zamiarem w celu uzyskania najwyższej rangi zamiaru z odpowiedzi interfejsu API rangi.
    * Przechwyć wybór użytkownika i wyślij go w wywołaniu interfejsu API nagradzania. 

### <a name="recommended-bot-patterns"></a>Zalecane wzorce bot

* Nadaje się wywołania interfejsu API rangi personalizacji za każdym razem, gdy konieczne jest odróżnienie, w przeciwieństwie do buforowania wyników dla każdego użytkownika. Wynik niedozwolonego zamiaru może ulec zmianie w czasie dla jednej osoby i umożliwienie interfejsowi API rangi Eksplorowanie wariancji spowoduje przyspieszenie ogólnej nauki.
* Wybierz interakcję, która jest wspólna dla wielu użytkowników, aby mieć wystarczającą ilość danych do spersonalizowania. Na przykład monity wprowadzające mogą być lepiej dopasowane od mniejszych wyjaśnień na grafie konwersacji, do których może przejść tylko kilku użytkowników.
* Użyj wywołań interfejsu API rangi, aby włączyć funkcję "Pierwsza sugestia jest właściwa", gdzie użytkownik otrzymuje monit "czy chcesz X?". lub "Czy chodziło o X?" Użytkownik może po prostu potwierdzić; w przeciwieństwie do udzielania użytkownikom opcji, gdzie muszą wybierać z menu. Na przykład: "użytkownik chce zamówić kawę" bot: "czy chcesz mieć podwójny Espresso?". W ten sposób sygnał zarobkowy jest również silny, ponieważ odnosi się bezpośrednio do jednej sugestii.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak używać narzędzia Personalizacja z rozwiązaniem rekomendacji

Za pomocą aparatu rekomendacji odfiltruj duży katalog do kilku elementów, które można następnie przedstawić jako 30 możliwych akcji wysyłanych do interfejsu API rangi.

Aparaty rekomendacji można używać z personalizacją:

* Skonfiguruj rozwiązanie do [rekomendacji](https://github.com/Microsoft/Recommenders/). 
* Podczas wyświetlania strony Wywołaj model rekomendacji, aby uzyskać krótką listę zaleceń.
* Personalizacja wywołań w celu klasyfikowania danych wyjściowych rozwiązania rekomendacji.
* Wyślij opinię na temat akcji użytkownika przy użyciu wywołania interfejsu API nagradzania.


## <a name="pitfalls-to-avoid"></a>Pułapek, aby uniknąć

* Nie używaj personalizacji, gdy spersonalizowane zachowanie nie jest elementem, który można znaleźć dla wszystkich użytkowników, ale raczej należy zapamiętać, że należy zapamiętać dla określonych użytkowników lub pochodzi z listy alternatyw dla konkretnych użytkowników. Na przykład przy użyciu narzędzia Personalizacja w celu zaproponowania pierwszej kolejności Pizza na podstawie listy 20 możliwych elementów menu jest przydatna, ale który kontaktuje się z listą osób kontaktowych użytkowników, gdy wymaga pomocy dla pielęgnacji dzieci (na przykład "Grandma"), nie jest to coś, co jest Personalizable w Baza użytkownika.


## <a name="adding-content-safeguards-to-your-application"></a>Dodawanie zabezpieczeń zawartości do aplikacji

Jeśli aplikacja zezwala na duże wariancje zawartości widocznej dla użytkowników, a niektóre z nich mogą być niebezpieczne lub nieodpowiednie dla niektórych użytkowników, należy zaplanować z wyprzedzeniem, aby upewnić się, że odpowiednie zabezpieczenia są stosowane w celu uniemożliwienia użytkownikom nieakceptowalnego wyświetlenia treści. Najlepszym wzorcem do implementowania zabezpieczeń są:
    * Uzyskaj listę akcji do rangi.
    * Odfiltruj te, które nie są przeznaczone dla odbiorców.
    * Należy tylko klasyfikować te działania.
    * Wyświetl dla użytkownika górną rangę.

W niektórych architekturach powyższa sekwencja może być trudna do zaimplementowania. W takim przypadku istnieje alternatywne podejście do implementowania zabezpieczeń po klasyfikacji, ale konieczne jest zapewnienie, że działania, które wykraczają poza zabezpieczenia, nie są używane do uczenia modelu personalizowania.

* Uzyskaj listę akcji do rangi, z zdezaktywowaną nauką.
* Akcje rangi.
* Sprawdź, czy górna akcja jest opłacalna.
    * Jeśli Górna akcja jest wykonalna, Aktywuj uczenie dla tej rangi, a następnie Pokaż ją użytkownikowi.
    * Jeśli najwyższe działanie nie jest możliwe, nie należy aktywować uczenia w ramach tej klasyfikacji i zdecydować się na podstawie własnej logiki lub alternatywnej metody, które mają być widoczne dla użytkownika. Nawet jeśli używasz opcji Second-Najlepsza w rankingu, nie Uaktywniaj uczenia dla tej klasyfikacji.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Weryfikowanie odpowiedniej skuteczności personalizowania

Skuteczność personalizowania można monitorować okresowo przez wykonywanie [ocen w trybie offline](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Następne kroki

Informacje o tym [, gdzie można użyć personalizacji](where-can-you-use-personalizer.md).
Wykonaj [oceny w trybie offline](how-to-offline-evaluation.md)
