---
title: Przesyłanie strumieniowe Spark w usłudze Azure HDInsight
description: Jak używać aplikacji przesyłania strumieniowego Apache Spark w klastrach usługi HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406263"
---
# <a name="overview-of-apache-spark-streaming"></a>Omówienie Apache Spark przesyłania strumieniowego

[Apache Spark](https://spark.apache.org/) Funkcja przesyłania strumieniowego zapewnia przetwarzanie strumieni danych w klastrach usługi HDInsight Spark z gwarancją, że każde zdarzenie wejściowe jest przetwarzane dokładnie jeden raz, nawet jeśli wystąpi awaria węzła. Strumień Spark to długotrwałe zadanie, które odbiera dane wejściowe z wielu różnych źródeł, w tym platformy Azure Event Hubs, IoT Hub platformy Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), raw gniazda TCP lub z monitorowania Apache Hadoop systemów plików [przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) . W przeciwieństwie do samego procesu sterowanego zdarzeniami, Strumień Spark przetwarza dane wejściowe w oknach czasu, takich jak 2-sekundowy wycink, a następnie przekształca każdą partię danych przy użyciu map, zmniejszania, sprzęgania i wyodrębniania operacji. Strumień Spark zapisuje następnie przekształcone dane do systemów plików, baz danych, pulpitów nawigacyjnych i konsoli programu.

![Przetwarzanie strumieni z użyciem usługi HDInsight i usługi przesyłania strumieniowego Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Aplikacje strumieniowe Spark muszą czekać część sekund, aby zebrać każdą *mikropartię* zdarzeń przed wysłaniem tej partii na potrzeby przetwarzania. Z kolei aplikacja oparta na zdarzeniach natychmiast przetwarza każde zdarzenie. Opóźnienie przesyłania strumieniowego Spark jest zwykle w ciągu kilku sekund. Zalety metody Micro-Batch to wydajniejsze przetwarzanie danych i prostsze obliczenia zagregowane.

## <a name="introducing-the-dstream"></a>Wprowadzenie do DStream

Przesyłanie strumieniowe Spark reprezentuje ciągły strumień danych przychodzących przy użyciu *strumienia zdyskretyzowanej* o nazwie DStream. DStream można utworzyć ze źródeł danych wejściowych, takich jak Event Hubs lub Kafka, lub stosując przekształcenia na innym DStream.

DStream zapewnia warstwę abstrakcji na podstawie nieprzetworzonych danych zdarzeń.

Zacznij od pojedynczego zdarzenia, zapoznaj się z odczytem temperatury ze połączonego termostatu. Po odebraniu tego zdarzenia w aplikacji przesyłania strumieniowego Spark zdarzenie jest przechowywane w niezawodny sposób, w którym jest replikowana na wielu węzłach. Odporność na uszkodzenia gwarantuje, że awaria jednego węzła nie spowoduje utraty zdarzenia. Platforma Spark używa struktury danych, która dystrybuuje dane między wieloma węzłami w klastrze, gdzie każdy węzeł zwykle utrzymuje własne dane w pamięci w celu uzyskania najlepszej wydajności. Ta struktura danych jest nazywana *rozproszonym zestawem danych* (RDD).

Każdy RDD reprezentuje zdarzenia zbierane przez zdefiniowany przez użytkownika czas o nazwie *Interwał partii*. Po upływie każdego interwału partii tworzony jest nowy RDD, który zawiera wszystkie dane z tego interwału. Ciągły zestaw odporne są gromadzone w DStream. Jeśli na przykład interwał wsadowy jest dłuższy niż jedna sekunda, DStream emituje wsadowe co sekundę zawierające jedną RDD, która zawiera wszystkie dane, które zostały odebrane w ciągu sekundy. Podczas przetwarzania DStream, zdarzenie temperatury pojawia się w jednej z tych partii. Aplikacja Spark Streaming przetwarza partie zawierające zdarzenia i ostatecznie działa na danych przechowywanych w każdym RDD.

![Przykład DStream ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikacji do przesyłania strumieniowego Spark

Aplikacja do przesyłania strumieniowego Spark to długotrwała aplikacja, która odbiera dane ze źródeł pozyskiwania, stosuje przekształcenia do przetwarzania danych, a następnie wypchnięcie danych do jednego lub większej liczby miejsc docelowych. Struktura aplikacji przesyłania strumieniowego Spark ma część statyczną i część dynamiczną. Część statyczna określa, skąd pochodzą dane, jakie przetwarzanie ma być wykonywane na danych, oraz miejsce, w którym powinny się znaleźć wyniki. W części dynamicznej jest uruchomiona aplikacja przez czas oczekiwania na sygnał zatrzymania.

Na przykład następująca prosta aplikacja odbiera wiersz tekstu za pośrednictwem gniazda TCP i zlicza liczbę wyświetlanych plików.

### <a name="define-the-application"></a>Definiowanie aplikacji

Definicja logiki aplikacji zawiera cztery kroki:

1. Utwórz element StreamingContext.
2. Utwórz element DStream z StreamingContext.
3. Zastosuj przekształcenia do DStream.
4. Wyprowadza wyniki.

Ta definicja jest statyczna i żadne dane nie są przetwarzane do momentu uruchomienia aplikacji.

#### <a name="create-a-streamingcontext"></a>Utwórz StreamingContext

Utwórz StreamingContext z SparkContext, który wskazuje na klaster. Podczas tworzenia StreamingContext należy określić rozmiar partii w sekundach, na przykład:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Utwórz DStream

Za pomocą wystąpienia StreamingContext Utwórz DStream wejściowy dla źródła danych wejściowych. W takim przypadku aplikacja jest oglądana pod kątem wyglądu nowych plików w domyślnym magazynie dołączonym do klastra usługi HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Zastosuj przekształcenia

Przetwarzanie przetwarzania można zaimplementować, stosując przekształcenia na DStream. Ta aplikacja odbiera jeden wiersz tekstu jednocześnie z pliku, dzieli każdy wiersz na wyrazy, a następnie używa wzorca ze zmniejszeniem mapy do zliczania, ile razy pojawia się każdy wyraz.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Wyniki wyjściowe

Wypchnij wyniki transformacji do systemów docelowych, stosując operacje wyjściowe. W takim przypadku wynik każdego przebiegu przez obliczenie jest drukowany w danych wyjściowych konsoli.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację przesyłania strumieniowego i uruchom do momentu otrzymania sygnału zakończenia.

```
ssc.start()
ssc.awaitTermination()
```

Aby uzyskać szczegółowe informacje na temat interfejsu API usługi Spark, a także źródeł zdarzeń, przekształceń i operacji wyjściowych, które obsługuje, zobacz [Apache Spark Przewodnik programowania przesyłania strumieniowego](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Następująca przykładowa aplikacja jest samodzielna, więc można ją uruchomić wewnątrz [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). W tym przykładzie tworzony jest makietowe źródło danych w klasie DummySource, która wyprowadza wartość licznika i bieżący czas w milisekundach co pięć sekund. Nowy obiekt StreamingContext ma interwał partii wynoszący 30 sekund. Za każdym razem, gdy tworzona jest partia, aplikacja przesyłania strumieniowego analizuje wygenerowane RDD, konwertuje RDD na ramkę danych Spark i tworzy tabelę tymczasową dla ramki danych.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Odczekaj około 30 sekund od rozpoczęcia powyższej aplikacji.  Następnie można wykonać zapytanie o ramy czasowe w celu wyświetlenia bieżącego zestawu wartości obecnych w partii, na przykład za pomocą tego zapytania SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

Wynikowe dane wyjściowe wyglądają następująco:

| wartość | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Istnieje sześć wartości, ponieważ DummySource tworzy wartość co 5 sekund, a aplikacja emituje partię co 30 sekund.

## <a name="sliding-windows"></a>Okna przewijania

Aby wykonać obliczenia agregowane na DStream w pewnym okresie czasu, na przykład aby uzyskać średnią temperaturę w ciągu ostatnich dwóch sekund, można użyć operacji *przewijania okna* dołączonego do przesyłania strumieniowego Spark. Ruchome okno ma czas trwania (długość okna) i interwał, w jakim zawartość okna jest szacowana (interwał slajdu).

Przesuwane okna mogą się nakładać, na przykład można zdefiniować okno o długości co najmniej dwóch sekund, które slajdy są co sekundę. Oznacza to, że za każdym razem, gdy wykonywane jest obliczanie agregacji, okno będzie zawierać dane z ostatniej sekundy poprzedniego okna oraz nowe dane w ciągu następnych sekund.

![Przykładowe okno początkowe ze zdarzeniami temperatury](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Przykładowe okno ze zdarzeniami temperatury po przesuwaniu](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Poniższy przykład aktualizuje kod, który używa DummySource, do zbierania partii do okna z jednominutowym czasem trwania i slajdem jednominutowym.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Po pierwszej minucie istnieją 12 wpisów — sześć wpisów z każdej z dwóch partii zebranych w oknie.

| wartość | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Funkcje okna przesuwania dostępne w interfejsie API przesyłania strumieniowego Spark obejmują okna, countByWindow, reduceByWindow i countByValueAndWindow. Aby uzyskać szczegółowe informacje o tych funkcjach, zobacz [przekształcenia w DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

W celu zapewnienia odporności i odporności na uszkodzenia, funkcja przesyłania strumieniowego Spark polega na tworzeniu punktów kontrolnych, co pozwala na dalsze nieprzerwanie przetwarzania strumieni, nawet w przypadku awarii węzła. W usłudze HDInsight platforma Spark tworzy punkty kontrolne do trwałego magazynu (Azure Storage lub Data Lake Storage). Te punkty kontrolne przechowują metadane dotyczące aplikacji przesyłania strumieniowego, takie jak konfiguracja, operacje zdefiniowane przez aplikację i wszystkie partie, które zostały dodane do kolejki, ale nie zostały jeszcze przetworzone. W niektórych przypadkach punkty kontrolne również obejmują zapisywanie danych w odporne, aby szybciej odbudować stan danych z tego, co jest obecne w odporne zarządzanym przez platformę Spark.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego Spark

Zazwyczaj tworzysz aplikację do przesyłania strumieniowego Spark lokalnie do pliku JAR, a następnie wdróż ją na platformie Spark w usłudze HDInsight, kopiując plik JAR do magazynu domyślnego dołączonego do klastra usługi HDInsight. Aplikację można uruchomić za pomocą interfejsów API REST usługi LIVY dostępnych w klastrze przy użyciu operacji POST. Treść wpisu zawiera dokument JSON, który zawiera ścieżkę do pliku JAR, nazwę klasy, której Metoda Main definiuje i uruchamia aplikację przesyłania strumieniowego oraz opcjonalnie wymagania dotyczące zasobów zadania (takie jak liczba modułów wykonujących, pamięć i rdzenie). i wszystkie ustawienia konfiguracji wymagane przez kod aplikacji.

![Wdrażanie aplikacji do przesyłania strumieniowego Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stan wszystkich aplikacji można również sprawdzić za pomocą żądania GET w odniesieniu do punktu końcowego usługi LIVY. Na koniec możesz przerwać działającą aplikację, wydając żądanie DELETE względem punktu końcowego usługi LIVY. Aby uzyskać szczegółowe informacje o interfejsie API usługi LIVY, zobacz [zdalne zadania z Apache usługi Livy](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Przewodnik programowania przesyłania strumieniowego Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Zdalne uruchamianie zadań Apache Spark za pomocą platformy Apache usługi LIVY](apache-spark-livy-rest-interface.md)
