---
title: Korzystanie z MapReduce i Zwinięciea przy użyciu Apache Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, jak zdalnie uruchamiać zadania MapReduce z Apache Hadoop w usłudze HDInsight przy użyciu programu zwinięcie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: 607020f1d540e83a4d049b96b9ab9a4ebcd385f0
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157257"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Uruchamianie zadań MapReduce za pomocą Apache Hadoop w usłudze HDInsight przy użyciu usługi REST

Dowiedz się, jak używać interfejsu API REST Apache Hive WebHCat do uruchamiania zadań MapReduce na Apache Hadoop w klastrze usługi HDInsight. Zwinięcie służy do zademonstrowania, w jaki sposób można korzystać z usługi HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania zadań MapReduce.

> [!NOTE]  
> Jeśli masz już doświadczenie w korzystaniu z serwerów Hadoop opartych na systemie Linux, ale dopiero zaczynasz korzystać z usługi HDInsight, zapoznaj się z [informacjami o Apache Hadoop opartych na systemie Linux w](../hdinsight-hadoop-linux-information.md) dokumencie usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Dostępne opcje:
  * Windows PowerShell lub,
  * [Zwinięcie](https://curl.haxx.se/) z [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Uruchamianie zadania MapReduce

> [!NOTE]  
> W przypadku korzystania z zwinięcie lub innej komunikacji REST z WebHCat należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy użyć nazwy klastra jako części identyfikatora URI, który jest używany do wysyłania żądań do serwera.
>
> Interfejs API REST jest zabezpieczony przy użyciu [podstawowego uwierzytelniania dostępu](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS, aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.

### <a name="curl"></a>Narzędzie Curl

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne poniżej. Ten przykład jest oparty na środowisku systemu Windows, należy poprawić w miarę potrzeb w danym środowisku.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    W tym poleceniu są używane następujące parametry:

   * **-u**: wskazuje nazwę użytkownika i hasło używane do uwierzytelniania żądania
   * **-G**: wskazuje, że ta operacja jest żądaniem Get

   Początek identyfikatora URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, jest taki sam dla wszystkich żądań.

    Otrzymasz odpowiedź podobną do następującej:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia. W razie potrzeby zmodyfikuj ścieżkę do **JQ** .

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Koniec identyfikatora URI (/MapReduce/jar) informuje WebHCat, że to żądanie uruchamia zadanie MapReduce z klasy w pliku JAR. W tym poleceniu są używane następujące parametry:

   * **-d**: `-G` nie jest używany, dlatego żądanie jest domyślnie ustawiane jako metoda post. `-d` określa wartości danych, które są wysyłane wraz z żądaniem.
     * **User.Name**: użytkownik, który uruchamia polecenie
     * **jar**: Lokalizacja pliku JAR, który zawiera klasę do uruchomienia
     * **Klasa**: Klasa, która zawiera logikę MapReduce
     * **ARG**: argumenty, które mają zostać przekazane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane na potrzeby danych wyjściowych

    To polecenie powinno zwrócić identyfikator zadania, którego można użyć do sprawdzenia stanu zadania:

       job_1415651640909_0026

1. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp wartość `JOBID` wartością **rzeczywistą** zwróconą w poprzednim kroku. W razie konieczności Popraw lokalizację **JQ** .

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne poniżej. Zastąp `CLUSTERNAME` rzeczywistą nazwą klastra. Wykonaj polecenie i wprowadź hasło logowania klastra po wyświetleniu monitu.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy można nawiązać połączenie z klastrem usługi HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Otrzymasz odpowiedź podobną do następującej:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Koniec identyfikatora URI (/MapReduce/jar) informuje WebHCat, że to żądanie uruchamia zadanie MapReduce z klasy w pliku JAR. W tym poleceniu są używane następujące parametry:

    * **User.Name**: użytkownik, który uruchamia polecenie
    * **jar**: Lokalizacja pliku JAR, który zawiera klasę do uruchomienia
    * **Klasa**: Klasa, która zawiera logikę MapReduce
    * **ARG**: argumenty, które mają zostać przekazane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane na potrzeby danych wyjściowych

   To polecenie powinno zwrócić identyfikator zadania, którego można użyć do sprawdzenia stanu zadania:

       job_1415651640909_0026

1. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>Obie metody

1. Jeśli zadanie zostało zakończone, zwracany jest stan `SUCCEEDED`.

1. Gdy stan zadania został zmieniony na `SUCCEEDED`, wyniki zadania można pobrać z usługi Azure Blob Storage. Parametr `statusdir`, który jest przesyłany z zapytaniem, zawiera lokalizację pliku wyjściowego. W tym przykładzie lokalizacja jest `/example/curl`. Ten adres przechowuje dane wyjściowe zadania w domyślnym magazynie klastrów w `/example/curl`.

Możesz wyświetlić i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat pracy z obiektami BLOB w interfejsie wiersza polecenia platformy Azure, zobacz artykuł [Używanie interfejsu wiersza polecenia platformy Azure z dokumentem usługi Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać więcej informacji o interfejsie REST używanym w tym artykule, zobacz [odwołanie WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
