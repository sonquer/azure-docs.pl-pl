---
title: Tworzenie potoku Apache Spark Machine Learning — Azure HDInsight
description: Użyj biblioteki Apache Spark Machine Learning, aby utworzyć potoki danych w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494666"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Tworzenie potoku uczenia maszynowego platformy Apache Spark

Skalowalna biblioteka Machine Learning Apache Spark (MLlib) zapewnia możliwości modelowania w środowisku rozproszonym. Pakiet Spark [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) to zestaw interfejsów API wysokiego poziomu wbudowanych w ramki danych. Te interfejsy API ułatwiają tworzenie i dostrajanie praktycznych potoków uczenia maszynowego.  Program *Spark Machine Learning* odwołuje się do tego interfejsu API opartego na ramce Dataframe MLlib, a nie starszego interfejsu API potoku opartego na RDD.

Potok uczenia maszynowego (ML) to kompletny przepływ pracy łączący wiele algorytmów uczenia maszynowego. Aby przetwarzać dane i uczyć się na nich, należy wykonać wiele czynności, co wymaga sekwencji algorytmów. Potoki definiują etapy i kolejność procesu uczenia maszynowego. W MLlib etapy potoku są reprezentowane przez określoną sekwencję PipelineStages, w których transformator i szacowania wykonują zadania.

Transformator jest algorytmem, który przekształca jedną ramkę danych z inną przy użyciu metody `transform()`. Na przykład transformator funkcji może odczytać jedną kolumnę ramki Data, mapując ją do innej kolumny i wyprowadza nową ramkę danych z dołączoną do niej kolumną.

Szacowania jest abstrakcją algorytmów uczenia i jest odpowiedzialny za Instalowanie i uczenie się zestawu danych w celu utworzenia transformatora. Szacowania implementuje metodę o nazwie `fit()`, która akceptuje ramkę danych i tworzy ramkę danych, która jest transformatorem.

Każde wystąpienie bezstanowe transformatora lub szacowania ma własny unikatowy identyfikator, który jest używany podczas określania parametrów. Oba te parametry używają jednolitego interfejsu API.

## <a name="pipeline-example"></a>Przykład potoku

Aby zademonstrować praktyczne użycie potoku z tablicą, w tym przykładzie użyto pliku danych `HVAC.csv` przykładowego, który jest wstępnie załadowany do domyślnego magazynu dla klastra usługi HDInsight, Azure Storage lub Data Lake Storage. Aby wyświetlić zawartość pliku, przejdź do katalogu `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` zawiera zestaw godzin zarówno w przypadku, jak i rzeczywiste temperatury dla systemów HVAC (*ogrzewania, wentylacji i*klimatyzacji) w różnych budynkach. Celem jest uczenie modelu danych i utworzenie prognozy dla danego budynku.

Następujący kod:

1. Definiuje `LabeledDocument`, w którym są przechowywane `BuildingID`, `SystemInfo` (identyfikator i wiek systemu) oraz `label` (1,0, jeśli kompilacja jest zbyt gorąca, 0,0 w przeciwnym razie).
2. Tworzy niestandardową funkcję parsera `parseDocument`, która pobiera wiersz (wiersz) danych i określa, czy kompilacja jest "gorąca", porównując docelową temperaturę z rzeczywistą temperaturą.
3. Stosuje parser podczas wyodrębniania danych źródłowych.
4. Tworzy dane szkoleniowe.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Ten przykładowy potok ma trzy etapy: `Tokenizer` i `HashingTF` (obie transformatory) i `Logistic Regression` (szacowania).  Wyodrębnione i przeanalizowane dane w `training` Dataframe przechodzą przez potok po wywołaniu `pipeline.fit(training)`.

1. Pierwszy etap, `Tokenizer`, dzieli kolumnę wejściową `SystemInfo` (składającą się z identyfikatora systemowego i wartości wieku) do `words`j kolumny wyjściowej. Ta nowa kolumna `words` zostanie dodana do ramki Dataframe. 
2. Drugi etap `HashingTF`, konwertuje nową kolumnę `words` na wektory funkcji. Ta nowa kolumna `features` zostanie dodana do ramki Dataframe. Te pierwsze dwa etapy to Transformatory. 
3. Trzeci etap, `LogisticRegression`, to szacowania, a więc potok wywołuje metodę `LogisticRegression.fit()` w celu utworzenia `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Aby wyświetlić nowe `words` i `features` kolumny dodane przez Transformatory `Tokenizer` i `HashingTF` oraz próbkę `LogisticRegression` szacowania, uruchom metodę `PipelineModel.transform()` w pierwotnej ramce Dataframe. W kodzie produkcyjnym następnym krokiem jest przekazanie testowej ramki danych w celu zweryfikowania szkolenia.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Obiekt `model` może być teraz używany do prognozowania. Aby uzyskać pełny przykład tej aplikacji do uczenia maszynowego i instrukcje krok po kroku dotyczące ich uruchamiania, zobacz [Tworzenie aplikacji Apache Spark Machine Learning w usłudze Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Zobacz też

* [Nauka danych przy użyciu Scala i Apache Spark na platformie Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
