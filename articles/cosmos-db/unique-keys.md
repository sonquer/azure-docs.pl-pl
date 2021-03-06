---
title: Używanie unikatowych kluczy w Azure Cosmos DB
description: Dowiedz się, jak definiować i używać unikatowych kluczy dla bazy danych Azure Cosmos. W tym artykule opisano również, jak unikatowe klucze dodają warstwę integralność danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869837"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Ograniczenia unikatowego klucza w Azure Cosmos DB

Klucze unikatowe umożliwiają dodanie warstwy integralności danych do kontenera usługi Azure Cosmos. Podczas tworzenia kontenera usługi Azure Cosmos można utworzyć unikatowe Zasady kluczy. Przy użyciu unikatowych kluczy upewnij się, że co najmniej jedna wartość w partycji logicznej jest unikatowa. Można również zagwarantować unikatowość dla każdego [klucza partycji](partition-data.md).

Po utworzeniu kontenera z unikatowymi zasadami kluczy, utworzenie nowej lub aktualizacji istniejącego elementu, co spowodowało wystąpienie duplikatu w obrębie partycji logicznej, jest blokowane, zgodnie z ograniczeniami unikatowego klucza. Klucz partycji połączony z unikatowym kluczem gwarantuje unikatowość elementu w zakresie kontenera.

Rozważmy na przykład kontener Azure Cosmos z adresem e-mail jako ograniczenie UNIQUE Key i `CompanyID` jako klucz partycji. W przypadku skonfigurowania adresu e-mail użytkownika przy użyciu unikatowego klucza każdy element ma unikatowy adres e-mail w ramach danego `CompanyID`. Nie można tworzyć dwóch elementów ze zduplikowanymi adresami e-mail i z tą samą wartością klucza partycji. 

Aby utworzyć elementy o tym samym adresie e-mail, ale nie te same imię, nazwisko i adres e-mail, Dodaj więcej ścieżek do unikatowych zasad kluczy. Zamiast tworzyć unikatowe klucze wyłącznie na podstawie adresu e-mail, można również utworzyć unikatowy klucz z kombinacją imię, nazwisko i adres e-mail. Ten klucz jest znany jako złożony klucz unikatowy. W takim przypadku każda unikatowa kombinacja trzech wartości w ramach danego `CompanyID` jest dozwolona. 

Na przykład kontener może zawierać elementy o następujących wartościach, gdzie każdy element honoruje ograniczenie UNIQUE Key.

|CompanyID|Imię|Nazwisko|Email address (Adres e-mail)|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

W przypadku próby wstawienia innego elementu z kombinacjami wymienionymi w poprzedniej tabeli zostanie wyświetlony komunikat o błędzie. Błąd oznacza, że nie spełniono ograniczenia UNIQUE Key. Otrzymujesz `Resource with specified ID or name already exists` lub `Resource with specified ID, name, or unique index already exists` jako komunikat zwrotny. 

## <a name="define-a-unique-key"></a>Zdefiniuj unikatowy klucz

Klucze unikatowe można definiować tylko podczas tworzenia kontenera usługi Azure Cosmos. Unikatowy klucz jest objęty zakresem partycji logicznej. W poprzednim przykładzie w przypadku partycjonowania kontenera na podstawie kodu POCZTOWego można wykonać zduplikowane elementy w każdej partycji logicznej. Podczas tworzenia unikatowych kluczy należy wziąć pod uwagę następujące właściwości:

* Nie można zaktualizować istniejącego kontenera, aby użyć innego unikatowego klucza. Innymi słowy po utworzeniu kontenera przy użyciu unikatowych zasad kluczy nie można zmienić zasad.

* Aby ustawić unikatowy klucz dla istniejącego kontenera, Utwórz nowy kontener z ograniczeniem Unique Key. Użyj odpowiedniego narzędzia do migracji danych, aby przenieść dane z istniejącego kontenera do nowego kontenera. W przypadku kontenerów SQL Użyj [Narzędzia do migracji danych](import-data.md) , aby przenieść dane. W przypadku kontenerów MongoDB Użyj [mongoimport. exe lub mongorestore. exe](mongodb-migrate.md) , aby przenieść dane.

* Unikatowe Zasady kluczy mogą zawierać maksymalnie 16 wartości ścieżki. Na przykład wartości mogą być `/firstName`, `/lastName`i `/address/zipCode`. Każda unikatowa zasada klucza może mieć maksymalnie 10 unikatowych ograniczeń klucza lub kombinacji. Ścieżki połączone dla każdego unikatowego ograniczenia indeksu nie mogą przekraczać 60 bajtów. W poprzednim przykładzie pierwsze imię, nazwisko i adres e-mail są jednym ograniczeniem. To ograniczenie powoduje użycie 3 z 16 możliwych ścieżek.

* Gdy kontener ma unikatowe Zasady kluczy, opłaty za [jednostkę żądania](request-units.md) do tworzenia, aktualizowania i usuwania elementu są nieco wyższe.

* Unikatowe klucze rozrzedzone nie są obsługiwane. Jeśli brakuje niektórych unikatowych wartości ścieżki, są one traktowane jako wartości null, które uwzględniają część ograniczenia unikatowości. Z tego powodu może istnieć tylko jeden element z wartością null do spełnienia tego ograniczenia.

* W unikatowych nazwach kluczy jest rozróżniana wielkość liter. Rozważmy na przykład kontener z ograniczeniem Unique Key ustawionym na `/address/zipcode`. Jeśli dane zawierają pole o nazwie `ZipCode`, Azure Cosmos DB wstawia wartość "null" jako unikatowy klucz, ponieważ `zipcode` nie jest taka sama jak `ZipCode`. Ze względu na to, że nie ma uwzględniania wielkości liter, wszystkie inne rekordy z kod pocztowy nie mogą być wstawiane, ponieważ zduplikowane wartości "null" naruszają ograniczenie UNIQUE Key.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md)
* Zapoznaj [się z tematem Definiowanie unikatowych kluczy](how-to-define-unique-keys.md) podczas tworzenia kontenera
