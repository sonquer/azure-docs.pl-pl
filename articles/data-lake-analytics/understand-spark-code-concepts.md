---
title: Poznaj Apache Spark pojęcia dotyczące kodu dla deweloperów Azure Data Lake Analytics U-SQL.
description: W tym artykule opisano Apache Spark pojęć, które pomagają deweloperom U-SQL zrozumieć koncepcje kodu platformy Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424007"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Informacje o kodzie Apache Spark dla deweloperów U-SQL

Ta sekcja zawiera ogólne wskazówki dotyczące przekształcania skryptów U-SQL na Apache Spark.

- Rozpoczyna się od [porównania z modelami przetwarzania dwóch języków](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Zawiera wskazówki dotyczące sposobu:
   - [Przekształć skrypty](#transform-u-sql-scripts) , w tym [wyrażenia zestawu wierszy](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) U-SQL
   - [Kod platformy .NET](#transform-net-code)
   - [Typy danych](#transform-typed-values)
   - [Obiekty katalogu](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Objaśnienie języka U-SQL i i procesów przetwarzania

Przed rozpoczęciem migrowania Azure Data Lake Analytics "U-SQL scripts do platformy Spark warto zrozumieć ogólne informacje o języku i filozofiami przetwarzania tych dwóch systemów.

Skrypt U-SQL to deklaratywny język zapytań oparty na języku SQL, który używa modelu przepływu danych i umożliwia łatwe osadzanie i skalowanie kodu użytkownika pisanego w programie .NET (na przykład C#), Python i języka R. Rozszerzenia użytkownika mogą implementować proste wyrażenia lub funkcje zdefiniowane przez użytkownika, ale mogą także zapewnić użytkownikowi możliwość implementacji, tak zwane Operatory zdefiniowane przez użytkownika, które implementują operatory niestandardowe w celu wykonywania transformacji na poziomie zestawu wierszy, wyodrębniania i pisania danych wyjściowych.

Platforma Spark jest strukturą skalowalną w poziomie, która oferuje kilka powiązań języka w Scala, Java, Python, .NET itp., w przypadku których przede wszystkim piszesz kod w jednym z tych języków, tworzenie abstrakcji danych o nazwie odpornych na błędy rozproszonych zestawów DataSet (RDD), dataframes i DataSets i następnie Przekształć je przy użyciu języka specyficznego dla domeny (DSL) w języku LINQ. Udostępnia również SparkSQL jako deklaratywny podjęzyk dla elementów DataSet i Abstracts Dataframe. DSL oferuje dwie kategorie operacji, transformacji i akcji. Zastosowanie transformacji do abstrakcji danych nie spowoduje wykonania przekształcenia, ale zamiast tego kompiluje plan wykonywania, który zostanie przesłany do oceny z akcją (na przykład zapisanie wyniku do tymczasowej tabeli lub pliku lub wydrukowanie wynik).

W tym przypadku podczas tłumaczenia skryptu U-SQL do programu Spark należy zdecydować, który język ma być używany do co najmniej generowania abstrakcji ramki danych (która jest obecnie najczęściej używanym abstrakcją danych) i czy chcesz napisać deklaratywną przepływu danych przekształcenia przy użyciu języka DSL lub SparkSQL. W niektórych bardziej złożonych przypadkach może być konieczne podzielenie skryptu U-SQL na sekwencję platformy Spark i innych kroków wdrożonych przy użyciu Azure Batch lub Azure Functions.

Ponadto Azure Data Lake Analytics oferuje język U-SQL w środowisku usługi zadań bezserwerowych, natomiast zarówno Azure Databricks, jak i usługa Azure HDInsight oferują platformę Spark w postaci usługi klastra. Podczas przekształcania aplikacji należy wziąć pod uwagę skutki tworzenia, ustalania rozmiarów, skalowania i likwidowania klastrów.

## <a name="transform-u-sql-scripts"></a>Przekształć skrypty U-SQL

Skrypty U-SQL są zgodne z następującym wzorcem przetwarzania:

1. Dane są odczytywane z plików bez struktury przy użyciu instrukcji `EXTRACT`, lokalizacji lub zestawu plików, a także wbudowanego lub zdefiniowanego przez użytkownika ekstraktora oraz żądanego schematu lub z tabel U-SQL (tabel zarządzanych lub zewnętrznych). Jest reprezentowana jako zestaw wierszy.
2. Zestawy wierszy są przekształcane w wiele instrukcji U-SQL, które stosują wyrażenia U-SQL do zestawów wierszy i tworzą nowe zestawy wierszy.
3. Na koniec wynikowe zestawy wierszy są wyprowadzane do obu plików przy użyciu instrukcji `OUTPUT`, która określa lokalizacje i wbudowany lub zdefiniowany przez użytkownika element, lub tabelę U-SQL.

Skrypt jest oceniany jako opóźnieniem, co oznacza, że każdy krok wyodrębniania i przekształcania składa się z drzewa wyrażenia i oceniany globalnie (przepływu danych).

Programy Spark są podobne w przypadku używania łączników platformy Spark do odczytywania danych i tworzenia ramek dataframes, a następnie stosowania przekształceń do ramek danych przy użyciu technologii LINQ, podobnej do języka DSL lub SparkSQL, a następnie zapisania wyniku w plikach, tymczasowych tabelach Spark, Niektóre typy języków programowania lub konsoli programu.

## <a name="transform-net-code"></a>Przekształcanie kodu platformy .NET

Język wyrażeń języka U-SQL jest C# i oferuje różne sposoby skalowania niestandardowego kodu platformy .NET.

Ponieważ platforma Spark obecnie nie obsługuje wykonywania kodu platformy .NET, należy ponownie napisać wyrażenia do równoważnego wyrażenia Spark, Scala, Java lub Python lub znaleźć sposób wywoływania kodu platformy .NET. Jeśli skrypt korzysta z bibliotek platformy .NET, dostępne są następujące opcje:

- Przetłumacz swój kod platformy .NET na Scala lub Python.
- Podziel skrypt U-SQL na kilka kroków, w których używasz procesów Azure Batch, aby zastosować transformacje .NET (jeśli można uzyskać akceptowalną skalę)
- Użyj powiązania języka platformy .NET dostępnego w środowisku typu open source o nazwie Moebius. Ten projekt nie jest w obsługiwanym stanie.

W każdym przypadku, jeśli masz dużą liczbę logiki .NET w skryptach U-SQL, skontaktuj się z nami za pomocą przedstawiciela konta Microsoft, aby uzyskać dalsze wskazówki.

Poniżej znajdują się szczegółowe informacje dotyczące różnych przypadków środowiska .NET C# i użycia w skryptach języka U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Przekształcanie skalarnych wyrażeń U C# -SQL

Język wyrażeń języka U-SQL to C#. Wiele wartości skalarnych wbudowanych wyrażeń U-SQL są implementowane natywnie w celu zwiększenia wydajności, podczas gdy bardziej złożone wyrażenia mogą być wykonywane przez wywołanie do programu .NET Framework.

Platforma Spark ma własny język wyrażeń skalarnych (albo jako część DSL lub in SparkSQL) i umożliwia wywoływanie funkcji zdefiniowanych przez użytkownika w języku macierzystym.

Jeśli w języku U-SQL znajdują się wyrażenia skalarne, należy najpierw znaleźć najbardziej odpowiednie natywnie zrozumiałe wyrażenie skalarne platformy Spark, aby uzyskać największą wydajność, a następnie zamapować inne wyrażenia na funkcję zdefiniowaną przez użytkownika w języku hostingu platformy Spark.

Należy pamiętać, że platforma C# .NET i ma inną semantykę typu niż języki hostingu platformy Spark i DSL. Więcej szczegółów na temat typów różnic systemowych można znaleźć [poniżej](#transform-typed-values) .

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Przekształcanie skalarnych funkcji .NET zdefiniowanych przez użytkownika i agregatorów zdefiniowanych przez użytkownika

Język U-SQL umożliwia wywoływanie dowolnych skalarnych funkcji .NET i wywoływanie agregatorów zdefiniowanych przez użytkownika w programie .NET.

Platforma Spark oferuje również obsługę funkcji zdefiniowanych przez użytkownika i agregatorów zdefiniowanych przez użytkownika, które są zapisywane w większości języków hostingu, które mogą być wywoływane z linii DSL i SparkSQL platformy Spark.

### <a name="transform-user-defined-operators-udos"></a>Przekształcanie operatorów zdefiniowanych przez użytkownika (udo)

Język U-SQL zawiera kilka kategorii operatorów zdefiniowanych przez użytkownika (udo), takich jak wyodrębnianie, wypełniania, stopki, procesory, appliers i łączenie, które mogą być napisywane w środowisku .NET (i-do pewnego stopnia w języku Python i R).

Platforma Spark nie oferuje tego samego modelu rozszerzalności dla operatorów, ale ma równoważne możliwości dla niektórych.

Platforma Spark równoważna z ekstraktorami i wyciągami to łączniki Spark. W przypadku wielu ekstrakcji U-SQL można znaleźć odpowiedni łącznik w społeczności platformy Spark. W przypadku innych użytkowników konieczne będzie napisanie łącznika niestandardowego. Jeśli wyekstraktor języka U-SQL jest skomplikowany i korzysta z kilku bibliotek .NET, warto utworzyć łącznik w Scala, który używa międzyoperacyjnego do wywołania biblioteki .NET, która wykonuje rzeczywiste przetwarzanie danych. W takim przypadku konieczne będzie wdrożenie środowiska uruchomieniowego .NET Core do klastra Spark i upewnienie się, że przywoływane biblioteki .NET są zgodne z .NET Standard 2,0.

Inne typy udo języka U-SQL będą musiały być ponownie zapisywane przy użyciu funkcji zdefiniowanych przez użytkownika i agregatorów oraz semantycznie odpowiedniego wyrażenia Spark DLS lub SparkSQL. Na przykład procesora można zamapować na wybór różnych wywołań UDF, spakowanych jako funkcja, która pobiera ramkę danych jako argument i zwraca ramkę Dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Przekształć opcjonalne biblioteki U-SQL

Język U-SQL zawiera zestaw opcjonalnych i demonstracyjnych bibliotek oferujących funkcje [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, Avro](https://github.com/Azure/usql/tree/master/Examples/DataFormats), a także niektóre [usługi poznawcze](data-lake-analytics-u-sql-cognitive.md).

Platforma Spark oferuje odpowiednio własną integrację języków Python i R, pySpark i Spark oraz udostępnia łączniki do odczytu i zapisu danych JSON, XML i AVRO.

Jeśli musisz przekształcić skrypt odwołujący się do bibliotek usługi poznawczej, zalecamy skontaktowanie się z nami za pośrednictwem przedstawiciela konta Microsoft.

## <a name="transform-typed-values"></a>Przekształć wpisane wartości

Ponieważ system typu U-SQL jest oparty na systemie typu .NET, a platforma Spark ma własny system typów, który ma wpływ na powiązanie języka hosta, należy upewnić się, że typy, na których pracujesz, są zamknięte i dla pewnych typów, zakresy typów, precyzja i/lub skala mogą być nieco inne. Ponadto język U-SQL i platforma Spark traktują `null` wartości inaczej.

### <a name="data-types"></a>Typy danych

Poniższa tabela zawiera równoważne typy w Spark, Scala i PySpark dla danego typu U-SQL.

| U-SQL | platforma Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Aby uzyskać więcej informacji, zobacz:

- [org. Apache. Spark. SQL. Types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Typy Spark SQL i dataframes](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala — typy wartości](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. SQL. Types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Traktowanie wartości NULL

W platformie Spark, typy na domyślne zezwalają na wartości NULL w języku U-SQL, jawnie Oznacz skalarną, niebędącą obiektem jako wartość null. Chociaż platforma Spark umożliwia zdefiniowanie kolumny jako niedopuszczające wartości null, nie będzie wymuszać ograniczenia i [może prowadzić do nieprawidłowego wyniku](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

W platformie Spark wartość NULL wskazuje, że wartość jest nieznana. Wartość NULL platformy Spark różni się od żadnej wartości, łącznie z tą samą. Porównania między dwoma wartościami NULL platformy Spark lub między wartością NULL a dowolną inną wartością zwraca nieznane, ponieważ wartość każdego z wartości NULL jest nieznana.  

To zachowanie różni się od języka U-SQL, który C# jest zgodny z semantyką, w której `null` różni się od żadnej wartości, ale jest równe.  

W ten sposób SparkSQL `SELECT` instrukcji, która używa `WHERE column_name = NULL` zwraca zero wierszy, nawet jeśli w `column_name`jest wartości NULL, a w języku U-SQL zwróci wiersze, w których `column_name` jest ustawiona na `null`. Podobnie instrukcja `SELECT` platformy Spark, która używa `WHERE column_name != NULL` zwraca zero wierszy, nawet jeśli w `column_name`nie ma wartości null, a w języku U-SQL zwróci wiersze, które mają wartość różną od null. W takim przypadku, jeśli chcesz, aby semantyka sprawdzania wartości null w języku U-SQL, należy użyć odpowiednio [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) i [IsNotNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (lub jego odpowiedniku DSL).

## <a name="transform-u-sql-catalog-objects"></a>Przekształcanie obiektów wykazu U-SQL

Istotną różnicą jest to, że skrypty U-SQL mogą korzystać z obiektów wykazu, z których wiele nie ma bezpośredniego odpowiednika platformy Spark.

Platforma Spark zapewnia obsługę koncepcji magazynu metadanych usługi Hive, głównie baz danych i tabel, dzięki czemu można mapować bazy danych i schematy U-SQL do baz danych programu Hive oraz tabele języka U-SQL w tabelach platformy Spark (zobacz [przeniesienie danych przechowywanych w tabelach U-SQL](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), ale nie obsługuje widoków. funkcje o wartościach tabelowych (TVFs), procedury składowane, zestawy U-SQL, zewnętrzne źródła danych itp.

Obiekty kodu U-SQL, takie jak widoki, TVFs, procedury składowane i zestawy, można modelować za pomocą funkcji kodu i bibliotek w platformie Spark i przywoływanych przy użyciu funkcji języka hosta i mechanizmów abstrakcji procedur (na przykład przy użyciu importowania Moduły języka Python lub odwołujące się do funkcji Scala.

Jeśli do udostępniania danych i obiektów kodu między projektami i zespołami użyto wykazu U-SQL, równoważne mechanizmy udostępniania muszą być używane (na przykład Maven do udostępniania obiektów kodu).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Przekształć wyrażenia zestawu wierszy U-SQL i wyrażenia skalarne oparte na języku SQL

Język podstawowy języka U-SQL jest przekształcany w zestawy wierszy i jest oparty na SQL. Poniżej znajduje się niepełna lista najpopularniejszych wyrażeń zestawu wierszy oferowanych w języku U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ agregaty +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` wyrażeń
- `CROSS`/`OUTER` wyrażenia `APPLY`
- `PIVOT`/`UNPIVOT` wyrażeń
- Konstruktor zestawu wierszy `VALUES`

- Ustaw wyrażenia `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Ponadto język U-SQL zawiera różne wyrażenia skalarne oparte na języku SQL, takie jak

- wyrażenia okna `OVER`
- różne wbudowane agregatory i funkcje klasyfikacji (`SUM`, `FIRST` itp.)
- Niektóre z najbardziej znanych wyrażeń skalarnych SQL: `CASE`, `LIKE`, (`NOT`) `IN`, `AND`, `OR` itd.

Platforma Spark oferuje równoważne wyrażenia w postaci DSL i SparkSQL w większości tych wyrażeń. Niektóre wyrażenia nie są obsługiwane natywnie w platformie Spark, muszą być ponownie napisywane przy użyciu kombinacji natywnych wyrażeń platformy Spark i semantycznie równoważnych wzorców. Na przykład `OUTER UNION` będą musiały zostać przetłumaczone na równoważną kombinację projekcji i Unii.

Z powodu różnej obsługi wartości NULL, sprzężenie U-SQL będzie zawsze zgodne z wierszem, jeśli obie kolumny, które są porównywane, zawierają wartość NULL, podczas gdy sprzężenie w platformie Spark nie są zgodne z takimi kolumnami, chyba że zostaną dodane jawne sprawdzenia wartości null.

## <a name="transform-other-u-sql-concepts"></a>Przekształć inne koncepcje U-SQL

Język U-SQL oferuje również różne inne funkcje i koncepcje, takie jak zapytania federacyjne dotyczące SQL Server baz danych, parametrów, skalarnych i wyrażeń lambda, zmiennych systemowych, `OPTION` wskazówek.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Zapytania federacyjne dotyczące baz danych SQL Server/tabel zewnętrznych

Język U-SQL udostępnia źródła danych i tabele zewnętrzne, a także bezpośrednie zapytania względem Azure SQL Database. Chociaż platforma Spark nie oferuje tych samych abstrakcyjnych obiektów, udostępnia [Łącznik Spark dla Azure SQL Database](../sql-database/sql-database-spark-connector.md) , który może służyć do wykonywania zapytań dotyczących baz danych SQL.

### <a name="u-sql-parameters-and-variables"></a>Parametry U-SQL i zmienne

Parametry i zmienne użytkownika mają równoważne koncepcje w usłudze Spark i ich języki hostingu.

Na przykład w Scala można zdefiniować zmienną za pomocą słowa kluczowego `var`:

```
var x = 2 * 3;
println(x)
```

Zmienne systemowe U-SQL (zmienne zaczynające się od `@@`) można podzielić na dwie kategorie:

- Settable, zmienne systemowe, które można ustawić na określone wartości mające wpływ na zachowanie skryptów
- Zmienne systemowe informacyjne, które zgłaszają informacje o poziomie systemu i zadania

Większość zmiennych systemowych settable nie ma bezpośredniego odpowiednika w platformie Spark. Niektóre zmienne systemu informacyjnego można modelować przez przekazanie informacji jako argumentów podczas wykonywania zadania, inne mogą mieć równoważną funkcję w języku hostingu platformy Spark.

### <a name="u-sql-hints"></a>Wskazówki U-SQL

Język U-SQL oferuje kilka składni sposobów udostępniania wskazówek do programu do optymalizacji zapytań i aparatu wykonywania:  

- Ustawianie zmiennej systemowej U-SQL
- klauzula `OPTION` skojarzona z wyrażeniem zestawu wierszy w celu dostarczenia wskazówki dotyczącej danych lub planu
- Wskazówka sprzężenia w składni wyrażenia sprzężenia (na przykład `BROADCASTLEFT`)

Optymalizator zapytań oparty na kosztach platformy Spark ma własne możliwości zapewniania wskazówek i dostrajania wydajności zapytań. Zapoznaj się z odpowiednią dokumentacją.

## <a name="next-steps"></a>Następne kroki

- [Omówienie formatów danych platformy Spark dla deweloperów U-SQL](understand-spark-data-formats.md)
- [.NET dla Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Uaktualnij rozwiązania do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Przekształcanie danych przy użyciu działania Spark w Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Przekształcanie danych przy użyciu działania programu Hive platformy Hadoop w Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co to jest Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
