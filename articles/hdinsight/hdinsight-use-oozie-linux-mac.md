---
title: Korzystanie z przepływów pracy Oozie usługi Hadoop w usłudze Azure HDInsight opartej na systemie Linux
description: Korzystanie z usługi Hadoop Oozie w usłudze HDInsight opartej na systemie Linux. Dowiedz się, jak zdefiniować przepływ pracy Oozie i przesłać zadanie Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744914"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Korzystanie z usługi Apache Oozie z usługą Apache Hadoop do definiowania i uruchamiania przepływu pracy w usłudze Azure HDInsight opartej na systemie Linux

Dowiedz się, jak używać platformy Apache Oozie z usługą Apache Hadoop w usłudze Azure HDInsight. Oozie to przepływ pracy i system koordynacji, który zarządza zadaniami usługi Hadoop. Usługa Oozie jest zintegrowana z stosem usługi Hadoop i obsługuje następujące zadania:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Można również użyć Oozie do planowania zadań specyficznych dla systemu, takich jak programy Java lub skrypty powłoki.

> [!NOTE]  
> Kolejną opcją zdefiniowania przepływów pracy za pomocą usługi HDInsight jest użycie Azure Data Factory. Aby dowiedzieć się więcej na temat Data Factory, zobacz [Korzystanie z platformy Apache świni i Apache Hive z Data Factory][azure-data-factory-pig-hive]. Aby korzystać z Oozie w klastrach z pakiet Enterprise Security, zobacz [Uruchamianie platformy Apache Oozie w klastrach usługi HDInsight Hadoop z pakiet Enterprise Security](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi Hadoop w usłudze HDInsight**. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Klient SSH**. Zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Azure SQL Database**.  Zobacz [Tworzenie bazy danych Azure SQL Database w Azure Portal](../sql-database/sql-database-get-started.md).  W tym artykule jest stosowana baza danych o nazwie **oozietest**.

* [Schemat identyfikatora URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. `wasb://` w usłudze Azure Storage `abfs://` dla Azure Data Lake Storage Gen2 lub `adl://` dla Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI będzie `wasbs://`. Zobacz również [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Przykładowy przepływ pracy

Przepływ pracy używany w tym dokumencie zawiera dwie akcje. Akcje to definicje zadań, takich jak uruchamianie programu Hive, Sqoop, MapReduce lub innych procesów:

![Diagram przepływu pracy Oozie usługi HDInsight](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Akcja Hive uruchamia skrypt HiveQL, aby wyodrębnić rekordy z `hivesampletable` dołączonego do usługi HDInsight. Każdy wiersz danych zawiera opis odwiedzania z określonego urządzenia przenośnego. Format rekordu wygląda podobnie do następującego tekstu:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skrypt Hive używany w tym dokumencie zlicza łączną liczbę wizyt dla każdej z platform, takich jak Android lub iPhone, i przechowuje liczby w nowej tabeli programu Hive.

    Aby uzyskać więcej informacji na temat programu Hive, zobacz [Korzystanie z Apache Hive z usługą HDInsight][hdinsight-use-hive].

2. Akcja Sqoop eksportuje zawartość nowej tabeli programu Hive do tabeli utworzonej w Azure SQL Database. Aby uzyskać więcej informacji na temat Sqoop, zobacz [Korzystanie z platformy Apache Sqoop z usługą HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Aby poznać obsługiwane wersje Oozie w klastrach usługi HDInsight, zobacz [co nowego w wersjach klastra Hadoop udostępnianych przez usługi HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Tworzenie katalogu roboczego

Oozie oczekuje na przechowywanie wszystkich zasobów wymaganych dla zadania w tym samym katalogu. Ten przykład używa `wasbs:///tutorials/useoozie`. Aby utworzyć ten katalog, wykonaj następujące czynności:

1. Edytuj Poniższy kod, aby zastąpić `sshuser` nazwą użytkownika SSH klastra i Zastąp `CLUSTERNAME` nazwą klastra.  Następnie wprowadź kod, aby połączyć się z klastrem usługi HDInsight przy [użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby utworzyć katalog, użyj następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` parametr powoduje utworzenie wszystkich katalogów w ścieżce. Katalog `data` służy do przechowywania danych używanych przez skrypt `useooziewf.hql`.

3. Edytuj Poniższy kod, aby zastąpić `sshuser` nazwą użytkownika SSH.  Aby upewnić się, że Oozie może personifikować konto użytkownika, użyj następującego polecenia:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Można zignorować błędy wskazujące, że użytkownik jest już członkiem grupy `users`.

## <a name="add-a-database-driver"></a>Dodawanie sterownika bazy danych

Ponieważ ten przepływ pracy używa Sqoop do eksportowania danych do bazy danych SQL, należy podać kopię sterownika JDBC użytego do współdziałania z bazą danych SQL. Aby skopiować sterownik JDBC do katalogu roboczego, użyj następującego polecenia w sesji SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Sprawdź rzeczywisty sterownik JDBC, który istnieje w `/usr/share/java/`.

Jeśli przepływ pracy używa innych zasobów, takich jak jar, który zawiera aplikację MapReduce, należy również dodać te zasoby.

## <a name="define-the-hive-query"></a>Definiowanie zapytania programu Hive

Wykonaj następujące kroki, aby utworzyć skrypt programu Hive Query Language (HiveQL), który definiuje zapytanie. W dalszej części tego dokumentu będziesz używać zapytania w przepływie pracy Oozie.

1. Z poziomu połączenia SSH Użyj następującego polecenia, aby utworzyć plik o nazwie `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

1. Po otwarciu edytora GNU Nano Użyj następującego zapytania jako zawartości pliku:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    W skrypcie są używane dwie zmienne:

   * `${hiveTableName}`: zawiera nazwę tabeli, która ma zostać utworzona.

   * `${hiveDataFolder}`: zawiera lokalizację do przechowywania plików danych dla tabeli.

     Plik definicji przepływu pracy. XML w tym artykule przekazuje te wartości do tego skryptu HiveQL w czasie wykonywania.

1. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**, wprowadź **Y**, a następnie wybierz klawisz **Enter**.  

1. Użyj następującego polecenia, aby skopiować `useooziewf.hql` do `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    To polecenie zapisuje plik `useooziewf.hql` w magazynie zgodnym z systemem plików HDFS dla klastra.

## <a name="define-the-workflow"></a>Definiowanie przepływu pracy

Definicje przepływu pracy Oozie są zapisywane w języku definicji procesów usługi Hadoop (hPDL), który jest językiem definicji procesu XML. Wykonaj następujące kroki, aby zdefiniować przepływ pracy:

1. Użyj następującej instrukcji, aby utworzyć i edytować nowy plik:

    ```bash
    nano workflow.xml
    ```

2. Po otwarciu edytora nano wprowadź następujący kod XML jako zawartość pliku:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    W przepływie pracy są zdefiniowane dwie akcje:

   * `RunHiveScript`: to działanie jest akcją startową i uruchamia `useooziewf.hql` skrypt Hive.

   * `RunSqoopExport`: Ta akcja eksportuje dane utworzone na podstawie skryptu Hive do bazy danych SQL przy użyciu Sqoop. Ta akcja jest uruchamiana tylko w przypadku pomyślnego działania `RunHiveScript`.

     Przepływ pracy zawiera kilka wpisów, takich jak `${jobTracker}`. Te wpisy zostaną zastąpione wartościami używanymi w definicji zadania. Definicję zadania utworzysz w dalszej części tego dokumentu.

     Należy również zwrócić uwagę na wpis `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` w sekcji Sqoop. Ten wpis nakazuje Oozie udostępnienie tego Archiwum dla Sqoop, gdy ta akcja zostanie uruchomiona.

3. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**, wprowadź **Y**, a następnie wybierz klawisz **Enter**.  

4. Użyj następującego polecenia, aby skopiować plik `workflow.xml` do `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Tworzenie tabeli

> [!NOTE]  
> Istnieje wiele sposobów łączenia się z SQL Database, aby utworzyć tabelę. W poniższej procedurze użyto rozwiązania [FreeTDS](https://www.freetds.org/) z klastra usługi HDInsight.

1. Użyj następującego polecenia, aby zainstalować FreeTDS w klastrze usługi HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Edytuj Poniższy kod, aby zastąpić `<serverName>` nazwą serwera SQL Azure, a `<sqlLogin>` z logowaniem do serwera SQL Azure.  Wprowadź polecenie, aby nawiązać połączenie ze wstępnie wymaganą bazą danych SQL.  Wprowadź hasło w wierszu polecenia.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Otrzymujesz dane wyjściowe podobne do następującego tekstu:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. W wierszu `1>` wprowadź następujące wiersze:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Te instrukcje tworzą tabelę o nazwie `mobiledata`, która jest używana przez przepływ pracy.

    Aby sprawdzić, czy tabela została utworzona, użyj następujących poleceń:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zobaczysz dane wyjściowe podobne do następującego tekstu:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Zamknij narzędzie TSQL, wprowadzając `exit` w wierszu `1>`.

## <a name="create-the-job-definition"></a>Tworzenie definicji zadania

W definicji zadania opisano, gdzie znaleźć plik Workflow. XML. Opisano w nim również, gdzie można znaleźć inne pliki używane przez przepływ pracy, takie jak `useooziewf.hql`. Ponadto definiuje wartości właściwości używanych w przepływie pracy i skojarzonych plikach.

1. Aby uzyskać pełny adres magazynu domyślnego, użyj następującego polecenia. Ten adres jest używany w pliku konfiguracji utworzonym w następnym kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    To polecenie zwraca informacje, takie jak poniższy kod XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Jeśli klaster HDInsight używa usługi Azure Storage jako magazynu domyślnego, zawartość elementu `<value>` rozpoczyna się od `wasbs://`. Jeśli zamiast tego użyto Azure Data Lake Storage Gen1, rozpoczyna się od `adl://`. Jeśli Azure Data Lake Storage Gen2 jest używany, rozpoczyna się od `abfs://`.

    Zapisz zawartość elementu `<value>`, która jest używana w następnych krokach.

2. Edytuj Poniższy kod XML w następujący sposób:

    |Wartość symbolu zastępczego| Zamieniono wartość|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Wartość odebrana z kroku 1.|
    |admin| Nazwa logowania dla klastra usługi HDInsight, jeśli nie jest administratorem.|
    |serverName| Nazwa serwera usługi Azure SQL Database.|
    |sqlLogin| Logowanie do serwera usługi Azure SQL Database.|
    |sqlPassword| Hasło logowania do serwera usługi Azure SQL Database.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Większość informacji w tym pliku służy do wypełniania wartości używanych w plikach Workflow. XML lub ooziewf. HQL, takich jak `${nameNode}`.  Jeśli ścieżka jest ścieżką `wasbs`, należy użyć pełnej ścieżki. Nie skracaj go do zaledwie `wasbs:///`. Wpis `oozie.wf.application.path` definiuje, gdzie znaleźć plik Workflow. XML. Ten plik zawiera przepływ pracy, który został uruchomiony przez to zadanie.

3. Aby utworzyć konfigurację definicji zadania Oozie, użyj następującego polecenia:

    ```bash
    nano job.xml
    ```

4. Po otwarciu edytora nano wklej edytowany kod XML jako zawartość pliku.

5. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**, wprowadź **Y**, a następnie wybierz klawisz **Enter**.

## <a name="submit-and-manage-the-job"></a>Przesyłanie zadania i zarządzanie nim

Poniższe kroki służą do przesyłania przepływów pracy Oozie do klastra i zarządzania nimi. Oozie polecenie jest przyjaznym interfejsem za pośrednictwem [interfejsu API REST Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Korzystając z polecenia Oozie, należy użyć nazwy FQDN dla węzła głównego usługi HDInsight. Ta nazwa FQDN jest dostępna tylko z klastra lub Jeśli klaster znajduje się w sieci wirtualnej platformy Azure, z innych komputerów w tej samej sieci.

1. Aby uzyskać adres URL do usługi Oozie, użyj następującego polecenia:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Spowoduje to zwrócenie informacji, takich jak poniższy kod XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Częścią `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` jest adres URL do użycia z poleceniem Oozie.

2. Edytuj kod, aby zastąpić adres URL otrzymany wcześniej. Aby utworzyć zmienną środowiskową dla adresu URL, użyj poniższego polecenia, aby nie trzeba było wprowadzać go dla wszystkich poleceń:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Aby przesłać zadanie, wykonaj następujące czynności:

    ```bash
    oozie job -config job.xml -submit
    ```

    To polecenie ładuje informacje o zadaniu z `job.xml` i prześle je do Oozie, ale nie uruchamia go.

    Po zakończeniu wykonywania polecenia powinien on zwrócić identyfikator zadania, na przykład `0000005-150622124850154-oozie-oozi-W`. Ten identyfikator jest używany do zarządzania zadaniem.

4. Edytuj Poniższy kod, aby zastąpić `<JOBID>` IDENTYFIKATORem zwróconym w poprzednim kroku.  Aby wyświetlić stan zadania, użyj następującego polecenia:

    ```bash
    oozie job -info <JOBID>
    ```

    Spowoduje to zwrócenie informacji takich jak następujący tekst:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    To zadanie ma stan `PREP`. Ten stan wskazuje, że zadanie zostało utworzone, ale nie zostało uruchomione.

5. Edytuj Poniższy kod, aby zastąpić `<JOBID>` IDENTYFIKATORem zwróconym wcześniej.  Aby uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -start <JOBID>
    ```

    Jeśli sprawdzisz stan po tym poleceniu, jest w stanie uruchomienia, a informacje są zwracane dla akcji w ramach zadania.  Wykonanie zadania potrwa kilka minut.

6. Edytuj Poniższy kod, aby zastąpić `<serverName>` nazwą serwera SQL Azure, a `<sqlLogin>` z logowaniem do serwera SQL Azure.  Po pomyślnym *zakończeniu zadania* możesz sprawdzić, czy dane zostały wygenerowane i wyeksportowane do tabeli bazy danych SQL przy użyciu następującego polecenia.  Wprowadź hasło w wierszu polecenia.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    W wierszu `1>` wprowadź następujące zapytanie:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Zwracane informacje są podobne do następujących:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Aby uzyskać więcej informacji na temat polecenia Oozie, zobacz [Narzędzie wiersza polecenia Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Interfejs API REST usługi Oozie

Za pomocą interfejsu API REST Oozie można tworzyć własne narzędzia, które współpracują z Oozie. Poniżej przedstawiono informacje dotyczące korzystania z interfejsu API REST usługi Oozie w usłudze HDInsight:

* **Identyfikator URI**: można uzyskać dostęp do interfejsu API REST spoza klastra w `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Uwierzytelnianie**: Aby przeprowadzić uwierzytelnianie, użyj interfejsu API dla konta http klastra (administratora) i hasła. Przykład:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Aby uzyskać więcej informacji na temat korzystania z interfejsu API REST Oozie, zobacz [interfejs API usług sieci Web platformy Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interfejs użytkownika sieci Web Oozie

Interfejs użytkownika sieci Web Oozie zapewnia internetowy widok stanu zadań Oozie w klastrze. Korzystając z interfejsu użytkownika sieci Web, można wyświetlić następujące informacje:

   * Stan zadania
   * Definicja zadania
   * Konfigurowanie
   * Wykres akcji w zadaniu
   * Dzienniki dla zadania

Możesz również wyświetlić szczegółowe informacje o akcjach w ramach zadania.

Aby uzyskać dostęp do interfejsu użytkownika sieci Web Oozie, wykonaj następujące czynności:

1. Utwórz tunel SSH dla klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Używanie tunelowania SSH z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po utworzeniu tunelu Otwórz interfejs użytkownika sieci Web Ambari w przeglądarce sieci Web przy użyciu identyfikatora URI `http://headnodehost:8080`.

3. W lewej części strony wybierz pozycję **Oozie** > **szybkie linki** > **interfejs użytkownika sieci Web Oozie**.

    ![Kroki interfejsu użytkownika sieci Web Apache Ambari Oozie](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Interfejs użytkownika sieci Web Oozie domyślnie wyświetla uruchomione zadania przepływu pracy. Aby wyświetlić wszystkie zadania przepływu pracy, wybierz pozycję **wszystkie zadania**.

    ![Zadania przepływu pracy konsoli sieci Web Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Aby wyświetlić więcej informacji o zadaniu, wybierz zadanie.

    ![Informacje o zadaniu usługi HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Na karcie **Informacje o zadaniu** można zobaczyć podstawowe informacje o zadaniu oraz poszczególne akcje w ramach zadania. Za pomocą kart w górnej części strony można wyświetlić **definicję zadania**, **konfigurację zadania**, uzyskać dostęp do **dziennika zadań**lub wyświetlić ukierunkowany wykres o wartościach (DAG) zadania w obszarze **zadanie DAG**.

   * **Dziennik zadań**: wybierz przycisk **Pobierz dzienniki** , aby pobrać wszystkie dzienniki dla zadania, lub użyj pola **Wypełnij filtr wyszukiwania** , aby odfiltrować dzienniki.

       ![Dziennik zadań usługi HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **DAG zadania**: DAG jest graficznym przeglądem ścieżek danych wykonanych za pomocą przepływu pracy.

       ![Usługa HDInsight Apache Oozie Job Dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. W przypadku wybrania jednej z akcji na karcie **Informacje o zadaniu** zostanie wystawiona informacja dotycząca akcji. Na przykład wybierz akcję **RunSqoopExport** .

    ![Informacje o akcji zadania Oozie usługi HDInsight](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Możesz zobaczyć szczegóły akcji, na przykład link do **adresu URL konsoli**. Użyj tego linku, aby wyświetlić informacje o śledzeniu zadania dla tego zadania.

## <a name="schedule-jobs"></a>Planowanie zadań

Można użyć koordynatora, aby określić częstotliwość uruchamiania, zakończenia i wystąpienia zadań. Aby zdefiniować harmonogram dla przepływu pracy, wykonaj następujące czynności:

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **Coordinator. XML**:

    ```bash
    nano coordinator.xml
    ```

    Użyj następującego kodu XML jako zawartości pliku:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Zmienne `${...}` są zamieniane przez wartości w definicji zadania w czasie wykonywania. Zmienne są następujące:
    >
    > * `${coordFrequency}`: czas między uruchomionymi wystąpieniami zadania.
    > * `${coordStart}`: godzina rozpoczęcia zadania.
    > * `${coordEnd}`: godzina zakończenia zadania.
    > * `${coordTimezone}`: zadania koordynatora są w stałej strefie czasowej bez czasu letniego, zazwyczaj reprezentowane przy użyciu czasu UTC. Ta strefa czasowa jest określana jako strefa czasowa *przetwarzania Oozie.*
    > * `${wfPath}`: ścieżka do przepływu pracy. XML.

2. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**, wprowadź **Y**, a następnie wybierz klawisz **Enter**.

3. Aby skopiować plik do katalogu roboczego dla tego zadania, użyj następującego polecenia:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Aby zmodyfikować utworzony wcześniej plik `job.xml`, użyj następującego polecenia:

    ```bash
    nano job.xml
    ```

    Wprowadź następujące zmiany:

   * Aby polecić Oozie uruchomienie pliku koordynatora zamiast przepływu pracy, Zmień `<name>oozie.wf.application.path</name>` na `<name>oozie.coord.application.path</name>`.

   * Aby ustawić zmienną `workflowPath` używaną przez koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Zastąp tekst `wasbs://mycontainer@mystorageaccount.blob.core.windows` wartością użytą w pozostałych wpisach w pliku Job. XML.

   * Aby zdefiniować początkową, końcową i częstotliwość dla koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Te wartości ustawiają czas rozpoczęcia na 12:00 PM w dniu 10 maja 2018 i godzinę zakończenia do 12 maja 2018. Interwał uruchamiania tego zadania jest ustawiony na codziennie. Częstotliwość jest w minutach, przez co 24 godziny x 60 minut = 1440 minut. Na koniec strefa czasowa jest ustawiona na czas UTC.

5. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**, wprowadź **Y**, a następnie wybierz klawisz **Enter**.

6. Aby przesłać i uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -config job.xml -run
    ```

7. Jeśli przejdziesz do interfejsu użytkownika sieci Web Oozie i wybierzesz kartę **zadania koordynatora** , zobaczysz informacje takie jak na poniższej ilustracji:

    ![Karta zadania koordynatora konsoli sieci Web Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Następny wpis materializację** zawiera czas następnego uruchomienia zadania.

8. Podobnie jak w przypadku wcześniejszego zadania przepływu pracy, w przypadku wybrania wpisu zadania w interfejsie użytkownika sieci Web zostaną wyświetlone informacje o zadaniu:

    ![Informacje o zadaniach koordynatora Apache Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Ten obraz pokazuje tylko pomyślne uruchomienia zadania, a nie poszczególne akcje w ramach zaplanowanego przepływu pracy. Aby wyświetlić poszczególne akcje, wybierz jeden z wpisów **akcji** .

    ![Karta informacje o zadaniach konsoli sieci Web OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą interfejsu użytkownika Oozie można wyświetlać dzienniki Oozie. Interfejs użytkownika Oozie zawiera również linki do dzienników JobTracker dla zadań MapReduce, które zostały uruchomione w ramach przepływu pracy. Wzorzec do rozwiązywania problemów powinien być:

   1. Wyświetl zadanie w interfejsie użytkownika sieci Web Oozie.

   2. Jeśli wystąpi błąd lub błąd konkretnej akcji, wybierz akcję, aby zobaczyć, czy pole **komunikatu o błędzie** zawiera więcej informacji na temat błędu.

   3. Jeśli jest dostępna, użyj adresu URL z akcji, aby wyświetlić więcej szczegółów, takich jak dzienniki JobTracker, dla akcji.

Poniżej znajdują się konkretne błędy, które można napotkać i sposoby ich rozwiązywania.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: nie można zainicjować klastra

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły zadania zawierają `RunHiveScript` stanu jako **START_MANUAL**. Po wybraniu akcji zostanie wyświetlony następujący komunikat o błędzie:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Przyczyna**: adresy magazynu obiektów blob platformy Azure używane w pliku **Job. XML** nie zawierają kontenera magazynu ani nazwy konta magazynu. Format adresu magazynu obiektów BLOB musi być `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Rozwiązanie**: Zmień adresy magazynu obiektów BLOB używane przez zadanie.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie nie może personifikować użytkownika &lt;&gt;

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły zadania zawierają `RunHiveScript` stanu jako **START_MANUAL**. W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

    JA002: User: oozie is not allowed to impersonate <USER>

**Przyczyna**: bieżące ustawienia uprawnień nie zezwalają Oozie na personifikowanie określonego konta użytkownika.

**Rozwiązanie**: Oozie może personifikować użytkowników w grupie **Użytkownicy** . Użyj `groups USERNAME`, aby wyświetlić grupy, do których należy konto użytkownika. Jeśli użytkownik nie jest członkiem grupy **Użytkownicy** , użyj następującego polecenia, aby dodać użytkownika do grupy:

    sudo adduser USERNAME users

> [!NOTE]  
> Usługa HDInsight może potrwać kilka minut, ponieważ użytkownik dodaliśmy do grupy.

### <a name="launcher-error-sqoop"></a>BŁĄD uruchamiania (Sqoop)

**Objawy**: stan zadania zmienia się na **zabity**. Szczegóły zadania przedstawiają stan `RunSqoopExport` jako **błąd**. W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Przyczyna**: Sqoop nie może załadować sterownika bazy danych wymaganego do uzyskania dostępu do bazy danych.

**Rozwiązanie**: w przypadku korzystania z Sqoop z zadania Oozie należy dołączyć sterownik bazy danych z innymi zasobami, takimi jak Workflow. XML, zadanie używa. Należy również odwołać się do archiwum zawierającego sterownik bazy danych z sekcji `<sqoop>...</sqoop>` pliku Workflow. XML.

Na przykład w przypadku zadania w tym dokumencie należy wykonać następujące czynności:

1. Skopiuj plik `mssql-jdbc-7.0.0.jre8.jar` do katalogu **/Tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Zmodyfikuj `workflow.xml`, aby dodać następujący kod XML w nowym wierszu powyżej `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób definiowania przepływu pracy Oozie oraz uruchamiania zadania Oozie. Aby dowiedzieć się więcej na temat pracy z usługą HDInsight, zobacz następujące artykuły:

* [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight][hdinsight-upload-data]
* [Korzystanie z usługi Apache Sqoop z usługą Apache Hadoop w usłudze HDInsight][hdinsight-use-sqoop]
* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight][hdinsight-use-hive]
* [Opracowywanie programów MapReduce w języku Java dla usługi HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
