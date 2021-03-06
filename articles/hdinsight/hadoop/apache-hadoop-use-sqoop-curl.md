---
title: Eksportowanie danych przy użyciu programu Apache Sqoop w usłudze Azure HDInsight
description: Dowiedz się, jak zdalnie przesyłać zadania Apache Sqoop do usługi Azure HDInsight przy użyciu zwinięcia.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767590"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Uruchamianie zadań Apache Sqoop w usłudze HDInsight z użyciem zapełniania

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać zawieszania do uruchamiania zadań Apache Sqoop w klastrze Apache Hadoop w usłudze HDInsight. W tym artykule przedstawiono sposób eksportowania danych z usługi Azure Storage i importowania ich do bazy danych SQL Server przy użyciu programu zwinięcie. Ten artykuł jest kontynuacją [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

Zwinięcie służy do zademonstrowania, w jaki sposób można korzystać z usługi HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania, monitorowania i pobierania wyników zadań Sqoop. Działa to przy użyciu interfejsu API REST WebHCat (dawniej znanego jako Templeton) dostarczonego przez klaster usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

* Klient do wykonywania zapytań dotyczących Azure SQL Database. Rozważ użycie [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) lub [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Zwinięcie](https://curl.haxx.se/). Zwinięcie to narzędzie służące do transferowania danych z lub do klastra usługi HDInsight.

* [JQ](https://stedolan.github.io/jq/). Narzędzie JQ służy do przetwarzania danych JSON zwracanych z żądań REST.

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Przesyłanie zadań Apache Sqoop przy użyciu programu zwinięcie

Aby wyeksportować dane za pomocą zadań Apache Sqoop z usługi Azure Storage do SQL Server, użyj go.

> [!NOTE]  
> Używając programu Curl lub innego połączenia REST z usługą WebHCat, należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy również użyć nazwy klastra jako części identyfikatora URI stosowanego przy wysyłaniu żądań do serwera.

Dla poleceń w tej sekcji Zastąp `USERNAME` użytkownikiem w celu uwierzytelnienia w klastrze i Zastąp `PASSWORD` hasłem dla konta użytkownika. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS (HTTP Secure), aby mieć pewność, że poświadczenia są bezpiecznie wysyłane do serwera.

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne poniżej. Ten przykład jest oparty na środowisku systemu Windows, należy poprawić w miarę potrzeb w danym środowisku.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Użytkownik powinien otrzymywać odpowiedź podobną do następującej:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Aby przesłać zadanie Sqoop, użyj następującego elementu:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    W tym poleceniu są używane następujące parametry:

   * **-d** — ponieważ `-G` nie jest używany, żądanie jest domyślnie ustawiane jako metoda post. `-d` określa wartości danych, które są wysyłane wraz z żądaniem.

       * **User.Name** — użytkownik, który uruchamia polecenie.

       * **polecenie** — polecenie Sqoop do wykonania.

       * **statusdir** — katalog, w którym zostanie zapisany stan dla tego zadania.

     To polecenie zwróci identyfikator zadania, którego można użyć do sprawdzenia stanu zadania.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp `JOBID` wartością zwróconą w poprzednim kroku. Na przykład jeśli wartość zwracana była `{"id":"job_1415651640909_0026"}`, `JOBID` być `job_1415651640909_0026`. W razie konieczności Popraw lokalizację `jq`.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Jeśli zadanie zostało zakończone, stan zostanie **zakończony pomyślnie**.

   > [!NOTE]  
   > To żądanie zwinięcie zwraca dokument JavaScript Object Notation (JSON) z informacjami o zadaniu. JQ służy do pobierania tylko wartości stanu.

1. Po zmianie stanu zadania na **powodzenie**można pobrać wyniki zadania z usługi Azure Blob Storage. Parametr `statusdir` przeprowadzony z zapytaniem zawiera lokalizację pliku wyjściowego. w tym przypadku `wasb:///example/data/sqoop/curl`. Ten adres przechowuje dane wyjściowe zadania w katalogu `example/data/sqoop/curl` w domyślnym kontenerze magazynu używanym przez klaster usługi HDInsight.

    Możesz użyć Azure Portal, aby uzyskać dostęp do obiektów BLOB stderr i stdout.

1. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następujących zapytań z klienta SQL, aby wyświetlić wyeksportowane dane:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — za pomocą usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do Microsoft SQL Server lub Azure SQL Database nie obsługuje obecnie operacji wstawiania zbiorczego.
* Przetwarzanie wsadowe — za pomocą usługi HDInsight opartej na systemie Linux, gdy jest używany przełącznik `-batch` podczas wykonywania operacji INSERT, Sqoop będzie wykonywał wiele wstawek zamiast wsadowych operacje wstawiania.

## <a name="summary"></a>Podsumowanie

Jak pokazano w tym dokumencie, można użyć pierwotnego żądania HTTP do uruchamiania, monitorowania i wyświetlania wyników zadań Sqoop w klastrze usługi HDInsight.

Aby uzyskać więcej informacji o interfejsie REST używanym w tym artykule, zobacz <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Przewodnik interfejsu API REST usługi Apache Sqoop</a>.

## <a name="next-steps"></a>Następne kroki

[Korzystanie z usługi Apache Sqoop z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-sqoop.md)

Inne artykuły dotyczące usługi HDInsight obejmujące zwinięcie:

* [Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API REST platformy Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Uruchamianie zapytań Apache Hive z Apache Hadoop w usłudze HDInsight przy użyciu usługi REST](apache-hadoop-use-hive-curl.md)
* [Uruchamianie zadań MapReduce za pomocą Apache Hadoop w usłudze HDInsight przy użyciu usługi REST](apache-hadoop-use-mapreduce-curl.md)