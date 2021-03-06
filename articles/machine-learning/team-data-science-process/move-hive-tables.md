---
title: Tworzenie tabel programu Hive i ładowanie danych z magazynu obiektów Blob — zespołu danych dla celów naukowych
description: Korzystanie z zapytań Hive, aby utworzyć tabele programu Hive i ładowanie danych z usługi Azure blob storage. Partycjonowanie tabel programu Hive i użyj zoptymalizowane pod kątem wiersz kolumnowych (ORC) odpowiadający ustawieniom lokalnym poprawić wydajność zapytań.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722530"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage

W tym artykule przedstawiono ogólny zapytań programu Hive, które umożliwiają tworzenie tabel programu Hive i ładowanie danych z usługi Azure blob storage. Wskazówki udostępniane są również na partycjonowania tabel programu Hive i przy użyciu zoptymalizowane pod kątem wiersz kolumnowych (ORC) odpowiadający ustawieniom lokalnym poprawić wydajność zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-introduction.md).
* Zainicjowano obsługę administracyjną dostosowane klaster Hadoop w usłudze HDInsight.  Jeśli potrzebujesz instrukcji, zobacz [Konfigurowanie klastrów w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Włączony zdalny dostęp do klastra, zalogowany, a następnie otworzyć konsolę wiersza polecenia usługi Hadoop. Jeśli potrzebujesz instrukcji, zobacz [Zarządzanie klastrami Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure blob storage
Jeśli utworzono maszynę wirtualną platformy Azure, postępując zgodnie z instrukcjami podanymi w temacie [Konfigurowanie maszyny wirtualnej platformy Azure na potrzeby zaawansowanej analizy](../../machine-learning/data-science-virtual-machine/overview.md), ten plik skryptu powinien zostać pobrany do programu *C:\\users\\\<nazwa użytkownika\>\\dokumenty\\katalogu skryptów analizy danych* na maszynie wirtualnej. Te zapytania Hive wymagają tylko podania schematu danych i konfiguracji magazynu obiektów blob platformy Azure w odpowiednich polach, które mają być gotowe do przesłania.

Załóżmy, że dane dla tabel programu Hive znajdują się w **nieskompresowanym** formacie tabelarycznym i że dane zostały przekazane do domyślnego (lub do dodatkowego) kontenera konta magazynu używanego przez klaster usługi Hadoop.

Jeśli chcesz mieć na celu podwyższenie poziomu **danych o podróży z NYC taksówkami**, musisz:

* **Pobierz** pliki danych o podróży z 24 [NYC taksówkami](https://www.andresmh.com/nyctaxitrips) (12 plików podróży i 12-tańszych plików),
* **Rozpakuj** wszystkie pliki do plików CSV, a następnie
* **Przekaż** je do domyślnych (lub odpowiednich kontenerów) konta usługi Azure Storage; Opcje tego konta są wyświetlane w temacie [Korzystanie z usługi Azure Storage przy użyciu klastrów usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Proces przekazywania plików CSV do kontenera domyślnego na koncie magazynu można znaleźć na tej [stronie](hive-walkthrough.md#upload).

## <a name="submit"></a>Jak przesłać zapytania Hive
Zapytania programu hive można przesyłać za pomocą:

* [Przesyłanie zapytań programu Hive przy użyciu wiersza polecenia usługi Hadoop w węzła głównego klastra Hadoop](#headnode)
* [Przesyłanie zapytań programu Hive przy użyciu edytora Hive](#hive-editor)
* [Przesyłanie zapytań programu Hive za pomocą poleceń Azure PowerShell](#ps)

Zapytania hive działają podobnego do SQL. Jeśli znasz program SQL, możesz znaleźć [gałąź dla użytkowników SQL ściągawka arkusz](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

Podczas przesyłania zapytań programu Hive, można też sterować miejsce docelowe danych wyjściowych z zapytań programu Hive, czy jest to na ekranie lub do pliku lokalnego, w węźle głównym lub do obiektu blob platformy Azure.

### <a name="headnode"></a>Przesyłanie zapytań programu Hive przy użyciu wiersza polecenia usługi Hadoop w węzła głównego klastra Hadoop
Jeśli zapytanie Hive jest złożone, przesłania go bezpośrednio w Hadoop węzłem głównym klastra zwykle prowadzi do niewyłącznej szybciej niż przesłaniem go za pomocą skryptów Edytor Hive lub programu Azure PowerShell.

Zaloguj się do węzła głównego klastra usługi Hadoop, Otwórz wiersz polecenia usługi Hadoop na pulpicie węzła głównego, a następnie wpisz polecenie `cd %hive_home%\bin`.

Istnieją trzy sposoby, aby przesłać zapytania Hive w wierszu polecenia usługi Hadoop:

* bezpośrednio
* Używanie plików ". HQL"
* za pomocą konsoli poleceń programu Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Przesłać zapytania Hive bezpośrednio w wiersza polecenia usługi Hadoop.
Można uruchomić polecenie, takie jak `hive -e "<your hive query>;`, aby przesłać proste zapytania Hive bezpośrednio w wierszu polecenia usługi Hadoop. Oto przykład, gdzie czerwoną otoczkę przedstawiono polecenia, który przesyła zapytania programu Hive i zielone pole przedstawia wyniki zapytania programu Hive.

![Polecenie, aby przesłać zapytania Hive z danymi wyjściowymi z zapytań Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Prześlij zapytania programu Hive w plikach ". HQL"
Jeśli zapytanie Hive jest bardziej skomplikowane i ma wiele wierszy, edytowanie zapytań, w wierszu polecenia lub konsoli poleceń programu Hive nie jest praktyczne. Alternatywą jest użycie edytora tekstu w węźle głównym klastra Hadoop w celu zapisania zapytań programu Hive w pliku "HQL" w katalogu lokalnym węzła głównego. Następnie zapytanie Hive w pliku ". HQL" można przesłać przy użyciu argumentu `-f` w następujący sposób:

    hive -f "<path to the '.hql' file>"

![Zapytanie programu Hive w pliku ". HQL"](./media/move-hive-tables/run-hive-queries-3.png)

**Pomiń drukowanie ekranu stanu postępu zapytań programu Hive**

Domyślnie po wysłaniu zapytania programu Hive w wierszu polecenia usługi Hadoop postęp zadań Map/Reduce są drukowane na ekranie. Aby pominąć drukowanie ekranu mapy/zmniejszyć postęp zadania, można użyć argumentu `-S` ("S" w Wielkiej litery) w wierszu polecenia w następujący sposób:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Przesłać zapytania Hive w konsoli poleceń programu Hive.
Możesz również najpierw wprowadzić konsolę poleceń Hive, uruchamiając polecenie `hive` w wierszu polecenia usługi Hadoop, a następnie przesyłając zapytania programu Hive w konsoli poleceń Hive. Oto przykład. W tym przykładzie dwie czerwone pola Zaznacz polecenia służące do wprowadzania konsoli poleceń programu Hive i zapytania programu Hive przesłanego w konsoli poleceń programu Hive, odpowiednio. Zielone pole prezentuje dane wyjściowe z zapytania programu Hive.

![Otwórz konsolę polecenia Hive i wprowadź polecenie, wyświetlanie wyników zapytania programu Hive](./media/move-hive-tables/run-hive-queries-2.png)

Poprzednie przykłady danych wyjściowych bezpośrednio wyniki zapytania programu Hive na ekranie. W węźle głównym lub obiektu blob platformy Azure, można zapisać danych wyjściowych do pliku lokalnego. Następnie można użyć innych narzędzi, aby dalej analizować dane wyjściowe zapytań programu Hive.

**Wyniki zapytania programu Hive do pliku lokalnego.**
Aby dane wyjściowe wyniki zapytania programu Hive w lokalnym katalogu w węźle głównym, musisz przesłać zapytania Hive w wierszu polecenia usługi Hadoop w następujący sposób:

    hive -e "<hive query>" > <local path in the head node>

W poniższym przykładzie dane wyjściowe zapytania programu Hive są zapisywane w pliku `hivequeryoutput.txt` w katalogu `C:\apps\temp`.

![Dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-1.png)

**Wyniki zapytania programu Hive dla danych wyjściowych do obiektu blob platformy Azure**

Mogą również przesyłać dane wyjściowe wyniki zapytania programu Hive do obiektu blob platformy Azure w ramach domyślnego kontenera klastra Hadoop. Zapytania programu Hive w taki sposób, w tym jest następująca:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

W poniższym przykładzie dane wyjściowe zapytania programu Hive są zapisywane w katalogu obiektów BLOB `queryoutputdir` w ramach domyślnego kontenera klastra usługi Hadoop. W tym miejscu wystarczy podać nazwę katalogu bez nazwy obiektu blob. Błąd jest zgłaszany, jeśli podano nazwy katalogów i obiektów blob, takie jak `wasb:///queryoutputdir/queryoutput.txt`.

![Dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-2.png)

Jeśli otworzysz domyślnego kontenera w klastrze usługi Hadoop, za pomocą Eksploratora usługi Azure Storage, możesz wyświetlić dane wyjściowe zapytania programu Hive, jak pokazano na poniższej ilustracji. Filtr (wyróżniony za czerwoną otoczkę) można zastosować tylko pobrać obiekt blob z określonej litery w nazwach.

![Eksplorator usługi Azure Storage przedstawiający dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Przesyłanie zapytań programu Hive przy użyciu edytora Hive
Możesz również użyć konsoli zapytania (edytora Hive), wprowadzając adres URL formularza *https:\//\<Nazwa klastra usługi Hadoop >. azurehdinsight. net/Home/HiveEditor* w przeglądarce internetowej. Musi być zalogowany widzą tę konsolę i dlatego należy poświadczeń klastra usługi Hadoop.

### <a name="ps"></a>Przesyłanie zapytań programu Hive za pomocą poleceń Azure PowerShell
Program PowerShell umożliwia również przesłać zapytania Hive. Aby uzyskać instrukcje, zobacz [przesyłanie zadań programu Hive przy użyciu programu PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Tworzenie bazy danych i tabel programu Hive
Zapytania programu Hive są udostępniane w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) i można z niego pobrać.

Oto zapytanie Hive, które tworzy tabelę programu Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Poniżej przedstawiono opisy pól, które należy podłączyć i inne konfiguracje:

* **Nazwa bazy danych\<\>** : Nazwa bazy danych, którą chcesz utworzyć. Jeśli chcesz tylko użyć domyślnej bazy danych, można pominąć zapytanie "*CREATE DATABASE...* ".
* **Nazwa tabeli\<\>** : Nazwa tabeli, która ma zostać utworzona w ramach określonej bazy danych. Jeśli chcesz użyć domyślnej bazy danych, tabela może być bezpośrednio określona przez *\<nazwę tabeli\>* bez \<\>nazwy bazy danych.
* **\<separatora pól\>** : separator, który ogranicza pola w pliku danych do przekazania do tabeli programu Hive.
* **\<separator wierszy\>** : separator, który ogranicza wiersze w pliku danych.
* **\<lokalizację magazynu\>** : Lokalizacja usługi Azure Storage w celu zapisania danych tabel programu Hive. Jeśli nie określisz *lokalizacji \<lokalizacji przechowywania\>* , baza danych i tabele są domyślnie przechowywane w *gałęzi/magazynie/* katalogu w domyślnym kontenerze klastra Hive. Jeśli chcesz określić lokalizację magazynu, lokalizację magazynu musi mieścić się w domyślny kontener dla bazy danych i tabel. Ta lokalizacja musi być określona jako lokalizacja względem domyślnego kontenera klastra w formacie *"wasb:///\<Directory 1 >/"* lub *"wasb:///\<Directory 1 >/\<Directory 2 >/"* , itp. Po wykonaniu zapytania, katalogi względne są tworzone w domyślnym kontenerze.
* **TBLPROPERTIES ("Skip. Header. line. Count" = "1")** : Jeśli plik danych zawiera wiersz nagłówka, należy dodać tę właściwość **na końcu** zapytania *CREATE TABLE* . W przeciwnym razie wiersz nagłówka jest ładowany jako rekord w tabeli. Jeśli plik danych nie ma wiersz nagłówka, można pominąć tę konfigurację w zapytaniu.

## <a name="load-data"></a>Ładowanie danych do tabel programu Hive
Oto zapytanie Hive, który ładuje dane do tabeli programu Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<ścieżkę do\>danych obiektu BLOB** : Jeśli plik obiektu BLOB do przekazania do tabeli programu Hive znajduje się w domyślnym kontenerze klastra usługi HDInsight Hadoop, *ścieżka\<do danych obiektu BLOB\>* powinna mieć format *"wasb://\<directory w tym kontenerze >/\<nazwa pliku obiektu BLOB >"* . Można także pliku obiektu blob w kontenerze dodatkowe klastra usługi HDInsight Hadoop. W takim przypadku *\<ścieżka do danych obiektów blob\>* powinna mieć format *wasb://\<nazwa kontenera >\<nazwa konta magazynu >. blob. Core. Windows. NET/\<obiekt blob nazwa pliku > '* .

  > [!NOTE]
  > Dane obiektu blob do przekazania do tabeli programu Hive, musi mieć domyślne lub dodatkowego kontenera konta magazynu dla klastra usługi Hadoop. W przeciwnym razie zapytanie o *dane ładowania* nie powiedzie się, ponieważ nie może uzyskać dostępu do danych.
  >
  >

## <a name="partition-orc"></a>Tematy zaawansowane: partycjonowana tabela i przechowywanie danych Hive w formacie ORC
Jeśli dane są obszerne, partycjonowania tabeli jest korzystne dla zapytań, które należy tylko skanować kilka partycji tabeli. Na przykład jest uzasadnione w celu podzielenia danych dzienników witryny sieci web według daty.

Oprócz partycjonowania tabel programu Hive, jest również przydatne do przechowywania danych programu Hive w formacie zoptymalizowane pod kątem wiersz kolumnowych (ORC). Aby uzyskać więcej informacji na temat formatowania ORC, zobacz <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Używanie plików ORC poprawia wydajność, gdy program Hive odczytuje, zapisuje i przetwarza dane</a>.

### <a name="partitioned-table"></a>Partycjonowanej tabeli
Oto zapytanie Hive, które tworzy tabeli partycjonowanej i ładowania danych do niego.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Podczas wykonywania zapytania dotyczącego tabel partycjonowanych zaleca się dodanie warunku partycji na **początku** klauzuli `where`, co zwiększa efektywność wyszukiwania.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Przechowywanie danych Hive w formacie ORC
Nie można bezpośrednio ładowanie danych z magazynu obiektów blob do tabel programu Hive, które są przechowywane w formacie ORC. Poniżej przedstawiono kroki, które należy wykonać, aby załadować dane z platformy Azure, obiekty BLOB do tabel programu Hive są przechowywane w formacie ORC.

Utwórz zewnętrzną tabelę **przechowywaną jako textfile** i Załaduj dane z magazynu obiektów BLOB do tabeli.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Tworzenie tabeli wewnętrznej przy użyciu tego samego schematu jako tabeli zewnętrznej w kroku 1, przy użyciu tego samego ogranicznik pola i przechowywania danych programu Hive w formacie ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Wybierz dane z tabeli zewnętrznej w kroku 1, a następnie wstawianie do tabeli ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Jeśli tabela textfile *\<Nazwa bazy danych\>.\<nazwa zewnętrznej tabeli textfile\>* zawiera partycje, w kroku 3 polecenie `SELECT * FROM <database name>.<external textfile table name>` wybiera zmienną partycji jako pole w zwracanym zestawie danych. Wstawianie go do *\<j nazwy bazy danych\>.\<nazwa tabeli ORC\>* nie powiedzie się, ponieważ *\<Nazwa bazy danych\>.\<ORC nazwa tabeli\>* nie ma zmiennej partycji jako pola w schemacie tabeli. W takim przypadku należy wybrać pola, które mają zostać wstawione do *\<nazwy bazy danych\>.\<nazwę tabeli ORC\>* w następujący sposób:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Można bezpiecznie porzucić *nazwę tabeli\<zewnętrznych plików tekstowych\>* podczas korzystania z następującej kwerendy po wstawieniu wszystkich danych do *nazwy bazy danych\<\>.\<nazwa tabeli Orc\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po wykonaniu tej procedury, należy mieć tabelę z danymi w formacie ORC, gotowe do użycia.  
