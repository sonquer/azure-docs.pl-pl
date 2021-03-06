---
title: Utwórz klucz partycji syntetycznej w Azure Cosmos DB
description: Dowiedz się, jak używać syntetycznych kluczy partycji w kontenerach usługi Azure Cosmos w celu równomiernego dystrybuowania danych i obciążenia między kluczami partycji
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441217"
---
# <a name="create-a-synthetic-partition-key"></a>Tworzenie syntetycznego klucza partycji

Najlepszym rozwiązaniem jest posiadanie klucza partycji z wieloma różnymi wartościami, takimi jak setki lub tysiące. Celem jest równomierne dystrybuowanie danych i obciążeń między elementami skojarzonymi z tymi wartościami klucza partycji. Jeśli taka właściwość nie istnieje w danych, można utworzyć *klucz partycji syntetycznej*. W tym dokumencie opisano kilka podstawowych technik generowania klucza partycji syntetycznej dla Twojego kontenera Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Łączenie wielu właściwości elementu

Można utworzyć klucz partycji, łącząc wiele wartości właściwości w jedną sztuczną `partitionKey` właściwość. Klucze te są określane jako klucze syntetyczne. Rozważmy na przykład następujący przykładowy dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

W przypadku poprzedniego dokumentu jedną z opcji jest ustawienie/deviceId lub/Date jako klucza partycji. Użyj tej opcji, jeśli chcesz podzielić kontener na podstawie jego identyfikatora lub daty. Kolejną opcją jest połączenie tych dwóch wartości w Właściwość syntetycznej `partitionKey`, która jest używana jako klucz partycji.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

W scenariuszach w czasie rzeczywistym można mieć tysiące elementów w bazie danych. Zamiast ręcznie dodać klucz syntetyczny, zdefiniuj logikę po stronie klienta, aby połączyć wartości i wstawić klucz syntetyczny do elementów w kontenerach Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Używanie klucza partycji z sufiksem losowym

Kolejną możliwą strategią do dystrybucji obciążenia jest bardziej równomierne dołączenie liczby losowej na końcu wartości klucza partycji. Podczas dystrybucji elementów w ten sposób można wykonywać równoległe operacje zapisu między partycjami.

Przykładem jest to, że klucz partycji reprezentuje datę. Można wybrać liczbę losową z zakresu od 1 do 400 i połączyć ją jako sufiks do daty. Ta metoda powoduje użycie wartości klucza partycji, takich jak `2018-08-09.1`,`2018-08-09.2`i tak dalej, przez `2018-08-09.400`. Ponieważ klucz partycji jest losowo wykonywany, operacje zapisu w kontenerze każdego dnia są równomiernie rozłożone na wiele partycji. Ta metoda skutkuje lepszą równoległością i większą przepustowością.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Używanie klucza partycji z wstępnie obliczonymi sufiksami 

Strategia losowego sufiksu może znacznie poprawić przepływność zapisu, ale trudno jest odczytywać określony element. Nie znasz wartości sufiksu, która została użyta podczas napisana elementu. Aby ułatwić odczytywanie pojedynczych elementów, Użyj strategii wstępnie obliczonych sufiksów. Zamiast używać liczby losowej do dystrybuowania elementów między partycjami, należy użyć liczby, która jest obliczana na podstawie elementu, który ma być wysyłany do zapytania.

Rozważmy poprzedni przykład, w którym kontener używa daty jako klucza partycji. Teraz Załóżmy, że każdy element ma atrybut `Vehicle-Identification-Number` (`VIN`), do którego chcemy uzyskać dostęp. Ponadto Załóżmy, że często uruchamiane są zapytania, aby znaleźć elementy `VIN`, a nie datę. Zanim aplikacja zapisze element do kontenera, może obliczyć sufiks skrótu na podstawie numeru VIN i dołączyć go do daty klucza partycji. Obliczenia mogą generować liczbę z przedziału od 1 do 400, która jest równomiernie dystrybuowana. Ten wynik jest podobny do wyników wytwarzanych przez metodę strategii losowego sufiksu. Wartość klucza partycji to data połączona z obliczonym wynikiem.

Ta strategia polega na tym, że zapisy są równomiernie rozłożone w wartości kluczy partycji i między partycjami. Można łatwo odczytać określony element i datę, ponieważ można obliczyć wartość klucza partycji dla określonego `Vehicle-Identification-Number`. Zaletą tej metody jest to, że można uniknąć tworzenia jednego klucza partycji gorącej, tj. klucza partycji, który pobiera wszystkie obciążenia. 

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat koncepcji partycjonowania można znaleźć w następujących artykułach:

* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się więcej o [tym, jak zainicjować przepływność w kontenerach i bazach danych usługi Azure Cosmos](set-throughput.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
