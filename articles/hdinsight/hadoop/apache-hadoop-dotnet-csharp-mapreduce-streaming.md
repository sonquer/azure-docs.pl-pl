---
title: Używanie C# z usługą MapReduce na platformie Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, C# jak używać programu do tworzenia rozwiązań MapReduce z Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561800"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Używanie C# z usługą MapReduce streaming na Apache Hadoop w usłudze HDInsight

Dowiedz się, C# jak za pomocą programu utworzyć rozwiązanie MapReduce w usłudze HDInsight.

Apache Hadoop Streaming to narzędzie, które umożliwia uruchamianie zadań MapReduce przy użyciu skryptu lub pliku wykonywalnego. W tym przykładzie platforma .NET służy do implementowania mapowania i redukcji dla rozwiązania zliczania wyrazów.

## <a name="net-on-hdinsight"></a>Platforma .NET w usłudze HDInsight

Klastry usługi HDInsight używają systemu [mono (https://mono-project.com)](https://mono-project.com) do uruchamiania aplikacji .NET. W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji. Aby uzyskać więcej informacji na temat wersji programu mono zawartej w usłudze HDInsight, zobacz [składniki Apache Hadoop dostępne dla różnych wersji usługi HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa przesyłanie strumieniowe Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następujący:

1. Usługa Hadoop przekazuje dane do funkcji mapowania (*mapera. exe* w tym przykładzie) na stdin.
2. Maper przetwarza dane i emituje rozdzielaną tabulatorami pary klucz/wartość do STDOUT.
3. Dane wyjściowe są odczytywane przez platformę Hadoop, a następnie przekazywane do usługi*w ramach* tego przykładu.
4. W ramach tego ograniczenia są odczytywane pary klucz/wartość rozdzielane znakami tabulacji, przetwarzanie danych, a następnie emitowanie wyniku jako pary klucz/wartość z ograniczeniami tabulacji.
5. Dane wyjściowe są odczytywane przez platformę Hadoop i zapisywane w katalogu wyjściowym.

Aby uzyskać więcej informacji na temat przesyłania strumieniowego, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Znajomość pisania i kompilowania C# kodu, który jest przeznaczony dla .NET Framework 4,5.

* Sposób przekazywania plików exe do klastra. Kroki opisane w tym dokumencie wykorzystują Data Lake Tools for Visual Studio do przekazywania plików do magazynu podstawowego klastra.

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](https://docs.microsoft.com/powershell/azure/overview).

* Klient SSH (opcjonalnie). Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. `wasb://` w usłudze Azure Storage `abfs://` dla Azure Data Lake Storage Gen2 lub `adl://` dla Azure Data Lake Storage Gen1. Jeśli na potrzeby usługi Azure Storage lub Data Lake Storage Gen2 jest włączony bezpieczny transfer, identyfikator URI zostałby `wasbs://` lub `abfss://`odpowiednio zapoznaj się również z [bezpiecznym transferem](../../storage/common/storage-require-secure-transfer.md).


## <a name="create-the-mapper"></a>Tworzenie mapowania

W programie Visual Studio Utwórz nową aplikację konsolową .NET Framework o nazwie *Maper*. Użyj następującego kodu dla aplikacji:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć plik */bin/debug/Mapper.exe* w katalogu projektu.

## <a name="create-the-reducer"></a>Utwórz ograniczenie

W programie Visual Studio Utwórz nową aplikację konsolową .NET Framework o nazwie *zmniejszającej*. Użyj następującego kodu dla aplikacji:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć plik */bin/debug/Reducer.exe* w katalogu projektu.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

Następnie musisz przekazać do magazynu usługi HDInsight aplikacje do *odnajdywania* i *zmniejszania* .

1. W programie Visual Studio wybierz pozycję **wyświetl** > **Eksplorator serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure...** i Ukończ proces logowania.

1. Rozwiń klaster usługi HDInsight, do którego chcesz wdrożyć tę aplikację. Zostanie wyświetlona pozycja z tekstem **(domyślne konto magazynu)** .

   ![Konto magazynu, klaster usługi HDInsight, Eksplorator serwera, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Jeśli wpis **(domyślne konto magazynu)** można rozszerzyć, używasz **konta usługi Azure Storage** jako magazynu domyślnego dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie **(kontener domyślny)** .

   * Jeśli nie można rozszerzyć wpisu **(domyślnego konta magazynu)** , używasz **Azure Data Lake Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, kliknij dwukrotnie wpis **(domyślne konto magazynu)** .

1. Aby przekazać pliki. exe, należy użyć jednej z następujących metod:

    * Jeśli używasz **konta usługi Azure Storage**, wybierz ikonę **Przekaż obiekt BLOB** .

        ![Ikona przekazywania do usługi HDInsight dla programu mapowania, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        W oknie dialogowym **Przekaż nowy plik** w obszarze **Nazwa pliku**wybierz pozycję **Przeglądaj**. W oknie dialogowym **przekazywanie obiektu BLOB** przejdź do folderu *bin\Debug* dla projektu *mapowania* , a następnie wybierz plik *mapera. exe* . Na koniec wybierz pozycję **Otwórz** , a następnie kliknij **przycisk OK** , aby ukończyć przekazywanie.

    * W przypadku **Azure Data Lake Storage**kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie **Przekaż**. Na koniec wybierz plik *Maper. exe* , a następnie wybierz pozycję **Otwórz**.

    Po zakończeniu przekazywania pliku *mapowania. exe* należy powtórzyć proces przekazywania dla plików. *exe* .

## <a name="run-a-job-using-an-ssh-session"></a>Uruchamianie zadania: używanie sesji SSH

Poniższa procedura opisuje sposób uruchamiania zadania MapReduce przy użyciu sesji SSH:

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj jednego z następujących poleceń, aby uruchomić zadanie MapReduce:

   * Jeśli domyślnym magazynem jest **usługa Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Jeśli domyślnym magazynem jest **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Jeśli domyślnym magazynem jest **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Na poniższej liście opisano, co każdy parametr i opcja reprezentuje:

   * *Hadoop-Streaming. jar*: Określa plik JAR, który zawiera funkcje MapReduce przesyłania strumieniowego.
   * `-files`: Określa pliki *mapera. exe* i *redukuje. exe* dla tego zadania. Deklaracja protokołu `wasbs:///`, `adl:///`lub `abfs:///` przed każdym plikiem jest ścieżką do katalogu głównego domyślnego magazynu klastra.
   * `-mapper`: Określa plik, który implementuje mapowanie.
   * `-reducer`: Określa plik, który implementuje zmniejszenie.
   * `-input`: określa dane wejściowe.
   * `-output`: określa katalog wyjściowy.

3. Po zakończeniu zadania MapReduce Użyj następującego polecenia, aby wyświetlić wyniki:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Następujący tekst to przykład danych zwróconych przez to polecenie:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Uruchamianie zadania: korzystanie z programu PowerShell

Użyj poniższego skryptu programu PowerShell, aby uruchomić zadanie MapReduce i pobrać wyniki.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt poprosi o nazwę i hasło konta logowania do klastra wraz z nazwą klastra usługi HDInsight. Po zakończeniu zadania dane wyjściowe zostaną pobrane do pliku o nazwie *Output. txt*. Poniższy tekst to przykład danych w pliku `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z usługi MapReduce z usługą HDInsight, zobacz [Używanie MapReduce w Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).

Aby uzyskać informacje na C# temat korzystania z platformy Hive i świni, zobacz [Używanie funkcji zdefiniowanej przez C# użytkownika z usługami Apache Hive i Apache świni](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Aby uzyskać informacje na C# temat korzystania z funkcji burzy w usłudze HDInsight, zobacz [ C# tworzenie topologii dla Apache Storm w usłudze HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
