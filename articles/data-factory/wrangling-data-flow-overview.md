---
title: Przetwarzanie przepływy danych w Azure Data Factory
description: Omówienie przepływów danych przetwarzanie w Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048358"
---
# <a name="what-are-wrangling-data-flows"></a>Co to są przepływy danych przetwarzanie?

Organizacje muszą wykonywać przygotowanie i przetwarzanie danych w celu uzyskania dokładnej analizy złożonych danych, które w dalszym ciągu rosną. Przygotowanie danych jest wymagane, aby organizacje mogły korzystać z danych w różnych procesach firmowych i skrócić czas do wartości.

Przetwarzanie przepływy danych w Azure Data Factory umożliwiają iteracyjne wykonywanie bezobsługowego przygotowywania danych w skali chmury. Przetwarzanie przepływy danych integrują się z usługą [Power Query online](https://docs.microsoft.com/power-query/) i udostępniają funkcje Power Query M dla użytkowników usługi Data Factory.

Przepływ danych przetwarzanie umożliwia przetłumaczenie M wygenerowanego przez Edytor zestawu połączonych online Power Query na kod Spark na potrzeby wykonywania w skali chmury.

Przepływy danych przetwarzanie są szczególnie przydatne w przypadku inżynierów danych lub integratorów danych obywatela.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="fast-interactive-data-exploration-and-preparation"></a>Szybka interaktywna Eksploracja i przygotowanie danych

Wiele inżynierów danych i integratorów danych obywatela mogą interaktywnie eksplorowanie i przygotowywanie zestawów DataSet w skali chmury. Ze względu na wielkość, różnorodność i szybkość danych w jeziorach danych, użytkownicy potrzebują efektywnej metody eksplorowania i przygotowywania zestawów danych. Na przykład może być konieczne utworzenie zestawu danych, który zawiera wszystkie informacje demograficzne klienta dla nowych klientów od 2017. Nie masz mapowania na znany element docelowy. Przeszukasz, przetwarzanie i przygotowywanie zestawy danych, aby spełnić wymagania przed opublikowaniem go w usłudze Lake. Przepływy danych przetwarzanie są często używane w przypadku mniej formalnych scenariuszy analitycznych. Zestawy danych jego mogą służyć do wykonywania transformacji i operacji uczenia maszynowego.

### <a name="code-free-agile-data-preparation"></a>Niezależne od kodu przygotowanie danych Agile

Integrator danych obywatela poświęca więcej niż 60% czasu na wyszukiwanie i przygotowywanie danych. Są one niezbędne do wykonania kodu w celu zwiększenia produktywności operacyjnej. Umożliwienie integratorom danych obywatelom wzbogacanie, kształtowanie i publikowanie danych przy użyciu znanych narzędzi, takich jak Power Query online w skalowalny sposób znacząco zwiększają produktywność. Przepływ danych przetwarzanie w Azure Data Factory umożliwia znanego Power Query edytora zestawu narzędzi w trybie online, aby umożliwić integratorom danych obywatelom szybkie rozwiązywanie błędów, standaryzację danych i tworzenie wysokiej jakości danych w celu wspierania podejmowania decyzji biznesowe.

### <a name="data-validation"></a>Sprawdzanie poprawności danych

Wizualnie Skanuj dane w sposób niezależny od kodu, aby usunąć wszelkie elementy odstające, anomalie i zachować zgodność z kształtem w celu uzyskania szybkiej analizy.

## <a name="supported-sources"></a>Obsługiwane źródła

| Łącznik | Format danych | Typ uwierzytelniania |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Klucz konta |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | CSV | Jednostka usługi |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Klucz konta, nazwa główna usługi |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Uwierzytelnianie SQL |
| [Analiza usługi Azure Synapse](connector-azure-sql-data-warehouse.md) | - | Uwierzytelnianie SQL |

## <a name="the-mashup-editor"></a>Edytor mashupów

Podczas tworzenia przepływu danych przetwarzanie, wszystkie źródłowe zestawy DataSet stają się zapytaniami zestawu danych i są umieszczane w folderze **ADFResource** . Domyślnie UserQuery będzie wskazywała na pierwsze zapytanie zestawu danych. Wszystkie przekształcenia należy wykonać w UserQuery, ponieważ zmiany w zapytaniach zestawu danych nie są obsługiwane ani nie zostaną utrwalone. Zmiana nazwy, Dodawanie i usuwanie zapytań nie jest obecnie obsługiwane.

![Przetwarzanie](media/wrangling-data-flow/editor.png)

Obecnie nie wszystkie Power Query funkcje M są obsługiwane dla przetwarzanie danych, mimo że są dostępne podczas tworzenia. Podczas kompilowania przepływów danych przetwarzanie zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Aby uzyskać więcej informacji na temat obsługiwanych transformacji, zobacz [Przetwarzanie Data Flow Functions](wrangling-data-flow-functions.md).

Obecnie przepływ danych przetwarzanie obsługuje tylko zapisywanie do jednego ujścia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć przepływ danych przetwarzanie](wrangling-data-flow-tutorial.md).